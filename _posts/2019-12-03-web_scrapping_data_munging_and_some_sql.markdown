---
layout: post
title:      "Web scrapping, Data Munging, and some SQL "
date:       2019-12-04 04:52:42 +0000
permalink:  web_scrapping_data_munging_and_some_sql
---

In this post, I will explain how to scrape content from a website, wrangle it into a dataframe, and even add it to a `SQL` database. This project focused on scrapping a horror films website, and storing the results in a dataframe and a `SQL` database. 


## Importing Libraries

First, we will `import libraries`:

```
import bs4
import requests
import pandas as pd
import time
import random
import math
import sqlite3
import numpy as np
```

We will use bs4 and request to load and find content, we will use pandas and sqlite3 munge and store our data. We will use time, random, numpy, and math in two different ways we can skin this cat.


## Using `BeautifulSoup` and `Requests`

Next, we will use the `BeautifulSoup4` library and the `Requests` library to parse Horror film data from [Fandom.com](http://Fandom.com).

```
url = 'https://horror.fandom.com/wiki/Alphabetical_List_of_Horror_Films'
page = requests.get(url)
page.content


soup = bs4.BeautifulSoup(page.content, 'html.parser')
soup
```

Passing  `soup` will return the `HTML` code of the defined URL where we can search for more film titles/links and add them to a list:

```
movies = soup.find('div',{"id":"mw-content-text"})
movie_list = movies.find_all('ul')
movie_list[1].find('a', href=True)['href']       
```

This will return the the film specific reference, so we still need to have a way to list all urls given the endpoints we found. We also need to consider that some movies have sequels, so we will use a `for loop` and a couple of `if statements` to help us navigate the given conditions, and this will differ from site to site.


```
movie_links = []
base_url = "https://horror.fandom.com"
for movies in movie_list:
    if len(movies.find_all('a',href=True)) >1:
        for movie in movies:
            link = movie.find('a', href=True)
            if link:
                movie_links.append(base_url + link['href'])
    else:
        link = movies.find('a', href=True)
        if link:
            movie_links.append(base_url + link['href'])
            
print(len(movie_links))
movie_links
```

We can see that we found `609` movie titles at the time of running this code, so now we have exactly what we need to start scraping the site for the content we need. 


##  Scraping with a `FOR LOOP` and Multiple Conditions
The most important thing to keep in mind is that some web pages may be different even if both pages are on the same website. It's always good to write in some test, so you can see what errors occur during scraping, if you are using a `for loop`. For this project, we wanted to store the movie's title, release date, number of deaths in the movie (on and off screen), revenue, and the length. 

Below you can see how I tried to handle situations where some or none of the data was available. I also added print statements to show if certain data was missing for each iteration. We will only pull 10, as to not set off any alarms with the website security.


```
titles = []
release_dates = []
num_deaths = []
num_on_screen_deaths = []
revenues = []
lengths = []

for url in movie_links[:10]: # Looping through all movie urls listed on the site
    
    # Requesting page and parsing html content
    page = requests.get(url)
    soup = bs4.BeautifulSoup(page.content, 'html.parser')
    
    # Pulling title from page and added to titles list
    title = soup.find('h1', attrs={"class":'page-header__title'}).text
    titles.append(title)
    print(title, url)
    
    # Pulling release date updating list
    release_day = soup.find('div',text='Release Date(s)')
    if release_day is not None:
        release_date = release_day.find_next('div').text.strip()
        release_dates.append(release_date)
    else:
        release_dates.append('Missing')
    print('Release date available?', bool(release_day), release_date)

    # Pulling table for deaths (if any )and updating list
    any_deaths = soup.find('table', attrs={"class":'mw-collapsible'})
    if any_deaths is not None:
        deaths = len(pd.read_html(str(any_deaths))[0])
        num_deaths.append(deaths)
    else:
        num_deaths.append('Missing')
    print("Any deaths available?", bool(any_deaths))

    # Pulling table for deaths on screen (if any) and updating list
    any_deaths_on_screen = soup.find('table', attrs={"class":'mw-collapsible'})
    if any_deaths_on_screen is not None:
        on_screen_deaths = pd.read_html(str(any_deaths_on_screen))[0]
        if 'On Screen' in on_screen_deaths.columns:
            num_on_screen_deaths.append(
                len(on_screen_deaths[on_screen_deaths['On Screen'] == 'Yes']))
        else:
            num_on_screen_deaths.append('Missing')   
    else:
        num_on_screen_deaths.append('Missing') 
    print("Any on screen deaths available?", bool(any_deaths_on_screen))
    
    # Pulling revenue updating list
    rev = soup.find('div', text = ("Gross" or "Budget"))
    if rev is not None:
        revenue = rev.find_next('div').text.strip()
        revenues.append(revenue)
    else:
          revenues.append('Missing')
    print('Money made available?', bool(revenue))

    # Pulling Runtime and updating list
    leng = soup.find('div', text='Runtime')
    if leng is not None:
        length = leng.find_next('div').text.strip()
        lengths.append(length) 
    else:
        lengths.append('Missing')
    print('Runtime available?', bool(length), '\n')
```

Now, it's time for us to see our results in a `Pandas DataFrame`.


### Creating a Pandas DataFrame from our lists of datapoint 

Since the data is stored into lists, we can convert these lists into a `Pandas DataFrame`:

```
movie_df = pd.DataFrame()
movie_df['Title'] = titles
movie_df['Release Date'] = release_dates
movie_df['# of Deaths'] = num_deaths
movie_df['On Screen Deaths'] = num_on_screen_deaths
movie_df['Revenue'] = revenues
movie_df['Length'] = lengths
movie_df.head()

```

And that's the first way to skin this cat!


## Scrapping with a Function

Now for the second way to skin this cat! I decided to create a function that took in the url as a parameter, and returned the movie title, release date, number of deaths (on and off screen), revenue, and length.

```
def get_movie_info(url):
    response = requests.get(url, headers=headers)
    print(response.status_code, url)
    
    soup = bs4.BeautifulSoup(response.content, 'html.parser')
    title = soup.find('h1', attrs={"class":'page-header__title'}).text
    print("Movie Title:", title)
    
    # Pulling release date updating list
    release_day = soup.find('div',text='Release Date(s)')
    if release_day is not None:
        release_date = release_day.find_next('div').text.split("(")[0].strip()
        print('Release Date Found')
    else:
        release_date = None
        print('Release Date Missing')

    # Pulling table for deaths (if any )and updating list
    any_deaths = soup.find('table', attrs={"class":'mw-collapsible'})
    if any_deaths is not None:
        deaths = len(pd.read_html(str(any_deaths))[0])
        print('Number of Deaths Found')
    else:
        deaths = None
        print("Number of Deaths Missing")

    # Pulling table for deaths on screen (if any) and updating list
    any_deaths_on_screen = soup.find('table', attrs={"class":'mw-collapsible'})
    if any_deaths_on_screen is not None:
        on_screen_death = pd.read_html(str(any_deaths_on_screen))[0]
        if 'On Screen' in on_screen_death.columns:
            on_screen_deaths = len(on_screen_death[on_screen_death['On Screen'] == 'Yes'])
            print('Number of On-Screen Deaths Found')
        else:
            on_screen_deaths = None
            print('Number of On-Screen Deaths Missing')
    else:
        on_screen_deaths = None
        print('Number of On-Screen Deaths Missing')
    
    # Pulling revenue updating list
    rev = soup.find('div', text = ("Gross" or "Budget"))
    if rev is not None:
        if rev.find_next('div') is not None:
            revenue = rev.find_next('div').text.strip()
            print('Revenue or Budget Found')
    else:
        revenue = None
        print('Revenue or Budget Missing')

    # Pulling Runtime and updating list
    leng = soup.find('div', text='Runtime')
    if leng is not None:
        length = leng.find_next('div').text.split()[0].strip()
        print('Runtime Found')
    else:
        length = None
        print('Runtime Not Found')
          
    print('Movie Scrap Completed')
    return title, release_date, deaths, on_screen_deaths, revenue, length
```

Now this is when it get's tricky. We have already visited the site over 500 times already. And if this is one of your favorite sites, you may want to slow down, and not get pinged as a robot. 

In order to fly like a human, we can use `time` and headers.

```
headers = {'User-agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36'}
sleep_min = 0
sleep_max = 5
```

Now that we have some headers to go into our function, we can use a for loop with our function to have a more clean condensed version of our previous for loop, with the same results. We can even use our `time `methods to randomly `sleep`,pause, our loop for a random `seconds` between our specified `min` and `max` ranges. We can also use `time` to see how long each iteration takes, as well as the total time to scrape all the content. 

```
now = time.time()
titles = []
release_dates = []
num_deaths = []
num_on_screen_deaths = []
revenues = []
lengths = []


for i,url in enumerate(movie_links):
    title, release_date, deaths, on_screen_deaths, revenue, length = get_movie_info(url)
    
    titles.append(title)
    release_dates.append(release_date)
    num_deaths.append(deaths)
    num_on_screen_deaths.append(on_screen_deaths)
    revenues.append(revenue)
    lengths.append(length)
    print("{} out of {} | ".format(i+1,len(movie_links)), round(((i+1)/len(movie_links))*100, 2),"%")
    print('\nTime Elapsed:', time.time()-now, 'seconds.')
    time.sleep(random.uniform(sleep_min, sleep_max))
    print("""
        .WWWW.
       WWWW""'
     .WWWW O O
  .WWWW"WW.'-. 
 WWWWWWWWWWWWW.
WWWWWWWWWWWWWWW
"WWWWWWWWWW"'\___
 /  /__ __/\___( \
(____( \X( mrf /||\  
   / /||\ \
   \______/
    \ | \ |  
     )|  \|
    (_|  /|
    |X| (X|  
    |X| |X'._  
   (__| (____)
          
          """*3,'\n')

```

Then we can use the same technique to convert the datapoints from lists into a dataframe:

```
movie_df2 = pd.DataFrame()
movie_df2['Title'] = titles
movie_df2['Release Date'] = release_dates
movie_df2['# of Deaths'] = num_deaths
movie_df2['On Screen Deaths'] = num_on_screen_deaths
movie_df2['Revenue'] = revenues
movie_df2['Runtime (Minutes)'] = lengths
movie_df2.head()
```

To wrap up this tutorial, I wanted to show you all how to create a `SQL` database and also how to add a table, insert data, and how to store data from an `SQL` database, into a dataframe using `sqlite3`.


## Creating a SQL Database

To start, we have to establish a connection with an existing `.db`, or we can create one by passing in a name of choice. We will also need the cursor to execute a few commands:

```
conn = sqlite3.connect('movies2.db')
cur = conn.cursor()
```

Next, we will create a table where we we define our table headers, data types, as well as primary key:

```
cur.execute('''
            CREATE TABLE movies2(
            Title INT PRIMARY KEY,
            Release_Date TEXT,
            Num_Deaths INT,
            On_Screen_Deaths INT,
            Revenue TEXT,
            Runtime INT
            )
            ''')					
```


Now that we have an empty table, we can iterate through our lists to insert these fields into the movies database we just created. I used the following code to insert data via for loop:

```
for title, release_date, num_death, num_on_screen_death, revenue, length in zip(
    titles, release_dates, num_deaths, num_on_screen_deaths, revenues, lengths):
    cur.execute('''
                INSERT OR REPLACE INTO movies2
                VALUES(?, ?, ?, ?, ?, ?)
                ''',(title, release_date, num_death, num_on_screen_death, revenue, length))								
```

To verify if the data was inserted correctly, we can run a query and store the results in a dataframe:

```
cur.execute('''
            SELECT * FROM movies2''')
x = cur.fetchall()
movie_df3 = pd.DataFrame(x)
movie_df3.columns = [i[0] for i in cur.description]
movie_df3
```

Perfect! Now all we have to do is save our `SQL` database:

```
conn.commit()
```

I hope this tutorial has been helpful in understanding web scrapping, munging and `SQL`. I discussed methods to use during web scrapping via for loops and functions; how to load data from the web into a pandas dataframe; and also discussed the methods of storing data in a `SQL` database. If you're ever web scrapping, be sure to be mindful of the site you are scrapping, and be ethical in your approach. 


## Resources
```
'Igor - Frankenstein Junior by Morfina', 'https://www.asciiart.eu/movies/other'"""
        .WWWW.
       WWWW""'
     .WWWW O O
  .WWWW"WW.'-. 
 WWWWWWWWWWWWW.
WWWWWWWWWWWWWWW
"WWWWWWWWWW"'\___
 /  /__ __/\___( \
(____( \X( mrf /||\  
   / /||\ \
   \______/
    \ | \ |  
     )|  \|
    (_|  /|
    |X| (X|  
    |X| |X'._  
   (__| (____)
          
          """
```
```
'Web Scraping Lesson - Students', '\nWeb Scraping Demo With SQL','\nby Matt Sparr', 
'\nhttps://github.com/matthewsparr/Data-Science-Lessons/tree/master/Web%20Scraping'
```

