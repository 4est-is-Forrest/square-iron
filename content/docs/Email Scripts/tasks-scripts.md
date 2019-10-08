---
title: Tasks Script
weight: 2
layout: docs

---

<hr />

## Code
```python
"""
User Input
"""
workFolder = input("Enter folder you would like to work (Default=tasks): ") or 'tasks'
"""
Imports
"""
from inbox_common import *
"""
Run
"""
emails = root_folder.Folders['Automated'].Folders[workFolder].Items
while len(emails) > 0:
    """Check/Sort"""
    emails = root_folder.Folders['Automated'].Folders[workFolder].Items
    emails.sort("ReceivedTime")
    """Process Each"""
    for msg in emails:
        if msg.Unread == True:
            msg.Unread = False
            values = values_from_email(msg)
            if values is None:
                print('Skipped (see message subject):\n{}'.format(msg.subject))
                continue
            """Submit Request"""
            driver.get('https://atosnam.service-now.com/nav_to.do?uri=%2Fcom.glideapp.servicecatalog_cat_item_view.do%3Fv%3D1%26sysparm_id%3D225583303710efc08d0d1f9543990e6f%26sysparm_link_parent%3Dc167ce3137c45b408d0d1f9543990e6e%26sysparm_catalog%3D1f7442fd37845b408d0d1f9543990eaf%26sysparm_catalog_view%3Dcatalog_conduent')
            driver.switch_to.frame(WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, 'gsft_main'))))
            wait(10,0,'//*[@id="IO:63da1a84371093808d0d1f9543990eb7"]') #Wait for bottom to load
            """Fill in Request Fields"""
            #Wait for g_form to load
            success = False
            while not success:
                try:
                    driver.execute_script("g_form.setValue('IO:8389f27b374453808d0d1f9543990e82',{})".format(json.dumps(values['company'])))
                    success = True
                except:
                    print('g_form not loaded yet. Trying again...')
                    time.sleep(.5) 
            driver.execute_script("g_form.setValue('IO:beab7abb374453808d0d1f9543990efc',{})".format(json.dumps(values['caller_id'])))
            wait(10,0,'//*[@id="IO:e73b03743710efc08d0d1f9543990edb"]/option[14]').click()
            driver.execute_script("g_form.setValue('IO:db4743b03710efc08d0d1f9543990eaf',{})".format(json.dumps(values['description'])))
            wait(10,0,'//*[@id="IO:fe390b343710efc08d0d1f9543990e32"]/option[2]').click()
            driver.execute_script("g_form.setValue('IO:bc8cf27b374453808d0d1f9543990e2a','n/a')")
            time.sleep(.5)
            wait(5,0,'//*[@id="oi_order_now_button"]').click()
            """Update RITM Fields"""
            ritm = wait(15,0,'//*[@id="sc_cart_view"]/table/tbody/tr[1]/td[3]/a').text
            data = {
                'short_description':values['short_description'],
                'description':values['description'],
                'u_customer_po':values['time'],
                'contact_type':'email'
                }
            r = s.post('{}sc_req_item.do?JSONv2&sysparm_action=update&sysparm_query=number={}'.format(snow,ritm),json=data)
            parent = r.json()['records'][0]['sys_id']
            task = None
            """Wait/Obtain Task sys_id"""
            while task is None:
                try:
                    r = s.get('{}sc_task.do?JSONv2&sysparm_action=getKeys&sysparm_query=parent={}'.format(snow,parent))
                    task = r.json()['records'][0]
                except:
                    time.sleep(.5)
                    continue
            """Update Task Fields"""
            data = {
                'short_description':values['short_description'],
                'description':values['description'],
                'assignment_group':values['assignment_group']
                }
            r = s.post('{}sc_task.do?JSONv2&sysparm_action=update&sysparm_query=sys_id={}'.format(snow,task),json=data)
            number = r.json()['records'][0]['number']
            """Use ChromeDriver to attach email to ticket"""
            attach(msg,number)
            """Reply/Move to completed"""
            reply(values['email'],msg,number)
            """Check/Sort"""
            emails = root_folder.Folders['Automated'].Folders[workFolder].Items
            emails.sort("ReceivedTime")
driver.quit()
```