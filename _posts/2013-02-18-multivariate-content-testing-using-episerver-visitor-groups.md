---
id: 882
title: Multivariate content testing using EPiServer Visitor Groups
date: 2013-02-18T10:00:25+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=882
permalink: /2013/02/18/multivariate-content-testing-using-episerver-visitor-groups/
dsq_thread_id:
  - "1089473788"
  - "1089473788"
categories:
  - Episerver
---
I&#8217;ve been working closely with our performance marketing experts at <a title="POSSIBLE is a digital agency delivering world-class creative, with a focus on measurable business results." href="http://www.possible.com" target="_blank">POSSIBLE</a> recently. One of the key questions is the decision about which is the right technology to use to perform multivariate content tests. There are two technical approaches:

  1. **Client side** &#8211; from free tools such as <a title="Content Experiments in Google Analytics" href="https://support.google.com/analytics/bin/answer.py?hl=en&answer=1745147&topic=1745207&rd=1" target="_blank">Google Content Experiments</a> to paid-for services such as <a title="Optimizely AB testing" href="https://www.optimizely.com/" target="_blank">Optimizely</a>. These work by injecting javascript into your pages which alter the content seen by a user. This impacts browser performance and the effective site speed as seen by the user. In the case of Google Content Experiments users are redirected to page variants defined by editors as urls within the tool, where as Optimizely and the more advanced tools manipulate the DOM post page-load to set up the specifics of your test. The paid-for services offer a lot more flexibility for editors to set up their own tests through online interfaces, however from a content management perspective this means that your testing content lives outside of your CMS system, and so becomes more difficult to manage, maintain and govern.
  2. **Server side** &#8211; you can of course push the logic for AB testing back to your server. There are a number of open source .NET AB testing packages available, such as <a title="FairlyCertain is an A/B Testing library" href="http://www.fairtutor.com/fairlycertain/" target="_blank">FairlyCertain</a> which allow you to set up AB tests relatively simply as a developer, but offer no control over each test to site editors. More mature CMS platforms also offer these features.

### Multivariate testing in EPiServer

For EPiServer 7 (<a title=" 2nd Preview of the New EPiServer 7 CMS Editorial Interface " href="http://world.episerver.com/Articles/Items/2nd-Preview-of-the-New-EPiServer-7-CMS-Editorial-Interface/" target="_blank">Sparrowhawk UI</a>) &#8211; Multi-variant testing is now built into the core product. This is a great improvement and hooks into the &#8216;Block&#8217;s feature to allow editors to define different variations of blocks and then test and measure their performance.

Previously EPiServer offered multivariate testing through its <a title="EPiServer Campaign Monitor and Optimization (CMO) makes your job easier by showing you how well your landing pages are performing and what you can do to improve your conversion rates." href="http://www.episerver.com/Products/EPiServer-CMO/" target="_blank">Campaign Monitor and Optimization product </a>which amongst other features allowed for basic A/B testing of content on a page by page basis only (i.e. you must test a whole page against a variant &#8211; these are actually set up as different versions of the same page in EPiServer).

### An A/B testing Visitor Group

One of the limitations of CMO (beyond the ability to only run tests against full pages) is that all of your visitors are subjected to your test. Often (and certainly on high-traffic sites), there is already a clear segmentation and knowledge of visitor behaviour and you may only want to test particular user segments. I think the new Multi-variant testing features in EPiServer 7 should also overcome this limitation (but I haven&#8217;t played around enough yet).

So I set myself a challenge to see whether you could use the existing Personalization framework in CMS6 R2 to allow AB tests to be set only for specific visitor segments.Â The answer is &#8211; _yes_ &#8211; _sort of_. Specifically I wanted to enable the following situation:

  * Display an A/B content test on a single page to only to a single EPiServer Visitor Group

The first piece of this was to <a title="Developing Custom Visitor Group Criteria" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-6-R2/Visitor-Groups-Creating-Custom-Criteria/" target="_blank">build a new Visitor Group criterion</a> which matched for an editor set percentage of the time. The idea being thatÂ should be used in conjunction with an existing Visitor Group.

<p style="text-align: center;">
  Â <a href="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3.png"><img class="alignnone size-full wp-image-932" alt="ab-testing-with-visitor-groups3" src="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3.png" width="711" height="152" srcset="https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3.png 711w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3-300x64.png 300w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3-580x124.png 580w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups3-320x68.png 320w" sizes="(max-width: 711px) 100vw, 711px" /></a>
</p>

So here &#8211; we are going to enable an AB content test only against users of the &#8216;Test Querystring&#8217; visitor group. I&#8217;ve used the &#8216;Visitor Group Membership&#8217; criteria to specify that this new group should be composed of the &#8216;Test Querystring&#8217; group and the new randomly matching A/B testing criteria.

The second piece was the content setup. This was slightly more complicated (though not from a coding perspective). Below you will see three bits of content &#8211; all personalized and linked together in a fallback group (all standard CMS6 R2 functionality).

  1. Test content B &#8211; personalized only to match when the random A/B testing criterion matches and the user is in the &#8216;Test Querystring&#8217; group
  2. Test content A &#8211; personalized only to match when the user is in the &#8216;Test Querystring&#8217; group
  3. Fallback content &#8211; content displayed to a user not in the &#8216;Test Querystring&#8217; group

The order the content is listed in is extremely important. The EPiServer logic to work out which content piece from the WYSIWYG editor is displayed works on a first match basis. So if you put your content in a different order (with content A above B) you would short-circuit your test.

<p style="text-align: center;">
  <a href="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2.png"><img class="alignnone size-full wp-image-922" alt="ab-testing-with-visitor-groups2" src="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2.png" width="645" height="610" srcset="https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2.png 645w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2-300x284.png 300w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2-580x549.png 580w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups2-320x303.png 320w" sizes="(max-width: 645px) 100vw, 645px" /></a>
</p>

#### Great &#8211; so we have a content test &#8211; how do we see the results?

Measuring any conversion should be achieved in the same way as CMO &#8211; by setting up a separate conversion page and measuring the number of hits on that page &#8211; which is outside of this scope. Limited I know ðŸ™‚

You canÂ use the Visitor Group statistics gadget &#8211; though all this will tell you is whether the test is set up correctly and whether the A/B testing criteria works!

<p style="text-align: center;">
  <a href="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups.png"><img class=" wp-image-912 aligncenter" alt="ab-testing-with-visitor-groups" src="http://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups.png" width="568" height="238" srcset="https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups.png 812w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups-300x126.png 300w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups-768x322.png 768w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups-720x301.png 720w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups-580x243.png 580w, https://www.markeverard.com/wp-content/uploads/2013/02/ab-testing-with-visitor-groups-320x134.png 320w" sizes="(max-width: 568px) 100vw, 568px" /></a>
</p>

The source code is available as part of the <a title="Criteria Pack for EPiServer CMS on CodePlex" href="http://criteriapack.codeplex.com/" target="_blank">Criteria Pack on Codeplex</a> and of course there is a Nuget package on <a title="EPiServer Nuget feed" href="http://nuget.episerver.com/" target="_blank">Nuget.episerver.com</a> &#8211; (as soon as its been approved!)