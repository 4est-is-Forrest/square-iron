+++
img_path = ""
layout = "page"
subtitle = ""
title = "Environment & Context"

+++
## Professional Environment

From the beginning, when I first started pursuing Python as a skill, I worked as a Help Desk agent. The job always suited my school schedule very well so that I could do both full-time but it also left me substantial time to pursue my own academic interest quite frequently, hence my proficiency with Python and eventual conversion to IT process automation and other coding specialties.

Ultimately, I'm relied upon to code for my organization but I do so in an unofficial capacity, thus lacking API accesses and license privileges that would otherwise make my life easier. So keep in mind, that much of my code is working around this (I will be mindful to point it out).

I'm largely left to my own devices in finding areas to automate and/or improve, therefore nearly all of my code's were my own ideas and projects. Sometimes, colleagues approach me with a problem or an idea in which I work closely with them to build what they are looking for.

## Technical Environment

Most of these tools are meant to be independent with the capacity to be run across the team as seamlessly as possible without much user interaction aside from simple binary questions.

The OS environment is Windows 7/10 which isn't much of a factor.

## Work Arounds

#### _Proxy Issues_

The networking environment is typical for any large organization but I ran into fatal issues using Pip and Requests due to our traffic being required to pass through an NTLM proxy.

Thankfully, I found a permanent solution utilizing [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") in executable form as a Windows Service (start up). Utilizing PX's white list configuration, I gained the capability to use Pip and Requests without constant maintenance of a different service (CNTLM) on individual machines.

#### _ServiceNow JSON Web Service_ 