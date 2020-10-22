---
id: 5231
title: Validating image dimensions in EPiServer 7.5 CMS
date: 2017-07-11T16:47:43+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/3242-revision-v1/
permalink: /2017/07/11/3242-revision-v1/
---
<a title="EPiServer 7.5 CMS â€“ Modern, intuitive, and powerful!" href="http://world.episerver.com/Articles/Items/EPiServer-75-CMS--starting-to-show-its-full-potential/" target="_blank">EPiServer 7.5</a> delivers a completely new digital media management system offering a host of editor enhancements, including integration into the new editing interface, multiple file drag and drop file upload, inline media preview, and drag and drop placement of media into ContentAreas and the Rich-Text-Editor.

From a developer perspective, much has changed too. <a title="Working with Media programmatically" href="http://world.episerver.com/Blogs/Johan-Bjornfot/Dates1/2013/12/Working-with-Media-programmatically/" target="_blank">Media files are not stored through a Virtual Path Provider but as a Blob</a> (Binary Large OBject). This allows media assets to be stored and used in true elastic cloud scenarios, though the default blob provider still saves media files to disk (just as the old VPP system did).

Also new, is that media assets are now treated as first class content items. That is; they inherit from the IContent interface and have code definable properties allowing better definition of media meta data. This is now defined in a strongly typed manner just as you do for other IContent items such as pages, blocks and catalogue nodes and items in Commerce.

### Image size validation

A media item has a one-to-many mapping to a file extension, so EPiServer will only associate one IContent class with a particular file extension or collections of extensions. A basic content handling of media data is included in the Alloy Templates demo site, and includes three new IContent classes, inheriting from the core VideoData, ImageData and MediaData objects.

This one-to-many mapping means you have to take a slightly different approach to content modelling media items than you do for page or block types. For example, you may want to have banner images that must have a particular size, or a photo image that requires additional meta data than a standard image. For now you can&#8217;t do this. All files of a particular extension have to contain the same metadata.

This means that you cannot validate images for size when you upload them (as you&#8217;d restrict every single image with that extension to be that size). Although this feels restricting, it is perhaps the correct decision as media size only matters when you display your content (and although it hasn&#8217;t has much love recently, EPiServer does offer an <a title="Edit Images Directly From the EPiServer UI " href="http://world.episerver.com/Articles/Items/Edit-Images-Directly-From-the-EPiServer-UI/" target="_blank">online image editor</a>). It is only when you use your image within a particular presentation layer (page /block) that you really need to constrain it to a definite size.

### Validating an image used in context

You can apply validation to any property used within a page or block type via a <a title="Using validation attributes in EPiServer 7" href="http://www.david-tec.com/2012/06/EPiServer-7-Preview---Using-validation-attributes/" target="_blank">validation attribute</a>. Media assets are stored as content references in a page or block; with a specific UIHint attribute to allow the editing system to know that you require an IContent inheriting from ImageData.

I&#8217;ve put togther a validation attribute that you can add to a ContentReference property which will validate the size of any ImageData content referenced in that property.

<pre class="brush: csharp; title: ; notranslate" title="">[UIHint(UIHint.Image)]
   [Display(GroupName = SystemTabNames.Content,Order = 3)]
     [RequiredImageSize(Width = 300, Height = 100)]
     public virtual ContentReference Image { get; set; }

</pre>

You can specify a height and or a width in pixels. Currently you can only apply this to ContentReference properties but I can see it would also be useful to apply it to ContentAreas &#8211; I&#8217;ve just not got round to that yet ðŸ™‚

[<img class="aligncenter size-full wp-image-3362" alt="imagevalidation" src="http://www.markeverard.com/wp-content/uploads/2014/01/imagevalidation.png" width="500" height="168" srcset="https://www.markeverard.com/wp-content/uploads/2014/01/imagevalidation.png 500w, https://www.markeverard.com/wp-content/uploads/2014/01/imagevalidation-300x101.png 300w, https://www.markeverard.com/wp-content/uploads/2014/01/imagevalidation-320x108.png 320w" sizes="(max-width: 500px) 100vw, 500px" />](http://www.markeverard.com/wp-content/uploads/2014/01/imagevalidation.png)

The source code is available atÂ <a title="Chief2moro ImageDataExtensions on GitHub" href="https://github.com/markeverard/Chief2moro.ImageDataExtensions" target="_blank">https://github.com/markeverard/Chief2moro.ImageDataExtensions</a>

A package containing this validation attribute, along with some other image related goodies that I&#8217;ll blog about soon, is available in the EPiServer Nuget Feed &#8211;Â [http://nuget.episerver.com/](http://nuget.episerver.com/en/?search=possible&sort=MostDownloads&page=1&pageSize=10)Â &#8211; search for CHIEF2MORO.ImageDataExtensions