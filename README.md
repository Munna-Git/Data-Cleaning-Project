# Data Cleaning

Absolutely! Crafting a compelling and detailed documentation for your GitHub README is a great way to showcase your work and provide valuable insights for others. I’ll help you structure both documents in a storytelling tone, ensuring each project is presented uniquely and engagingly.

---

### **Project Title for API-Based Data Cleaning:**
**"Navigating Messy News Data: A Journey from API to Clean Insights"**

---

### **Documentation for API-Based Data Cleaning**

---

#### **Introduction**
Welcome to the world of messy data! In this project, we embarked on a journey to clean and analyze news articles fetched from the NewsAPI. Our goal was to identify trends in news topics, compare content quality across sources, and analyze publication patterns over time. By leveraging Python and Pandas, we tackled issues like inconsistent formatting, missing values, and unstructured text to transform raw data into actionable insights.

---

#### **Project Overview**
The dataset comprises news articles from multiple sources, including The Verge, Wired, BBC, and more. Each article contains metadata such as the source, author, title, description, URL, image URL, publication date, and content. However, the data was far from clean—missing values, inconsistent formatting, and unstructured text made it challenging to analyze effectively.

---

#### **Methodology**

##### **Step 1: Data Collection**
We chose the NewsAPI for its diverse and real-world data. The API allowed us to fetch articles based on various topics such as **science**, **technology**, **business**, and **environment**. To avoid hitting the API’s rate limits, we fetched **5 pages** of data (2 pages per topic), ensuring a balanced and varied dataset.

**Example Topics:**
- **Science**: Articles related to scientific discoveries and advancements.
- **Technology**: Coverage of tech innovations, startups, and industry trends.
- **Business**: Insights into corporate news, market trends, and economic analyses.
- **Environment**: Reports on climate change, sustainability, and ecological developments.

**Data Collection Script:**
```python
import requests
import pandas as pd
import time

API_KEY = "your_api_key"
BASE_URL = "https://newsapi.org/v2/everything"
TOPICS = ["science", "technology", "business", "environment"]

def fetch_news_data(topic, pages=2):
    all_articles = []
    for page in range(1, pages + 1):
        params = {
            "q": topic,
            "apiKey": API_KEY,
            "pageSize": 100,
            "page": page
        }
        
        try:
            response = requests.get(BASE_URL, params=params, timeout=60)
            response.raise_for_status()
            
            data = response.json()
            if "articles" in data:
                all_articles.extend(data["articles"])
                print(f"Fetched {len(data['articles'])} articles for {topic}. Total articles: {len(all_articles)}")
                
            time.sleep(60)  # Respect rate limits
            
        except requests.exceptions.RequestException as e:
            print(f"Error fetching page {page} for {topic}: {str(e)}")
            
    return all_articles

# Fetch data for each topic
articles = []
for topic in TOPICS:
    articles.extend(fetch_news_data(topic))

# Save raw data
df = pd.DataFrame(articles)
df.to_csv("raw_news_data.csv", index=False)
```

##### **Step 2: Data Cleaning**
The raw data contained several issues that needed addressing:
1. **Source Column**: Stored as a stringified dictionary (e.g., `{"id": "the-verge", "name": "The Verge"}`).
2. **Author Column**: Missing values and inconsistent formatting.
3. **Title and Description Columns**: HTML tags, special characters, and truncation markers.
4. **URL and Image URL Columns**: Invalid URLs or missing values.
5. **PublishedAt Column**: Mixed datetime formats.
6. **Content Column**: Truncated text and HTML tags.

**Cleaning Steps:**
1. **Source Column:**
   - Extracted `name` from the JSON string.
   - Converted to a new column `source_name`.
   - Dropped the original `source` column.

   ```python
   df["source_name"] = df["source"].apply(lambda x: eval(x).get("name", "Unknown Source"))
   df.drop(columns=["source"], inplace=True)
   ```

2. **Author Column:**
   - Removed extra spaces and standardized case.
   - Replaced missing values with `"Unknown Author"`.

   ```python
   df["author_clean"] = df["author"].str.strip().str.to_titlecase().fillna("Unknown Author")
   ```

