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

The function is tailored for three specific instances with differentiating login methods and landing pages. Other instances can be added with relative ease, needing only to define the login process and whether the instance requires username and password credentials.

* Instance1: PKI card login with pin (most used)
* Instance2: Username & Password
* Instance3: Username & Password

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

#### **_Local Function: wait =_** WebDriverWait

    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))

This function is just a short hand for the 'WebDriverWait' method, my preferred method in selecting HTML elements whenever Selenium is utilized. It looks neater and is convenient to use in the console.

#### **_Credential Handling_**

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

Instances requiring a username and password to login are defined in this loop's list. Additional instances can simply be added if desired. This block determines whether credentials have been supplied and, if not, collects them appropriately.

Along with making sure credentials are available/collected, it does that before Chromedriver is opened, ensuring the credential prompt is not covered by the Driver window.

A big reason for collecting the credentials in the first place is to return them for scripts and tools meant to run for extended periods of time. This way, should the session expire, logging in again can be automated.

#### **_Test Login State_**

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

If the file 'Cookies' exists, a Session object imports cookies associated with the provided instance (can be empty) and performs a lightweight JSON query. Any return code other than 200 adds the instance to the 'logins' list. If the 'Cookies' file does not exist, naturally, all instances are added to 'logins' list.

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