---
title: Connectors.py (Module)
layout: docs
weight: 

---
An essential module I use to simplify authentication with different ServiceNow instances in order to use the JSON Web Service (Session) or navigate the GUI (Webdriver).

## Context/Problem

**_Why JSON?_**

Simply due to policy, it was not possible for simple authentication with ServiceNow's various web services to be enabled for anyone beyond SNOW admins and developers. With the help of cookies however, access to the JSON Web Service via Requests is possible. 

**_Then why Driver?_**

The JSON web service has three important limitations: 

1. Obviously, something else must negotiate login and obtain cookies
2. Attaching items to ServiceNow records is not possible via JSON yet often necessary
3. ServiceNow records can not be changed to a resolved/closed state outside of the GUI for 'itil' users

**_Ultimately_**

A Webdriver that runs concurrently with a Session is often necessary for many of the tools I've deployed in this same environment.

## Summary

* Two functions to connect to various ServiceNow instances through a Selenium WebDriver and/or a Requests Session, 
* The Driver method minimizes login effort and after success, asserts cookies are saved locally, usually for a Session to import them.
* The Session method tries to import good cookies from the Driver and the user's Chrome browser. Optional 'login action' launches a temporary Driver until good cookies are saved.

## Details

This is a module specific to my help desk environment and most other scripts/tools for said environment depend on it. Chromedriver is my preferred Webdriver.

The general idea is to have reliable methods to initiate authenticated Sessions and/or Driver sessions with three different ServiceNow instances (while making it simple to add more). It also asserts that cookies are saved locally so that subsequent runs do not require a login action and, more importantly, so Sessions can also import these cookies in order to access the instance's JSON web service. Sessions also handle checking the validation of any existing cookies.

When only a Session is needed, cookies from the user's Chrome browser will also be considered a potential cookie source. The Session method also has an optional login action, using a Driver for logging in and closing it only after the cookies have been saved locally. 

I refer to saving cookies locally as 'persisting.'