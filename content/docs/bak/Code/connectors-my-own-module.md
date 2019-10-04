+++
img_path = ""
layout = "page"
subtitle = ""
title = "Connectors (My Own Module)"

+++

## Overview

I'm listing this module first as it is fundamental to many of my tools and convenient to use at any given time (in my environment). It think it also demonstrates what common and uncommon libraries I can use with one another to solve some general code labor.

## Problem

Most of my tools require some sort of connection with one or multiple instances of ServiceNow. Sometimes a browser connection and sometimes a Request Session is sufficient. I needed something I could easily call in any given script to establish one or both of those types of connections for any number of ServiceNow instances.

## Road-Blocks

At one time, Requests was not a very practical library I could utilize in my environment until I discovered Px-Proxy. It allowed me to send requests over the proxy by authenticating the traffic with the local current user's inherent login.

While that Request specific issue was solved, I learned that pure Request connections could not be made unless I was provided a specific username and password. My organization was extremely reluctant to hand these out to anyone beyond ServiceNow administrators, but I theorized I could attach the cookies from a Selenium browser or my own Chrome browser to the request and be allowed access, specifically to the JSON Web Service.
