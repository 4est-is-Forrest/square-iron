---
title: General Context
weight: 
template: docs

---
## Summary

* Tools and scripts to automate or aid in Help Desk processes
* Most are tools capable of being run by all team members
* Windows Environment
* [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") required for Pip and Requests to authenticate with organization NTLM Proxy
* Requests must import cookies in order to use ServiceNow's JSON web service due to multi-factor authentication requirement with most instances.

## Technical Environment

## Necessary Workarounds

#### _Proxy Issues_

The networking environment is typical for any large organization but I ran into fatal issues using Pip and Requests due to our traffic being required to pass through an NTLM proxy.

Thankfully, I found a permanent solution utilizing [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") in executable form as a Windows Service (start up). Utilizing PX's white list configuration, I gained the capability to use Pip and Requests without constant maintenance of a different service (CNTLM) on individual machines.

#### _ServiceNow JSON Web Service_

When I discovered the existence of this service, I was crestfallen to learn that I had no valid username or password to authenticate my requests.

In my testing of the service, I noticed I was able to view raw JSON in my browser as long as I had authenticated with the browser recently. I theorized this would translate to Request's Session class and I was right. By importing existing cookies from browsers, Python can utilize the service through requests alone.