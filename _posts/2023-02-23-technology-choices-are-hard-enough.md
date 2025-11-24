---
title: Technology Choices are Hard Enough. Making sense of Composable & Headless Hype
date: 2023-02-23T14:30:26+00:00
author: Mark Everard
layout: post
img: 2023-02-23-headless.jpeg
description: Decision making in the world of technology rarely seems clear-cut or straightforward. Choosing the best technology is often fraught. Here's my guide to help you navigate composable and the headless hype.
categories:
  - Opinion
tags: [CMS, Opinion, Architecture, Martech]

---
Decision making in the world of technology rarely seems clear-cut or straightforward. Choosing the best technology is often fraught, leaving a genuine fear of taking a misstep. Such missteps can be expensive; stretching project budgets or leaving white elephants that hamstring departments or organisations with unnecessary complexity and overhead.

I’ve worked in the area of digital marketing and content management systems (CMS) for the best part of 15 years. Nearly every business uses a content management system for their website, and nowadays CMSs form just one part of the modern marketers tech-tool-belt, often being combined with commerce, data and communication products to make up a broad range of technology termed a digital experience platform (DXP).

Content Management is a mature technology market. Choosing the right CMS and differentiating to work out best fit is complex. Adding to this complexity currently is the hype behind a new category of CMS, and a more granular architectural approach to deliver a DXP. 

You may recognise the following buzzwords, **Headless**, **Composable** but do you genuinely understand what they mean? What problems they solve and most importantly when you need them?

This confusion is fuelled, whether deliberately or not by technology vendors heavily marketing using technical buzzwords along with technology strategists and architects jumping on the hype bandwagon without fair consideration. A magpie approach to technology selection. ‘Oooh look it’s shiny’. Be careful of that…

That’s not to say the hype is without merit but hype alone is exhausting. 

I wrote this article as an attempt to inform anyone involved in a CMS / DXP selection. To help them navigate the headless, composable and technical terminology and to best understand:

> - What is a headless content management system?
> - When might you need one?
> - What is a composable architecture?
> - Why might you need one?
> - Common pitfalls and arriving at the best decision.

I hope it brings clarity.

### Glossary
No, this isn’t the end of the article. I’m deliberately started with a glossary to align on terms. A lot of discourse around the relative merits of headless systems and composable architectures is muddied by mixing or misuse of terms.

#### Headless
A type of content management system architecture, where your backend admin system for managing content is isolated from the system that displays content, which is termed the head. The CMS is thus ‘headless’. 

#### Decoupled
A term to describe two systems being wholly isolated from each other. Operation of each system is independent but they can ‘talk’ to one another. 

#### Composable
A composable architecture describes an architecture which delivers an array of complimentary functionality via one or more decoupled systems which often communicate via API.

#### Front-end
A system or part of a system that contains an interface for a user. In a CMS context, this is the website or mobile app that your customer’s use to view your marketing communications (aka the head). 

#### Channel
A marketing channel describes the medium used to communicate about your organisation, product or services. For digital channels, these are often specific to the type of communication technology used – e.g. web, email, SMS, instant message.

#### Hybrid
A combined content management system where the system can be operated as a true headless CMS and as a coupled CMS with responsibility for templating and content presentation.

#### API
In a headless system, the backend headless CMS needs a method to deliver content to be used by the head / front end. Primarily in modern headless systems this method is via set of application programming interfaces. (APIs). These API’s form part of the headless backend and allow a head ‘front-end’ to query and retrieve content from the backend to display. Primarily these are using GraphQL which is a structured language for performing language lookups.

#### SaaS
Software as a service. Refers to a cloud hosting model where the vendor leases both the software and hardware to you. You are limited in how you can customise the software.

#### PaaS
Platform as a service. Refers to a cloud hosting model where the vendor leases the hardware and licenses the software to you. You can customise the software.

