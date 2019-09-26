---
title: Connectors (module)
layout: docs
weight: 

---
## Purpose

* Functions to connect to various ServiceNow instances through a Selenium WebDriver object or a Requests Session. 
* The Driver method minimizes login effort and after success, asserts cookies are saved locally. 
* The Session method tries to import good cookies from various locations, optional login action

## Details

This is a module specific to the work environment and most other scripts/tools depend on it. Chromedriver is the only Webdriver ever used in my code.

The general idea is to have reliable methods to initiate authenticated Sessions and/or Driver sessions with three different ServiceNow instances. It asserts that cookies are saved locally so that subsequent runs do not require login. Sessions can also import these cookies in order to communicate with the respective instance's JSON web service. Sessions also handle checking the validation of any existing cookies.

When only a Session is needed, cookies from the user's Chrome browser will also be considered a potential cookie source. The Session method also has an optional login action, using a Driver for logging in and closing it only after the cookies have been saved locally (referred to as persisting in the code).

## Walk Through

#### _Imports_

    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.chrome.options import Options
    from selenium.webdriver.support import expected_conditions as EC
    from selenium.webdriver.common.by import By
    from selenium.webdriver.common.keys import Keys
    import requests
    import os,sys
    import browser_cookie3
    from getpass import getpass
    import time
    import traceback
    from shutil import copy

Standard imports one would expect, but the 'browser_cookie' library is worth elaborating on. This library offers methods to easily interact with browser cookies (obviously), but this gave me two important capabilities. It offered a way to assert that cookies have been saved locally (Chrome writes cookies in 30 second intervals) and also offered a way to import my actual browser's cookies, which is mostly just a convenience. 

Browser-Cookie