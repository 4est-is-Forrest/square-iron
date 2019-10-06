---
title: Connectors (Module)
layout: docs
weight: 
subtitle: ''

---
Connectors is an essential module I created and use to simplify authentication with different ServiceNow instances in order to use those instances' JSON Web Services (Session) and/or navigate their GUI's (Webdriver).

[Module Code](/docs/connectors/module-code/)

**Nearly every script in this environment depend on this module.**
<hr />

## Summary

**Note:** I use the terms 'Driver' and 'Webdriver' interchangeably in the documents ahead.

The module ultimately has three primary functions:

* **A Webdriver function** that minimizes login effort and, after successful login, asserts cookies are saved locally, usually in order for a Session to import them.
* **A Session method** that tries to import good cookies from the user's browser or the Webdriver's cookies. Optionally, a Webdriver launches temporarily to perform a login and closes once good cookies are available for the Session.
* **A 'cookie persist' method**, primarily used by the Webdriver method, to repeatedly test and wait until expected authentication cookies have been saved to the Webdriver's "Cookies" file. Details on 'cookie persistence' can be found below.

A Webdriver that runs concurrently with a Session is often necessary for many of the tools I've deployed in this environment, and this module makes creating and managing these objects simpler.

_See '_[_Help Desk Environment_]()_' for information on why Sessions and Webdrivers are both necessary._

<hr />

## Cookie Persistence (Module Context)

**Note:** I refer to a browser writing cookies locally as 'cookies persisting.' The third function of this module specifically addresses this and is really only used within the Spawn Driver function.

**Chrome's Interval:** Cookie persistence must be asserted in my module due to Google Chrome's 30-second interval when writing cookies to its 'Cookies' file. After a login is successful via a Webdriver, a Session will query the relevant instance's JSON service repeatedly until a status code 200 is returned. This is to ensure subsequent Sessions may immediately import these cookies so as to communicate with ServiceNow's JSON Web Service.

<hr />