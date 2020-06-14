---
layout: post
title: A history of NBA jersey numbers
description: Analysing trends in NBA jersey numbers
summary: Analysing trends in NBA jersey numbers
tags: [nba, python, viz]
photoloc: /assets/posts/jerseys2/
last_update: May 03, 2020
version: 1
---

This post is a continuation of the [previous post](/posts/jersey-numbers), diving a bit more into the numbers and trends to figure out some patterns in NBA uniform number choices. Let's get started.

```python
#Import data from csv
jerseys = pd.read_csv("jersey_numbers.csv")

@decorator
def function(jerseyNumber):
    if jerseyNumber:
        return jerseyNumber + 1
    else:
        return 1
    
class PlayerClass(PlayerName):
    def __init__(self):
        self.name = PlayerName


#Calculate the most popular and max wearers for each year
jerseys["Popular"] = jerseys.drop(['UNK'],axis=1).idxmax(axis=1)
jerseys["Max"] = jerseys.drop(['UNK','Popular'],axis=1).idxmax(axis=1)

#Fix the single-digit columns
jerseys["03"] = jerseys["03"] + jerseys["3"]
jerseys["07"] = jerseys["07"] + jerseys["7"]
jerseys["09"] = jerseys["09"] + jerseys["9"]
jerseys = jerseys.drop(["3","7","9"],axis=1)
jerseys.rename(columns={str(i):"0" + str(i) for i in range(1,10)},inplace=True)

#Reorder the columns
cols = jerseys.columns.tolist().sort()
jerseys = jerseys[cols]
```

A couple of fun facts for the 2018-19 season. The most popular uniform number in the NBA was number 3. 30 players wore it! One for every team in the league, but 5 teams have retired that number - it's so popular that multiple players on one team wore it in the same year. Somehow the Cavs let **four** players wear the number 3 last year...[^1] Number 0 is more popular popular than 00, and always has been. 


## Footnotes
[^1]: [George Hill](https://www.basketball-reference.com/players/h/hillge01.html), [Patrick McCaw](https://www.basketball-reference.com/players/m/mccawpa01.html), [Marquese Chriss](https://www.basketball-reference.com/players/c/chrisma01.html), and [Kobi Simmons](https://www.basketball-reference.com/players/c/chrisma01.html)
