---
id: 1442
title: Serving EPiServer CMS 6 and Composer over SSL
date: 2013-03-25T14:45:57+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=1442
permalink: /2013/03/25/serving-episerver-cms-6-and-composer-over-ssl/
dsq_thread_id:
  - "1160904521"
  - "1160904521"
categories:
  - Episerver
---
It&#8217;s often a requirement for enterprise customers to maximise security on their public-facing websites and to request that access to the EPiServer interface is locked down.

With the standard EPiServer CMS there are <a title="Securing EPiServer Edit and Admin mode" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-60/Securing-Edit-and-Admin/" target="_blank">a number of ways to do this</a>, from removing the edit ui bindings from public facing web-servers to serving the edit mode over https. Forcing the edit ui over SSL largely comes down to just updating your EPiServer configuration (uiUrl and utilUrl) so that they explicitly state that the interfaces should be served over https. EPiServer then will not serve the interfaces over http, any http requests for them will result in a 404, so you can be sure that nobody can get access via an unsecured channel.

Things can get a lot more complicated with a load balanced physical server set-up / enterprise sites or with the addition of EPiServer Composer. In this post I&#8217;ll attempt to walk through a few common scenarios and how we&#8217;ve managed to overcome them.

### Composer over SSL

Lets talk through the following scenario. You have a single front end server, which has an SSL certificate installed directly onto it, and so it listens and responds to requests over port 80 and port 443. You want to force all editing and interaction with EPiServer interfaces to occur over a secure channel. Oh and your editors love creating flexible layouts so you also have Composer installed.

<p style="text-align: center;">
  <img class="size-full wp-image-1492 aligncenter" alt="EPiServerSSL-single" src="http://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-single.png" width="400" height="181" srcset="https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-single.png 400w, https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-single-300x136.png 300w, https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-single-320x145.png 320w" sizes="(max-width: 400px) 100vw, 400px" />
</p>

Composer adds complication due to the fact that it offers editors the ability to directly edit page content through the view url. For example &#8211; when using Composer edit on page mode &#8211; you&#8217;ll see a URL with the following format in your browser address bar.

http://www.mysite.com/?idkeep=True&DE\_VM=4&DE\_LNK=13\_3404&DE\_RND=1734871469&id=13_3404

So now we need to

  * Serve the ui / admin / util paths over SSL
  * Serve Composer on-page editing over SSL

In this case &#8211; if you set the uiUrl and utilUrl to force a secure channel in configuration, then all will work as expected when editors navigate around the site using the EPiServer context menus and navigation. However, although editors will be pushed to edit over SSL, Composer on-page edit will still be available over http &#8211; but only if they manually change the URL, which most sane people would regard as a &#8216;tiny&#8217; security hole. The suggestion here is to physically <a title="EPiServer Composer configuration" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-Composer/EPiServer-Composer-40---Configuration-Settings/" target="_blank">separate your view and edit mode functions</a> into individual installations.

### Load-Balanced Composer over SSL

Ok, so lets add another layer of complexity 🙂

We now have multiple front end web-servers, sat behind a load-balancer. The SSL certificate is installed on the load-balancer which listens on port 80 and port 443. The load-balancer performs the encryption / decryption and forwards requests on to the front-end webservers over port 80, but with some additional information added to the internal request to let the web-servers know that this was originally a secure request. You want to force all editing and interaction with EPiServer interfaces to occur over a secure channel and yes, we still have Composer.

In this scenario we can&#8217;t just update the EPiServer configuration, as the application never sees https requests. If you try this and set the uiUrl and utilUrl to https:, you&#8217;ll find that your edit and admin modes will return 404&#8217;s. This is because for every edit and admin mode request; EPiServer checks the request to make sure the full request matches the configuration setting. As the load-balancer has offloaded the SSL, the request as seen by the web-server is a standard http request meaning you&#8217;ll end up with a 404 as EPiServer can&#8217;t match the request to the configuration.

So out-of the box EPiServer as an application doesn&#8217;t handle this situation &#8211; but that doesn&#8217;t mean it can&#8217;t be achieved

### IISRewrite to the rescue

<a title="Url rewrite for IIS" href="http://www.iis.net/downloads/microsoft/url-rewrite" target="_blank">IISRewrite is an module</a> for IIS 7+ that allows server administrators the ability to add in powerful rewrite and redirect rules to your website. Think of it as the <a title="Apache Module mod_rewrite" href="http://httpd.apache.org/docs/current/mod/mod_rewrite.html" target="_blank">mod_rewrite</a> equivalent for the Windows stack. This allows you to intercept and change requests very high in the request pipeline, before your application sees them. You have access to the full request so you can write rules based on url, querystring or any http header. For this scenario we are going to use it to force requests onto https and http depending on what path is being requested.

<img class="size-full wp-image-1472 aligncenter" alt="EPiServerSSL-multiple" src="http://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-multiple.png" width="650" height="239" srcset="https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-multiple.png 650w, https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-multiple-300x110.png 300w, https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-multiple-580x213.png 580w, https://www.markeverard.com/wp-content/uploads/2013/03/EPiServerSSL-multiple-320x118.png 320w" sizes="(max-width: 650px) 100vw, 650px" /> 

