---
title: Helping EPiServer editors Find unused content
date: 2015-03-16T10:00:28+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "3598119848"
categories:
  - Episerver
tags: [Episerver-CMS, Find, Search]
---
It might sound obvious, but content management is about more than just content creation and editing. It is also knowing when your content has reached the end of its useful lifespan. It means knowing when it&#8217;s time to &#8216;Move to Trash&#8217;, and when it&#8217;s time to &#8216;Move to Trash&#8217; permanently!

I&#8217;ve seen as many content management systems ultimately fail due to poor on-going content control by editorial teams as I have through poor initial implementations by developers. There are many reasons that your content can grow out of control. When it does, your CMS becomes nothing more than a publishing platform and a quite inefficient one.

> _&#8220;I&#8217;m sure I&#8217;ve written an item like that before&#8230;&#8221;_  
> _&#8220;Yeah, it&#8217;s in here somewhere, I just can&#8217;t find it&#8230;&#8221;_  
> _&#8220;I don&#8217;t think that content is used but I&#8217;m not sure, so I didn&#8217;t archive or delete it, just in case&#8230;&#8221;_

Tidying up after yourself whatever your discpline, is just good practise. Whether its content, a codebase or your email inbox. Being scared to delete is a sign that you aren&#8217;t in control.

One of the editors I work with on an EPiServer site wasn&#8217;t scared to delete content, but he was overawed by the effort needed to discover unneeded content.

This got me thinking. There must be a better and easier way than trawling through a hierarchical tree&#8230;

## Content reporting using a slice

![Unused content](/assets/uploads/2015/03/usused-content-slice1.png)

EPiServer recently reintroduced <a title="Re-introducing PowerSlice for EPiServer CMS" href="http://world.episerver.com/articles/items/re-introducing-powerslice-for-episerver-cms/" target="_blank">PowerSlice</a> as a &#8216;supported&#8217; add-on. PowerSlice uses <a title="EPiServer Find. Advanced Search Made Easy" href="http://find.episerver.com/" target="_blank">EPiServer Find</a> to give editors a view of content which goes beyond the traditional hierarchical structure. It allows developers to very easily &#8216;slice up&#8217; content in any number of ways.

Whilst thinking about the best way to implement a feature for editors to report on unused content, it dawned on me that creating a slice for &#8216;unused content&#8217; would be incredibly easy

* PowerSlice already has a well-thought out <a title="PowerSlice demo video on YouTube" href="https://www.youtube.com/watch?v=vaGZGpQB394" target="_blank">user interface</a>
* PowerSlice allows editors to very quickly jump to managing content using the standard EPiServer interface / good for if you want to find and then delete content

I&#8217;ve put together two slices, to help editors find unreferenced blocks and media, using standard methods from the EPiServer <a title="EPiServer IContentRepository SDK" href="http://world.episerver.com/documentation/Class-library/?documentId=cms/7/7df6647c-0343-7aee-b8a4-36ee761ae397" target="_blank">IContentRepository</a> interface.

### Unused Blocks Slice
View the code on <a href="https://gist.github.com/markeverard/84754f0c64e18d6bd238">Gist</a>.

### Unused Media Slice
View the code on <a href="https://gist.github.com/markeverard/7605620426bbb9599a73">Gist</a>.

These slices and PowerSlice were such a good solution that I&#8217;d argue that PowerSlice should be bundled out of the box with a Find install. It could perhaps form the basis of a new and more flexible content reporting function? Reporting is something that is in need of a little love in EPiServer 7/8.

Thoughts?