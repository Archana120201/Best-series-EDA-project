# Best-series-EDA-project
! pip install bs4

import numpy as np
import pandas as pd

import requests
from bs4 import BeautifulSoup


URL='https://www.goodreads.com/list/show/1381.Best_Series'

page=requests.get(URL)
page

page.status_code

htmlcode=page.text

soup=BeautifulSoup(htmlcode)

print(soup.prettify())

#### URL=`https://www.goodreads.com/list/show/1381.Best_Series`

#### title - > span itemprop = `name` 
#### author - > span itemprop = `author`
#### rating - > span class = `minirating`
#### score - > a href = `#`
#### votes - > span class = `smallText uitext`
                


title=soup.find('span',attrs={'itemprop':'name'})
title.text

author=soup.find('span',attrs={'itemprop':'author'})
author.text

rating=soup.find('span',attrs={'class':'minirating'})
rating.text

score=soup.find('a',attrs={'href':'#'})
score.text

votes=soup.find('span',attrs={'class':'smallText uitext'})
votes.text

#### All URL's

#### https://www.goodreads.com/list/show/1381.Best_Series?page=2
#### https://www.goodreads.com/list/show/1381.Best_Series?page=3
#### https://www.goodreads.com/list/show/1381.Best_Series?page=4
#### https://www.goodreads.com/list/show/1381.Best_Series?page=5
#### https://www.goodreads.com/list/show/1381.Best_Series?page=6

# Code



'''
URL='https://www.goodreads.com/list/show/1381.Best_Series?page=6'
'''
for i in range(1,7):
    print('https://www.goodreads.com/list/show/1381.Best_Series?page={}'.format(i))

#### URL = `https://www.goodreads.com/list/show/1381.Best_Series`

#### title - > span itemprop = `name` 
#### author - > span itemprop = `author`
#### rating - > span class = `minirating`
#### score - > a href=`#`
#### votes - > span class = `smallText uitext`

### Web Scrapping

title=[]
author=[]
rating=[]
score=[]
votes=[]

for i in range(1,6):
    URL='https://www.goodreads.com/list/show/1381.Best_Series?page={}'.format(i)
    
    page=requests.get(URL)
    htmlcode=page.text
    soup=BeautifulSoup(htmlcode)
    
    for x in soup.find_all('td',attrs={'width':'100%'}):
        
        Title=x.find('span',attrs={'itemprop':'name'})
        if Title is None:
            title.append(np.NaN)
        else:
            title.append(Title.text)
            
        Author=x.find('span',attrs={'itemprop':'author'})
        if Author is None:
            author.append(np.NaN)
        else:
            author.append(Author.text)
            
        Rating=x.find('span',attrs={'class':'minirating'})
        if Rating is None:
            rating.append(np.NaN)
        else:
            rating.append(Rating.text)   
            
        Score=x.find('a',attrs={'href':'#'})
        if Score is None:
            score.append(np.NaN)
        else:
            score.append(Score.text)
            
        Votes=x.find('span',attrs={'class':'smallText uitext'})
        if Votes is None:
            votes.append(np.NaN)
        else:
            votes.append(Votes.text)
            

print(len(title))
print(len(author))
print(len(rating))
print(len(score))
print(len(votes))

#### Create a DataFrame and save it in csv file

df = pd.DataFrame({'Title' :title,'Author':author,'Rating' : rating,'Score':score,'Votes':votes})

df.head()

import os
os.getcwd()

df.to_csv(r"C:\Users\bbhav\Downloads\Best_Series.ipynb.csv", index = False)

df.head()

df.to_excel(r"C:\Users\bbhav\Downloads\Best_Series.ipynb.xlsx")

# Extracting the Details using Regex

import numpy as np
import pandas as pd

import re
import warnings
warnings.filterwarnings('ignore')

df = pd.read_csv(r"C:\Users\bbhav\Downloads\Best_Series.ipynb.csv")
df.head()

### Cleaning Author col

