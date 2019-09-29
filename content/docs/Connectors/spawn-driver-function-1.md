+++
layout = "docs"
title = "Spawn Session (Function)"
weight = 1

+++
Creates an authenticated Request's Session for a specific instance of ServiceNow.

#### **_Arguments:_**

* Instance: Unlike 'spawn_driver,' only one instance can be passed
* Login Action (optional): If True, a temporary driver opens for the purpose of logging into the relevant instance; closed once cookies are saved
* Credentials (optional): Primarily used when 'login_action' is True.

#### **_Functionality:_**

A series of Sessions test the user's regular Chrome cookies, followed by the local Driver's cookies. In either case, the Session is returned. Credentials can be passed for use by the 'login action,' passing the instance to 'Spawn Driver' with 'persist' set to False.

Only one instance may be passed due to cookies from different instances having common names. One instance is usually all that's needed, but for cases where multiple are necessary, spawning multiple Sessions is easy enough.

Credentials are not returned simply because if they are needed, a Driver function will be called anyway.

The 'login action' argument is primarily for use within a console, not a script. Spawn Session calls almost always follow Spawn Driver calls, thus 'login action' is often moot.

</hr>

## Breakdown

#### **_Global Constants & Imports_**

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
    
    PROXY = '<My Local Machine>:<Px-Proxy Port>'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
    DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
    COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
    CHROME_OPTIONS = webdriver.ChromeOptions()
    CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))

#### **_Local Constants_**

    s = requests.Session()
    s.proxies['https'] = PROXY
    domain = '{}.service-now.com'.format(inst)
    url = 'https://{}/sys_user.do?JSONv2&sysparm_record_count=1&sysparm_action=getKeys'.format(domain)

The new Session's proxy settings are set to the Px-Proxy (See Doc: 'General Context'). Local constants are set appropriately.

#### **_Regular Chrome's Cookies_**

    cookies = browser_cookie3.chrome(domain_name=domain)
    for cookie in cookies:
        s.cookies.set(cookie.name, cookie.value)
    r = s.get(url)
    if r.status_code == 200:
        return s

Cookies associated with the passed instance are extracted using the 'browser-cookie' library (See Doc: 'Connectors (Module)') from the user's Chrome Browser. A simple GET query is sent and if the response code is 200, stop and return the Session, otherwise, next step.

If no cookies are found, 'None' is returned which still results in the function proceeding to the next step.

#### **_Chromedriver's Cookies_**

    if os.path.exists(COOKIE_FILE):
        cookies = browser_cookie3.chrome(COOKIE_FILE,domain_name=domain)
        for cookie in cookies:
            s.cookies.set(cookie.name, cookie.value)
        r = s.get(url)
        if r.status_code == 200:
            return s

Cookies associated with the passed instance are extracted using the 'browser-cookie' library (See Doc: 'Connectors (Module)') from the Driver's 'Cookies' file, assuming it exists. Another query is performed and any return code other than 200 results in the function proceeding to the next step.

#### **_Instance1's Annoying Redirect_**

    if login_action:
        spawn_driver(instances=[inst],persist=False,credentials=credentials)
        return spawn_session(inst=inst,credentials=credentials)
    else:
        return None

At this point, either 'None' is returned or a 'login action' is performed, essentially calling the Spawn Driver method while passing the necessary instance and, optionally, credentials. The 'Persist' argument is set to False so that the driver is closed.