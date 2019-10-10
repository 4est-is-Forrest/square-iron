---
title: Incidents Script
weight: 1
layout: docs

---
<hr />

## Code
```python
"""
User Input
"""
workFolder= input("Enter folder you would like to work (Default=incidents): ") or 'incidents'
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
            data = {
                'watchlist':values['watchlist'],
                'company':values['company'],
                'caller_id':values['caller_id'],
                'assignment_group':values['assignment_group'],
                'short_description':values['short_description'],
                'description':values['description'],
                'impact':'3',
                'urgency':'3',
                'contact_type':'email',
                'category':'Workplace',
                'subcategory':'Generic',
                'u_category3':'Other',
                'u_occured':values['time'],
                'u_owning_group':"d78ef88fdb5383409e75abc5ca961970",
                'location':"ca019514db34d744d3a4a026ca9619bf"
                }
            """Insert"""
            r = s.post('{}incident.do?JSONv2&sysparm_action=insert'.format(snow),json=data)
            number = r.json()['records'][0]['number']
            print(number)
            """Use ChromeDriver to attach email to ticket"""
            attach(msg,number)
            """Reply/Move to completed"""
            reply(values['email'],msg,number)
            """Check/Sort"""
            emails = root_folder.Folders['Automated'].Folders[workFolder].Items
            emails.sort("ReceivedTime")
driver.quit()
```

## Details

Because the modules of my design handle much of the work, the script is easy to parse without going into great detail. The script on its own must handle selecting and iterrating the selected folder and differs from 'Tasks' in that the ticket can be created from JSON, calling the Webdriver only to attach the email file to the record.