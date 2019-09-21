---
title: Context & Reference
weight: 5
template: docs
---

## Technical Environment

Most of these tools and scripts are meant to be run by several individuals. Most have the capacity to be used across a team regardless of environment.with the capacity to be run across the team as seamlessly as possible without much user interaction aside from simple binary questions.

The OS environment is Windows 7/10 which isn't much of a factor.

## Necessary Workarounds

#### _Proxy Issues_

The networking environment is typical for any large organization but I ran into fatal issues using Pip and Requests due to our traffic being required to pass through an NTLM proxy.

Thankfully, I found a permanent solution utilizing [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") in executable form as a Windows Service (start up). Utilizing PX's white list configuration, I gained the capability to use Pip and Requests without constant maintenance of a different service (CNTLM) on individual machines.

#### _ServiceNow JSON Web Service_

When I discovered the existence of this service, I was crestfallen to learn that I had no valid username or password to authenticate my requests.

In my testing of the service, I noticed I was able to view raw JSON in my browser as long as I had authenticated with the browser recently. I theorized this would translate to Request's Session class and I was right. By importing existing cookies from browsers, Python can utilize the service through requests alone.
