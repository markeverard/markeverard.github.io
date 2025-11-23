---
title: Using Optimizely with modern micro front end architectures.
date: 2024-10-20T14:30:26+00:00
author: Mark Everard
layout: post
img: 2024-10-11-microfrontends.jpg
description: Optimizely products have now evolved so that the full Optimizely One suite can be used within cutting edge modern composable architectures to provide significant agility, scale and speed improvements to online experiences.
categories:
  - Opinion
tags: [Opinion, Optimizely, Architecture]

---
Optimizely products have now evolved so that the full **Optimizely One** suite can be used within cutting edge modern composable architectures to provide significant agility, scale and speed improvements to online experiences.

Culminating in the release of their SaaS CMS. This has been a multi-year journey for Optimizely. They have engineered cloud native solutions such as Optimizely Graph, improving their headless CMS and Commerce capabilities whilst maintaining their heritage of focus on the end user and of ease-of-use, with their new Visual Builder.

## Microfront-ends
Microfront-ends (MFEs) are an architectural pattern and approach which mimic the benefits of micro services for API’s but applies them to user interface elements, such as a web page or component.

> MFEs divide a user interface into isolated, small pieces of functionality, enabling them to be operated independently.

This separation ensures that each MFE can be developed, deployed and scaled without impact on other MFEs or the rest of the application.

For high-traffic e-commerce platforms, this translates to improved performance and stability as different parts of the website can be optimized and scaled according to their specific demands. For instance, product pages can be scaled to deal with increased load without scaling the checkout or user profile sections, or changes to the payment section can be deployed without impacting the rest of the checkout.

Increasingly large e-commerce sites and global web platforms are choosing to build using an MFE approach, as at scale the operational and cost benefits can be significant.

## Why choose an MFE architecture?

At a high level an MFE architecture offers:

 > **Independent deployments** - Updates can be rolled out without a full deployment cycle, safe in the knowledge that a single MFE deployment cannot impact other MFEs, removing the risk of widespread outage or significant downtime. 

> **Resilience and fault isolation** - From an application perspective, MFEs are extremely fault tolerance. A failure in one MFE cannot cascade and impact the entire application. For instance, an issue with a payment micro front end would not prevent users adding to their cart, or completing their order via another payment method, whilst the original issue is resolved 

> **Improved development agility** - Traditional monolithic solutions can slow down development cycles due to their complexity and interdependencies. MFE’s foster an environment where multiple development teams can work concurrently on different features without fear (or the rework required) of conflicting change or regression bugs.

> **Technology diversity and innovation** - MFEs allow teams to choose the most suitable technology stack for their specific needs. A team responsible for a recommendation engine might leverage the latest innovative machine learning libraries alongside user interface teams adopting their choice of latest front end frameworks.

> **Focussed expertise and better maintainability** - Breaking down the front-end into smaller parts allows you to focus your development teams on specific functional areas, working with smaller applications, rather than learning a single giant enterprise application (which is often prohibitive).  

This isolation allows each team to apply its own coding and development standards appropriate to the functional area. Together these can reduce developer onboarding times and increase quality as the teams more quickly master their respective domains and functional areas.

## Caution

It is of course worthwhile noting that MFE architectures are inherently more complex than traditional application architectures, and so are most suited to more complex enterprise solutions with large development teams.

If you’re operating at scale and interested in learning more about MFE architectures or how Optimizely can be used to deliver them, then please get in touch.