3. **Title and Description Columns:**
   - Removed HTML tags, truncation markers, and special characters.
   - Cleaned text for consistency.

   ```python
   def clean_text(text):
       if pd.isna(text):
           return ""
       text = re.sub(r"<.*?>", "", text)  # Remove HTML tags
       text = re.sub(r"\[+\d+ chars\]", "", text)  # Remove truncation markers
       text = text.encode("ascii", "ignore").decode("utf-8")  # Fix encoding issues
       return text.strip()

   text_columns = ["title", "description", "content"]
   for col in text_columns:
       df[col] = df[col].apply(clean_text)
   ```

4. **URL and Image URL Columns:**
   - Validated URLs and flagged invalid ones.
   - Replaced invalid URLs with `"Invalid URL"`.

   ```python
   def is_valid_url(url):
       if pd.isna(url) or not isinstance(url, str):
           return False
       return url.startswith("http")

   df["valid_url"] = df[["url", "urlToImage"]].applymap(is_valid_url).all(axis=1)
   df["invalid_url_flag"] = ~df["valid_url"]
   ```

5. **PublishedAt Column:**
   - Converted to standardized datetime.
   - Dropped rows with invalid dates.

   ```python
   df["published_at_clean"] = pd.to_datetime(df["publishedAt"], errors="coerce")
   df.dropna(subset=["published_at_clean"], inplace=True)
   ```

6. **Content Column:**
   - Removed HTML tags and special characters.
   - Cleaned text for analysis.

7. **Duplicate Removal:**
   - Dropped duplicate articles based on `title` and `url`.

   ```python
   df = df.drop_duplicates(subset=["title", "url"], keep="first")
   ```

8. **Handling Missing Values:**
   - Filled missing values in `author_clean` with `"Unknown Author"`.
   - Filled missing values in `published_at_clean` with `"Unknown Date"`.

   ```python
   df["author_clean"].fillna("Unknown Author", inplace=True)
   df["published_at_clean"].fillna(pd.to_datetime("Unknown Date"), inplace=True)
   ```

##### **Step 3: Saving Cleaned Data**
After cleaning, the dataset was saved as a CSV file for further analysis.

```python
df.to_csv("cleaned_news_data.csv", index=False)
```

---

#### **Conclusion**
Through meticulous data cleaning, we transformed raw, messy data into a structured and usable format. This project not only improved the quality of the dataset but also provided valuable insights into news trends, content quality, and publication patterns. The cleaned dataset can now be used for various analyses, such as topic modeling, sentiment analysis, and trend identification.

---

# **Project Title for LinkedIn Dataset:**
**"Refining Professional Profiles: A Deep Dive into LinkedIn Data"**

---

### **Documentation for LinkedIn Dataset**

---

#### **Introduction**
Welcome to the world of professional data! In this project, we explored and cleaned a dataset of LinkedIn profiles. Our goal was to refine and standardize the data to enable meaningful analysis of professional networks, job titles, industries, and more. By leveraging Python and Pandas, we addressed issues like inconsistent formatting, missing values, and unstructured text to transform raw data into actionable insights.

---


#### **Conclusion**
Through meticulous data cleaning, we transformed raw, messy data into a structured and usable format. This project not only improved the quality of the dataset but also provided valuable insights into professional networks, job titles, industries, and more. The cleaned dataset can now be used for various analyses, such as network analysis, job title trends, and industry comparisons.

---

### **Final Thoughts**
Both projects demonstrate the importance of data cleaning in transforming raw, unstructured data into actionable insights. By addressing inconsistencies, missing values, and unstructured text, we ensured that the datasets are ready for further analysis and visualization. Whether it's analyzing news trends or professional networks, clean data is the foundation for meaningful insights.


This is a fantastic draft — hilarious, honest, and very relatable for anyone who’s wrangled messy data! 🤝 I’ll now rewrite it into a **professional but friendly story-driven portfolio write-up**, keeping the humor where it fits and maintaining your voice throughout.

---

## 🧼 Data Cleaning Project: Story of the Messiest Dataset I’ve Ever Seen

### Introduction

This project involved deep cleaning of a healthcare dataset that, to put it mildly, had seen better days. With only 102 rows and 10 columns, one might assume it’d be a quick task. But datasets, like onions, have layers — and this one had plenty. Missing values, inconsistent formats, weird characters, and even some Excel-era time travel — it had everything.

