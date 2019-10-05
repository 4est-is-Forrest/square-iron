---
title: Values From Email (Function)
weight: 
layout: docs

---
This function is used to parse and validate arguments inserted into an email's subject line. See this section's [overview](/docs/email-toolset/) for details regarding these arguments.

<hr />

## Code

    def values_from_email(m):
        
        #Email Address
        try:
            email = re.findall(r'&&\s*(.+)\s*&&',m.subject)[0]
        except:
            email= m.senderemailaddress
        if '@' not in email:
            email= m.sender.GetExchangeUser().PrimarySmtpAddress
        if email.upper() in do_not_reply:
            m.subject= 'Helpdesk/Safe Sender - ' + m.subject
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

## Breakdown

        #Email Address
        try:
            email = re.findall(r'&&\s*(.+)\s*&&',m.subject)[0]
        except:
            email= m.senderemailaddress
        if '@' not in email:
            email= m.sender.GetExchangeUser().PrimarySmtpAddress
        if email.upper() in do_not_reply:
            m.subject= 'Helpdesk/Safe Sender - ' + m.subject
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