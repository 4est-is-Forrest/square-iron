---
title: General Context
weight: 
template: docs

---
## Environment

Most of these tools and scripts are meant to be run by any individual in a help desk environment. Meaning, they can be run reliably across a team regardless of the what local machine they are used on. 

They must also be simple to use due to a global lack of coding knowledge among the intended users.

The OS environment is a mix of Windows 7 and Windows 10, with Windows 10 increasingly becoming the norm/required.

## Necessary Workarounds

#### _Proxy Issues: Px-Proxy Solution_

Requests Sessions and Pip requests, by themselves, are unable to negotiate authentication with the environment's NTLM proxy, thus they fail. 

**CNTLM:** The software, [CNTLM](http://cntlm.sourceforge.net/), offered a method to address this by acting as an intermediary proxy that handled the authentication. So long as the configured user credentials for CNTLM were kept up to date, Pip and Requests could be pointed at it and reliably reach their destinations.

**Px-Proxy**: While CNTLM was an effective solution, I opted to utilize [Px-Proxy](https://github.com/genotrance/px "Px-Proxy") (basically a Python version of CNTLM) instead because of its capability to use the current user's credentials without any configuration.

With help of the author's '.bat' file held in Px-Proxy's repository, I created an executable from the Python code and added it as a Windows Startup Service. This, together with white listing subnets expected to use my scripts, all resulted in a constant and reliable path for Pip and Requests to pass through, with very minimal maintenance.

#### _ServiceNow JSON Web Service_

When I discovered the existence of this service, I was crestfallen to learn that I had no valid username or password to authenticate my requests.

In my testing of the service, I noticed I was able to view raw JSON in my browser as long as I had authenticated with the browser recently. I theorized this would translate to Request's Session class and I was right. By importing existing cookies from browsers, Python can utilize the service through requests alone.