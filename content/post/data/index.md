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

Now that we have seen the basic description of our dataset, we preprocess the dataset so that it caters to the specific needs of our problem. When we contextualize our dataset, there might be instances where we will have to add or remove features. We must also decide on what to do if we have missing values in our dataset.

### Are there any unnecessary features in the dataset that need to be removed?
From our Action Plan, we have two main tasks. The first is to get the posting date of the tweets that accuse Leni Robredo of cheating in the 2016 Philippine General Elections. Remember that the `Date posted` feature was already previously discussed. This means that this is one of the features that we will definitely keep. Meanwhile, our second task is to analyze the frequency of the tweets per date. For this, we will no longer need the following features: `ID`, `Timestamp`, `Group`, `Collector`, `Keywords`, `Account name`, `Account bio`, `Screenshot`,`Category`, `Topic`, `Following`, `Followers`, `Tweet`, `Tweet Translated`,  `Likes`, `Replies`, `Retweets`, `Quote Tweets`, `Views`, `Rating`, `Reasoning`, `Remarks`, `Reviewer`, and `Review`. Here we drop the unnecessary features mentioned earlier and print the remaining features:

```python

# Drop unecessary columns
df = df.drop([
        'ID', 'Timestamp', 'Group', 'Collector', 'Keywords', 
        'Account name', 'Account bio', 'Screenshot','Category', 
        'Topic', 'Following', 'Followers', 'Tweet', 'Tweet Translated', 
        'Likes', 'Replies', 'Retweets', 'Quote Tweets', 'Views', 
        'Rating', 'Reasoning', 'Remarks', 'Reviewer', 'Review'
    ],
    axis = 1
)
pretty_log('Remaining columns', list(df.columns))
 
```
_Output:_
```txt

=============================================
Remaining columns:
[   'Tweet URL',
    'Account handle',
    'Account type',
    'Joined',
    'Location',
    'Tweet Type',
    'Date posted',
    'Content type',
    'Tweet ID']
 
```

We need the `Date posted` column to include the tweet in the frequency count per day and the `Tweet ID` to ensure that this tweet is unique. We also retain the `Account handle`, `Account type`, `Joined`, `Location`, `Tweet Type`, and `Content type` feature for additional exploration.

### Are there any missing values in the dataset?
Now that we have eliminated the unnecessary features in our dataset, we can now proceed to check if our relevant features have missing values. This is to prevent errors when processing our data. To do this, we use panda's `isna()` method.

```python

# Check for missing values
pretty_log('Number of missing values per feature', df.isna().sum())
 
```
_Output:_
```txt

=============================================
Number of missing values per feature:
Tweet URL           0
Account handle      0
Account type        0
Joined              0
Location          209
Tweet Type          0
Date posted         0
Content type        0
Tweet ID            0
dtype: int64
 
```
We can see that there are 209 missing entries for Location. This is understandable because not all twitter users put their location in their profile. To alleviate this, we are going to use panda's `fillna` method to fill in the null values with an empty string.

```python

df['Location'] = df['Location'].fillna('')
pretty_log('Number of missing values per feature', df.isna().sum())
 
```
_Output:_
```txt

=============================================
Number of missing values per feature:
Tweet URL         0
Account handle    0
Account type      0
Joined            0
Location          0
Tweet Type        0
Date posted       0
Content type      0
Tweet ID          0
dtype: int64
 
```
We have completely handled missing values in the dataset.

### Are there any columns in the dataset that need to be processed to simplify the data exploration process?
Because of the particular format of the `Date posted` column, Python’s pandas module interprets the values in this column as a string and not as a `datetime` object. This is problematic because we want to easily extract specific parts of our date like the year, month, and date. We can also easily perform operations like subtraction to get the elapsed time between two given date and timestamp. To accomplish this, we had to process the `Date posted` column in Python. We use `pd.to_datetime()` to convert the timestamp string to `pd.Timestamp` and `dt.date` to trim out the time portion of the timestamp:

```python

df['Date posted'] = pd.to_datetime(df['Date posted'], format = "%d/%m/%Y %H:%M:%S").dt.date

pretty_log('Date posted values', df.head()['Date posted'])
 
```
_Output:_
```txt

=============================================
Date posted values:
0    2018-01-10
1    2018-01-19
2    2018-01-21
3    2018-01-22
4    2018-01-22
Name: Date posted, dtype: object
 
```
With this, we have binned the `Date posted` feature by date which originally has a by-second interval.

Let us again get a glimpse of the dataset to see the differences made by the preprocess step:

```python

pretty_log('First entry of the dataset', df.iloc[0])

```
_Output:_
```txt

=============================================
First entry of the dataset:
Tweet URL         https://twitter.com/VincegDelgado/status/95122...
Account handle                                       @VincegDelgado
Account type                                             Identified
Joined                                                        06/23
Location                                    The Global City, Taguig
Tweet Type                                              Text, Reply
Date posted                                              2018-01-10
Content type                                               Rational
Tweet ID                                         951221523662057473
Name: 0, dtype: object
 
```
This is enough to proceed with data exploration.

## Data Exploration