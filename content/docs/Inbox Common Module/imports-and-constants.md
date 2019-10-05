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

Some advantages to this method:

* It doesn't depend on anything other than an open instance of Outlook
* User specific information can be obtained from the current Outlook session
* I've found otherwise complicated tasks, such as managing email attachments