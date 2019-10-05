---
title: Inbox Common
weight: 
layout: docs

---
This document subsection will breakdown the module 'Inbox Common' much like the breakdown of the 'Connectors' module.

<hr />

## Summary

This module ultimately contains every function the scripts 'Tasks' and 'Incidents' commonly use in order accomplish their similar tasks. Probably the most important mechanism they use from this module would be:

* **Values from email** function parses email subject lines for required and optional arguments and returns a dictionary containing that information.
* **Attach** function which simply attaches the current email file to the newly created ServiceNow ticket.
* **Reply** function that replies to all with the ticket number while also removing previously configured 'do not reply' addresses
* **Queue Spellcheck** function simply spellchecks resolver group designations parsed from email subject lines during 'Values from email' call.

<hr />