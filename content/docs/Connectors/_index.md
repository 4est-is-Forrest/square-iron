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

_See 'General Context' for information on why both Sessions and a Webdriver are necessary._

<hr />

## Cookie Persistence

_Not covered in Doc: General Context_

**Note:** I refer to a browser writing cookies locally as 'cookies persisting.' The third function of this module specifically addresses this and is really only used within the Spawn Driver Function.

Cookies persistence must be asserted due to Google Chrome's 30-second intervals of writing cookies to its 'Cookies' file. After a login is successful, a Session will query the instance's JSON service repeatedly until a status code 200 is returned.