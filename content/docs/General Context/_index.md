---
title: Environment & Context
weight: 
template: docs

---
**Global Use:** The tools and scripts in the sections ahead are meant to be run by any individual in a help desk environment. Meaning, they can be run reliably across a team, regardless of the local system.

**Distribution:** Sometimes executables are distributed but it's simpler to have users install Python and run a setup.py specific to this environment.

**Simple:** They must also be simple to use due to a global lack of coding knowledge among the intended users.

**OS:** The OS environment is a mix of Windows 7 and Windows 10, with Windows 10 increasingly becoming the norm/required.

<hr/>

## Proxy Challenge & Solution

Requests Sessions and Pip requests, by themselves, are unable to negotiate authentication with the environment's NTLM proxy, thus they fail.

**CNTLM:** The software, [CNTLM](http://cntlm.sourceforge.net/), offered a method to address this by acting as an intermediary proxy and handling authentication. So long as the configured user credentials for CNTLM were kept up to date, Pip and Requests could pass through it and reliably reach their destinations.

**_Px-Proxy:_** While CNTLM was a perfectly effective solution, I opted to utilize [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") (basically a Python version of CNTLM) instead because of its capability to use the current user's credentials without any configuration.

**Implementation:** With help of the author's '.bat' file held in Px-Proxy's Git repository, I created an executable of Px-Proxy and added it as a Windows Startup Service. This, together with white listing subnets expected to use my scripts, resulted in a constant and reliable path for Pip and Requests to pass through across the team, with very minimal maintenance.

## Python & ServiceNow

[JSON Web Service Docs](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/inbound-other-web-services/concept/c_JSONv2WebService.html)

For quite sometime, I believed ServiceNow's GUI was the only way to interact with its data, mostly because the first service I requested access to was the REST API, in which I was denied access. Naturally, anyone beyond ServiceNow admins and developers would be denied simple authentication access to any of ServiceNow's multiple web services.

**_Selenium_**

Thus, a Selenium Webdriver seemed to be the only automation option available for this environment. Tools and scripts using this method were certainly crude and error-prone at first, but they did become more refined as I gained experience, eventually interacting with ServiceNow mostly through JavaScript. Still, I never felt satisfied using this method and fervently searched for alternatives when I had the time.

**_JSON Web Service Authentication_**

I eventually discovered the JSON Web Service was perfectly accessible so long as the HTTP requests were accompanied with valid authentication cookies for their respective ServiceNow instance. This lead to many of my tools and scripts being completely rewritten to utilize this method as much as possible.

**_Selenium Webdriver & JSON Web Service Together_**

The Webdriver is still necessary due to the JSON Service having three limitations. First, there is no way to negotiate authentication in this particular environment. Second, attaching files to ServiceNow records is not possible via JSON. And third, it is not possible to change a record's state to anything synonymous with closed or resolved. A Driver is used to handle these three cases but the vast majority of my code utilizes the JSON Web Service.