#### Monolith
A single technology system delivering a large amount of functionality. The origin of the term is geographical, describing a single massive stone or rock. You may remember them from “2001 – A Space Odyssey” where they are machines in black cuboids built by highly intelligent extra-terrestrials.

Now we’ve agreed on terms, we can combine them into the following statements. 

> - A headless CMS is suited to managing channel agnostic content.
> - A headless system is ‘composable’ and may form part of a ’composed’ architecture
> - A headless CMS has an API and will require one or more front-end ‘heads’ to use your content. 
> - Relative merits of headless systems
> - Decoupled CMS architectures like ‘headless’ systems are not new. The concept of a separation between content administration and presentation systems has been around since the very early CMS architectures.

A decoupled front end system is advantageous for reasons relating to its simplicity. 

- **Performance** – the decoupled front end often only displays content and can range from a static website to a client-side JavaScript app, both offering best available in-browser performance 
- **Security** – as the front end is simplified, there is less functionality that could be compromised. The attack vector is much reduced. A major benefit

Headless CMS’s are the latest cycle of decoupled CMS’s with one major difference. 

Headless systems only ever deal with raw content in the form of data. Previous decoupled systems perform some form of display logic before delivering templated content to the front-end. An example is illustrative. When content is changed in a decoupled CMS, the CMS creates html files by combining raw content with html templates, before transferring the resulting files to a server to act as a static website. If the content is changed further, the process is repeated. 

A headless system delivers only raw data via API, leaving any templating or display logic to be performed within each front-end head. 

This decoupling aids implementation. Development teams working on the experience layer (website or storefront) do not need to be CMS or Commerce system experts. They can use their standard tooling against the technology agnostics API’s with no requirement to become specialized on the specific platform. 

Working only with data, Headless systems are truly channel agnostic. They deliver just the raw content ingredients, to be combined into different recipes by each front-end head.

> If your organisation is trying to efficiently create and manage content to be used and reused across multiple channels, then a headless CMS is a sound choice.

Content reuse is a noble aspiration but is difficult to achieve, particularly at scale across large teams. Marketing content often has a short-half life, and very often teams only like to create content when they know where it’s going to be used. That way it can be optimised for display. This common line of thinking and behaviour can prevent you achieving an effective omni-channel content strategy, as there needs to be a mindset shift in how content is conceived and first created, to overcome those set behaviours. New technology alone won’t help you change course here. Your content operations should be adjusted simultaneously. A more significant business change. 

## Navigating the headless hype
The truth for most organisations is that their website is still their most important communications channel, meaning there is sound justification for content to be created specific for use on the web channel first.

In this case, you should consider whether a headless system is indeed best fit. Is your aspiration for multi-channel content achievable, and considering the importance of the web channel, are you willing to give up the advantages a web-first CMS can offer?

What are these advantages? It largely comes down to the editorial experience. A web-first CMS often provides visual WYSIWYG content editing, allowing editors to view and understand context, as the content is created and managed. This is a feature that most web editorial teams are used to and find valuable. Who wouldn’t want to see what the oil painting looks like above your fireplace before purchasing from the gallery?

Leading headless vendors and ecosystem partners such as Shopstory have recognised the importance of the web and are developing or have delivered page builder and WYSIWYG editing functionality for headless systems. This is a compromise position for a dogmatic headless CMS platform, balancing the importance of the editor experience and the dominance of the web channel, alongside the aspiration for channel agnostic content reuse. 

