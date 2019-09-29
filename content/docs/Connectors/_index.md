---
title: Connectors (Module)
layout: docs
weight: 
subtitle: ''

---
## Overview

An essential module I use to simplify authentication with different ServiceNow instances in order to use the instances' JSON Web Services (Session) and/or navigate their GUI's (Webdriver).

## Summary

Comprised of three functions:

* **A Driver method** that minimizes login effort and, after successful login, asserts cookies are saved locally, usually in order for a Session to import them.
* **A Session method** that tries to import good cookies from the Driver, failing that, the user's Chrome browser. Optionally, a Driver launches temporarily to perform a login and closes once good cookies are available for the Session.
* **A 'cookie persist' method**, primarily used by the Driver method, to repeatedly test and wait until expected authentication cookies have been saved to the Driver's 'Cookies' file. Details on 'cookie persistence' can be found below.

A Webdriver that runs concurrently with a Session is often necessary for many of the tools I've deployed in this environment, and this module makes creating and managing these connections simple, even from the console.

<hr />

## Context Summary

_See Doc: General Context for more details on the following._

#### **_Environment_**

This module is specific to a help desk environment and most other scripts/tools for said environment depend on it. Chromedriver is my preferred Webdriver.

#### **_Why JSON?_**

Simply due to policy, it was not possible for simple authentication with ServiceNow's various web services to be enabled for anyone beyond SNOW admins and developers. With the help of cookies however, access to the JSON Web Service via Requests is possible. This is true for all instances of ServiceNow utilized in this environment.

#### **_Then why a Driver too?_**

The JSON web service has three important limitations:

1. Obviously, something else must negotiate login and obtain cookies
2. Attaching items to ServiceNow records is not possible via JSON yet often necessary
3. ServiceNow records can not be changed to a resolved/closed state outside of the GUI for 'itil' users (I theorize)

## Cookie persistence

_Not covered in Doc: General Context_

**Note:** I refer to a browser writing cookies locally as 'cookies persisting.' The third function of this module specifically addresses this.

Cookies persistence must be asserted due to Google Chrome's 30-second intervals of writing to its 'Cookies' file. After a login is successful, a Session queries the instance's JSON service repeatedly until a status code 200.

When I was unaware of the 30 second interval, I was very confused why Sessions would often fail authentication within a script, yet, when I established the connection step-by-step in a console, it worked almost every time. I eventually noticed that the local cookies were almost always bad if the Driver was immediately closed after login, implying some timing issue. Thankfully, a simple Google search revealed Chrome's 30-second interval and I adjusted the module accordingly.