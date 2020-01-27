---
layout: post
title: Photography by numbers
description: Photography Statistics
summary: Quantifying my photography habits
tags: [stats, photography]
---

Using Python I was able to scrape through the EXIF data for every photo on my hard drive, which only dates back about ~5 years.

```python
sdPath = "/Volumes/Ben's Pics/Photos"
for subdir, dirs, files in os.walk(sdPath):
    for file in files:
        f = open(os.path.join(subdir, file), 'rb')
        tags = exifread.process_file(f)
        if len(tags) != 0:
            values = {categories[x]:str(tags[x]) for x in keys if x in tags}
            sdData = sdData.append(values, ignore_index=True)
```
