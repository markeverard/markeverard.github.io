---
id: 6014
title: Successful integrations depend on good information transfer
date: 2018-03-13T08:56:48+00:00
author: Mark Everard
layout: post
permalink: /2018/03/13/successful-integrations-depend-on-good-information-transfer/
dsq_thread_id:
  - "6586021529"
categories:
  - Opinion
  - Technology

---
System integration is often the most challenging aspect of any technical build, both from a development and cost perspective. Joining two systems together requires a solid interface that facilitates good information transfer, not just between your information systems but also between your technical teams.

## A SOLID INTERFACE FOR DEVELOPMENT
Getting the right information to support accurate and timely development against an API can be difficult. I&#8217;ve listed information that I think is important below. You don&#8217;t necessarily need them all, but they will make development progress less troublesome.

### Functional review
An important point but one that can often get overlooked before starting, especially in a creative environment. Are the API&#8217;s you are consuming fit for the purpose you are intending?

* **Can they support the functionality you are trying to offer?** You&#8217;d be surprised at the number of times I&#8217;ve seen application wire-frames signed off that rely on non-existent API functionality
* **Is the API already in production or is it still in development?** Working against a moving target could add effort unless a full specification is agreed / known upfront.
* **Does the API offer performance and an SLA that is appropriate for your usage?** There&#8217;s no point building a super fast application that is dependent on a slow and fragile API. You are only as strong as your weakest link.

### Documentation
Is the API &#8216;well-documented&#8217;, either online or in a document? Is it recent or is the information out-of-date?Â Well-documented is subjective but, in my view: in this context it meansÂ at least the following:

* **Protocol / Language** &#8211; what is the protocol you should use to communicate with the API (HTTP etc) and what is the data format / language you should use to communicate ( SOAP, REST etc)?
* **Endpoints** &#8211; where does the API live? Are there separate test and live systems? Are the use cases for each system explained? Are the data control policies for each environment explained &#8211; this is very important for API&#8217;s into communication systems. You don&#8217;t want your test system contacting real users. Make sure you know which system should perform the control.
* **Authentication** &#8211; is the API protected? What do you need to do to be able to authenticate with the API (Basic Auth, OAuth etc)
* **Methods** &#8211; is each method in the API explained, including any request parameters and expected responses? Are the expectations on data input (validations) described?
* **Error messages** &#8211; does the documentation explain how the API will notify and provide details of any errors, be that in validation of input or internal more fatal errors?
* **Examples** &#8211; are real code examples available or better yet is there an interactive sandbox, where you can try out example requests and receive mocked responses?

### Point of contact
Is there a point of contact for the API? Do you know where or who to turn to if you need any support whilst developing against or operating against an API? For mature platforms this could be a online documentation and a live &#8216;status&#8217; page. For line of business API&#8217;s this might be &#8216;the single tech guy in IT&#8217;. Either way you&#8217;ll need to make sure you know how to get help when you need it.

### Almost standard
A lot of the above is now commonplace (thankfully), driven particularly by the standards set by the larger social platforms and the increasing usage of API management platforms (such as [Jitterbit](https://www.jitterbit.com/)) in the enterprise.

I&#8217;m sure most developers would agree that developing against an API is often the most interesting type of technical work and developing against a &#8216;well-documented API&#8217; is also some of the most enjoyable.