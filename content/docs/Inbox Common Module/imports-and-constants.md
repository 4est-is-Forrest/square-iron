---
title: Imports & Constants
weight: 
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

**Win32com/pywin32 offers a method to interface with Outlook and therefore the environment's mail servers without need of an REST API token.**

Because this is an interface into the Outlook application, connections and authentication are already handled. Also, having the current user's email address readily available is also helpful in reliably obtaining "sys_id's" across different ServiceNow instances. Overall, having this interface available from the beginning is largely the reason I never bothered to request access to the Mail API. 

#### **_Fuzzywuzzy_**

[**Fuzzywuzzy**](https://pypi.org/project/fuzzywuzzy/0.3.0/) **is a useful library that offers a variety of string comparison methods and is primarily used in this module's spellcheck function.**

Specifically, the 'partial ratio' function is what I was ultimately looking to incorporate in the spellcheck function (explained later). I needed a way to compare strings without marking down due to a lack of characters.

Connectors is also one of the imports which has its own doc [section](https://3flqfei0stazaa.instant.forestry.io/docs/connectors/).

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

**Outlook and the folders** are pretty self explanatory. Interface with Outlook and obtain the help desk's inbox Folder object as well as the sub-folder where processed email will end up ('/root/year/month')