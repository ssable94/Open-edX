Amazon snapshot bug
===================

While working on AWS EC2 it was observed that if you create instance using snapshot, it causes rabbitmqctl to malfunction and celery workers cannot connect to amqp.

If you check log file /edx/var/log/cms/edx.log, you will see following error

.. code-block:: shell
    
    error: [Errno 104] Connection reset by peer 

if you check error log for workers (available in /edx/var/log/supervisor/) you will find similar

.. code-block:: shell

    [2015-11-03 14:07:56,018: ERROR/MainProcess] consumer: Cannot connect to amqp://celery:**@127.0.0.1:5672//: [Errno 111] Connection refused.
    Trying again in 4.00 seconds...

    [2015-11-03 14:08:00,036: ERROR/MainProcess] consumer: Cannot connect to amqp://celery:**@127.0.0.1:5672//: [Errno 111] Connection refused.
    Trying again in 6.00 seconds...

It means that all celery workers are unable to connect to amqp broker.

We need to have celery user is created. We can check celery user is defined in Open edX config \*.auth.json. Below is default value.

.. code-block:: shell

    "CELERY_BROKER_PASSWORD": "celery",
    "CELERY_BROKER_USER": "celery",

Solve the problem using following steps

.. code-block:: shell
    :linenos:
    
    # List rabbitnq users
    $ sudo rabbitmqctl list_users
    Listing users ...
    guest    [administrator]
    # create user
    $ sudo rabbitmqctl add_user celery celery
    Creating user "celery" ...
    # set permissions for celery user
    $ sudo rabbitmqctl set_permissions celery ".*" ".*" ".*"
    Setting permissions for user "celery" in vhost "/" ...
    # restart rabbitmq-server
    $ sudo service rabbitmq-server restart
     * Restarting message broker rabbitmq-server     [OK]
    # we need to restart the apps
    $ sudo /edx/bin/supervisorctl restart all


reference
`````````
1. `blogs.infinitesquares.net <http://blogs.infinitesquares.net/open-edx-issue-cannot-open-course-in-cms/>`__
