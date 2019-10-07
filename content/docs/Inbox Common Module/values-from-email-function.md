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
**Determine the email's subject user based on the sender's email address. Try to get that user's "sys_id" from ServiceNow via JSON query.**

Generally, the sender's email address is accurate enough to go off of. However, there are plenty of instances where the email was forwarded one or more times from it's original source. This is why the '&&' is an option to override the source address to be used in the ticket.

Internal senders can be consistently identified by a lack of '@' in the sender string and thus extra steps must be taken to obtain the actual SMTP address string.

Finally, a JSON query is attempted in order to obtain a ServiceNow 'sys_id'_ associated with that email address. This is for the 'watchlist' field in ServiceNow where user's are notified about their ticket's activity. If a 'sys_id' cannot be found, this field is ignored as it is not required.

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

**Parse the assignment/resolver group and spellcheck the string; use JSON to get 'sys_id.' Set 'short description' field.**

Resolver group in this instance of ServiceNow have a very long names and thus are very easily misspelled, hence why a spellchecker is necessary. The function will be described in greater detail in the following section. Depending on the spellcheck, the email is marked with an 'Invalid' string and skipped, or the resolver group's 'sys_id' is obtained from ServiceNow via JSON.

By default, the short description will be the emails subject line minus all of the arguments meant for this function. For instances where the subject line is too vague, using $$ allows for a specific string to be used instead.

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