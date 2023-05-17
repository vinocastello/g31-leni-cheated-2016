---
title: "Data"
date: 2023-03-08T14:32:17+08:00
menu: main
weight: -99
---

## Data Description

The data used in this study is a collection of tweets scraped from Twitter using an open-source scraper called [Scweet](https://github.com/Altimis/Scweet). We have modified the scraper based on our needs and its repository can be accessed [here](https://github.com/marshblocker/cs132-scraper). All of the collected tweets have one thing in common: they share misinformation/disinformation in Twitter by claiming that Leni Robredo have cheated during the 2016 General Election. We want to explore our dataset in a manner that proves the hypothesis we have established in the Overview section.

### Prerequisites

Before we can start exploring our dataset using _Python_, we need to import some libraries that will aid us in the exploration and some functions for uniform logging:

```python

# Import useful libraries
import numpy as np                  # For matrix computation
import pandas as pd                 # For manipulating tabular data
import matplotlib.pyplot as plt     # For plotting
from pprint import PrettyPrinter    # For pretty printing

PP = PrettyPrinter(indent=4)

def log(key: str, val: object):
    print('=============================================')
    print('{}: {}'.format(key, val))

def pretty_log(key: str, val: object):
    print('=============================================')
    print('{}:'.format(key))
    PP.pprint(val)
 
```
We also need a function for getting the dataset online (which is currently stored in _Google Sheets_) and storing it as a `pandas.DataFrame`:

```python

# URL of the Google Sheets instance where the data is currently stored.
SHEETS_URL = 'https://docs.google.com/spreadsheets/d/13po7PpREeyEyJ6lV4JXhDYYBud8lsO2djxssOquiNYk/export?gid=928081655&format=csv'

def get_dataframe() -> pd.DataFrame:
    df = pd.read_csv(SHEETS_URL)

    return df
 
```

### First glimpse of the dataset

We can now proceed. First, we store the dataset to `df`:

```python

df = get_dataframe()
 
```

To get a glimpse of what the dataset looks like, we print the first entry in the dataframe:

```python

pretty_log('First entry', df.iloc[0])
 
```
_Output:_
```txt

=============================================
First entry:
ID 31-1
Timestamp 03/31/2023 16:43:12
Tweet URL https://twitter.com/VincegDelgado/status/95122...
Group 31
Collector De Castro, Hans Gabriel
Category RBRD
Topic Leni cheated during the 2016 General Election
Keywords fakevp, mandaraya, 2016, leni, cheater, vp, ro...
Account handle @VincegDelgado
Account name Bee Scent Tea
Account bio A father, a Mentor, a Student, and I make change!
Account type Identified
Joined 06/23
Following 405
Followers 7
Location The Global City, Taguig
Tweet correct! y shud he? he is our genuine VP not t...
Tweet Translated NaN
Tweet Type Text, Reply
Date posted 01/10/2018 22:37:38
Screenshot NaN
Content type Rational
Likes 1.0
Replies 0.0
Retweets 1.0
Quote Tweets 0.0
Views NaN
Rating NaN
Reasoning The tweet is a mis/disinformation since PET ju...
Remarks NaN
Tweet ID 951221523662057473
Reviewer NaN
Review NaN
Name: 0, dtype: object
 
```
That's a lot of features! Later on, we will process our dataset to remove some features that we do not need. But for now, we will answer some basic questions about our dataset.

### How many tweets are in the dataset?

Let's count the total entries in the dataframe to get the total number of tweets we
have collected:
```python

log('Total number of collected tweets', len(df))
 
```
_Output:_
```txt

=============================================
Total number of collected tweets: 468
 
```
We have collected 468 tweets.

### What are the features of the dataset?

Let's get the shape of the dataset and print the number of columns it has:

```python

log('Total number of features of the dataset', df.shape[1])
 
```
_Output:_
```txt

=============================================
Total number of features of the dataset: 33
 
```
Our dataset have 33 total features. Let us provide a brief description of each feature:

| Feature | Data Type | Description |
| ------- | --------- | ----------- |
| ID | `str` | Unique identifier to the data entry |
| Timestamp | `str` | When the data entry was included in the dataset |
| Tweet URL | `str` | URL that directs to the tweet associated to the data entry |
| Group | `int` | Our group number |
| Collector | `str` | Name of the group member who collected the data entry |
| Category | `str` | Which category the project belongs to, e.g. Robredo, Martial Law, etc. |
| Topic | `str` | Specific topic related to the category of the project |          
| Keywords | `str` | Set of words used to find the associated tweet |
| Account handle | `str` | Unique identifier to the user who posted the tweet. Begins with @. |
| Account name | `str` | Display name of the user. Not necessarily unique. |    
| Account bio | `str` | Biography of the user. |  
| Account type | `str` | Accounts are categorized based on their authenticity. Can be 'Anonymous', 'Identified', or 'Media'. |
| Joined | `str` | The exact month and year the user joined Twitter. |         
| Following | `int` | How many users the user follows. |       
| Followers | `int` | How many users follow the user. |
| Location | `str` | Location of the user. |        
| Tweet | `str` | The text content of the associated tweet. |           
| Tweet Translated | `str` | English translation of the tweet. Optional. |
| Tweet Type | `str` | The mediums and forms used to post the tweet. Can be 'Text', 'Image', 'Video', 'URL', and/or 'Reply'. |      
| Date posted | `str` | The exact date the tweet was posted. | 
| Screenshot | `str` | Screenshot of the Twitter page where the tweet can be found. Optional. |
| Content type | `str` | How the tweet is written. Can be 'Rational', 'Emotional', 'Transactional', or a combination of the three. |    
| Likes | `int` | Number of likes the tweet has received. |           
| Replies | `int` | Number of replies the tweet has received. |
| Retweets | `int` | Number of retweets the tweet has received. |        
| Quote Tweets | `int` | Number of quote tweets the tweet has received. Optional. |    
| Views | `int` | Number of views the tweet has received. Optional. |
| Rating | `str` | How we rate the veracity of the tweet. Optional. |          
| Reasoning | `str` | Provided reason on how the tweet contains misinformation/disinformation. |
| Remarks | `str` | Additional comments regarding the data entry. |
| Tweet ID | `str` | Unique identifier to the tweet. Optional. |       
| Reviewer | `str` | Name of the data entry reviewer. |       
| Review | `str` | The evaluation of the reviewer. |

`Tweet ID` is the only feature that is not included in the initial set of features
specified by the project requirements. We included it to remove duplicate tweets
in the dataset. We generated the `Tweet ID` column using the following Excel function (where `2` in `C2` can change based on the row of the data entry):

```txt

MID(C2,FIND("/status/",C2)+8,50)
 
```
The function extracts the right substring of the rightmost `/` separator in the `Tweet URL` feature which represents the id of the tweet. For example, the tweet url `https://twitter.com/foo/status/123456` yields `123456` as its tweet id.

We do not need all the features listed above, so we first need to preprocess the data before we can begin with data exploration.

## Data Preprocessing

Now that we have seen the nature of our dataset by exploring some of its characteristics, we modify the dataset so that it caters to the specific needs of our problem. When we contextualize our dataset, there might be instances where we will have to add or remove columns. We must also decide on what to do if we have missing values in our dataset.
### Are there any unnecessary columns in the dataset that need to be removed?
From our Action Plan, we have two main tasks. The first is to get the postsing date of the tweets that accuse Leni Robredo of cheating in the 2016 Philippine General Elections. Remember that the `Date posted` column was already previously discussed. This means that this is one of the columns that we will definitely keep. Meanwhile, our second task is to analyze the frequency of the tweets per date. For this, we will no longer need the following columns: `ID`, `Timestamp`, `Group`, `Collector`, `Keywords`, `Account name`, `Account bio`, `Screenshot`,`Category`, `Topic`, `Following`, `Followers`, `Tweet Translated`,  `Likes`, `Replies`, `Retweets`, `Quote Tweets`, `Views`, `Rating`, `Reasoning`, `Remarks`, `Reviewer`, and `Review`. These tweets were not included because they are not directly related to the to the frequency of tweets per day. At the bare minimum, we primarily need the `Date posted` column to include the tweet in the frequency count per day and the `Tweet ID` to ensure that this tweet is unique.

```python

g31_data = g31_data.drop(['ID', 'Timestamp', 'Group', 'Collector', 'Keywords', 'Account name', 'Account bio', 'Screenshot','Category', 'Topic', 'Following', 'Followers', 'Tweet Translated', 'Likes', 'Replies', 'Retweets', 'Quote Tweets', 'Views', 'Rating', 'Reasoning', 'Remarks', 'Reviewer', 'Review'],axis = 1)
g31_data.head()
 
```

### Are there any missing values in the dataset?
Now that we have eliminated the unnecessary columns in our dataset, we can now proceed to check if our relevant columns have missing values. This is to prevent errors when processing our data. To do this, we use panda's `isna` method.

```python

# check for missing values
print(f"Number of entries with missing values = {g31_data.isna().sum()}")
 
```
Next, we can see that there are 209 missing entries for Location. This is understandable because not all twitter users put their location in their profile. To alleviate this, we are going to use panda's `fillna` method to fill in the null values with an emptys string.

```python

g31_data['Location'] = g31_data['Location'].fillna('')
print(f"Number of entries with missing values = {g31_data.isna().sum()}")
 
```

### Are there any columns in the dataset that need to be processed to simplify the data exploration process? Maybe create a new column?
Because of the particular format of the `Date posted` column, Python’s pandas module interprets the values in this column as a string and not as a `datetime` object. This is problematic because we want to easily extract specific parts of our date like the year, month, and date. We can also easily perform operations like subtraction to get the elapsed time between two given date and timestamp. To accomplish this, we had to process the `Date posted` column in Python. Using the `strptime` method of the `datetime` object from the `datetime` module, we were able to convert the string date into a datetime object. We accomplished it using this:

```python

import datetime as datetime
string_date_time = “07/02/2018 11:38:08”
dt = datetime.strptime(string_date_time, "%d/%m/%Y %H:%M:%S")
 
```

```python

g31_data['Date posted'] = pd.to_datetime(g31_data['Date posted'], format = "%d/%m/%Y %H:%M:%S")
g31_data.head()
 
```

## Data Exploration