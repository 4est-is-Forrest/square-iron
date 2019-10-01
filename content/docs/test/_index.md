---
layout: docs
weight: 
title: Inbox Automation

---
In this section, I will elaborate on the toolset I created to automate the vast majority of labor associated with parsing and processing emails from a high volume help desk inbox (\~300 emails/day).

<hr />

## Summary

This toolset/system is built on two personal modules and three scripts that perform the work:

* [Connectors Module](/docs/connectors/): Establishes Session and Webdriver connections with ServiceNow instances
* Inbox Module: Primary use is to parse arguments from emails and return JSON ready Python dictionary
* Tasks Script: Creates ServiceNow Task tickets based on email arguments
* Incidents Script: Creates ServiceNow Incident tickets based on email arguments
* Client Portal Script: Creates ServiceNow tickets within a client's instance without JSON capability

This toolset has a small and simple syntax that must be used within emails' subject lines so as to pass important arguments into the ticket's creation.

What use to require additional hires and a dedicated team to manage, is now leisurely worked by one individual with a processing rate higher than the email intake.

<hr />