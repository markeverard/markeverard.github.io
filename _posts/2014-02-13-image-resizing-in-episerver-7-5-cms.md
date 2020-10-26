---
title: Image resizing in EPiServer 7.5 CMS
date: 2014-02-13T10:00:34+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2014/02/13/image-resizing-in-episerver-7-5-cms/
dsq_thread_id:
  - "2258467161"
categories:
  - Episerver
---
Dynamic image scaling is an often sought after feature for interface designers and content editors. Whilst not natively supporting this feature; EPiServer 7.5 CMS contains a neat hidden feature (Thanks <a title="Allan Thraen (@athraen) on Twitter" href="https://twitter.com/athraen" target="_blank">@athraen</a> for the heads up) that allows a limited but useful ability to automatically scale your images.

True dynamic scaling, and by that I mean that your http request for an image also contains your requested height, width and cropping options, is a complex operation to perform successfully, just check out the interesting blog posts at <a title="ImageResizer.net" href="http://imageresizing.net/" target="_blank">http://imageresizing.net/</a>. Note there is a community integration of <a title="Get ImageResizer to play along with EPiServer 7.5" href="http://world.episerver.com/Blogs/Andre-Hedberg/Dates/2013/12/Get-ImageResizer-to-play-along-with-EPiServer-75/" target="_blank">ImageResizer.Net with EPiServer 7.5</a>.

The new media editing interface allows in-line preview of uploaded images via a scaled thumbnail representation of the image. These are created automatically by EPiServer for any ImageData content types that are created.Â This is achieved via a <a title="Image descriptor attribute" href="http://world.episerver.com/Documentation/Items/Developers-Guide/EPiServer-CMS/75/Content/Assets-and-media/Media-types-and-templates/#ImageDescriptor" target="_blank">ImageDescriptor attribute</a> which can be placed on any &#8216;routed&#8217; blob property (i.e those on ImageData content types).

![Thumbnails in the media edit interface](/assets/uploads/2014/02/media-thumbnails.png) 

The **ImageDescriptor** is pretty cool, but not so helpful when you start working with real images with differing aspect ratios.

A simple use case could be that for every uploaded image you want to create a version that has a maximum width of 300 px, which could be used in a site sidebar. The ImageDescriptor as-is forces you to define a height and width at compile time meaning that all uploaded images will be resized to the same width and height specified in the attribute. Any additional height or width will be filled with white-space.

The <a title="Chief2moro.ImageDataExtensions" href="http://nuget.episerver.com/en/OtherPages/Package/?packageId=Chief2moro.ImageDataExtensions" target="_blank">CHIEF2MORO.ImageDataExtensions</a> nuget package I <a title="Validating image dimensions in EPiServer 7.5 CMS" href="http://www.markeverard.com/2014/02/03/validating-image-dimensions-in-episerver-7-5-cms/" target="_blank">blogged</a> about previously also contains a couple of additional attributes to help overcome this limitation. Extend your ImageData content object (ImageFile in Alloy) with a few Blob properties for the different sizes and mark up with the attributes. The <a title="Blob routing in EPiServer 7.5 CMS" href="http://world.episerver.com/Documentation/Items/Developers-Guide/EPiServer-CMS/75/Routing/BLOB-routing/" target="_blank">blob routing</a> feature in EPiServer means that these scaled images are available publically (<imagename>.jpg/image250 and <imagename>.jpg/half). Behind the scenes both of these descriptors use the underlying ThumbnailManager class contained in the EPiServer API.

~~~csharp
   [ScaffoldColumn(false)]
   [ImageWidthDescriptor(Width = 250)]
   public virtual Blob image250 { get; set; }

   [ScaffoldColumn(false)]
   [ImageScaleDescriptor(Percent = 50)]
   public virtual Blob half { get; set; }
~~~

The **ImageWidthDescriptor** only requires you to specify the scaled width and will calculate the height at content publish time from the originally uploaded image dimensions to ensure that the aspect ratio is maintained (so you get no padding / whitespace)

The **ImageScaleDescriptor** allows you to specify a scale and will calculate the desired height and width from the original image.