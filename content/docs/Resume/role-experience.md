+++
excerpt = "Details on technologies and languages I've practiced."
layout = "docs"
title = "Technology & Language Experience"
weight = 4

+++
## Overview

#### Technologies

* Web Services & RESTful APIs Interaction
* Google Cloud Platform
* ServiceNow
* IAM & LDAP Frameworks
* Natural Language Processing
* Flask & Jinja2
* Selenium (Python)
* Windows COM API (Python)
* Pandas (Python)

#### Languages

* Python 3
* HTML, CSS & JS
* JQuery
* SQL, NoSQL, MySQL, SQLite
* Powershell Scripting
* Bash Scripting
* Ruby
* Google’s GCloud Console Tool Set

<hr>

## Technology Details

#### _Web Services and REST API's_

I use ServiceNow's JSON Web Service API regularly in my code. As explained in my 'Code Samples' section, I utilize JSON as a work around for lack of sufficient permissions necessary to utilize ServiceNow's REST API. That said, the JSON Web Service is, in my opinion, the next best thing. I use Python's Requests to send POSTs and attach JSON objects in cases where I want insert or update records. It is also my go to method to query 'sys_ids' and other object attributes associated with said object via sending GETs.

I have taken advantage of a few different API's Google Cloud has to offer, RESTful or otherwise. See the 'Google Cloud Platform' portion of this document.

#### _Google Cloud Platform_

Much of my experience encompasses general navigation of both the console and shell as well as more specific features like Google App Engine, Google API's, and Google Datastore.

Aside from the web application I made to familiarize myself with a variety of elements, another project I tackled with in the Google Cloud environment was a simple Python program that converted and submitted audio to Google's Speech-to-Text API and return the text output to file.

#### _ServiceNow_

In my role at Atos, I work with a variety of ticketing systems but almost all of them are various instances of the ServiceNow. My experience includes GUI navigation, ServiceNow's Client JavaScript, encoded URL queries and ServiceNow's web services (JSON, CSV, Excel).

In my efforts to automate as well as work around road blocks, I understand a great deal about the inner workings of ServiceNow from an administrator's stand point including ACLs and server-side scripting.

#### _IAM & LDAP Frameworks_

The 'Systems Administration' course went in depth with LDAP and many of the help desk tools provided to us were built on Active Directory or some IAM platform such as NetIQ. Regarding IAM, much of my practice and interaction with them derive from querying their data and sometimes administering specific attributes associated with end user accounts. Having never been on the administrative side, I haven't had the opportunity to use the various available Python libraries to interact with these systems outside of LDAP.

#### _Natural Language Processing_

Early into 2019, I began experimenting with the new Python library, Flair. It provided a convenient set of tools to build, train, test and improve NLP models.

Recently, I've started utilizing Flair with better practices in mind than when I started, particularly sanitizing data and using binary labels. My hope is to create a combination of reliable models to coincide with a few compilations of past data statistics in order to make reliable and multiple conclusions about any given email a help desk would receive.

While my current tools I've written handle 90% of the labor (email to ticket conversion process itself) they still rely on humans to either check the argument predictions or fill in the arguments themselves. My overall goal is reduce (perhaps eliminate) that remaining 10% as much as I can while creating an automatic process to build this custom system for any help desk across our organization.

#### _Flask & Jinja2_

Naturally, experience with Jinja2 was a product of my utilization of Flask. Building a web application on Google's App Engine forced me to learn a great deal about both. Learning Jinja2 was very beneficial as it revealed a new and convenient way to produce reports in various formats rather than solely Excel or CSV.

#### _Selenium_

I understand that Selenium is often utilized for web QA Testing but I've drawn a great deal of use out of it with automation. Many of the tools and scripts I write are able to accomplish most, if not, all of their objectives using SeviceNow's JSON web service. However, some objectives are beyond my permissions so I use Selenium as viable work around.

