---
title: Environment & Context
weight: 
template: docs

---
**Global Use:** The tools and scripts in the sections ahead are meant to be run by any individual in a help desk environment. Meaning, they can be run reliably across a team, regardless of the local system.

**Distribution:** Sometimes executables are distributed but it's simpler to have users install Python and run a setup.py specific to this environment.

**Simple:** They must also be simple to use due to a global lack of coding knowledge among the intended users.

**OS:** The OS environment is a mix of Windows 7 and Windows 10, with Windows 10 increasingly becoming the norm/required.

**ServiceNow:** Because the following scripts are for a help desk environment, the vast majority of them involve interacting with some instance of ServiceNow.

<hr/>

## Proxy Challenge & Solution

Requests Sessions and Pip requests, by themselves, are unable to negotiate authentication with the environment's NTLM proxy, thus they fail.

**CNTLM:** The software, [CNTLM](http://cntlm.sourceforge.net/), offered a method to address this by acting as an intermediary proxy and handling authentication. So long as the configured user credentials for CNTLM were kept up to date, Pip and Requests could pass through it and reliably reach their destinations.

**_Px-Proxy:_** While CNTLM was a perfectly effective solution, I opted to utilize [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") (basically a Python version of CNTLM) instead because of its capability to use the current user's credentials without any configuration.

**Implementation:** With help of the author's '.bat' file held in Px-Proxy's Git repository, I created an executable of Px-Proxy and added it as a Windows Startup Service. This, together with white listing subnets expected to use my scripts, resulted in a constant and reliable path for Pip and Requests to pass through across the team, with very minimal maintenance.

<hr/>

## Python & ServiceNow (GUI/JSON)

[JSON Web Service Docs](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/inbound-other-web-services/concept/c_JSONv2WebService.html)

Naturally, anyone beyond ServiceNow admins and developers would be denied simple authentication access to any of ServiceNow's multiple web services (JSON, REST, etc).

**_Selenium:_** A Selenium Webdriver initially seemed to be the only automation option available for this environment when it came to interacting with ServiceNow. The initial scripts written under this pretense navigated ServiceNow's GUI and favored sending JavaScript to a page in order to accomplish its objective.

**_Requests:_** I eventually discovered ServiceNow's JSON Web Service was perfectly accessible so long as the HTTP requests were accompanied with valid authentication cookies imported from a browser. This lead to major script revisions favoring the Requests library over Selenium as much as possible.

**_Both:_** The JSON Web Service has three important limitations (for 'itil' users at least). 

1. There is no way to negotiate authentication in this environment outside of ServiceNow's GUI. 
2. Attaching files to ServiceNow records is not possible via JSON. 
3. It is not possible to change a record's state to anything synonymous with closed or resolved. 

For these three commonly performed tasks, a Driver reliably fills the automation roll.