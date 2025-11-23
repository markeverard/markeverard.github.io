---
id: 549
title: Serving videos to iOS devices from EPiServer VPP folders
date: 2011-07-05T10:00:39+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1073079962"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS]
---
We recently launched an EPiServer site that made moderate, but high-profile usage of video and was also designed to be iOS device friendly &#8211; meaning no Flash and Html5 used to display video. Late on in development / integration we came across an issue where the mp4 / m4v videos file that were being served from an EPiServer VPP folder did not play when viewed on an iOS device. A little investigation found that the video files would play correctly when served natively from a static / non-content managed location.

_(Note: If you&#8217;re serving a large amount of media content, then you&#8217;d be better off looking at a full media hosting solution rather than just serving from the EPiServer VPP)._

### Http-Headers

Obviously the files were identical, so the only difference was in how IIS was serving them. Below are the Http responses (captured using FireBug) for the identical .m4v video files, one served from a VPP, and one from a native path.

[<img class="aligncenter size-full wp-image-551" title="vppm4v" src="/assets/uploads/2011/06/vppm4v.gif" alt="" width="374" height="150" />](/assets/uploads/2011/06/vppm4v.gif)

[<img class="size-full wp-image-550 aligncenter" title="staticm4v" src="/assets/uploads/2011/06/staticm4v.gif" alt="" width="375" height="165" />](/assets/uploads/2011/06/staticm4v.gif)

The significant difference is the **Accept-Ranges : bytes** header. Cool &#8211; so we can just add in this header to our response and we&#8217;re sorted right? **WRONG I&#8217;m afraid!**

### HTTP/1.1 Accept-Ranges header field

The **Accept-Ranges** response-header field allows the server to indicate its acceptance of range requests for a resource. This means that the server is open to partial downloads of files, and that clients (web-browsers) can download a limited &#8216;chunk&#8217; of a file by requesting a specific byte-range, for-example bytes 5001-9999.

Going back to our original issue of why the files didn&#8217;t play on an iOS device, a quick look on the [Apple developer notes](http://developer.apple.com/library/safari/#documentation/AppleApplications/Reference/SafariWebContent/CreatingVideoforSafarioniPhone/CreatingVideoforSafarioniPhone.html#//apple_ref/doc/uid/TP40006514-SW6 "Configure your server for iOS media") (via [StackOverflow](http://stackoverflow.com/questions/3397241/does-iphone-ipad-safari-require-accept-ranges-header-for-video "Does iPhone/iPad Safari require 'Accept-Ranges' header for video?")) confirms that the web server needs to be configured to correctly handle byte-range requests. This explains why our file doesn&#8217;t work on an iOS device when served from an EPiServer VPP.

### EPiServer.Web.StaticFileHandler

Our site was running using IIS7.5 in integrated pipeline mode, meaning that pretty much the whole Request / Response pipeline is handled by ASP.NET modules. Looking in the EPiServer site web.config you can see that the location path elements for VPP paths (Global /PageFiles etc) contain the HttpHandlers that are responsible for serving VPP filess. This is a bespoke EPiServer file handler implementation (EPiServer.Web.StaticFileHandler), which unlike the default IIS7 StaticFileHandler does not support Range-Requests.

### The Solution

Now you didn&#8217;t think that I&#8217;d explain all of this, without heroically coming up with a solution did you? Well I am providing a solution, but its hardly heroic as most of the hard work was done many years ago by [Scott Mitchell](http://scottonwriting.net/ScottMitchell.asp "Scott Mitchell") &#8211; see [Range-Specific Requests in ASP.NET](http://dotnetslackers.com/articles/aspnet/Range-Specific-Requests-in-ASP-NET.aspx "Range Specific Requests in ASP.NET"). In his post Scott explains and provides an example of how to roll a HttpHandler with support for range-specific HTTP requests.

I&#8217;ve inherited from the RangeRequestHandlerBase class that Scott posted in the above article, and overidden a few methods adding in some EPiServer specifics, such as mapping the request path to the physical VPP file path and also using the EPiServer.Web.MimeMapping class to handle mapping from an extension to a mime type.

~~~csharp
public class RangeRequestFileHandler : RangeRequestHandlerBase
{
    ///
    /// Returns a FileInfo object from the mapped VirtualPathProviderFile
    ///
    /// <param name="context" />
    ///
    public override FileInfo GetRequestedFileInfo(HttpContext context)
    {
        UnifiedFile file = GetFileInfoFromVirtualPathProvider(context);

        if (file == null)
            return null;

        PreventRewriteOnOutgoingStream();
        return new FileInfo(file.LocalPath);
    }

    private static UnifiedFile GetFileInfoFromVirtualPathProvider(HttpContext context)
    {
        return GenericHostingEnvironment.VirtualPathProvider.GetFile(context.Request.FilePath) as UnifiedFile;
    }

    ///
    /// Returns the MIME type from the mapped VirtualPathProviderFile
    ///
    /// <param name="context" />
    ///
    public override string GetRequestedFileMimeType(HttpContext context)
    {
        UnifiedFile file = GetFileInfoFromVirtualPathProvider(context);
        return MimeMapping.GetMimeMapping(file.Name);
    }

    ///
    /// Prevents episerver rewrite on outgoing stream.
    ///
    private void PreventRewriteOnOutgoingStream()
    {
        if (UrlRewriteProvider.Module != null)
            UrlRewriteProvider.Module.FURLRewriteResponse = false;
    }
~~~

This handler can be used to serve files from your VPP folders by adding the following configuration (IIS7 only folks &#8211; which if you&#8217;re not using then you really should be pushing for an upgrade!). This handler listed below is set up only to serve files of type .m4v &#8211; and remember that it does not provide any of the native EPiServer functionality for setting the staticFile cache expiration time.

~~~xml
<location path="Global">
     <system.webServer>
         <handlers>
              <add name="webresources" path="WebResource.axd" verb="GET" type="System.Web.Handlers.AssemblyResourceLoader"/>
              <add name="videofiles" path="*.m4v" verb="*" type="FortuneCookie.RangeRequestFileHandler.RangeRequestFileHandler, FortuneCookie.RangeRequestFileHandler"/>
              <add name="wildcard" path="*" verb="*" type="EPiServer.Web.StaticFileHandler, EPiServer"/>
         </handlers>
      </system.webServer>
      <staticFile expirationTime="-1.0:0:0"/>
 </location>
 ~~~

The full source code can be downloaded from the Code section at EPiServer World and a [NuGet](http://nuget.episerver.com "EPiServer Nuget Feed") package is available that will auto-magically add in the code and configure your Global and PageFile VPP&#8217;s to serve .mp4, .m4v and .mov video files using the RangeRequestFileHandler.