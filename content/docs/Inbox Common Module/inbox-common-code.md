---
title: Full Code
weight: 1
layout: docs

---
The module's full code with light annotations.

<hr />

    outlook= win32com.client.Dispatch('Outlook.Application')
    root_folder = outlook.GetNamespace('MAPI').Folders['help.desk@inbox.com'].Folders['Inbox']
    completed_folder = root_folder.Folders[str(datetime.today().year)].Folders[datetime.today().month - 1]
    snow = 'https://instance1.service-now.com/'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    REPLY_TEMPLATE = RESOURCES + '/template.msg'
    do_not_reply = ['< long list of addresses to remove from replies >']
    do_not_reply = [s.upper() for s in do_not_reply]
    """
    Functions
    """
    #Short-hand wait function
    def wait(x,y,z):
        els = [By.XPATH, By.CSS_SELECTOR, By.PARTIAL_LINK_TEXT]
        return WebDriverWait(driver, x).until(EC.presence_of_element_located((els[y], z)))
    
    #Queue Spellchecker
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
        
    def values_from_email(m):
        
        #Email Address
        try:
            email = re.findall(r'&&\s*(.+)\s*&&',m.subject)[0]
        except:
            email= m.senderemailaddress
        if '@' not in email:
            email= m.sender.GetExchangeUser().PrimarySmtpAddress
        if email.upper() in do_not_reply:
            m.subject= 'Helpdesk Sender - ' + m.subject
            m.save()
            return None
        r = s.get('{}sys_user.do?JSONv2&sysparm_action=getKeys&sysparm_query=email={}'.format(snow,email))
        try:
            user = r.json()['records'][0]
        except:
            user = ''
            pass
        
        #Assignment Group
        try:
            queue_name = re.findall('\{\{\s*(.+)\s*\}\}',m.subject)[0]
            queue_name = queue_spellcheck(queue_name)
            r = s.get('{}sys_user_group.do?JSONv2&sysparm_action=getKeys&sysparm_query=name={}'.format(snow,queue_name))
            queue = r.json()['records'][0]
        except:
            m.subject= 'Invalid Assignment Group - ' + m.subject
            m.save()
            return None
    
        #Short Description
        try:
            short = re.findall('\$\$\s*(.+)\s*\$\$',m.subject)[0]
        except:
            short = re.sub('[\$\$\{\{%%&&].+','',m.subject)
    
        #Client
        try:
            client_name = re.findall('%%\s*(.+)\s*%%', m.subject)[0]
            r = s.get('{}core_company.do?JSONv2&sysparm_action=getKeys&sysparm_query=name={}'.format(snow,client_name))
            client = r.json()['records'][0]
        except:
            client_name = 'Conduent'
            r = s.get('{}core_company.do?JSONv2&sysparm_action=getKeys&sysparm_query=name={}'.format(snow,client_name))
            client = r.json()['records'][0]
    
        #Generic User
        if client_name.lower() == 'client':
            sys_user = 'Generic Client'
        else:
            sys_user = 'Generic User {}'.format(client_name)
        r = s.get('{}sys_user.do?JSONv2&sysparm_action=getKeys&sysparm_query=name={}'.format(snow,sys_user))
        sys_user = r.json()['records'][0]
        
        #Values Obtained
        ticketBody= 'From: ' + email + '\nSent: ' + datetime.strftime(m.receivedtime, '%Y-%m-%d %H:%M:%S') + '\nTo: ' + m.to + '\nCC: ' + m.cc + '\nSubject: ' + m.subject + '\n\n' +  m.body
        print('\n{}\n\n{}\n\n{}\n\n{}'.format(m.subject,email,m.receivedtime,queue_name.upper()))
        print('=' * 50)
        """Mark/Return"""
        m.subject = 'Working...'+ m.subject
        m.save()
        return {
            'email':email,
            'watchlist':user,
            'company':client,
            'caller_id':sys_user,
            'assignment_group':queue,
            'short_description':short,
            'description':ticketBody,
            'time':datetime.strftime(m.receivedtime, '%Y-%m-%d %H:%M:%S')
            }
    
    #Attach .msg file to Snow ticket
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
    
    #Reply to user
    def reply(email,msg,number):
    
        """Clean/Set reply recipients"""
        reply_to = []
        for r in msg.recipients:
            if '@' not in r.address:
                reply_to.append(r.addressentry.GetExchangeUser().primarysmtpaddress)
            else:
                reply_to.append(r.address)
        rm = do_not_reply + [email.upper()]
        reply_to = [addr for addr in reply_to if addr.upper() not in rm]
    
        """Construct Reply Email"""
        temp= outlook.CreateItemFromTemplate(REPLY_TEMPLATE)
        temp.htmlbody= temp.htmlbody.replace('ticketNumber',number)
        reply = msg.reply
        reply.subject = re.sub('Working...','',reply.subject)
        reply.subject = number + ' - ' + re.sub('[\$\$\{\{%%].+','',reply.subject)
        msg.subject = reply.subject
        reply.to = email
        reply.cc = '; '.join(reply_to)
        reply.htmlbody = temp.htmlbody + reply.htmlbody
    
        """Send/Save/Move"""
        reply.send
        msg.save()
        msg.move(completed_folder)
    """
    Debug Utilities
    """
    def reset(msg):
        msg.subject = re.sub('Working\.\.\.','',msg.subject)
        msg.subject = re.sub('No Assignment Group - ','',msg.subject)
        msg.unread = True
        msg.save()
    """
    Inits
    """
    driver,creds = spawn_driver()
    s = spawn_session()
    lqlist = pickle.load(open(RESOURCES + '/lqlist.p','rb'))
    