===============
Troubleshooting
===============

Permission issues when running Docker container on Taupage AMI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
If you get permission issues (e.g. ``chown: changing ownership of foobar: Operation not permitted``) when running your Docker image on Taupage,
you probably run a Docker image assuming to run as ``root``. Taupage starts Docker containers with an unprivileged user by default.
You can test your Docker image locally with ``docker run -u 998 ...``.
Usually all apps (especially JVM-based applications) should be able to run as non-root.
Sadly most Docker images from the official Docker Hub assume running as root.


If you really need to run your Docker container as ``root``, you can use the ``root: true`` Taupage config option.
See the :ref:`Taupage reference <taupage>` for details.


I cannot access my EC2 instance via SSH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you can get access to :ref:`odd` via :ref:`piu`, but accessing your private EC2 instance does not work: First check your server's security group. It must allow inbound traffic on TCP port 22 (SSH) from the "odd" bastion host.

If you get a "Permission denied (publickey)" error, check that your local SSH key agent is running:

.. code-block:: bash

    $ ssh-add -l
    # this should list your private key(s) (e.g. id_rsa)


How to read Docker logs on EC2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Docker logs containing your application's STDOUT are written to Syslog.
After getting :ref:`ssh-access` to your EC2 instance (running the Taupage AMI), you can grep them:

.. code-block:: bash

    $ grep docker /var/log/syslog


No internet connection (connection timeouts) on EC2 instance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you get connection timeouts on your EC2 instance, e.g. the Docker image download or SSH access fails (cannot download public SSH key from :ref:`even`):

* If your EC2 instance runs in a **DMZ subnet**: instances in DMZ subnets have no internet connection unless you assign a public IP.
  Usually you should start instances in internal subnets only and only use ELBs in the DMZ subnets.
* If your EC2 instance runs in an **Internal subnet**: check that your subnet routing table and NAT instance is working correctly.

Also check your instance's security group whether it allows outbound traffic.
