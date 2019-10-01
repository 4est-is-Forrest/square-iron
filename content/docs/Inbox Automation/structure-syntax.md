+++
template = "docs"
title = "Structure & Syntax"
weight = 1

+++
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

##  Scripts

With exception to the 'Client Portal' script (doc to be added at a later date), 'Tasks' and 'Incidents' are fundamentally the same but do have key process differences. To summarize contrasting these two:

* **Incidents** are for broken elements and are created with JSON, the Driver only comes into play to attach emails to their respective ticket records
* **Tasks** are request-like elements, first created with a Driver via a ServiceNow Catalog Request, JSON is then used to properly set fields for subsequent child tickets, ending by calling the Driver again to attach emails

See the scripts' respective documents for greater detail.

## Syntax

'Tasks' and 'Incidents' are dependent on a syntax in order to fill fields with information that often require human interpretation, such as a resolver group or a client code to create the ticket under. The arguments are added to each email's subject line encased in their respective symbol:

* %%<Client Code>%% (required)
* \{\{<Assignment Queue>\}\} (required)
* \$$<Ticket Short Description>$$ (optional)
* &&<Override Email Sender>&& (optional)

So long as the first two are provided on each email, the scripts have everything they need to accurately create any number of email tickets in rapid succession.

#### **_Example Case_**

If a user sends an email with the following subject line:
_Help Desk - I'm unable to log into my email, pls halp_

An agent would add, at the very least, the following and save the email:
_Help Desk - I'm unable to log into my email, pls halp %%Client1%% \{\{Email Group's Ticket Queue\}\}_

#### **_The Arguments_**

**( % ) Client codes** determine what customer to create the ticket under and therefore who to charge. 

**( \{ ) Assignment Queue** is the queue the ticket should be sent for completion or resolution.

**( $ ) Ticket subject** represents the ticket's 'short description' field. When this argument is excluded, the email's subject line (minus the formatted arguments) is passed as the ticket's short description. Of course, sometimes email subject lines are not very helpful or descriptive, hence why this argument exists.

**( & ) Source Override** is for overriding what user is attached to the ticket's watch list (a notification service) and what user will receive the ticket number reply. By default, the scripts utilize the sender's email address which must be overridden on automatically generated and forwarded email cases.