EPiServer should be configured to accept requests to the ui and util over any communication channel, and then IIS Rewrite is used to ensure that requests are forced into the correct communication channel by returning a <a title="HTTP 303 - See Other" href="http://en.wikipedia.org/wiki/HTTP_303" target="_blank">http 303</a> response to the browser, which will then initiate a new request. This is a similar approach that <a title="Kjetil Simensen Blog" href="http://www.epinova.no/blog/Kjetil-Simensen/" target="_blank">Kjetil Simensen</a> took &#8211; with his <a title="Make sure your EPiServer pages are always on https" href="http://www.epinova.no/blog/Kjetil-Simensen/dates/2012/2/making-sure-your-pages-always-are-on-https/" target="_blank">Secure pages module</a> (available on <a title="Epinova.SecurePages" href="http://nuget.episerver.com/en/OtherPages/Package/?packageId=Epinova.SecurePages" target="_blank">EPiServer Nuget</a>) though he uses a HttpModule within the application rather than IIS. The advantage, in my mind to using IIS is that you can make this setup environment specific meaning your development and test environments do not have to use SSL and also this gives greater flexibility for dealing with different SSL offload rules.

We need to add the following rules to IIS Rewrite. This can be done through IIS itself &#8211; but all this does is update your application web.config, so you can actually include these rules manually within your source control. In this scenario we need to check to see for particular application paths whether the original request was secure or not and if it forms part of the EPiServer interface. The rules need to:

  * Force all requests for the UI to be served over SSL
  * Force all requests for Util to be server over SSL
  * Force all requests for Composer ui elements to be served over SSL
  * Force all view mode requests that contain the Composer querystring parameters (idkeep=true and DE_*) to be served over SSL
  * Optionally force all requests that don&#8217;t match the above to be served over http

Our load-balancer offloads all requests and adds an additional http header into the request (SSL=true), so the presence/absence of this header is used to work out whether to force a redirect.

The IIS rules we needed to set up Composer and CMS are listed below. <a title="URL Rewrite Module Configuration Reference " href="http://www.iis.net/learn/extensions/url-rewrite-module/url-rewrite-module-configuration-reference" target="_blank">More details on the Xml syntax for IIS Rewrite</a>.

<pre class="brush: xml; title: ; notranslate" title="">&lt;rewrite&gt;
      &lt;rules&gt;
        &lt;rule name="ComposerOnPage Redirect to HTTPS" patternSyntax="Wildcard" stopProcessing="true"&gt;
          &lt;match url="*" /&gt;
          &lt;conditions&gt;
                        &lt;add input="{HTTP_SSL}" pattern="true" negate="true" /&gt;
                        &lt;add input="{QUERY_STRING}" pattern="*DE_VM=*" /&gt;
                        &lt;add input="{QUERY_STRING}" pattern="*DE_LNK=*" /&gt;
                        &lt;add input="{QUERY_STRING}" pattern="*DE_RND=*" /&gt;
                        &lt;add input="{QUERY_STRING}" pattern="*idkeep=true*" /&gt;
          &lt;/conditions&gt;
          &lt;action type="Redirect" url="https://{HTTP_HOST}/{R:0}" redirectType="SeeOther" /&gt;
        &lt;/rule&gt;
        &lt;rule name="Util Redirect to HTTPS" patternSyntax="Wildcard" stopProcessing="true"&gt;
          &lt;match url="util/*" /&gt;
          &lt;conditions&gt;
                        &lt;add input="{HTTP_SSL}" pattern="true" negate="true" /&gt;
          &lt;/conditions&gt;
          &lt;action type="Redirect" url="https://{HTTP_HOST}/{R:0}" redirectType="SeeOther" /&gt;
        &lt;/rule&gt;
        &lt;rule name="Ui Redirect to HTTPS" patternSyntax="Wildcard" stopProcessing="true"&gt;
          &lt;match url="ui*" /&gt;
          &lt;conditions&gt;
                        &lt;add input="{HTTP_SSL}" pattern="true" negate="true" /&gt;
          &lt;/conditions&gt;
          &lt;action type="Redirect" url="https://{HTTP_HOST}/{R:0}" redirectType="SeeOther" /&gt;
        &lt;/rule&gt;
        &lt;rule name="Composer Redirect to HTTPS" patternSyntax="Wildcard" stopProcessing="true"&gt;
          &lt;match url="Dropit/Plugin/Extension/UI/*" /&gt;
          &lt;conditions&gt;
                        &lt;add input="{HTTP_SSL}" pattern="true" negate="true" /&gt;
          &lt;/conditions&gt;
          &lt;action type="Redirect" url="https://{HTTP_HOST}/{R:0}" redirectType="SeeOther" /&gt;
        &lt;/rule&gt;
      &lt;/rules&gt;
    &lt;/rewrite&gt;
</pre>

### Beware of mixed mode security

When setting up these rules be aware of <a title="EPiServer security" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-60/Protecting-Your-Site-From-Session-Hijacking/" target="_blank">mixed mode</a> security. Browsers will block any request over an insecure channel when the initial response is secure. This can often be seen through the EPiServer interface as blank tabs / screens. For example, without the Composer On page rule listed above, the Composer tab in edit mode would request the page over http: would would be blocked by the browser. You should take care to include additional rules for any other plugins you may have in your solution &#8211; otherwise you may just see a blank screen due to your browser blocking the request. <a title="Fiddler - web debugging proxy" href="http://www.fiddler2.com/fiddler2/" target="_blank">Fiddler</a> / <a title="Firebug - web development plugin for Firefox" href="http://getfirebug.com/" target="_blank">Firebug</a> are good ways of exposing this

### Summary

In conclusion, I&#8217;ve walked through a more complicated SSL set up for EPiServer Composer and CMS 6 which used IIS Rewrite to force all interactions with EPiServer CMS6 and Composer to be served over a secure channel.