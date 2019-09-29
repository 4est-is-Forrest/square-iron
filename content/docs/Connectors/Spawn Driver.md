---
title: Spawn Driver (Function)
layout: docs
weight: 3

---
Minimizes login effort and, after successful login, asserts cookies are saved locally, usually in order for a Session to import them.

<hr/>

## Code
```python
    #Spawn Authenticated Driver for one or more Snow instance
    def spawn_driver(instances=['instance1'],credentials={},persist=True):
        """
        Functions
        """
        #Shorthand WebdriverWait
        def wait(x,y,z):
            els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
            return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))

        """Resolve/Collect credentials as necessary"""
        for inst in instances:
            if inst in ['instance2','instance3']: #Username/Password Instances
                try:
                    usr = credentials['{}_usr'.format(inst)]
                    pwd = credentials['{}_pwd'.format(inst)]
                except:
                    usr = input('Username ({}): '.format(inst))
                    credentials['{}_usr'.format(inst)] = usr
                    pwd = getpass('Password ({}): '.format(inst))
                    credentials['{}_pwd'.format(inst)] = pwd

        """Test Driver Cookies per instance"""
        if os.path.exists(COOKIE_FILE):
            s = requests.Session()
            s.proxies['https'] = PROXY
            logins = []
            for inst in instances:
                domain = '{}.service-now.com'.format(inst)
                url = 'https://{}/sys_user.do?JSONv2&sysparm_record_count=1&sysparm_action=getKeys'.format(domain)
                cookies = browser_cookie3.chrome(COOKIE_FILE, domain)
                for cookie in cookies:
                    s.cookies.set(cookie.name, cookie.value)
                r = s.get(url)
                if r.status_code != 200:
                    logins.append(inst)
        else:
            logins = instances

        """Reset Cookies for instance1 if login needed"""
        if 'instance1' in logins:
            try:
                os.remove(COOKIE_FILE)
            except:
                pass

        """Execute Logons"""
        driver = webdriver.Chrome(CHROME_DRIVER,options=CHROME_OPTIONS)
        for inst in logins:
            if inst == 'instance1': #PKI Card Login
                driver.get('https://instance1.service-now.com/login_with_sso.do?glide_sso_id=<xx...xx>')
                wait(120,0,'//*[@id="filter"]')
            elif inst == 'instance3':
                driver.get('https://instance3.service-now.com/id=instance3_landing')
                wait(10,0,'//*[@id="username"]').send_keys(credentials['instance3_usr'])
                wait(10,0,'//*[@id="username"]').send_keys(Keys.ENTER)
                wait(10,0,'//*[@id="User_ID"]').send_keys(credentials['instance3_usr'])
                wait(10,0,'//*[@id="Password"]').send_keys(credentials['instance3_pwd'])
                wait(10,0,'//*[@id="Password"]').send_keys(Keys.ENTER)
                wait(20,0,'//*[@id="filter"]')
            elif inst == 'instance2':
                driver.get('https://instance2.service-now.com/')
                wait(10,0,'//*[@id="username"]').send_keys(credentials['instance2_usr'])
                wait(10,0,'//*[@id="password"]').send_keys(credentials['instance2_pwd'])
                wait(10,0,'//*[@id="password"]').send_keys(Keys.ENTER)
                wait(20,0,'//*[@id="filter"]')
            driver_cookie_persist(inst)
        #Return credentials and/or Driver
        if persist:
            return driver,credentials
        else:
            driver.quit()
            return credentials
```
## **Arguments**

- Instances: list of one or more expected ServiceNow instances; Default: The most used instance: 'instance1'
- Credentials: (optional) dictionary of credentials for instances other than 'instance1.' Default is none. Dictionary key nomenclature is simply: instance_usr/pwd
- Persist: Whether the Driver should be kept open after completed login/logins; Default is True

## **Functionality**

The function is tailored for three specific instances with differentiating login methods and landing pages. Other instances can be added with relative ease, needing only to define the login process and whether the instance requires username and password credentials.

