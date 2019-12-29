.. _stress_field_guide:

Tempest Field Guide to Stress Tests
===================================

OpenStack is a distributed, asynchronous system that is prone to race condition
bugs. These bugs will not be easily found during
functional testing but will be encountered by users in large deployments in a
way that is hard to debug. The stress test tries to cause these bugs to happen
in a more controlled environment.

Stress tests are designed to stress an OpenStack environment by running a high
workload against it and seeing what breaks. The stress test framework runs
several test jobs in parallel and can run any existing test in Tempest as a
stress job.

Environment
-----------
This particular framework assumes your working Nova cluster understands Nova
API 2.0. The stress tests can read the logs from the cluster. To enable this
you have to provide the hostname to call 'nova-manage' and
the private key and user name for ssh to the cluster in the
[stress] section of tempest.conf. You also need to provide the
location of the log files:

    - target_logfiles = "regexp to all log files to be checked for errors"
    - target_private_key_path = "private ssh key for controller and log file nodes"
    - target_ssh_user = "username for controller and log file nodes"
    - target_controller = "hostname or ip of controller node (for nova-manage)
    - log_check_interval = "time between checking logs for errors (default 60s)"

To activate logging on your console please make sure that you activate `use_stderr`
in tempest.conf or use the default `logging.conf.sample` file.

Running default stress test set
-------------------------------

Installation
------------
#. You first need to clone this repo.::

    $ git clone https://github.com/openstack/tempest-stress
    $ cd  tempest_stress

#. Install::

    $ python setup.py install

#. Install `Tempest`_

.. _Tempest: http://docs.openstack.org/developer/tempest/overview.html#quickstart


Configuration
*************

To Run stress tests, two configuration files are needed:

1. tempest.conf - As per Tempest configuration guidelines here: `configuration`_

2. stress_tests.conf - same location as tempest.conf

later one is being used to define all config options specific to stress tests.

.. _configuration: http://docs.openstack.org/developer/tempest/configuration.html#tempest-configuration


Run
***

Run all tests::

    $ run-tempest-stress -a -d 30

Run specific test::

    $ run-tempest-stress -t ./tempest_stress/etc/server-create-destroy-test.json -d 30
This sample test tries to create a few VMs and kill a few VMs.

For more information please refer run-tempest-stress CLI help::

    $ run-tempest-stress -h

Additional Tools
----------------

Sometimes the tests don't finish, or there are failures. In these
cases, you may want to clean out the nova cluster. We have provided
some scripts to do this in the ``tools`` subdirectory.
You can use the following script to destroy any keypairs,
floating ips, and servers:

tempest_stress/tools/cleanup.py
