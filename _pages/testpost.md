---
layout: test
title: Test Article
permalink: /testgrid
photoloc: /assets/gor/
dated: May 02, 2020
last_update: May 03, 2020
version: 1.10
---

When I bought my new camera I also got the [kit lens](https://www.kenrockwell.com/fuji/x-mount-lenses/18-55mm.htm). It's a great lens, much better than a typical 'kit lens', but at the time I was worried that I'd miss having the longer focal lengths of the 24-105mm lens I was moving on from. Like all decisions, this dilemma could be helped by having more information. How do I usually take photos? Which lens did I use the most? What's my favourite focal length?

{% include article_pic.html
   folder=page.photoloc
   file="GOR-5.jpg"
   class="testimg-full"
   caption="The Great Ocean Road, 2017"
%}

## EXIF data
Every digital photo includes information embedded in the file about how each picture was taken, called [EXIF data](https://en.wikipedia.org/wiki/Exif). The EXIF data for a given photo depends on the camera used, but generally includes things like the camera make and model, shutter speed, focal length, aperture, data and time of capture, even the location of the photo. Some people choose to [strip their photos](https://www.howtogeek.com/203592/what-is-exif-data-and-how-to-remove-it/) of EXIF data if they're uploading them to the internet.

So what?

With Python we can pull back the covers and take a look at the EXIF data for any photo. There are [web services](https://exifdata.com/) that offer EXIF viewing, but for this purpose Python is better for two reasons: I'd prefer to keep hold of my own data, and we can tailor the analysis to our needs. 

```python
#Imports
import datetime, exifread, os
import pandas as pd
import numpy as np

f = open("testImage")
testExif = exifread.process_file(tags)
```

## Using Python
[Exifread](https://pypi.org/project/ExifRead/) is a Python module that extracts exif metadata from jpg and tiff files. It's simple, and plenty for our purposes. The main `process_file` method returns a dictionary of each of a picture's EXIF tags in  and the corresponding value for each tag. 

Using Exifread we can write a simple Python script to scrape through the EXIF data for every photo on my hard drive. This drive only dates back a couple of years; I didn't have any earlier drives on hand - should be enough photos for a representative sample.

```python
import os
import exifread
import pandas as np
from datetime import datetime

# Directory of the photos - this case my external hard drive
photoDir = "/Volumes/Ben's Pics/Photos"

# EXIF tags to be saved, with cleaned up names for storage
exifTags = {
    'EXIF Fnumber':'Aperture',
    'EXIF DateTimeOriginal':'Date',
    'EXIF ExposureTime':'Exposure',
    'EXIF FocalLength':'Focal Length',
    'EXIF ISOSpeedRatings':'ISO',
    'MakerNote LensModel':'Lens'
}

# Blank dataframe with columns from the tags we want
photoDF = pd.DataFrame(columns = list(categories.values()))
# Walk through all the subdirectories and files in photoDir
# Add tags from every photo as a row in photoDF
for subdir, dirs, files in os.walk(photoDir):
    for file in files:
        f = open(os.path.join(subdir, file), 'rb')
        tags = exifread.process_file(f)
        # Dodgy check for photo files - non-jpgs won't have any tags!
        if len(tags) != 0:
            values = {categories[x]:str(tags[x]) for x in list(categories.values()) if x in tags}
            sdData = photoDF.append(values, ignore_index=True)
```
The last remaining step is to clean up the data a bit for analysis - the exif data is stored as a string, so we need to convert the values to integers. We ado a bit of work the aperture, exposure, and ISO numbers to make them a bit more understandable. 

```python
def fractionate(setting):
    # Splits a fraction in string form to a float
    try:
        components = setting.split('/')
        fraction = int(components[0]) / int(components[1])
    except:
        fraction = float(setting)
    finally:
        return fraction
        
# Fractionate aperture and exposure values, inverting exposure 
photoDF[['Aperture', 'Exposure']] = photoDF[['Aperture', 'Exposure']].apply(fractionate)
photoDF['Exposure'] = photoDF['Exposure'].apply(lambda x: 1/x)
# Convert ISO and focal length to integers, convert date to datetime object
photoDF[['ISO', 'Focal Length']] = photoDF[['ISO', 'Focal Length']].apply(pd.to_numeric, errors='coerce')
photoDF['Date'] = photoDF['Date'].apply(lambda x: datetime.strptime(x, '%Y:%m:%d %H:%M:%S'))        

```

We now have a dataframe with an entry for every photo on the hard drive. This is enough to start answering the main question, while also finding out a bit more about my photography habits.

What focal length do I use the most?
