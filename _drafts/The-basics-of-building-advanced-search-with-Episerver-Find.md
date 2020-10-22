---
id: 4711
title: The basics of building advanced search with Episerver Find
date: 2017-07-11T16:31:36+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=4711
permalink: /?p=4711
categories:
  - Episerver
---
It can be very straightforward to build simple search functionality using Episerver Find, some claim it can take only a few hours 🙂

Search scernarios are often vital to the user experience and advanced site navigation pathways and so are often more complex to put together than a simple search results page.&nbsp;

Episerver Find&nbsp;

## Unified or Typed?

The Episerver Find API allows you to perform a search in two quite different ways.&nbsp;

Unified search is an out-of-the-box keyword search, that handles result projection for you (i.e. all results are returned in a common format), where as a Typed Search allows you to do &nbsp;a more advanced lookup directly against your defined Episerver content model (or any .NET type you add to your index).

You will need to decide which API suits your requirements best.&nbsp;

If you are building a keyword site-search over all of your content you should probably use the Unified Search API, otherwise you may be best with the Typed search API.

## **What data do you need in your index?**

It&#8217;s important to carefully design the information you keep in your search index so you can perform the type of queries that are required.

By default, Episerver Find indexes every content item in your system and makes it very easy to perform filtering against properties of your content items.

It&#8217;s highly likely that your search requirements will require you to present a search across multiple content items, perhaps because of an implied relationship (as an example think about searching for products and product variants, two different content types but the search interface masks the underlying model, you&#8217;d just search for &#8216;yellow jumpers&#8217;)

There are a number of approaches to doing this

  * Extend the content object at index time, meaning that what is indexed in Find content contains an extension of the data contained in your original content type.&nbsp;
  * Use the nested search capability of Episerver Find (Elastic Search)

You have a couple of options for doing this, extending the object itself using (c#) extension methods, or if you have a strongly typed relationship in you model itself, then configuring Episerver Find to allow queries to be performed against these nested classes

This is probably best explained by way of an example.

&nbsp;

SearchClient.Instance.Conventions.ForInstancesOf<Product>().IncludeField(p => p.IsInStock())

where the IsInStock() extension iterates over the

You can do this any number of times, for any of the types contained in your search index.

Remeber that when using this approach, you will have to ensure that any change to a object whose data is rolled up into tanother type, should also cause a reindex of its parent (which contains the aggregated data from the child). Out-of the-box the Episerver Find integration that handles synchronising content itesm with the database

&nbsp;

&nbsp;

Nested classes &#8211;&nbsp;

with by building extensions methods to include additional data. An example might be where you want to filter itesm based on some

Another similar approach here is to use a nested class. The Find API supports searching and filtering for more complex objects / models with a single level of hierarchy (for a example a product, with a list of product variants, or a blog with a strongly typed author object). This can satisfy some of the advanced filtering, however the API options for filtering via nested objects are not quite as complete

## **2. Performance**

Episerver Find is a external service, meaning that requests made to the search index will result in serialised JSON being&nbsp;sent over the wire (via https) to the Episerver Find cloud. As with any external API, you should consider performance when designing your integration. Consider the number of request needed to perform a search / lookup content. &nbsp;The Find API is rate limited, so for a high-volume site this is a critical step . The Find API allows you to approach this in a few different ways:

  * #### **Multi-search**

The API allows you to aggregate multiple search requests into a single external call to Find, saving you the overhead and latency of many single requests. It&#8217;s quite common that you may&nbsp;need to perform multiple different queries to gather the data needed for a single web page, so remember this one!

[Find out more&nbsp;from the Episerver World documentation](https://world.episerver.com/documentation/developer-guides/find/NET-Client-API/searching/Multiple-searches-in-one-request/)

  * #### **Caching**

The Find API has some very effective caching functionality built in which can be added via the use of one extension method. Under the hood this builds a cache using the in-built .NET cache using a key built from the request parameters (meaning identical requests can be served from the cache.

You can add further dependencies into the cache call, in line with standard ASP.net functionality meaning you can invalidate the Find cache based on any other event that occur in your system.

&nbsp;

&nbsp;Search insight and optimisation&nbsp;

Search functionality is great, but letting your editor see your search functionalities effectiveness is even better,

Episerver Find also has a built-in tracking ability that captures really good insight around keyword searches performed on your site, and provides an UI so that your Search administrator can view this data and optimise your content and search results. It can capture (queries, queries that returned zero&nbsp;results and queries that returned irrelevant results). This is extremely easy to set up for Unified search, but you will have to do a little configuration for any keyword searches returned using Typed searches.

[See EMVP Henrik Fransas&#8217; post about configuring tracking for typed / custom searches](https://world.episerver.com/blogs/Henrik-Fransas/Dates/2015/2/how-to-do-custom-query-and-click-tracking-with-episerver-find/)

Conventions

**4. Conventions**

**Facets**

Search factes are now common place that perhaps calling them advanced search is a misnomer. There is a full facet API in Episerver Find that is simple to use. One important item / gotcha to look out for is that by-default only 10 facet terms&nbsp;are returned. Its easy to change this setting using the API&#8217;s to . The default limit is a nod to performance, so you need to balance out changing that with the size of your data set and how many terms&nbsp;are likely to be returned.

[How to change the default term facet count](https://world.episerver.com/forum/developer-forum/EPiServer-Search/Thread-Container/2013/6/Term-facet-count/)

&nbsp;

&nbsp;

On startup you can . In an Episerver initialisation module

**Fail gracefully**

Remember that Episerver Find is a cloud hosted search index and so using it will add an external service dependency to your site. The SLA&#8217;s offered for the Find service are very good but you should absolutely handle Episerver have written about

&nbsp;

&nbsp;