Author

# regex=r'\>[A-Za-z]+\s[A-Za-z]+'

df['Author']=df['Author'].str.replace('\n','').replace('\(Goodreads Author\)','',regex=True)

df.head()

### Splitting Rating col

df[['Ratings','Reviews']]=df['Rating'].str.split('—',expand=True)

df.head()

# Dropping Rating col

df.drop('Rating',axis=1,inplace=True)

df.head()

### Cleaning Score col

df['Score']=df['Score'].str.replace('score: ','')

df.Score=df.Score.apply(lambda x: int(x.replace(',','')) if ',' in x else int(x))

df.head()

### Cleaning Votes col

regex=r'(.*)\s?people'

df['Votes']=df['Votes'].apply(lambda x:re.findall(regex,x))

df.Votes=df.Votes.apply(lambda x:''.join(x))

df.Votes=df.Votes.apply(lambda x: int(x.replace(',','')) if ',' in x else int(x))

df.head()

### Cleaning Ratings col

df['Ratings']=df['Ratings'].str.replace('avg rating','').replace('really liked it','',regex=True)

df['Ratings']=df['Ratings'].apply(lambda x: float(x))

df.head()

### Cleaning Reviews col

df['Reviews']=df['Reviews'].str.replace('ratings','')

df['Reviews']=df['Reviews'].apply(lambda x: int(x.replace(',','')) if ',' in x else int(x))

df.head()

### Creating Overview col

df['Overview']=df['Ratings'].apply(lambda x: 'Excellent' if x>=4.5 else('Good' if x>=4 else('Average' if x>=3 else x)))

df.head()

### Creating Hashtag col

Title

df['Hashtag']='#1'

df.head()

df['Hashtag'][90:91]='#0'

df.head()

df.Hashtag.value_counts()

df['Hashtag'][467:468]='#2'

df.head()

##regex=r'\W\d'
##df['Hashtag']=df['Title'].apply(lambda x:re.findall(regex,x))

##df.head()

df.Hashtag.value_counts()

### Creating Denotes col

df['Denotes']=df['Hashtag'].apply(lambda x: 'First Book' if x=='#1' else('Prequel' if x=='#0' else('Prequel of the next Book' if x=='#2' else x)))

df.Denotes=df.Denotes.apply(lambda x:''.join(x))

df.head()

df.Hashtag=df.Hashtag.apply(lambda x:''.join(x))

df.head()

### Creating Series col

regex=r'\((.*)?,'

df['Series']=df['Title'].apply(lambda x:re.findall(regex,x))

df.Series=df.Series.apply(lambda x:''.join(x))

df.head()

### Cleaning Title col

regex=r'(.*)\('

df['Title']=df['Title'].apply(lambda x:re.findall(regex,x))

df['Title']=df['Title'].apply(lambda x:''.join(x))

df.head()

# replacing the empty rows with nan value

df=df.replace(r"^\s*$",np.nan,regex=True)

df

df.dropna(inplace=True)

df.head()

df.reset_index(inplace=True)

df.head()

df.drop(["index"],axis=1,inplace=True)

df.head()

#1 means the first book of the Series 
#0 means the prequel of the Series
#2 means the second book or prequel of the next book

df.info()

df.describe()

# Analysis

x=df.groupby(["Votes"])[['Title']]
x.first()

l=df.groupby(['Title'])[['Ratings']]

l.first()

## Data Visualization

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings("ignore")

df.head()

### Univariate

### > I want to know which Author has written highest number of Books

df["Author"].value_counts().head(15).plot.bar()
plt.xlabel('Author')
plt.ylabel(('Count'))
plt.title('Number of Books Written by Author')
plt.xticks(rotation=90)
plt.show()


### Observation:

 >* From the above bar plot we observed that there are three Authors who wrote maximum number of Books 

### > I want to know the percentage of individual Overview

# pie plot

