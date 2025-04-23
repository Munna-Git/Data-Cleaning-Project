# **API-Driven Data Cleaning for Real-World Data**
---

### **Documentation for API-Based Data Cleaning**


### **Project Overview**
Cleaning messy data is one thing… cleaning it as it arrives from the internet? That’s a whole new challenge.
In most data cleaning projects, you’re handed a CSV file, maybe downloaded from Kaggle, maybe pulled from a database. But in this project, I wanted to go one step further: **I fetched real-time data from an external API**. Specifically, the NewsAPI. The result? A dataset made up of fresh, dynamic, real-world news articles with all the messiness and unpredictability that comes with it.

This wasn’t just a cleaning job. It was a test of how well I can handle **live, unstructured, API-fed data**, the kind that doesn’t wait for you to be ready, and certainly doesn’t come wrapped in a bow.

From decoding JSON-like fields, parsing HTML-laden content, managing broken URLs, to dealing with missing authors and chaotic datetime formats, every part of this dataset needed attention. And that’s exactly what I gave it.

---

### **Methodology**

##### **Step 1: Data Collection**
What makes this project unique isn’t just the cleaning, it’s the source of the data. While others start with a static dump, I began with **Python scripts calling an API**, gathering news articles in real time across categories like:

- **Science**: Articles related to scientific discoveries and advancements.
- **Technology**: Coverage of tech innovations, startups, and industry trends.
- **Business**: Insights into corporate news, market trends, and economic analyses.
- **Environment**: Reports on climate change, sustainability, and ecological developments.


##### **Step 2: Data Cleaning**
The raw data contained several issues that needed addressing:
1. **Source Column**: Stored as a stringified dictionary (e.g., {"id": "the-verge", "name": "The Verge"}).
2. **Author Column**: Missing values and inconsistent formatting.
3. **Title and Description Columns**: HTML tags, special characters, and truncation markers.
4. **URL and Image URL Columns**: Invalid URLs or missing values.
5. **PublishedAt Column**: Mixed datetime formats.
6. **Content Column**: Truncated text and HTML tags.

**Cleaning Steps:**
1. **Source Column:**
   - Extracted 'name' from the JSON string.
   - Converted to a new column 'source_name'.
   - Dropped the original 'source' column.

2. **Author Column:**
   - Removed extra spaces and standardized case.
   - Replaced missing values with "Unknown Author".

3. **Title and Description Columns:**
   - Removed HTML tags, truncation markers, and special characters.
   - Cleaned text for consistency.

4. **URL and Image URL Columns:**
   - Validated URLs and flagged invalid ones.
   - Replaced invalid URLs with "Invalid URL".

5. **PublishedAt Column:**
   - Converted to standardized datetime.
   - Dropped rows with invalid dates.

6. **Content Column:**
   - Removed HTML tags and special characters.
   - Cleaned text for analysis.

7. **Duplicate Removal:**
   - Dropped duplicate articles based on 'title' and 'url'.

8. **Handling Missing Values:**
   - Filled missing values in 'author_clean' with "Unknown Author".
   - Filled missing values in 'published_at_clean' with "Unknown Date".


##### **Step 3: Saving Cleaned Data**
After cleaning, the dataset was saved as a CSV file for further analysis.

#### **Conclusion**
Through meticulous data cleaning, I transformed raw, messy data into a structured and usable format. This project not only improved the quality of the dataset but also provided valuable insights into news trends, content quality, and publication patterns. The cleaned dataset can now be used for various analyses, such as topic modeling, sentiment analysis, and trend identification.

---

# **Data Cleaning Project of the Messiest Dataset**

### **Documentation for LinkedIn Dataset**



### **Project Overview**

This project involved deep cleaning of a healthcare dataset that, to put it mildly, had seen better days. With only 102 rows and 10 columns, one might assume it’d be a quick task. But datasets, like onions, have layers, and this one had plenty. Missing values, inconsistent formats, weird characters, and even some Excel-era time travel. I general it had everything.

So here’s how I tackled it.


### Step 1: The AGE Column (a.k.a. The Identity Crisis)