When I first started automating processes, I started with exclusively using this library to interact with ServiceNow, which of course was nothing short of crude, unstable and needlessly complex given my inexperience at the time. But making them gave me a great deal of practice with both Selenium and was one of the first libraries I devoted my time to learning after acquiring fundamental knowledge of Python.

I have since converted my code to use primarily use JSON and Selenium as a work around when needed.

#### _Windows COM API_

This is my go-to Python library to interact with Outlook and was another one of my first libraries I learned to utilize. With Pywin32, I can access an existing instance of Outlook and utilize it's objects. A viable and functional work around lack of an API key when it comes to Office 365 and ensures anything I write can be run independently on various user systems without need of a password.

I go into more detail in my code samples as I use it very often. There you can see specifics about how useful the library has ultimately proven to be with Python.

#### _Pandas_

Perhaps one of my favorite and most used libraries due to its great number of capabilities, convenience when working with data and, in my case, it's versatility in completing a wide range of varying objectives in my code.

Pandas was instrumental in my data analysis efforts and was also key in creating corpuses to be fed into Flair. I also rely on it to help maintain data continuity (with the help of Xlsxwriter) should an exception be raised during execution of my code. The convenience of being able to read and write CSV's quickly  or the ability to create data frames directly from a request reply's JSON, are just a few reasons why I use it so often in my code.

Some, if not, many of my code samples will provide some context on how I've used it.

## Language Details

#### _Python 3_

Python is the primary language I utilize by far. I've been immersed in it consistently for more than three years at the time of this writing. I've used it to interact with other languages, applications, databases, operating systems and web services. In depth Python coding examples can be found in the 'Coding' section of my site.

I do have a lot of experience with Python 2 due to NKU's favoritism of Linux distributions, but I'm not sure if that counts for much given it's date of deprecation. 

#### _HTML, CSS, JS & JQuery_

The foundations of these markup and scripting languages for me was the course in 'Web Development' I completed. However, given the nature of my automation, I constantly use browser developer tools in all manner of ways from element targeting to activating page JavaScript functions. Additionally, I received a great amount of practical experience with these languages when developing my Google App Engine web application. During that project, I also learned the fundamentals of JQuery and used it to make the application one dynamic page from the user's perspective.

#### _SQL, NoSQL, MySQL, SQLite_

My experience with SQL and MySQL is from college courses 'Database Administration' and 'Web Server Administration' respectively. With regards to MySQL, my simple LAMP web server project produced the majority of my experience with MySQL and Python's capability to interact with it.

My NoSQL experience stems from my experience with Google Datastore for my web application which, as I understand, is built on NoSQL methods and principles.

SQLite, aside from occasionally using it in the work environment, was my tool of choice to practice with SQL syntax and familiarize myself with the language.

#### _PowerShell Scripting_

I received most of my PowerShell experience in the 'Windows Administration' course I completed as well as through my own curiosity. While I've been exposed to it quite a bit, I often use Python instead to accomplish any scripting task needed on my local machine or I'll create executable for other machines that don't have Python installed. 

I use it and Windows Command Prompt interchangeably for console navigation and some other basic objectives.

#### _Bash Scripting_

Another product of NKU's overwhelmingly Linux-based learning environment. The 'Unix' course touched on this topic a bit but much of the practical aspect happened with regard to the five CentOS VM's within my 'Systems Administration' course. We were often granted our choice of scripting language to complete the elements of each project and Bash scripting was my team's preference.

#### _Ruby_

Entirely academic experience, specifically the courses 'Scripting I' and 'II.' By the time I started the second course, I had been using Python over three years. Given how similar the two languages are in several object-based aspects, adapting my knowledge of Python and utilizing Ruby instead was not very difficult. Ruby has grown on me but I still favor Python primarily because of how long I've been exclusively used it.

#### _Google's GCloud Console Tool_

A command line utility built-in into Google's web shells (and optionally locally installed). I concluded that much of Google Cloud's GUI functions could easily be executed in the shell just by using GCloud. While I haven't had experience yet with other cloud platforms, I speculate they operate under similar principles as this, though I won't know for sure until I experiment with them.