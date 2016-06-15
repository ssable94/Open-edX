Basic Configuration
-------------------

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

Installing Theme (Stanford theme)
---------------------------------

1. Modify /edx/app/edx_ansible/server-vars.yml to contain following variables set to given values.

.. code-block:: javascript
    :linenos:
    
    edxapp_use_custom_theme: true
    edxapp_theme_name: 'stanford'
    edxapp_theme_source_repo: 'git://github.com/Stanford-Online/edx-theme.git'
    edxapp_theme_version: 'HEAD'

2. Now run the provisioning script

.. code-block:: shell
    :linenos:
    
    $ sudo /edx/bin/update edx-platform named-release/dogwood.3

References
----------

1. Standford theme installation: http://www.dangtrinh.com/2014/03/edx-platform-using-standford-them-for.html

Fixing 'view course in studio' and 'view live' buttons
------------------------------------------------------

'View course in studio' button will take you from learning platform to CMS, so that you can modify the course.
'View live' button will take you from CMS to LMS platform.

Thus, these two buttons are used to navigate between LMS and CMS platform for a course.

Hence, LMS needs to know the DNS for CMS, to configure it, in file /edx/app/edxapp/lms.env.json change the value of "CMS_BASE" to DNS of CMS. Similarly, for CMS to know DNS of LMS, in file /edx/app/edxapp/cms.env.json change the value of "LMS_BASE" to DNS of LMS.

Deleting courses
----------------

To delete the course you need to know id of the course. use the first command to list ids of available courses and next two commands to delete the course.

.. code-block:: shell
    :linenos:
    
    sudo -u www-data /edx/bin/python.edxapp /edx/bin/manage.edxapp lms dump_course_ids --settings aws
    sudo -u edxapp /edx/bin/python.edxapp ./manage.py cms --settings=aws delete_orphans <course id> --commit
    sudo -u edxapp /edx/bin/python.edxapp ./manage.py cms --settings=aws delete_course <course id>

