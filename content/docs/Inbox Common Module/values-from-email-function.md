---
title: Values From Email (Function)
weight: 3
layout: docs

---
This function is used to parse and validate arguments inserted into an email's subject line using simple regular expressions. See this section's [overview](/docs/email-toolset/) for details regarding these arguments.

<hr />

## Function Code

```python
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
```

## Breakdown

#### **_User & User Email Address_**

```python
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
```

**Determine the email's subject user based on the sender's email address (default). Try to get that user's "sys_id" from ServiceNow via JSON query.**

Generally, the sender's email address is enough. For instances where the message was forwarded, '&&' can be used to set a specific address as the source.

A JSON query is sent in order to obtain a ServiceNow 'sys_id' associated with that email address in order to add the user to ticket fields.

#### **_Resolver Group & Short Description_**

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

**Parse the assignment/resolver group and spellcheck the string; use JSON to get 'sys_id' of the group. Determine 'short description' field.**

Depending on the spellcheck results, the email is either marked as 'Invalid' and skipped, or the resolver group's 'sys_id' is obtained from ServiceNow.

By default, the short description will be the emails subject line minus the inserted arguments. For instances where the subject line is too vague, '$$' allows for a specific string to be used instead.

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

**Obtain the client name and generic user name associated with that client. Get their "sys_id's" via JSON.**

In this context, this particular customer has multiple clients under them and so tickets must be created under those specific clients (for billing purposes). Using specific users is not possible when using these client codes (this is why the 'watchlist 'field is necessary) so a generic user associated with every client code must fill the required user field. Conveniently, for the vast number of these client codes, the generic user's name always follows the listed format above.

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

**Print values for the human running the script, mark the email in progress and return the dictionary.**

Finally, the ticket's 'description' field is always made to look like a forward version of the email. The email is marked to indicate it is being processed and the emails information is printed for the script user monitoring its progress. Lastly, a dictionary is returned which will be added to and eventually used to created a ticket via JSON or a Selenium Webdriver.