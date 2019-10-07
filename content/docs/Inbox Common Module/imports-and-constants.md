---
title: Imports & Constants
weight: 2
layout: docs

---
This document offers details into the constants and imports within the 'Inbox Common' module.

<hr />

## Imports

    import win32com.client
    from datetime import datetime
    import re
    from fuzzywuzzy import fuzz
    from connectors import *
    from pathlib import Path

#### **_Win32com.client_**

**Win32com/pywin32 offers a method to interface with Outlook and therefore the environment's mail servers without need of a REST API token.**

Because this is an interface into the Outlook application, connections and authentication are already handled. Also, having the current user's email address readily available is also helpful in reliably obtaining "sys_id's" across different ServiceNow instances. 

#### **_Fuzzywuzzy_**

[**Fuzzywuzzy**](https://pypi.org/project/fuzzywuzzy/0.3.0/) **is a useful library that offers a variety of string comparison methods and is primarily used in this module's spellcheck function.**

Specifically, the 'partial ratio' function is what I was ultimately looking to incorporate in the spellcheck function (explained later). I needed a way to compare strings without marking down due to a lack of characters.

#### **_Connnectors_**

A crucial module documented in this doc [section](https://3flqfei0stazaa.instant.forestry.io/docs/connectors/).

#### **_RE (Regex)_**

Parsing email subject lines for specific arguments is based entirely on simple regular expressions. The function 'Values from Email' will illustrate this in the following section.

<hr />

## Constants

    outlook= win32com.client.Dispatch('Outlook.Application')
    root_folder = outlook.GetNamespace('MAPI').Folders['help.desk@inbox.com'].Folders['Inbox']
    completed_folder = root_folder.Folders[str(datetime.today().year)].Folders[datetime.today().month - 1]
    snow = 'https://instance1.service-now.com/'
    RESOURCES = os.environ['homepath'] + '/__resources__'
    REPLY_TEMPLATE = RESOURCES + '/template.msg'
    do_not_reply = ['< long list of inboxes to remove from replies >']
    do_not_reply = [s.upper() for s in do_not_reply]
    
    lqlist = pickle.load(open(RESOURCES + '/lqlist.p','rb'))
    driver,creds = spawn_driver()
    s = spawn_session()

**Outlook and the folders** are pretty self-explanatory. Interface with Outlook and obtain the help desk's inbox folder object as well as the sub-folder object where processed emails are moved ('/root/year/month')

**The reply template and 'lqlist' are** sourced from the 'resources' folder, an 'assets' directory created and maintained by a separate 'setup' script run periodically on users' machines.

**Do not reply** is a list of email addresses that must always be removed from the reply's recipients when replying to a user. Made up mostly of other help desk shared inboxes and addresses associated with automated services. This list is only pertinent within the context of the Email Toolset and is thus maintained manually (and constantly added to). It is converted to upper for reliable string comparison.

**The 'lqlist' list** is used by the spellcheck function. It is a list of all queues associated with the subject account (250+) in lowercase format.

**Note:** Connectors function calls are actually located at the bottom but displayed here as constants. The full code document reflects this.