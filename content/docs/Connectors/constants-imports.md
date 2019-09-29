+++
layout = "docs"
title = "Global Constants & Imports"
weight = 2

+++
Information regarding imports and constants for this module.

<hr />

## _Imports_

```python
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
```

The [browser-cookie3](https://pypi.org/project/browser-cookie3/0.6.0/) library is worth elaborating on. This library offers methods to easily interact with browser cookies (obviously) thus, offering two important capabilities. Firstly, it offered a way to validate and test cookies with the help of Sessions and secondly, importing regular Chrome cookies for Sessions became possible. The latter is mostly just a nice convenience. So long as I was logged in to the instance via my preferred browser, I could easily interface with the JSON web service the moment I needed to.

## _Constants_

```python
    PROXY = '<My Local Machine>:<Px-Proxy Port>'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
    DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
    COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
    CHROME_OPTIONS = webdriver.ChromeOptions()
    CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))
```

#### **_Px-Proxy_**

[Px-proxy](https://github.com/genotrance/px) is necessary for Sessions to play nice with the environment's NTLM proxy. As the author, Genotrance, describes it, "An HTTP proxy server to automatically authenticate through an NTLM proxy."

_See the '_[_General Context_](/docs/general-context/)_' document for more information on this environment's proxy challenges and solutions ._

#### **_Chromedriver Executable Path_**

There is a separate script distributed to users in order to make it easy to keep all assets, modules and scripts on their machine up to date and in the correct locations. Regarding the constants above, the directory named 'RESOURCES' is in the user's home path and contains 'chromedriver.exe' thanks to this separate script.

#### **_Chromedriver Profile Directory_**

Chromedriver will create the profile directory if one does not already exist. The profile allows the Driver to behave like a regular browser in the sense that cookies, history, and other objects of that nature are retained. It is also independent of the user's default Chrome profile, primarily because regular Chrome and the Driver cannot access the same profile concurrently.

<hr />