I started with the **'Age'** column and it didn’t know who it was. It had:
- Numeric values like '45',
- Words like "forty",
- Mixed entries like "85 years",
- And of course, nulls.

My approach:
- Parsed the string-based numbers using regex and 'word2number'.
- Standardized everything into clean numeric ages.
- Filled missing values using **mean imputation**.
- Finally, converted the column into `int`.

The same logic worked perfectly for the **'Heart_Rate'** column too, which had its own mix of numbers, floats, words, and blanks.


### Step 2: The Gender Column - When 'nan' Isn’t NaN

Next came **'Gender'**. It had:
- Upper and lower case entries,
- A few blanks,
- And that sneaky string "nan", which looks like a missing value but isn’t.

What I learned: if you run 'str.lower()' before handling actual 'NaN' values, you might accidentally convert them into the string "nan". Whoops.

So, I:
- Standardized case using '.str.lower()',
- Replaced actual missing values with "unknown",
- Replaced any string "nan" that snuck in with "unknown" too.


### Step 3: Diagnosis - The Column That Needed a Translator

**'Diagnosis'** was a jumble of:
- "H.B.P", "HBP", "Elevated Pressure", "High blood pressure", "high BP" - all of which meant the same thing.
- "Hypertension" and "HTN" - also the same.
- And of course, '??', '1234', and 'NaN'.

So I:
- Replaced all variations of high blood pressure and hypertension with standardized labels.
- Removed garbage values like '??' and '1234' (those patients need a second opinion).
- Treated missing values accordingly.


### Step 4: The Date Columns - Oh Boy 😅

Ah yes, **the three date columns**: 'Check_in_Date', 'First_Consultation', and 'Next_Visit'.

Sample entries:
```
'Saturday, October 21, 2017', 44819, Z04, Unknown, 14/12/2025, 2018-08-21abc, 20211227
```

Here’s what I faced:
- Excel serial numbers like '44819' (converted using Excel’s epoch).
- Free-text strings like "2018-08-21abc" (cleaned using regex).
- Format mix-ups (DD/MM/YYYY vs. YYYYMMDD).
- Garbage like "Z04" and "Unknown".

And the kicker? You can't just fill missing dates with the median, not when each column has a different meaning. So:

### Step 5: Chronological Sanity Checks

Each date field meant something specific:
- 'Check_in_Date': when the patient walked in.
- 'First_Consultation': when they saw a doctor.
- 'Next_Visit': if and when they returned.

So I set rules:
- If **'check_in' exists** but **'first_consult' is before it** -> Drop.
- If **'next_visit' is before 'first_consult'** -> mark as "No return".
- If **'check_in' is missing** but other two are valid -> fill with "Unknown" (or leave as 'NaT' for datetime purity).
- If **'check_in' is after 'next_visit'** → that’s time travel, and it had to go.

With these filters, only the logically sound rows stayed.


### Step 6: Outliers and Final Imputations

At this point, the finish line was near - or so I thought.

I ran an outlier check and discovered:
- A patient with **'Age = 0'** - unless we’re treating newborns, that’s an issue.
- A **'Heart_Rate = 0.0'** - no comment. 🫠

The 0s were treated as invalid and either removed or imputed appropriately:
- **'Age'** -> mean.
- **'Heart_Rate'** -> mode.

As for the datetime columns, I learned (the hard way) that trying to fill 'NaT' values with strings turns the whole column into 'object' type. So in the end, I left 'NaT' alone - they represent missing timestamps just fine.

---

### **Final Thoughts**

Cleaning this dataset felt like a detective novel. Just when I thought I’d solved the mystery, another clue (or typo) emerged. But with patience, precision, and a bit of fun, I turned a chaotic dataset into a structured, analyzable format.
Both projects demonstrate the importance of data cleaning in transforming raw, unstructured data into actionable insights. By addressing inconsistencies, missing values, and unstructured text, we ensured that the datasets are ready for further analysis and visualization. Whether it's analyzing news trends or professional networks, clean data is the foundation for meaningful insights.
