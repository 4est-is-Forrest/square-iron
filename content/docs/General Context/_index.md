---
title: General Context
weight: 
template: docs

---
## Summary

* Tools and scripts to automate or aid in Help Desk processes
* Most are tools capable of being run by all team members
* Professionally: Windows Environment; Academically: Linux
* [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") required for Pip and Requests to authenticate with organization NTLM Proxy
* Requests must import cookies in order to use ServiceNow's JSON web service due to multi-factor authentication requirement with most instances.

## Details

#### _Tools utilized by any team member_

Most of my code is to be used by other team members who are not coders. They can't be too complex to run or if they are, documentation and/or training must accompany them. 

#### _Windows_

Primarily Windows 7 until Windows 10 became the new requirement Academically, I've used Python on Linux distributions.

#### _Proxy Issues_

On their own, Requests and Pip will fail against the required NTLM proxy in this particular environment. I used CNTLM for a while but I wanted something lower maintenance. 

[Px-Proxy](https://github.com/genotrance/px "Px-Proxy") solved this problem with it's capability to use the currently logged in user. I white list machines utilizing my code and set myself as the proxy in the tools. Set as a Windows Start-up Service due to the tools' dependence on its availability.

#### _ServiceNow JSON Web Service_

Most instances used in this environment have multi-factored authentication methods and I would not be granted access to any kind of key or credentials to get around this.

I discovered that ServiceNow's JSON Web Service was accessible from a browser so long as the user was logged in. This translated into the Requests library and it became possible for my tools and scripts to utilize the web service instead of a GUI.