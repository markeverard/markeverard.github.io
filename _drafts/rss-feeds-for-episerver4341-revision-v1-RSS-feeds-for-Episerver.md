---
id: 4351
title: RSS feeds for Episerver
date: 2016-08-06T15:03:59+00:00
author: Mark Everard
layout: revision
guid: http://markeverardcom.azurewebsites.net/?p=4351
permalink: /2016/08/06/4341-revision-v1/
---
I&#8217;ve released a few further tweaks to my <a href="http://nuget.episerver.com/en/OtherPages/Package/?packageId=Chief2moro.SyndicationFeeds" target="_blank">RSS / ATOM add-on</a> for <a href="http://www.episerver.com" target="_blank">Episerver</a> &#8211; <a href="https://github.com/markeverard/Chief2moro.SyndicationFeeds" target="_blank">CHIEF2MORO.SyndicationFeeds</a>.

### Whats new in version 3.0?

**On request filtering**

I&#8217;ve taken another look at filtering, which is a feature included from [version 2](http://www.markeverard.com/2015/10/26/enhanced-rss-atom-feeds-for-episerver/). I&#8217;ve modified the inbuilt IFeedContentFilterer to allow Feed pages to filter items by category via querystring parameters. This has caused a breaking change (and helped me understand how abstractions can help stable API design).

The feature allows editors to set up single feeds and for those feeds to provide subsets of data by responding to Category names that are passed via a comma separated querystring value. e.g. www.mysite.com/feed?categories=Alloy,Blog,Technology.

The default FeedFilterer has also been modified so that a content item has to be a member of all categories (both querystring and editor set) to appear. Previously it had to be a member of just one category filter.

**Editor set cache**

Each feed page now includes a new property allowing an editor to cache the feed output for a given number of seconds. This is to help performance for those feed pages on sites with a large amount of content.

**Validation**

I made some minor amends to help the feeds to validate, and made sure I correctly understood the [RSS](http://cyber.law.harvard.edu/rss/rss.html) / ATOM specification in respect of [LastUpdated and Publish dates](https://msdn.microsoft.com/en-us/library/system.servicemodel.syndication.syndicationitem.lastupdatedtime%28v=vs.110%29.aspx)

### It’s on Nuget

The source code is available at <a title="Chief2moro.SyndicationFeeds by markeverard on GitHub" href="https://github.com/markeverard/Chief2moro.SyndicationFeeds" target="_blank">https://github.com/markeverard/Chief2moro.SyndicationFeeds</a>.

A package (currently v3.0.0.0) is available in the Episerver Nuget Feed – [http://nuget.episerver.com/](http://nuget.episerver.com/en/?search=possible&sort=MostDownloads&page=1&pageSize=10) – search for CHIEF2MORO.SyndicationFeeds