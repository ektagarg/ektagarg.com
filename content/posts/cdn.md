---
title: "CDN: Content Delivery Network"
date: 2021-04-03T02:01:58+05:30
description: ""
tags: [Networking, CDN, TIL]
featured_image: "/images/cdn/cdn_header.jpg"
categories: Networking
comment : true
---

## What is a CDN?
It is a service that accelerates internet content delivery to make your website faster.  

## What is the challenge that CDN addresses?
For eg: we have software that has users from all around the world, but we don't have servers all around the world and the experience that those users have due to that dynamic.

__Let's understand the problem with an example:__

![Users located worldwide](/images/cdn/cdn.jpg)

I have a simple diagram showing I have a server hosted in Singapore and I have users all around the world. In total, I have got 50 users who are accessing my website [10 in New York, 5 in London, 10 in Singapore, 15 in Delhi, 10 in Hong Kong] 

Users in London make a request to the server, they are 67 hundred miles from the server in Singapore so they make calls from up and down from this far. And the amount of time it takes is measured in milliseconds and just that round trip would be about 140 ms, for our users in New York, that would be about 230 ms and it would take lesser time in Delhi and Singapore as it is closer in proximity. So, this means the further the users are away, the longer it takes and ultimately the slower the website would be for the users.

This is the problem that CDN solves by __reducing the amount of distance between the resources a website needs__ in order to serve its users. So what is CDN exactly doing here?

It places CDN around the world in as many locations as possible. 

In our above example, let's assume we have placed CDN in every location the users are trying to access our content from so it's in New York, Delhi, Singapore, Hong Kong, London.

So now when a user from London tries to access the website, it's first retrieved by the content delivery network service and then distributed around the world. So we have a single request down to the Singapore server. It's now distributed around the world and our users now instead of going all the way to Singapore can retrieve content directly from their closest geographical location, drastically reducing the amount of time it takes to retrieve the content.

Benefits:
1. Increased response time for users located worldwide.
2. Reduction in load on just one server or we can say that load is getting distributed.
3. Reduction in the amount of capacity required on a single server in low.
4. Increase in the uptime in server as the users are now making calls in a distributed manner.
5. Increase in security through obscurity.

Thanks for reading. Stay tuned!