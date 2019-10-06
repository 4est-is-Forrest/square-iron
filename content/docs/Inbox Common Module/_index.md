---
title: Inbox Common
weight: 
layout: docs

---
This document section will breakdown the module 'Inbox Common' much like the breakdown of the 'Connectors' module.

<hr />

## Summary

This module ultimately contains every function the scripts 'Tasks' and 'Incidents' commonly use in order to accomplish their work. The most important mechanisms in this module would be:

* **Values from email** **function** that parses email subject lines for the required and optional ticket arguments; returning a dictionary
* **Attach** **function** which simply attaches the email file to the newly created ServiceNow ticket
* **Reply** function that replies to all with the ticket number while also removing previously configured 'do not reply' addresses
* **Queue Spellcheck** function simply spellchecks resolver group designations parsed from email subject lines during 'Values from email' call.

<hr />