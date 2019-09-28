+++
layout = "docs"
title = "Constants & Imports"
weight = 1

+++
Additional information regarding imports and constants.

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

The [browser-cookie3](https://pypi.org/project/browser-cookie3/0.6.0/) library is worth elaborating on. This library offers methods to easily interact with browser cookies (obviously) providing two important capabilities I required. Firstly, it offered a way to validate and test cookies with the help of Sessions and secondly, importing regular Chrome cookies for Sessions became possible. The latter is mostly just a nice convenience.

#### _Constants_

    PROXY = '<My Local Machine>:<Px-Proxy Port>'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
    DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
    COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
    CHROME_OPTIONS = webdriver.ChromeOptions()
    CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))

[Px-proxy](https://github.com/genotrance/px) is necessary for Sessions to play nice with the environment's NTLM proxy. As the author, Genotrance, describes it, "An HTTP proxy server to automatically authenticate through an NTLM proxy." See the 'General Context' section for more information on this and why it is necessary.

There is a separate script all my users run to keep all assets, modules and scripts up to date and where I want them. In this context, the directory named 'RESOURCES' is in the user's home path and contains 'chromedriver.exe' because of this separate script.

Chromedriver will create the profile directory if one does not already exist. The profile allows the Driver to behave like a regular browser in the sense that cookies, history, and other objects of that nature are retained. It is also independent of the user's default Chrome profile, primarily because regular Chrome and the Driver cannot access the same profile concurrently.