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

[browser-cookie3](https://pypi.org/project/browser-cookie3/0.6.0/)

Standard imports one would expect, but the 'browser_cookie3' library is worth elaborating on. This library offers methods to easily interact with browser cookies (obviously), but this gave me two important capabilities. It offered a way to assert that cookies have been saved locally (Chrome writes cookies in 30 second intervals) and also offered a way to import my actual browser's cookies, which is mostly just a convenience.

#### _Constants_

    PROXY = '<My Local Machine>:<Px-Proxy Port>'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
    DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
    COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
    CHROME_OPTIONS = webdriver.ChromeOptions()
    CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))

Px-proxy is necessary for Sessions to play nice with an NTLM Proxy. See document 'General Context' for details on both.

There is a separate script for new Python installations that ensures 'resources' exists in the script user's home path (among other things). Essentially a uniform location to house multiple assets for multiple tools/scripts.

Chromedriver will create the profile directory if one does not already exist.

#### _Function: Spawn Driver_

    def spawn_driver(instances=['instance1'],credentials={},persist=True):

**Arguments:**

* Instances: list of one or more expected ServiceNow instances; Default: The most used instance
* Credentials: (optional) dictionary of credentials for instances other than 'instance1.' Default is none. Dictionary key nomenclature is simply: instance_usr/pwd
* Persist: Whether the Driver should be kept open after completed login/logins; Default is True

**Functionality:**

The function is tailored for three specific instances with differentiating login methods and landing pages. Other instances can be added with relative ease, needing only to define the login process and whether the instance requires username and password credentials.

* Instance1: PKI card login with pin (most used)
* Instance2: Username & Password
* Instance3: Username & Password

</hr>

**_Local Functions_**

    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))

The function is just a short hand for the 'WebDriverWait' method, my preferred method in selecting HTML elements whenever I use Selenium. It looks neater and is convenient to use in the console. 

**_Credential Handling_**

    for inst in instances:
        if inst in ['instance2','instance3']:
            try:
                usr = credentials['{}_usr'.format(inst)]
                pwd = credentials['{}_pwd'.format(inst)]
            except:
                usr = input('Username ({}): '.format(inst))
                credentials['{}_usr'.format(inst)] = usr
                pwd = getpass('Password ({}): '.format(inst))
                credentials['{}_pwd'.format(inst)] = pwd

Instances requiring a username and password to login are defined in this loop's list. Additional instances can simply be added to this list. This block determines whether credentials have been supplied and, if not, collects them appropriately. 

Along with making sure credentials are available/collected, it does that before Chromedriver is opened which can easily cover the script's/tool's window. 

The reason behind collecting the credentials in the first place is to return them for scripts and tools meant to run for extended periods time.