+++
layout = "docs"
title = "Module Code"
weight = 1

+++
Complete module code with light annotations.

<hr />

```python
from selenium import webdriver
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
"""
Constants
"""
PROXY = '<My Local Machine>:<Px-Proxy Port>'
RESOURCES = os.environ['homepath'] + '/__resources__'
CHROME_DRIVER = RESOURCES + '/chromedriver.exe'
DRIVER_DATA = os.environ['homepath'] + '/Driver Data'
COOKIE_FILE = DRIVER_DATA + '/Default/Cookies'
CHROME_OPTIONS = webdriver.ChromeOptions()
CHROME_OPTIONS.add_argument("user-data-dir={}".format(DRIVER_DATA))
"""
Functions
"""
#Spawn Authenticated Driver for one or more Snow instances
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

#Spawn authenticated Session for any defined SNOW instance
def spawn_session(inst='instance1',login_action=False,credentials={}):
	s = requests.Session()
	s.proxies['https'] = PROXY
	domain = '{}.service-now.com'.format(inst)
	url = 'https://{}/sys_user.do?JSONv2&sysparm_record_count=1&sysparm_action=getKeys'.format(domain)

	"""Chrome(browser) Cookies Test/Return"""
	cookies = browser_cookie3.chrome(domain_name=domain)
	for cookie in cookies:
		s.cookies.set(cookie.name, cookie.value)
	r = s.get(url)
	if r.status_code == 200:
		return s
	
	"""Driver Cookies Test/Return"""
	if os.path.exists(COOKIE_FILE):
		cookies = browser_cookie3.chrome(COOKIE_FILE,domain_name=domain)
		for cookie in cookies:
			s.cookies.set(cookie.name, cookie.value)
		r = s.get(url)
		if r.status_code == 200:
			return s
		
	"""Optional Login Action"""
	if login_action:
		spawn_driver(instances=[inst],persist=False,credentials=credentials)
		return spawn_session(inst=inst,credentials=credentials)
	else:
		return None

#Ensure Driver cookies are locally saved
def driver_cookie_persist(inst='instance1'):
	"""
	Constants
	"""
	s = requests.Session()
	s.proxies['https'] = PROXY
	domain = '{}.service-now.com'.format(inst)
	url = 'https://{}/sys_user.do?JSONv2&sysparm_record_count=1&sysparm_action=getKeys'.format(domain)    
	
	not_saved = True
	while not_saved:
		cookies = browser_cookie3.chrome(COOKIE_FILE,domain_name=domain)
		for cookie in cookies:
			s.cookies.set(cookie.name, cookie.value)
		r = s.get(url)
		if r.status_code == 200:
			not_saved = False
			print('Cookie persistence verified.')
		else:
			print('Waiting for driver cookies to persist, please wait...')
			time.sleep(5)
```