---
title: Imports & Constants
weight: 2
layout: docs

---
This document offers details into the constants and imports within the 'Inbox Common' module.

<hr />

## Imports

```python
import win32com.client
from datetime import datetime
import re
from fuzzywuzzy import fuzz
from connectors import *
from pathlib import Path
```

**Win32com/pywin32** offers a method to interface with Outlook and therefore the environment's mail servers without need of a REST API token.

[**Fuzzywuzzy**](https://pypi.org/project/fuzzywuzzy/0.3.0/) is a useful library that offers a variety of string comparison methods and is used in this module's spellcheck function.

[Connectors](/docs/connectors/) is a crucial module I created for this environment.

**Regex** is used to parse the arguments inserted into an email's subject line. See [Syntax](http:///docs/email-toolset/#syntax) for details on these arguments.

<hr />

## Constants

```python
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
```

**Outlook and the folders** are pretty self-explanatory. Interface with Outlook and obtain the help desk's inbox folder object as well as the sub-folder object where processed emails are moved ('/root/year/month')

**The reply template and 'lqlist'** are sourced from the 'resources' folder, an 'assets' directory created and maintained by a separate 'setup' script run periodically on users' machines.

**Do not reply** is a list of email addresses that must always be removed from the reply's recipients list when replying to a user. Primarily, it's made up of other help desks and addresses associated with automated services. This list is only pertinent within the context of the Email Toolset and is thus maintained manually (and constantly added to) only within this module.

**The 'lqlist' list** is used by the spellcheck function. It is a list of all queues associated with the subject account (250+) in lowercase format.

<hr />