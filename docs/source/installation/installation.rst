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