df['Overview'].value_counts().plot.pie(autopct="%.2f%%")
plt.title('Overview on Ratings')
plt.legend(['Good(>=4)','Average(>=3)','Excellent(>=4.5)'])
plt.show()

#### Observation

>* From the above pie chart we observed that most of the Books have Good Overview, means most of the books have ratings between 4 and 4.5

### > I want to know the count of Overview

sns.countplot(x=df['Overview'], data=df)
plt.xticks(rotation = 90)
plt.show()

#### Observation:

>* From the above bar plot we can observe that tere are more than 250 Good Overview, more than 150 Average Overview and less than 25 Excellent Overview. 

### I want to show the relation between `Hashtag` and `Denotes` columns

plt.figure(figsize=(10,20))
df["Denotes"].value_counts().plot.pie(explode=[0,0,0.9],autopct='%.3f')
plt.title('Books Denotation')
plt.legend(['#1-First Book','#0-Prequal','#2-Prequel of the next Book'])
plt.show()

### Observation:

>* From the above pie plot we obseved that most of Books are denoted as #1 which are the First Book of the Series

### > I want to know the Density of Votes

sns.distplot(df['Votes'],kde=True)
plt.title('Density of Votes')
plt.show()

>* From the above plot we can observe that most of the Books got Votes between 0 to 500 and very few Books got Votes greater than 1000

### hist plot

plt.hist(df['Ratings'])
plt.xlabel('Ratings')
plt.title('Frequency of Ratings')
plt.show()

### Observation

* This hist plot denotes Frequency distribution of Ratings

* We can observe that 4.12 rating has highest frequency 

## Bivariate

### Categorical and Numerical

### > I want to know which Series got Votes above 1000

sns.barplot(x=df['Title'].head(6),y=df['Votes'],data=df)
plt.xticks(rotation = 90)
plt.title('Title and Votes')
plt.show()

>* From the above bar plot we can observe that Harry Potter and the Scorcer's Stone Book got Votes above 3000 and 4 more Books more above 1000

### > Relation between Votes and Score

# Scatter plot

sns.scatterplot(x=df['Score'],y=df['Votes'],data=df)
plt.title('Votes and Score')
plt.show()

>* There is perfect positive correlation between Score and Votes nearly equal to 1 . So they depend on each other


### > Relation between Ratings and Reviews

x=df['Ratings']
y=df['Reviews']
plt.xlabel('Ratings')
plt.ylabel('Reviews(In Lakhs)')
plt.title('Ratings and Reviews')
plt.scatter(x,y)
plt.show()

### Observation

>* From the above plot we can see Ratings and Reviews are not correlated, and they does'nt depend on each other

### Box plot

sns.boxplot(data=df,x='Overview', y='Ratings')
plt.xticks(rotation=90)
plt.show()

>* From the above plot we can see that there are outlayers only for Good and Average.

### violin plot

sns.violinplot(y=df['Votes'],x=df['Overview'],data=df)
plt.xticks(rotation = 90)
plt.show()

### Observation

>* From the above plot we can see that Average Overviewed Series have highest density of Votes

### Multivariate

v=df.select_dtypes(include='number')
v

### heatmap 

sns.heatmap(v.corr(),annot=True)

>* From the above heatmap plot we can observe that all numerical values are positively correlated. Score and Votes are highly correlated which means as Votes increases Score increases.Ratings and Reviews are less correlated which means as Ratings increases Reviews decreases

### pair plot

sns.pairplot(df.head(50))
plt.show()

### Observation

>* From the above pair plot we observed the both distribution of single variable and relation between two variable

### Scenario

a=float(input('Enter any Ratings greater than 4 but less than 5:'))
b=int(input('Enter any Votes greater than 1000 but less less than 3500:'))
c=int(input('Enter any Score greater than 200000 but less than 344000:'))
d=int(input('Enter any Reviews greater than 7900000 but less than 9100000:'))

df[(df['Ratings']>a)&(df['Votes']>b)&(df['Score']>c)&(df['Reviews']>d)]