This is the [‘race to the middle’](https://www.youtube.com/watch?v=8AtfypIddcw), a term coined by CMS expert [Deane Barker](https://www.linkedin.com/in/deane) to describe how both headless and web-first CMS platforms are moving towards the same target of hybrid CMS functionality. 

Is there any significant difference between a native headless CMS offering web WYSIWYG and a web-centric CMS offering a headless content API? 

There is certainly no longer any fundamental philosophical difference and so you should base your CMS decisions on the specifics of how your detailed functional content management and delivery needs are met, rather than the CMS market hype. 

**Remember the following:** 

> - Adjusting to a omni-channel content strategy is not purely a technology concern
> - Leading CMS’ now offer similar hybrid capability, regardless of their original use case.

## Composed thoughts around composable architectures 
Along with the hype around headless systems, there has been much buzz about composable architectures. This is a re-framing and logical application of the following well-known architectural principles.

> - Simplicity
> - Interoperability

Modern digital experience platforms are much more than just than just a website. Even the more simple DXP architectures bring together capability from many systems. 

The below is an illustration of a representative technical architecture for an ecommerce site for a large size organisation. It contains 20 different applications and services. 

![An example DXP ecommerce technical architecture](/assets/uploads/2023/02/1677150984596.png)
An example DXP ecommerce technical architecture

As the cloud has become prevalent, these example applications can be hosted as SaaS or PaaS solutions. Organisations no longer need to own the infrastructure to operate them, which in turn means they are more free to switch out or add new systems when required.  

Prior to the cloud, this flexibility was more difficult. Additionally getting new systems to work together in unison took significant and custom integration efforts. 

On an annual cycle CMS / DXP platform vendors would add to their feature sets with system updates, often expensive to install and based on what the vendor felt important for the overall market rather than what was relevant for you. The result. Inflated platforms that required inflated budgets to maintain and operate on your infrastructure.

Market forces dictate that cloud platform vendors will still enhance their offerings, the difference being that technical lock-in is much reduced and you don’t host them. Owning shelfware – software that is purchased but never used is more palatable if you don’t pay for it. There is of course still commercial and contractual lock-in to navigate. 

Modular systems provide the simplest path to the future growth of a combined platform such as a DXP. Modules or new cloud services can be bolted together to increase the overall platform capability with little impact on existing modules. This flexibility is particularly suited to digital and web experiences where organisations are expected to adapt quickly to meet customer expectation and respond to competition.

**Composable architectures offer:**

> - Flexibility to add further capability to your platform without starting over
> - Agility to enable you to swap out a system without extensive rework
> - The ability to scale individual components to meet your demand

## Navigating the hype
Given the underlying principals of flexibility and fluidity that composable architectures are derived from, then you’d be hard pushed to find any expert who would suggest they aren’t a sound approach. The DXP industry will continue to pivot in this direction. 

The pertinent question isn’t ‘Do you need a composable architecture?’ It is ‘How do you plan to get there’? This isn’t always obvious. It is a factor of your previous investment, your ambition for growth and your stack’s current performance.

## Not all monoliths need to be replaced

Repeat after me. _Monoliths bad. Composable good. Monoliths bad. Composable good._ This is the mantra the technology community have been chanting for a few years and is now often repeated as fact. Two things bother me about this.

> - How large is a solution before it becomes a monolith?
> - When does a monolith become a problem?

The answer to both of the above questions are **‘It depends’**, meaning the answer to ‘Do I need to replace my monolith?’ is also, **‘It depends’**. 

The truth is you’ll know if your system is _problematic_. You’ll feel it in the following ways.

> - Increased number of incidents and problems
> - Change becomes harder
> - There is a fear of shipping

The above won’t occur suddenly, but instead the impacts will accumulate over a longer period. You’ll encounter system stability issues that become more frequent or severe. You’ll see the level of effort required to develop, test and deploy changes increase as your development team best deal with overall system complexity. This will manifest as the dreaded project overrun or delay.

Altogether, these slow your ability to iterate quickly and ship those key changes or updates that you know are needed to hit targets. A definite problem, and so a time to reassess. 

That’s not to say you must re-architect given the above. Instead, your current circumstances should be factored into a case for change. What is the estimated improvement, reduction in maintenance, reduction in effort spent against incident response, compared to the cost of re-architecting? Is there an evolutionary path to change? Often organisations settle comfortably within this context due to budgetary constraints or priority. Weigh up the pros and cons then decide on the best route forward.

## Making the right call
New technology is often mistaken for a panacea, offering a better world where all of the current problems with your existing technology stack are gone in an instant. I hate to break it to you, but this is rarely true. Sorry.

It’s important to remember that technical architectures and architectural patterns are a response to specific problems. For composable, that is to enable greater flexibility for future change and growth. It comes with a cost. It always does. That being, the modular nature of composable architectures increases management overhead.

> - You have multiple vendors. This means more supplier relationships, more contracts and renewals and potentially higher cost
> - You have multiple support channels. During a critical incident who do you contact or who can help you identify who to contact? Who is responsible for issues that occur between systems?  
> - You have more integrations – meaning more potential points of failure and get areas (see above) 
> - Your business teams will be working across multiple interfaces increasing their training and enablement need and affecting productivity

Not insurmountable, but think carefully on the benefit for what you’ll get in return.

## The ‘Headless’ smile
We’re beginning to see the first early adopters of headless and composable stacks come up for air, and it’s a familiar tale. Success stories yes, but also stories of those who jumped too early, or pushed too far without sound reason and have come unstuck.

![The headless smile. A model of where composable and headless solutions have gained traction in organisations of different size.](/assets/uploads/2023/02/1677151892510.png)
I’ve termed this infographic the ‘headless’ smile and it will forever remind me of Peter Fuhren who helped my shaped this line of thought.

There are three key elements relating to the size and digital maturity of organisations, and the likelihood of them to have implemented a headless system as part of a composable architecture.

#### Small organisations 
Higher propensity based on developer capability. Selected a headless CMS’ based on the desire of the small development team or single developer who wanted to use the latest tech. A magpie decision but not necessarily problematic. Started down a composable architecture route but perhaps without due reasoning, but could stand them in good stead if they wish to add more capability to their DXP stack

#### Medium-sized organisations 
Low propensity. Organizations with medium traffic digital presence dominated by the web channel. Comfortably working with a single vendor DXP and using advanced capability. Not rapidly growing or scaling so not experiencing any performance issues or limiting factors

#### Enterprise organisations. 
High propensity based on fit for composable architecture. These are high volume, high growth organisations who require flexible architecture to deliver their objectives. Those in highly competitive spaces that are willing to invest more to achieve marginal performance gains 
The smile is a reflection on where I think headless and composable has current traction, and also perhaps illustrates the characteristics of those organisations who haven’t achieved success.

Isn’t this ultimately quite straightforward? Composable architectures are best fit for organisations that work at scale and speed. These are the organisations that have experienced the limits of more traditional coupled architectures. They are applying composable as a solution to a well-defined problem.

## The rising tide lifts all boats
Within the DXP space, we’re nearing the end of a cycle of rapid disruption caused by new headless vendors showcasing the possibilities that modern cloud hosting and open architectures provide.

As with any disruption, it follows a similar course.

> - A new breed of headless vendor have identified a problem and provided solutions
> - Existing vendors have responded
> - Early adopters have demonstrated pitfalls
> - The market consolidates

So where does that leave us? I think we’ve reached the middle. A point of equilibrium where customers are left with greater choice and flexibility to pick and choose the precise flavour of DXP to suit their stage of growth and need. Choice is never a bad thing.

The best thing about the middle in my opinion. DXP/CMS vendors will need to differentiate on features and impact rather than technical terminology. The words ‘headless’ and ‘composable’ will drop away from marketing materials. Confusion is reduced and I’ll be quietly happy that I don’t have to explain them once again. 

> This post was first published on LinkedIn Pulse on 23rd February 2023 <a href="https://www.linkedin.com/pulse/technology-choices-hard-enough-making-sense-headless-hype-everard" target="_blank" rel="noopener noreferrer">Technology Choices are Hard Enough. Making sense of Composable & Headless Hype</a>