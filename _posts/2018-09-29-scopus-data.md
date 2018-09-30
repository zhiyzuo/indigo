---
title: "Academic Data Retrieval via Elsevier Scopus"
layout: post
date: 2018-09-29 23:30
headerImage: false
tag:
- Scopus
- API
category: blog
hidden: false
author: zhiyzuo
description: A Python wrapper for Elsevier Scopus API
---

### Academic data retrieval via Elsevier Scopus

My research use extensive datasets from [Elsevier Scopus](https://www.scopus.com/). My work can never be done without their wonderful API services. To speed up the data collection process, I have been maintaining a simple Python package [`PyScopus`](http://zhiyzuo.github.io/python-scopus/) that basically covers most of the common APIs. Check out the quickstart <a href="http://zhiyzuo.github.io/python-scopus/doc/quick-start.html" target="_blank">here</a>. It is really user-friendly!

Recently, I decided to do something to solve name ambiguity problem in Scopus, although they have done a good job on this. Yet, similar names can be really hard to handle. Thankfully, one can match affiliation and author pairs to minimize the noise brought by name ambiguity. I describe them <a href="http://zhiyzuo.github.io/python-scopus/doc/disambiguition.html" target="_blank">here</a>. Hopefully this is helpful for those who are also using Scopus for data collection.
