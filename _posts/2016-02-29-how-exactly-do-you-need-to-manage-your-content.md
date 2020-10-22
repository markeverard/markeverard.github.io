---
id: 4381
title: How exactly do you need to manage your content?
date: 2016-02-29T11:00:29+00:00
author: Mark Everard
layout: post
guid: http://markeverardcom.azurewebsites.net/?p=4381
permalink: /2016/02/29/how-exactly-do-you-need-to-manage-your-content/
dsq_thread_id:
  - "4619975353"
categories:
  - Digital
  - Episerver
  - Opinion
  - Technology
tags:
  - homepage
---
Detailed content management requirements are often overlooked in the design and build of a content managed website. This can result in an implementation that lacks flexibility and incurs higher costs over its lifetime.

## Dual purpose

Web Content Management (WCM/CMS) implementations deliver two key capabilities for an organisation:

  1. A website, perhaps with a new or updated design or brand.
  2. The ability to manage the website content via a content management system

To achieve the best implementation outcome, it is important start your project with design measures and KPI&#8217;s that focus on both the success of the website as a business tool, as well as the CMS that enables it. Too often the focus is on just the website, largely because of the difficulty in measuring how well your content management system works for your organisation.

Whether your CMS is effective is rarely immediately apparent, instead it will emerge over time through your content team&#8217;s ability to operate autonomously and a reduction in future development need and cost.

## **A CMS is for life, not just for launch**

Even after a successful implementation, it is still easy to overlook your content management requirements. Marketing and campaign-led activity often have bespoke visual needs which can challenge your existing CMS design and content flexibility. They also often come with fixed deadlines and limited opportunity to fully assess the expected campaign content usage and lifespan.

The default position may be to &#8216;content manage all the things&#8217;, but do you really need to?

It is all to easy to build up technical debt within your implementation by designing bespoke types that are so specialised they offer little or no opportunity for reuse. This feature bloat not only increases the size of your code-base, making the landscape more complex for your technical teams. It also makes the system more complex for editorial teams as they have to navigate additional content types. If you have many content types in your CMS that are only used in a few places then you may already be suffering.

## **Don&#8217;t waste time (or money)**

To keep on the right track, you should always have your content editing team as a key stakeholder in any website or CMS development. After all, they are the team that will be using any functionality and be responsible for publishing campaign content / functionality on your website through your CMS. It is only sensible that their feedback is taken onboard.

The basics of content reuse requirements aren&#8217;t that difficult.:

  1. I need to reuse this content and/or design in many places across the website
  2. I may reuse this content occasionally
  3. I am unlikely to reuse this content, but I need to be able to change it at short notice
  4. I shouldn’t need to change the content. If I do I am happy to rely on technical support

The answers will drive very different technical solutions, ranging from fully reusable content managed features to a set of static pages to be thrown away after use. The middle ground here is also useful, which allows more technically minded CMS editors and admins to standup flat HTML pages through the CMS interface (an example for Episerver &#8211; <a href="http://tedgustaf.com/blog/2011/4/publishing-plain-html-pages-in-episerver/" target="_blank">http://tedgustaf.com/blog/2011/4/publishing-plain-html-pages-in-episerver/</a>).

The difference in cost and implementation effort can be substantial.

Why spend money and valuable time building reusable content elements for a one-off campaign if you don&#8217;t really need to?