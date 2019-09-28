---
title: Connectors (Module)
layout: docs
weight: 

---
An essential module I use to simplify authentication with different ServiceNow instances in order to use the JSON Web Service (Session) or navigate the GUI (Webdriver).

## Summary

Two Functions:

* The Driver method minimizes login effort and, after success, asserts cookies are saved locally, usually in order for a Session to import them.
* The Session method tries to import good cookies from the Driver, failing that, the user's Chrome browser. Optionally, a Driver launches temporarily to perform a login and closes once good cookies are available for the Session.

## Context

**_Why JSON?_**

Simply due to policy, it was not possible for simple authentication with ServiceNow's various web services to be enabled for anyone beyond SNOW admins and developers. With the help of cookies however, access to the JSON Web Service via Requests is possible. This is true for all instances of ServiceNow utilized in this environment.

**_Then why Driver?_**

The JSON web service has three important limitations:

1. Obviously, something else must negotiate login and obtain cookies
2. Attaching items to ServiceNow records is not possible via JSON yet often necessary
3. ServiceNow records can not be changed to a resolved/closed state outside of the GUI for 'itil' users (I theorize) 

**_Ultimately_**

A Webdriver that runs concurrently with a Session is often necessary for many of the tools I've deployed in this same environment, and this module made creating and managing these connections much simpler.

## Details

This is a module specific to a help desk environment and most other scripts/tools for said environment depend on it. Chromedriver is my preferred Webdriver.

The general idea is to have reliable methods to initiate authenticated Sessions and/or Driver sessions with three different ServiceNow instances (while making it simple to add more). It also asserts that cookies are saved locally so that subsequent runs do not require a login action and, more importantly, so Sessions can also import these cookies in order to access the instance's JSON web service.

**Cookie persistence** must be asserted due to Google Chrome's 30-second intervals of writing to its 'Cookies' file. Sessions handle and validate the cookies before anything else takes place, otherwise bad or 'non-existent' cookies would likely be imported to the Session, resulting in failure of the entire goal.

When only a Session is needed, cookies from the user's Chrome browser will also be considered a potential cookie source. The Session method has an optional login action, using a Driver for logging in and closing it only after the cookies have been saved locally.

**Note:** I refer to saving cookies locally as 'persisting.'