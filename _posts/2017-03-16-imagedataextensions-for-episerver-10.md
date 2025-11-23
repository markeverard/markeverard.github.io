---
id: 4671
title: ImageDataExtensions for Episerver 10
date: 2017-03-16T15:11:58+00:00
author: Mark Everard
layout: post
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Open-source]
---
Thanks to [Luc Gosso](http://devblog.gosso.se/about-luc-gosso/) embracing the true meaning of open source software and &#8216;community&#8217; (you give back at least as much as you take, in case you&#8217;re wondering) there is now a version of my [ImageDataExtensions](https://github.com/markeverard/Chief2moro.ImageDataExtensions) package compatible with Episerver CMS 10.

### Why the long wait?

<img class="alignright wp-image-4591 size-medium" src="/assets/uploads/2017/03/IMG_8196-225x300.jpg" alt="She's already schooling me at JavaScript...." width="225" height="300"  /> 

Ignoring another [small package](http://www.markeverard.com/wp-content/uploads/2017/03/IMG_8196.jpg) that has entirely changed how I spend my spare time, the reason is that the [ImageDataExtensions](https://github.com/markeverard/Chief2moro.ImageDataExtensions) package relies on a unsupported Episerver API to deliver its core resizing functionality, and I wasn&#8217;t sure what approach to take.

As part of Episerver&#8217;s quality approach they have been reducing their [surface API](http://world.episerver.com/blogs/Henrik-Nystrom/Dates/2016/10/introducing-changes-to-reduce-our-public-api/), that is the number of methods that are available to developers to integrate and interact with the platform. This makes entire sense as they can focus their efforts on in-depth testing and validation of key features without worrying about methods that us eager developers have reflected out and used for more than their original intention.

The [ThumbnailManager](http://world.episerver.com/documentation/Class-library/?documentId=cms/9/8FAD1CB) class that provides the resizing capability has been marked as an internal API (and moved to the Episerver.Internal namespace). Although still available to develop against, Episerver provide no promises around its operation or signature , i.e a. change to this method /API wouldn&#8217;t be considered a breaking change to the platform (though it would be for this package).

The point to remember is &#8220;**if you are using� this package / or you intend to, please be aware that this means your functionality could break with any Episerver release and make sure you are comfortable with how you will deal with that&#8221;**.

## What next?

[Luc&#8217;s](http://devblog.gosso.se/) contribution got me thinking about how best to provide a way out for those of you that want to safely use and rely on this functionality.

I&#8217;ve now abstracted the resizing functionality into its own [interface](https://github.com/markeverard/Chief2moro.ImageDataExtensions/blob/master/src/Chief2moro.ImageDataExtensions/IResizeBlobProcessor.cs) and made it pluggable through the [IOC container](https://github.com/markeverard/Chief2moro.ImageDataExtensions/blob/master/src/Chief2moro.ImageDataExtensions/ImageDataExtensionsConfigurationModule.cs), enabling you to provide your own resizing implementation (thus no longer depending on the Episerver Internal ThumbnailManager class)

I&#8217;ve also created a [package](http://nuget.episerver.com/en/OtherPages/Package/?packageId=Chief2moro.ImageDataExtensions.ImageResizer) that uses everyone&#8217;s favourite Asp.net Image resizing module ([ImageResizer](https://imageresizing.net/)). There is already an [Episerver integration](https://nuget.episerver.com/en/OtherPages/Package/?packageId=ImageResizer.Plugins.EPiServerBlobReader) allowing true dynamic image resizing of any image. The new add-on delegates the image resizing on upload to ImageResizer rather than the ThumbnailManager.

I&#8217;ve dropped this into another package [Chief2moro.ImageDataExtensions.ImageResizer](http://nuget.episerver.com/en/OtherPages/Package/?packageId=Chief2moro.ImageDataExtensions.ImageResizer) in case you&#8217;re interested.

_**Is now the right time to say that I think Image scaling and resizing should absolutely be part of the core platform. I rarely come across a project that doesn&#8217;t have that need 😉**_