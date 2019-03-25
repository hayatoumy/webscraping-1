# webscraping-1
First Project in Web Scraping

Goal: scrape the chubbygrub.com website to collect restaurants' food items and all information about them. 

```# Import libaries
import pandas as pd
from bs4 import BeautifulSoup
import requests```

## Step 1: Create a soup object 
```url = 'http://chubbygrub.com/'
res = requests.get(url)
res.status_code```
It is a good practice to always check the status of the website before scraping. 

```soup = BeautifulSoup(res.content, 'lxml')```

## Step 2: Scrape the home page soup for every restaurant

Note: Your best bet is to create a list of dictionaries, one for each restaurant. Each dictionary contains the restaurant's name and slug. 


```a = soup.find_all('a', {'class' : 'btn btn-lg btn-primary'})
names = [ai.text for ai in a]
#names```

```slugs = [ai.attrs['href'].replace('/restaurants/','') for ai in a]
```

```restaurants = []
for k, v in zip(names, slugs):
    dictionary = {}
    dictionary['name'] = k
    dictionary['slugs'] = v
    restaurants.append(dictionary)
    
# restaurants```

Sometimes I comment things out because I printed it, while I was developing the code, but it's a long output, so I comment it out after I'm done with it, but I leave it as a good reminder to always check your stuff before finishing your code. 

## Step 3: Using the slug, scrape each restaurant's page and create a single list of food dictionaries.

I'm gonna create the urls of the webpage of each restaurant, then loop through those:
```base_url = 'http://chubbygrub.com'
urls_list = [base_url + ai.attrs['href'] for ai in a]
```
```# testing out for one page: 
url = 'http://chubbygrub.com/restaurants/kfc/'
res = requests.get(url)
soup = BeautifulSoup(res.content, 'lxml')

table = soup.find('table', {'id' : 'items'})

food = []
for row in table.find_all('tr')[1:]:
#     print(row)
    items = {}
    
    items['calories'] = row.find('td', {'itemprop' : 'calories'}).text
    items['carbs'] = row.find('td', {'itemprop' : 'carbohydrateContent'}).text
    items['category'] = row.find('td', {'class' : 'hidden-xs category-column'}).text.strip()
    items['fat'] = row.find('td', {'itemprop' : 'fatContent'}).text
    items['name'] = row.find('td', {'itemprop' : 'name'}).text
    items['restaurant'] = soup.find('span', {'itemprop' : 'name'}).text
    
    food.append(items)
food
```

```from time import sleep #or import time, then later time.sleep(seconds)
```
This is an important step so you're an ethical web scraper and you don't shut down their server, or slow it down. 

```food = []
for u in urls_list:
    res = requests.get(u)
    sleep(1)
    soup = BeautifulSoup(res.content, 'lxml')
    table = soup.find('table', {'id' : 'items'})
    
    for row in table.find_all('tr')[1:]:
        items = {}    
        items['calories'] = row.find('td', {'itemprop' : 'calories'}).text
        items['carbs'] = row.find('td', {'itemprop' : 'carbohydrateContent'}).text
        items['category'] = row.find('td', {'class' : 'hidden-xs category-column'}).text.strip()
        items['fat'] = row.find('td', {'itemprop' : 'fatContent'}).text
        items['name'] = row.find('td', {'itemprop' : 'name'}).text
        items['restaurant'] = soup.find('span', {'itemprop' : 'name'}).text
        
        food.append(items)```

## Step 4: Create a pandas DataFrame from your list of foods

```df = pd.DataFrame(food)
df.shape
```
```df.to_csv('food_items.csv', index = False) #if index is true, adds another id column!```