So here’s how I tackled it.

---

### Step 1: The AGE Column (a.k.a. The Identity Crisis)

I started with the **`Age`** column — and it didn’t know who it was. It had:
- Numeric values like `45`,
- Words like `"forty"`,
- Mixed entries like `"85 years"`,
- And of course, nulls.

My approach:
- Parsed the string-based numbers using regex and `word2number`.
- Standardized everything into clean numeric ages.
- Filled missing values using **mean imputation**.
- Finally, converted the column into `int`.

The same logic worked perfectly for the **`Heart_Rate`** column too — which had its own mix of numbers, floats, words, and blanks.

---

### Step 2: The Gender Column – When `nan` Isn’t NaN

Next came **`Gender`**. It had:
- Upper and lower case entries,
- A few blanks,
- And that sneaky string `"nan"` — which looks like a missing value but isn’t.

What I learned: if you run `str.lower()` before handling actual `NaN` values, you might accidentally convert them into the string `"nan"`. Whoops.

So, I:
- Standardized case using `.str.lower()`,
- Replaced actual missing values with `"unknown"`,
- Replaced any string `"nan"` that snuck in with `"unknown"` too.

---

### Step 3: Diagnosis – The Column That Needed a Translator

**`Diagnosis`** was a jumble of:
- `"H.B.P"`, `"HBP"`, `"Elevated Pressure"`, `"High blood pressure"`, `"high BP"` — all of which meant the same thing.
- `"Hypertension"` and `"HTN"` — also the same.
- And of course, `'??'`, `'1234'`, and `NaN`.

So I:
- Replaced all variations of high blood pressure and hypertension with standardized labels.
- Removed garbage values like `'??'` and `'1234'` (those patients need a second opinion).
- Treated missing values accordingly.

---

### Step 4: The Date Columns — Oh Boy 😅

Ah yes, **the three date columns**: `Check_in_Date`, `First_Consultation`, and `Next_Visit`.

Sample entries:
```
'Saturday, October 21, 2017', 44819, Z04, Unknown, 14/12/2025, 2018-08-21abc, 20211227
```

Here’s what I faced:
- Excel serial numbers like `44819` (converted using Excel’s epoch).
- Free-text strings like `"2018-08-21abc"` (cleaned using regex).
- Format mix-ups (DD/MM/YYYY vs. YYYYMMDD).
- Garbage like `"Z04"` and `"Unknown"`.

And the kicker? You can't just fill missing dates with the median — not when each column has a different meaning. So:

---

### Step 5: Chronological Sanity Checks

Each date field meant something specific:
- `Check_in_Date` — when the patient walked in.
- `First_Consultation` — when they saw a doctor.
- `Next_Visit` — if and when they returned.

So I set rules:
- If **`check_in` exists** but **`first_consult` is before it** → 🚫 Drop.
- If **`next_visit` is before `first_consult`** → mark as `"No return"`.
- If **`check_in` is missing** but other two are valid → fill with `"Unknown"` (or leave as `NaT` for datetime purity).
- If **`check_in` is after `next_visit`** → that’s time travel, and it had to go.

With these filters, only the logically sound rows stayed.

---

### Step 6: Outliers and Final Imputations

At this point, the finish line was near — or so I thought.

I ran an outlier check and discovered:
- A patient with **`Age = 0`** — unless we’re treating newborns, that’s an issue.
- A **`Heart_Rate = 0.0`** — no comment. 🫠

The 0s were treated as invalid and either removed or imputed appropriately:
- **`Age`** → mean.
- **`Heart_Rate`** → mode.

As for the datetime columns, I learned (the hard way) that trying to fill `NaT` values with strings turns the whole column into `object` type. So in the end, I left `NaT` alone — they represent missing timestamps just fine.

---

### Final Thoughts

Cleaning this dataset felt like a detective novel. Just when I thought I’d solved the mystery, another clue (or typo) emerged. But with patience, precision, and a bit of fun, I turned a chaotic dataset into a structured, analyzable format.

And yes, I celebrated with a well-deserved lunch.

---

Let me know if you'd like this version formatted for your portfolio, blog, or LinkedIn! I can also export it as a PDF, markdown, or Word file.