---
title: Other Functions
weight: 3
layout: docs

---
The remaining functions found in the module 'Inbox Common.'

<hr />

#### **_Short-hand Wait_**

    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))

**A short-hand version of my preferred element selection method using Selenium.** 

<hr />

#### **_Queue Spellcheck_**

    def queue_spellcheck(q):
        if q.lower() not in lqlist:
            cur = 0
            for i in lqlist:
                rat = fuzz.partial_ratio(q.lower(),i)
                if rat > 80 and rat > cur:
                    best = i
                    cur = rat
            return best
        else:
            return q

**Serves to spellcheck resolver group names while parsing subject line arguments in the 'Value from Email' function.**

Because resolver group names employed in this environment's ServiceNow instance are very long and easy to misspell, spellchecking had to be incorporated into early versions of these tools. 

Using a list of all the group names in lowercase, the passed group argument is compared to each one. Partial ratio match is used because most misspellings are due to lack of characters or incorrect order of characters. The queue with the highest ratio (beyond 80) is the one that is returned. 

This method of spellchecking has proven accurate in this scope because these group names range from 15 characters to more than 30. With the addition that group names use at least two different characters from one another, its close to impossible to accidentally end up with something other than the intended group name.

<hr />

#### **_Attach_**

    def attach(msg,number):
        """Get Direct Ticket URL"""
        if 'INC' in number:
            url = '{}incident.do?sysparm_query=number={}'.format(snow,number)
        else:
            url = '{}sc_task.do?sysparm_query=number={}'.format(snow,number)
        driver.get(url)
        """Remove/Save"""
        path = str(Path('email.msg').absolute())
        try:
            os.remove(path)
        except:
            pass
        msg.saveas(path)
        """Attach/Remove"""
        wait(15,0,'//*[@id="header_add_attachment"]').click()
        driver.execute_script('window.alert = null')
        wait(10,0,'//*[@id="attachFile"]').send_keys(path)
        wait(15,2,'email.msg')
        os.remove(path)

**Called immediately after a task or incident number is obtained; attaches the email object to the ticket.**

Once a ticket number is created and the fields require no further edits, the previously initialized Webdriver (bottom of 'Inbox Common' Module) navigates directly to the ticket via ServiceNow's web interface in order to attach the email. This is to ensure there is no ambiguity between the ticket and email as far as details and it also ensures the email's attachments are also uploaded to the ticket.

The web elements accessed in this function are exactly the same regardless of ticket type and ultimately, this function simulates a user attaching the '.msg' object as quickly as the GUI will allow. 