The general process consists of testing a Driver's 'Cookies' file with temporary Sessions for each instance passed and adding each instance to a 'login' list should its test fail. Depending on the instance's login method, credentials are collected ahead of time and iteration is performed over the 'logins' list. Cookie persistence is asserted after every successful login.

## **Context**

- Instance1: PKI card login with pin (most used)
- Instance2: Username & Password
- Instance3: Username & Password

ServiceNow Instance URL = https://"instance".service-now.com/

<hr/>

## Breakdown

#### [**_Global Constants & Imports_**](/docs/connectors/spawn-driver-function/)
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

    PROXY = '<My Local Machine>:<Px-Proxy Port>'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
    DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
    COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
    CHROME_OPTIONS = webdriver.ChromeOptions()
    CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))
```
#### **_Local Function: wait =_** WebDriverWait
```python
    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))
```
This function is really just shorthand for the Selenium 'WebDriverWait' method, my preferred method in selecting HTML elements. It looks neater and is convenient to use in the console for testing/debugging.

#### **_Credential Handling_**
```python
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
```
**This block determines whether credentials have been supplied and, if not, collects them appropriately.**

Instances requiring a username and password to login are defined in this loop's list. Additional instances can simply be added.

Along with making sure credentials are available/collected, it does that before Chromedriver is opened, ensuring the credential prompt is not covered by the Driver window.

Collecting credentials is important for certain instances. Credentials can be returned and utilized in a script to sustain login for these instances over long periods.

#### **_Test Login State_**
```python
    if os.path.exists(COOKIE_FILE): # If the driver Cookie file exists (indicating a first run or not), import the cookies and perform a SNOW Get
        s = requests.Session()
        s.proxies['https'] = PROXY
        logins = []
        for inst in instances:
            domain = '{}.service-now.com'.format(inst)
            url = 'https://{}/sys_user.do?JSONv2&sysparm_record_count=1&sysparm_action=getKeys'.format(domain)
            cookies = browser_cookie3.chrome(COOKIE_FILE, domain)
            for cookie in cookies:
                s.cookies.set(cookie.name, cookie.value)
            r = s.get(url)
            if r.status_code != 200:
                logins.append(inst)
    else:
        logins = instances
```
**Tests any existing cookies for each instance and marks those that require login.**

If the file 'Cookies' exists, a Session object imports cookies associated with the current instance (no cookies may be present) and performs a quick JSON query. Any return status code other than 200 adds the instance to the 'logins' list. If the 'Cookies' file does not exist, naturally, all instances are added to 'logins' list.

Aside from saving time with logging in, knowing login states helps in defining exactly what the Driver should expect and what redirects to anticipate.

#### **_Instance1's 'Successful Logout' Redirect_**
```python
       if 'instance1' in logins:
        try:
            os.remove(COOKIE_FILE)
        except:
            pass
```
**Driver cookies are erased if logging into 'Instance1' is necessary.**

Instance1 will sometimes redirect browsers with expired cookies to a 'successfully logged out page' regardless of URL being accessed. Erasing cookies is the simplest way around this.

In this environment, 'Instance1' is the most commonly used and has cookies with the longest lifetime. Erasing all cookies is logical because if this instance's cookies are expired, the same is likely true for the others.

#### **_Execute logins; return Driver and/or Credentials_**
```python
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
```
**Iterate 'logins' list; authenticate and verify cookie persistence for each.**

Each instance has unique login portals. Because 'Instance1' requires a PKI card login and its cookies last quite a while, it's easiest for the user and driver to handle the authentication process, hence the 120 second timeout.

Adding instances is just a matter of defining more 'elif' blocks for each and accounting for the login method.

#### **_Return Credential and/or Driver_**
```python
    if persist:
    	return driver,credentials
    else:
    	driver.quit()
        return credentials
```
**Always returns credentials, passed 'persist' argument determines if Driver with be returned or closed.**

Returning credentials is useful for when sustained login over long periods of time is necessary and credentials are not defined ahead of time. 'Instance1' is the exception to this, however.

In most cases, after the Spawn Driver function has finished, Spawn Session is called immediately afterward.
<hr />