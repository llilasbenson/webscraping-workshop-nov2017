
# Scraping the Castro Speech Database

After learning how to scrape collections from the Internet Archive, we're now going to try our hand at scraping the Castro Speech Database, a digital collection held by the Benson Latin American Collection at UT Austin: http://lanic.utexas.edu/la/cb/cuba/castro.html

The Castro Speech Database contains full-text translations of over 2500 speeches, interviews, press conferences, and Cuban news articles by or about Fidel Castro from 1959 through 1996, based on the records of the Foreign Broadcast Information Service (FBIS), a U.S. government agency responsible for monitoring broadcast and print media in countries throughout the world.

The objective of this tutorial is to learn how to scrape text from a single webpage and to expand this technique to extract text from hundreds of webpages.
## 1. Scraping the content of a single webpage

In order to process HTML we pull down from the web, we'll be using a Python library called Beautiful Soup. The name comes from "Alice in Wonderland," which is a fun fact you can throw around at parties.


```
from bs4 import BeautifulSoup
from urllib import request
```

We'll now need to identify a base link to scrape from.


```
url = 'http://lanic.utexas.edu/project/castro/1959/'
```


```
html = request.urlopen(url).read()
print(html[0:4000])

```


```
soup = BeautifulSoup(html, 'lxml')

from pprint import pprint
pprint(soup.body)
```

This line says, "take the HTML that you've pulled down and get ready to do Beautiful Soup things to it." In programming speak, we're saying "turn that HTML into a Beautiful Soup object." Saying something is an object is a way of saying "I expect this data to have certain characteristics and be able to do certain things." In this case, BeautifulSoup gives us a series of ways to manipulate the HTML using HTML and CSS structural elements.

We can do things like:

* Get all the links


```
pprint(soup.find_all('a')[0:20])
```

We can say, get me all the text.


```
print(soup.text)
```

It might not be very clear, but that's just the text of the webpage as one long string with all the HTML stripped out.

How many links are there on this page anyway? We can find out by checking out the length of this ResultSet:


```
print(len(soup.find_all('a')))
```

## 2. Scraping the text of a single speech

Now that we've scraped the webpage containing an index of all the Castro speeches from 1959, let's focus on scraping a single speech from this index.


```
url = 'http://lanic.utexas.edu/project/castro/db/1959/19591128-1.html'
html = request.urlopen(url).read()
soup = BeautifulSoup(html, 'lxml')
raw_text = soup.select('pre')
clean_text = raw_text[0].text
print(clean_text)
```

The 'pre' bit in the lines of code above is using css syntax to walk the structure of the HTML document to get to what we want. I know that I need those particular selectors because I have examined the HTML for the page to see how it is organized. You can do this by going to your webpage and inspecting the element that you want by right clicking on it and selecting "inspect element". This particular code says, "find the 'pre' tags. Once we have all that, print out the text of those 'pre' tags.

Alright, so now that we've been able to extract the text from a single speech in the Castro speech database, let's expand the scope of our scraping to extract the text of hundreds of Castro speeches spanning several years!

## 3. Scraping Hundreds of Speeches from 1959-1970

First, create a new folder within your shared folder on the desktop to hold the indices of speeches for the years 1959-1970.

`os` is the 'operating system' python library, `chdir` is shorthand for 'change directory' and 'mkdir' is shorthand for 'make directory".


```
import os
!mkdir -p castro_speeches/indices-of-speeches_1959to1970
os.chdir('castro_speeches/indices-of-speeches_1959to1970')
```

Open your shared folder on the desktop to take a look and see if the folder was created.

Now we're going to identify the base URL we'll be scraping from. All the URLs in the Castro database reflect a highly structured, uniform naming system, with each file assigned a unique identifier and nested within a directory structure by year.

Using a special function called `wget` (short for 'website get'), we will scrape all of the URLs corresponding to the indices of speeches for each year from 1959-1970.

These files will be written into the folder `indices-of-speeches_1959to1970`

(Note that in the year range identified, we end at 1971, not 1970. This is because Python counts integers starting at 0 not 1, so ending at 1971 accommodates for this counting system.)



```
import subprocess

for i in range(1959,1971):
    base_url = 'http://lanic.utexas.edu/project/castro/'
    url = base_url + str(i)
    subprocess.call(['wget', url, '--adjust-extension'])
```

Check the folder `indices-of-speeches_1959to1970` you made within the sharedfolder on the desktop. Hopefully you see several html files corresponding to the years 1959-1970! You've successfully scraped HTML files from the web and can use these files to extract the specific text you're interested in!



```
html_filenames = [item for item in os.listdir('./') if '.html' in item]

html_filenames [:10] #this prints out the filenames for the first ten items in our list.
```

Now we're going to use Beautiful Soup to extract the URLs corresponding to each speech listed in the yearly indices.


```
url_list = []

for file in html_filenames:
    page = open(file).read()
    from bs4 import BeautifulSoup
    soup = BeautifulSoup(page, 'lxml')
    for link in soup.findAll('a'):
        try:
            url_list.append('http://lanic.utexas.edu' + link['href'])
        except Exception as e:
            print(e)
```

The output of repeated 'href' represent the empty entries in the 'a href' field throughout all the html files.


```
url_list # prints out the content of the list
```

How many speech URLs did we scrape? Let's find out!


```
len(url_list) # len means length. We're finding the number of URLs in our list.
```

Let's create a filtered list of only the urls that correspond to speeches.


```
urls_filtered = []

for item in url_list:
    if '/db/' in item:
        urls_filtered.append(item)

urls_filtered [:50]
```

Great! We have a filtered list of all the URLs we need. Now let's copy this list to a text file!


```
os.chdir('..')
with open('urls-speeches.txt', 'w') as file_out:
    for url in urls_filtered:
        file_out.write(url)
        file_out.write('\n')
```

Now we have a text file containing all the URLs we want to scrape content from. Open the Castro's shared folder on the desktop to open the `urls-speeches.txt` file we just made.

The next step is to create a new folder called 'speeches' where we'll dump all the text we scrape from the list of URLs we have.


```
!mkdir speeches
```

Now we're going to bulk scrape the HTML content of each URL that corresponds to a speech. Note that when we run the cell below, it'll take approximately 56 seconds. When that time has lapsed, we should have successfully downloaded 443 HTML files.


```
import subprocess

!wget -i urls-speeches.txt --wait=0.1 -P ./speeches/
```

Hooray! We now have an HTML file for each speech in the Castro Speech Database from 1959 to 1970!

Open one of the HTML files by control clicking and selecting "TextWrangler" from the "Open With" dropdown menu.

You'll see that the speech text we want is still embedded in HTML. Let's run Beautiful Soup to extract the speech texts from all 443 HTML files. Then, we'll create plain text files for each speech!


```
os.chdir('speeches')
         
html_filenames = [item for item in os.listdir('./') if '.html' in item]

for file in html_filenames:
    try:
        page = open(file, 'rb').read().decode('latin1') #read as byte stream
        soup = BeautifulSoup(page, 'lxml')
        body_text = soup.body
        raw_text = soup.select('pre')
        clean_text = raw_text[0].text
        with open(file.replace('.html', '.txt'), 'w') as file_out:
            file_out.write(clean_text)
    except Exception as e:
        print(file)
```

You should now have clean, plain text files of all the 1959 to 1970 speeches. With a few modifications to the code above, you can easily extend this web scraping to the entire database for speeches from 1959 to 1996!
