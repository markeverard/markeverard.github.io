---
id: 5421
title: A QueryString Visitor Group Criterion for EPiServer
date: 2017-07-11T16:50:28+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/656-revision-v1/
permalink: /2017/07/11/656-revision-v1/
---
A common usage for advanced website analytics systems (Omniture, Google etc) is to track the effectiveness of external digital marketing campaigns such as banner ads, pay-per-click, sponsored search results&#8230;&#8230;..

Technically, this is normally achieved by adding a querystring key to the campaign link url and then tracking requests that use this key. So for example, a Google Adwords campaign linking to your /products page would also include a querystring parameter of cid=marketingcampaign (example: http://yoursite.com/products?cid=marketingcampaign)

This querystring parameter can be picked up by your analytics implementation and used to track various aspects of the campaign.

On an EPiServer CMS6 R2 site, editors can use the Personalization/Visitor Group framework to provide a unique page experience per visitor, meaning that its possible to provide personalized content for each external marketing campaign on any page on your site.

Out-of-the-box, EPiServer provides a criterion which checks the incoming request Url or the page referrer, but not one that checks the querystring of the incoming request.

Creating a Visitor group criterion is <a title="Developing Custom Visitor Group Criteria" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-6-R2/Visitor-Groups-Creating-Custom-Criteria/" target="_blank">straight forward</a> &#8211; the only issue I&#8217;ve ever had trouble with; is when working on a .NET 4.0 project &#8211; <a title="Unable to implement abstract CriterionBase class in EPiServer " href="http://tedgustaf.com/en/blog/2011/4/criterionbase-no-suitable-method-found-to-override/" target="_blank">which was solved by Ted Nyberg.</a>

I&#8217;ve created a simple QueryString Criterion which will provide a match based on the following:

  1. Whether the current request contains a user specified querystring key
  2. and / or whether the current request contains a matching querystring key which also has the user specified value.

<p style="text-align: center;">
  <a href="http://markeverard.azurewebsites.net/wp-content/uploads/2011/11/querystringcriterion.png"><img class="aligncenter size-full wp-image-664" title="querystringcriterion" src="http://markeverard.azurewebsites.net/wp-content/uploads/2011/11/querystringcriterion.png" alt="" width="600" height="326" srcset="https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion.png 800w, https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion-300x163.png 300w, https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion-768x418.png 768w, https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion-720x392.png 720w, https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion-580x315.png 580w, https://www.markeverard.com/wp-content/uploads/2011/11/querystringcriterion-320x174.png 320w" sizes="(max-width: 600px) 100vw, 600px" /></a>
</p>

The source code is available as part of the <a title="Criteria Pack for EPiServer CMS on CodePlex" href="http://criteriapack.codeplex.com/" target="_blank">Criteria Pack on Codeplex</a> and of course there is a Nuget package on <a title="EPiServer Nuget feed" href="http://nuget.episerver.com/" target="_blank">Nuget.episerver.com</a> &#8211; (as soon as its been approved!)