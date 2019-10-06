---
layout: docs
weight: 
title: Inbox Automation

---
In the following section, I will elaborate on the scripts and tools I created to automate the vast majority of the labor in processing emails from a high volume help desk inbox (\~300 emails/day) into ServiceNow tickets.

<hr />

## Summary

These tools are built on two modules of my design and four scripts that perform the work, two of which will be excluded from the docs for now:

* [Connectors Module](/docs/connectors/): Establishes Session and Webdriver connections with ServiceNow instances
* [Inbox Module](/docs/inbox-common-module/): Primary use is to parse arguments from emails and return JSON ready Python dictionary
* Tasks Script: Creates ServiceNow Task tickets based on email arguments
* Incidents Script: Creates ServiceNow Incident tickets based on email arguments

These scripts have a small and simple syntax that must be used within emails' subject lines so as to pass important arguments into the ticket's creation.

<hr />

## Modules

**_Connectors:_** Connectors is specific to the help desk environment I have previously mentioned. It simplifies creating authenticated Sessions and Webdrivers with previously defined instances of ServiceNow. I devoted a whole [section](/docs/connectors/) to it because nearly every script deployed in this environment is dependent on it.

**_Inbox Common:_** This module is for exclusive use by the 'scripts 'Incidents' and 'Tasks.' It houses a few important functions and objects shared between the two scripts and will be broken down in one of the [following documents](/docs/inbox-common-module/) but the essentials are:

* Spawns a Driver and Session
* Interfaces with Outlook
* Contains a 'resolver group' spellchecker
* Gets arguments from emails' subject lines
* Attaches .msg files to ServiceNow records
* Replies to end users with their respective ticket numbers

## Scripts

With exception to the 'Client Portal' script (doc to be added at a later date), 'Tasks' and 'Incidents' are fundamentally the same but do have key process differences. To summarize contrasting these two:

* **Incidents** are for broken elements and are created with JSON, the Driver only comes into play to attach emails to their respective ticket records
* **Tasks** are request-like elements, first created with a Driver via a ServiceNow Catalog Request, JSON is then used to properly set fields for subsequent child tickets, ending by calling the Driver again to attach emails

See the scripts' respective documents for greater detail.

## Syntax

'Tasks' and 'Incidents' are dependent on a syntax in order to fill ticket fields with varying but specific values that often require human interpretation, such as a resolver group or a client code to create the ticket under. These arguments are added to each email's subject line encased in their respective symbols so as to be parsed by the 'Inbox Common' module's 'Values from email' function using basic regular expressions:

* %% Client Code %% (required)
* {{ Assignment Queue }} (required)
* $$ Ticket Short Description $$ (optional)
* && Override Email Sender && (optional)

So long as the first two are provided on each email, the scripts have everything they need to accurately create any number of email tickets in rapid succession.

#### **_Example Case_**

If a user sends an email with the following subject line:
_Help Desk - I'm unable to log into my email, pls halp_

An agent would add, at the very least, the following and save the email:
_Help Desk - I'm unable to log into my email, pls halp %%Client1%% {{Email Group's Ticket Queue}}_

#### **_The Arguments_**

**( % ) Client codes** determine what customer to create the ticket under and therefore who to charge.

**( { ) Assignment Queue** is the queue the ticket should be sent for completion or resolution.

**( $ ) Ticket subject** represents the ticket's 'short description' field. When this argument is excluded, the email's subject line (minus the formatted arguments) is passed as the ticket's short description. Of course, sometimes email subject lines are not very helpful or descriptive, hence why this argument exists.

**( & ) Source Override** is for overriding what user is attached to the ticket's watch list (a notification service) and what user will receive the ticket number reply. By default, the scripts utilize the sender's email address which must be overridden on automatically generated and forwarded email cases.