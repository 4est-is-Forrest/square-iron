---
template: docs
title: FUCK
weight: 4

---
This document aims to provide succinct clarity around this toolset's elements summarized in the section's parent [document](/docs/inbox-automation/).

## Modules

**_Connectors:_** Connectors is specific to the help desk environment I have previously mentioned. It simplifies creating authenticated sessions and Webdrivers with previously defined instances of ServiceNow. I devoted a whole [section](/docs/connectors/) to it because nearly every script deployed in this environment is dependent on it.

**_Inbox Common:_** This module is exclusive to this 'Inbox Automation' toolset, primarily the 'Incidents' and 'Tasks' scripts. It has a few important functions and objects shared between the two scripts and will be broken down in one of the following documents but the essentials are:

* Spawns a Driver and Session
* Interfaces with Outlook
* Contains a 'resolver group' spellchecker
* Function to get arguments from emails' subject lines
* Function to attach .msg files to ServiceNow records
* Reply to end user's with their respective ticket number

See this module's doc for an in depth rundown.