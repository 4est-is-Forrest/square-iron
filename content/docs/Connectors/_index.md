---
title: Connectors.py (Module)
layout: docs
weight: 

---
## Purpose

- Functions to connect to various ServiceNow instances through a Selenium WebDriver object or a Requests Session.
- The Driver method minimizes login effort and after success, asserts cookies are saved locally.
- The Session method tries to import good cookies from various locations, optional login action

## Details

This is a module specific to the work environment and most other scripts/tools depend on it. Chromedriver is the only Webdriver ever used in my code.

The general idea is to have reliable methods to initiate authenticated Sessions and/or Driver sessions with three different ServiceNow instances. It asserts that cookies are saved locally so that subsequent runs do not require login. Sessions can also import these cookies in order to communicate with the respective instance's JSON web service. Sessions also handle checking the validation of any existing cookies.

When only a Session is needed, cookies from the user's Chrome browser will also be considered a potential cookie source. The Session method also has an optional login action, using a Driver for logging in and closing it only after the cookies have been saved locally (referred to as persisting in the code).