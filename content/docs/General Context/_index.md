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

## Details

#### _Proxy Issues_

On their own, Requests and Pip will fail against the required NTLM proxy in this particular environment. I used CNTLM for a while but I wanted something lower maintenance. 

[Px-Proxy](https://github.com/genotrance/px "Px-Proxy") solved this problem with it's capability to use the currently logged in user. I white list machines utilizing my code and set myself as the proxy in the tools. Set as a Windows Start-up Service due to the tools' dependence on its availability.

#### _ServiceNow JSON Web Service_

Most instances 