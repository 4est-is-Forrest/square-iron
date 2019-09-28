+++
layout = "docs"
title = "Spawn Session (Function)"
weight = 1

+++
_spawn_session(inst='instance1',login_action=False,credentials={})_

## Overview

Tries to import good cookies from the Driver, failing that, the user's Chrome browser. Optionally, a Driver launches temporarily to perform a login and closes once good cookies are available for the Session to import.

#### **_Arguments:_**

* Instance: Unlike 'spawn_driver,' only one instance can be passed
* Login Action (optional): If True, a temporary driver opens for the purpose of logging into the relevant instance; closed once cookies are saved
* Credentials (optional): Primarily used when 'login_action' is True.

#### **_Functionality:_**

This function uses a series of Sessions to first test the user's regular Chrome cookies, then the Driver's cookies, and returns an authenticated Session with necessary proxy configuration as soon as good cookies are found. Credentials can be set to make the 'login_action' completely automatic. That is, Spawn Driver is called with the persist argument set to False.

Only one instance may be passed due to cookies from different instances having common names. Typically, one ServiceNow instance is all that's being used, but for cases where there's two or more instances, spawning multiple Sessions is easy enough.

Unlike the Spawn Driver function, credentials are not returned simply because if they are needed, a Driver function will be called anyways should capturing credentials be needed.

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

If the file 'Cookies' exists, the Session imports cookies associated with the provided instance (can be empty) and performs a lightweight JSON query. Any return code other than 200 adds the instance to the 'logins' list. If the 'Cookies' file does not exist, naturally, all instances are added to 'logins' list.

The Session query offers a quick way to test the state of cookies.

Aside from saving time with logging in, knowing login states ahead of time helps in defining what the Driver should expect and what redirects to anticipate.

#### **_Instance1's Annoying Redirect_**

       if 'instance1' in logins:
        try:
            os.remove(COOKIE_FILE)
        except:
            pass

The 'Cookies' file is erased if 'instance1' login is required. This is due to this particular instance's tendency to sometimes redirect those with expired cookies to a 'successful logged out page' regardless of URL being accessed (including the login URL).

Only erasing the domain's cookies does the redirect cease. Since 'instance1' is the most used and has cookies with the longest lifetime, it's safe to assume all other instance cookies have also expired.

#### **_Execute logins; return Driver and/or Credentials_**

    driver = webdriver.Chrome(CHROME_DRIVER,options=CHROME_OPTIONS)
    for inst in logins:
        if inst == 'instance1':
            driver.get('https://instance1.service-now.com/login_with_sso.do?glide_sso_id=<xxxxx>')
            wait(120,0,'//*[@id="filter"]')
        elif inst == 'instance2':
            driver.get('https://instance2.service-now.com/itserviceportal/?id=<xxxxx>')
            wait(10,0,'//*[@id="username"]').send_keys(credentials['instance2_usr'])
            wait(10,0,'//*[@id="username"]').send_keys(Keys.ENTER)
            wait(10,0,'//*[@id="User_ID"]').send_keys(credentials['instance2_usr'])
            wait(10,0,'//*[@id="Password"]').send_keys(credentials['instance2_pwd'])
            wait(10,0,'//*[@id="Password"]').send_keys(Keys.ENTER)
            wait(20,0,'//*[@id="filter"]')
        elif inst == 'instance3':
            driver.get('https://instance3.service-now.com/')
            wait(10,0,'//*[@id="username"]').send_keys('instance3_usr')
            wait(10,0,'//*[@id="password"]').send_keys('instance3_pwd')
            wait(10,0,'//*[@id="password"]').send_keys(Keys.ENTER)
            wait(20,0,'//*[@id="filter"]')
    	        driver_cookie_persist(inst)
        if persist:
            return driver,credentials
        else:
            driver.quit()
            return credentials

Each instance has unique login portals. Because 'instance1' requires a PKI card login and its cookies last quite a while, it's simpler for the user and driver to handle the authentication process themselves, hence the 120 second timeout.

#### **_Finally_**

By default, the driver remains open (and is returned) but can be closed for when a Session simply needs good cookies. Regardless of whether it closes or not, no action is taken until the cookies have been saved to file.

Again, returning credentials is useful for when sustained login over long periods of time is necessary and credentials are not defined ahead of time. 'Instance1' is the exception to this, however.