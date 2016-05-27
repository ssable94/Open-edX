.. Open edX (Dogwood on AWS) documentation master file, created by
   sphinx-quickstart on Mon May 23 23:13:23 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

========================================================
Open edX (Dogwood Relase) installation and configuration
========================================================

-------------------------------------
Implementation on Amazon Web Services
-------------------------------------

:Author: Sachin Sable
:Contact: ssable@umail.iu.edu
:organization: Data Science Program
:date: 24 May 2016
:status: This is a "work in progress"
:copyright: This document has been placed in the public domain. You may copy, modify, redistribute, reattribute, improve it, quote it at length, excerpt, incorporate.
:Dedication:
    For developers who want to install Open edX platform.
:abstract:

    This document is a demonstration of the how to install and configure open edX platform (dogwood release) on amazon web services.

.. meta::
   :keywords: open edX, dogwood, edx, AWS, amazon web servies
   :description lang=en: A demonstration of the open edX installation on amazon web services.





Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

About Open edx
==============
edX
---
EdX is a nonprofit online initiative created by founding partners Harvard and MIT and composed of dozens of leading global institutions, the xConsortium. EdX offers interactive online courses and MOOCs from the world’s best universities and institutions.

Open edX
--------
The Open edX platform is a free--and open source--course management system (CMS) that was originally developed by edX. The Open edX platform is used all over the world to host Massive Open Online Courses (MOOCs) as well as smaller classes and training modules.

`Visit Open edX website <https://open.edx.org>`__

Installation
============

Virtual Machine Instance
------------------------

Launch virtual machine instance in Amazon EC2

Follow steps

1. Click on 'Launch Instance'
2. Chose 'Community AMIs'
3. Search 'ubuntu 12.04 precise server'
4. Select the AMI
5. Configure the machine according to following attributes in the table


+-------------------+-------------------------------+
|Attribute          |Value                          |
+===================+===============================+
|OS                 |Ubuntu 12.04 precise server    |
+-------------------+-------------------------------+
|Architecure        |64 bit                         |
+-------------------+-------------------------------+
|Root device type   |EBS                            |
+-------------------+-------------------------------+
|Architecure        |64 bit                         |
+-------------------+-------------------------------+
|Virtualization type|paravirtual                    |
+-------------------+-------------------------------+
|Instance Type      |Compute optimized (c3.large)   |
+-------------------+-------------------------------+
|Instance Details   |Use default settings           |
+-------------------+-------------------------------+
|Storage            |General Purpose SSD (GP2)      |
+-------------------+-------------------------------+
|Size               |50 GB                          |
+-------------------+-------------------------------+


.. note::
    1. Do not forget to create security group allowing  traffic from required ports such as SSH on port 22, LMS and CMS traffic on port 80 and 18010.
    2. These are the suggested attributes, you can change the attributes except the operating system - 'ubuntu 12.04'

Prerequisite
------------

Update Ubuntu package sources

.. code-block:: shell

    sudo apt-get update -y
    sudo apt-get upgrade -y
    sudo reboot

Automated installation
----------------------

Follow following steps for automated installation

.. code-block:: shell
    :linenos:

    # Set environment variable to open edX release
    export OPENEDX_RELEASE=named-release/dogwood.3
    wget https://raw.githubusercontent.com/edx/configuration/master/util/install/ansible-bootstrap.sh -O - | sudo bash
    # Activate virtual environment
    source /edx/app/edx_ansible/venvs/edx_ansible/bin/activate
    wget https://raw.githubusercontent.com/edx/configuration/$OPENEDX_RELEASE/util/install/sandbox.sh -O - | bash

Basic information and Configuration
-----------------------------------

You should have running open edX system after performing automated installation. Next step is to configure open edX platform.

Domain Name
```````````

IP address of the instance running open edX can be mapped with domain name. It is good idea to have domain name as it will be easy to use. Also, it will be useful while integrating with other services, as many will require to configure DNS settings, such as DKIM signature.

Accessing open edX
``````````````````

lms is accessed using port 80, and cms is accessed using port 18010.

+-----------+-----------------------+
|LMS        |domain_name:80         |
+-----------+-----------------------+
|LMS admin  |domain_name:80/admin   |
+-----------+-----------------------+
|CMS        |domain_name:18010      |
+-----------+-----------------------+
|CMS admin  |domain_name:18010/admin|
+-----------+-----------------------+


Manage Users
````````````

Default users and passwords:

+-----------+-----------+
|Username   |Password   |
+===========+===========+
|honor      |edx        |
+-----------+-----------+
|audit      |edx        |
+-----------+-----------+
|verified   |edx        |
+-----------+-----------+
|staff      |edx        |
+-----------+-----------+

Initially no user has superuser access, thus we need to go through command line and give users superuser access.

We can change the user permissions by manually modifiying 'auth_user' table inside 'edxapp' database. It has two important parameters. First is 'is_staff' indicates if user can access admin console or not. Second is 'is_superuser' it indicates if user can modify the setting using admin console.

Let's give superuser access to user - staff.

.. code-block:: shell
    :linenos:
    
    $ sudo -u www-data /edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py lms --settings aws dbshell
    mysql> use edxapp;
    mysql> update auth_user set is_superuser=1 where username="staff";


for security purposes

.. note::
    To improve security, after giving superuser permissions to 'staff' user, login into admin console and change default password of 'staff'.

Integrating Amazon SES
----------------------

Before integrating SES we need to install postfix and configure it to send emails. Postfix should be configured properly and it should be able to send emails. After this step we need to verify email addresses from which emails will be sent. Emails can be varified from Amazon web service -> SES -> Email Addresses (Identity Management).

Resources for installing postfix and configuring Amazon SES
    1. `Postfix installation guide from ubuntu community <https://help.ubuntu.com/community/Postfix>`__
    2. `Integrating Amazon SES with Postfix <https://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html>`__


Configuring verification link
-----------------------------

When user





























