---
layout: post
title: Scraping Goodreads - Reading list 1
description: Scraping book info from Goodreads using Python
summary: Scraping book info from Goodreads using Python
tags: [site, python, reading, scraping, tutorial]
last_update: "-"
version: 1
photoloc: /assets/posts/readingList
---

This is first part of 3 parts ([Part 2](/posts/reading-list-2), [Part 3](/posts/reading-list-3)) outlining how to create and populate a [Reading List](/books) page. The final product will be mostly automated, requiring only ISBNs and the year read of each book to generate a page with the cover art and relevant details for each book. 

Here is an overview of the journey:
1. Web scraping Goodreads with Python using books' ISBNs, collecting book data and a folder full of book cover images.
2. Generating and populating a Reading List page like [mine](/books) using Liquid tags and Jekyll.
3. Styling and arranging the Reading List page using CSS grid. 

## Inspecting the scraping site

{% include article_pic.html
   file="goodreads_base.png"
   class="wide"
%}

Above is the goodreads website for the next book on my list: [The Anarchy](https://www.goodreads.com/book/show/42972023-the-anarchy), by William Dalrymple. 

{% include article_pic.html
   file="goodreads_highlight.png"
   class="wide"
%}

The Goodreads website is well documented for web scraping - every container has a clearly named `id` or `itemprop` attribute. If you want to pull extra info for each book, you just have to inspect any book's page to find the relevant tag. If we inspect the book title, we can see the relevant heading tag has `id = bookTitle`. We can also find the attributes for the all of the other pieces of information that we want. 

## Beautiful Soup
[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a Python library for parsing HTML files, like websites. Now that we've inspected a Goodreads book page, we can use BeatifulSoup and Python do the work of inspecting and reading for us. The BeautifulSoup `find` method allows us to specify 


Finally, here's our Goodreads scraping function:
```python
# Function that takes the book ISBN and returns key info
def goodreadsInfo(isbn):
    
    goodreadsURL = "https://www.goodreads.com/book/isbn/"
    r = get(goodreadsURL + isbn)
    
    if r:
        soup = BeautifulSoup(r.content, 'html.parser')

        title = soup.find(id="bookTitle")
        title = title.contents[0].strip()

        author = soup.find("span",itemprop="name")
        author = author.contents[0]
        
        rating = soup.find(itemprop="ratingValue")
        rating = float(rating.contents[0].strip())
        
        pagenums = soup.find(itemprop="numberOfPages")
        pagenums = int(pagenums.contents[0].split()[0])
        
        # Gets the url of the cover photo and saves it to disk in coverPath directory
        cover = soup.find(id="coverImage")["src"] 
        coverPath = f"/Users/BenPorter/website/assets/books/{title}.jpg"
        urllib.request.urlretrieve(cover,coverPath)
        print("Saved cover photo")

        bookInfo = [title, author, rating, pagenums]
        return bookInfo
    
    else:
        print("Error: Book not found")
```

## Next steps
Now we can use this function to generate a YAML file that contains our book info. We can also run this script when new books' ISBNs are added to our master list, and it will add to our database. The full script is on [Github](). 

The YAML file goes in the site's `/_data` directory, and will be used to power the reading list page we generate using Jekyll and Liquid in [Part 2](/posts/reading-list-2).

I think the next evolution of this process will be to use the [Goodreads API](https://www.goodreads.com/api/index), which should make things a bit more intuitive - requiring a book title rather than an ISBN.    
