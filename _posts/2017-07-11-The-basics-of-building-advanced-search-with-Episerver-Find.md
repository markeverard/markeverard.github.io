---
id: 4711
title: The basics of building advanced search with Episerver Find
date: 2017-07-11T16:31:36+00:00
author: Mark Everard
layout: post
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Search, Find]
---
It can be very straightforward to build simple search functionality using Episerver Find, some claim it can take only a few hours 🙂

Search scernarios are often vital to the user experience and advanced site navigation pathways and so are often more complex to put together than a simple search results page.

Here are **5 key elements** to consider when building your search and navigation experiences with Episerver Find


## 1. What data do you need in your index?

It's important to carefully design the information you keep in your search index so you can perform the type of queries that are required.

By default, Episerver Find indexes every content item in your system and makes it very easy to perform filtering against properties of your content items.

It'&#8217;'s highly likely that your search requirements will require you to present a search across multiple content items, perhaps because of an implied relationship (as an example think about searching for products and product variants, two different content types but the search interface masks the underlying model, you'd just search for '&#8216;'yellow jumpers'&#8217;')

There are a number of approaches to doing this

  * Extend the content object at index time, meaning that what is indexed in Find content contains an extension of the data contained in your original content type.
  * Use the nested search capability of Episerver Find (Elastic Search)

### Extending the content object

You have a couple of options for doing this, extending the object itself using (c#) extension methods, or if you have a strongly typed relationship in your model itself, then configuring Episerver Find to allow queries to be performed against these nested classes

This is probably best explained by way of an example.

 ~~~csharp
SearchClient.Instance.Conventions.ForInstancesOf<Product>().IncludeField(p => p.IsInStock())
~~~

where the IsInStock() extension caclulates whether that particular product is in stock.

You can do this any number of times, for any of the types contained in your search index.

Remeber that when using this approach, you will have to ensure that any change to a object whose data is rolled up into tanother type, should also cause a reindex of its parent (which contains the aggregated data from the child). Out-of the-box the Episerver Find integration that handles synchronising content itesm with the database

### Nested classes

Another similar approach here is to use a nested class. The Find API supports searching and filtering for more complex objects / models with a single level of hierarchy (for a example a product, with a list of product variants, or a blog with a strongly typed author object). This can satisfy some of the advanced filtering, however the API options for filtering via nested objects are not quite as complete meaning some types of adavnced search on nested objects may be difficult to achieve.

## 2. Unified or Typed?

The Episerver Find API allows you to perform a search in two quite different ways.

Unified search is an out-of-the-box keyword search, that handles result projection for you (i.e. all results are returned in a common format), where as a Typed Search allows you to do a more advanced lookup directly against your defined Episerver content model (or any .NET type you add to your index).

You will need to decide which API suits your requirements best.

If you are building a keyword site-search over all of your content you should probably use the Unified Search API, otherwise you may be best with the Typed search API.

## 3. Performance

Episerver Find is a external service, meaning that requests made to the search index will result in serialised JSON being sent over the wire (via https) to the Episerver Find cloud. As with any external API, you should consider performance when designing your integration. Consider the number of request needed to perform a search / lookup content. The Find API is rate limited, so for a high-volume site this is a critical step . The Find API allows you to approach this in a few different ways:

### Multi-search

The API allows you to aggregate multiple search requests into a single external call to Find, saving you the overhead and latency of many single requests. It's quite common that you may need to perform multiple different queries to gather the data needed for a single web page, so remember this one!

[Find out more&nbsp;from the Episerver World documentation](https://world.episerver.com/documentation/developer-guides/find/NET-Client-API/searching/Multiple-searches-in-one-request/)

### Caching

The Find API has some very effective caching functionality built in which can be added via the use of one extension method. Under the hood this builds a cache using the in-built .NET cache using a key built from the request parameters (meaning identical requests can be served from the cache.

You can add further dependencies into the cache call, in line with standard ASP.net functionality meaning you can invalidate the Find cache based on any other event that occur in your system.

### Facets

Search facets are now common place that perhaps calling them advanced search is a misnomer. There is a full facet API in Episerver Find that is simple to use. One important item / gotcha to look out for is that by-default only 10 facet terms are returned. Its easy to change this setting using the API's to . The default limit is a nod to performance, so you need to balance out changing that with the size of your data set and how many terms are likely to be returned.

[How to change the default term facet count](https://world.episerver.com/forum/developer-forum/EPiServer-Search/Thread-Container/2013/6/Term-facet-count/)

## 4. Search insight and optimisation

Search functionality is great, but letting your editor see your search functionalities effectiveness is even better,

Episerver Find also has a built-in tracking ability that captures really good insight around keyword searches performed on your site, and provides an UI so that your Search administrator can view this data and optimise your content and search results. It can capture (queries, queries that returned zero results and queries that returned irrelevant results). This is extremely easy to set up for Unified search, but you will have to do a little configuration for any keyword searches returned using Typed searches.

[See EMVP Henrik Fransas&#8217; post about configuring tracking for typed / custom searches](https://world.episerver.com/blogs/Henrik-Fransas/Dates/2015/2/how-to-do-custom-query-and-click-tracking-with-episerver-find/)

## 5. Fail gracefully

Remember that Episerver Find is a cloud hosted search index and so using it will add an external service dependency to your site. The SLA's offered for the Find service are very good but you should absolutely consider what should happen should it not be available. Such is Find's utility that many implementations become highly coupled with Find (mainly due to it's ability to perform advanced content lookup), so there may not be an approach which allows you to offer the same functionality. One way I've dealt with this in the past is to limit the avaiable functinality should Finf not be available, for eaxmple - hiding filtering capabiliyt on listing pages and instead offering a simple paginated list of content which is more easily achievable using the ContentLoader API's.

