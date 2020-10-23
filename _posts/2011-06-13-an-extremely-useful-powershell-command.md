---
title: 'An extremely useful Powershell command&#8230;..'
date: 2011-06-13T23:18:55+00:00
author: Mark Everard
layout: post
permalink: /2011/06/13/an-extremely-useful-powershell-command/
dsq_thread_id:
  - "1785457274"
categories:
  - Technology
---
By default Powershell exceptions and stderr outputs don&#8217;t give you much information beyond the exception name and the position in the script where the exception occurred. What you really want is a full stack trace.

You can get more information by running the following command; immediately after you&#8217;re troubled by a &#8216;troublesome&#8217; exception

> <pre>$error[0]|format-list force</pre>