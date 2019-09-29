---
title: Connectors (Module)
layout: docs
weight: 
subtitle: ''

---
This is an essential module I created and use to simplify authentication with different ServiceNow instances in order to use the instances' JSON Web Services (Session) and/or navigate their GUI's (Webdriver). 

**Nearly every script active in this environment rely on this module.**

<hr />

## Summary

Comprised of three functions:

* **A Driver method** that minimizes login effort and, after successful login, asserts cookies are saved locally, usually in order for a Session to import them.
* **A Session method** that tries to import good cookies from the user's browser, failing that, the Driver's locally saved cookies. Optionally, a Driver launches temporarily to perform a login and closes once good cookies are available for the Session.
* **A 'cookie persist' method**, primarily used by the Driver method, to repeatedly test and wait until expected authentication cookies have been saved to the Driver's 'Cookies' file. Details on 'cookie persistence' can be found below.

A Webdriver that runs concurrently with a Session is often necessary for many of the tools I've deployed in this environment, and this module makes creating and managing these connections simple, even from the console.

_See '_[_Environment & Context_](https://jjydyhotlchyoa.instant.forestry.io/docs/general-context/)_' for information on why Sessions and Webdrivers are both necessary._

<hr />

## Cookie Persistence

**Note:** I refer to a browser writing cookies locally as 'cookies persisting.' The third function of this module specifically addresses this and is really only used within the Spawn Driver function.

**Chrome's Interval:** Cookies persistence must be asserted due to Google Chrome's 30-second interval when writing cookies to its 'Cookies' file. After a login is successful via a Driver, a Session will query the relevant instance's JSON service repeatedly until a status code 200 is returned. This is to ensure subsequent Sessions may immediately import these cookies so as to communicate with ServiceNow's JSON Web Service.

<hr />