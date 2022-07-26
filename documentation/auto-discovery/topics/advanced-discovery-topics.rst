.. include:: /auto-discovery/media.rst
.. _Advanced-discovery-topics-doc:

*************************
Advanced discovery topics
*************************

====================
Obtaining SNMP walks
====================

Hyperview uses SNMP walks to enhance device definitions, to model and support devices that are discoverable with the SNMP protocol. The **snmpwalk file** is used to simulate the device and to test definitions.

It is recommended to install the applicable net-snmp package alongside the (Linux) Data Collector software. If that is not possible it can be installed on any machine that has a network line of sight to the target device.

Linux
-----

The `net-snmp <http://www.net-snmp.org/>`_ utilities are available on all supported Linux distributions.

On Debian-based distributions:

.. code::

    sudo apt install snmp

On RedHat-based distributions:

.. code::

    sudo dnf install net-snmp-utils

Windows
-------

There are multiple options.

1. Use net-snmp using a UNIX environment simulator such as `Cygwin <https://www.cygwin.com/>`_ or `WSL <https://docs.microsoft.com/en-us/windows/wsl/>`_.

2. Use a Linux Virtual Machine

macOS
-----

You can install net-snmp on macOS using `Homebrew <https://brew.sh/>`_

.. code::

    brew install net-snmp

Once the application is installed, the **snmpwalk** command can be used to obtain a full walk of a device. The amount of time it takes to perform the walk can vary from device to device and depends on external factors such as network speed and target device CPU load. It is recommended to perform the walk from the Data Collector machine, and for that machine to be on the same network as the device being walked. Expect the process to take a few minutes unless the device has a large amount of information such as a large network switch, in which case, it may take longer.

.. note:: If you are logged in to a remote machine when performing the walk via **ssh/putty** watch out for session timeouts. This would end the snmpwalk job. Standard UNIX/Linux tools such as **tmux, screen or nohup** may help in managing sessions and avoiding a timeout.

**Example:**

.. code::

    snmpwalk -v2c -c public -ObentU 192.168.10.10 . > /my_home_dir/snmpwalks/newrackpdu.snmpwalk
    #
    # Check the output file for errors and compress it
    #
    gzip /my_home_dir/snmpwalks/newrackpdu.snmpwalk

.. note:: The **-ObentU** command option is required and important to be able to parse the output with our tools.

Once the walk is obtained, inspect that it is complete. Look for a "``No more variables left in this MIB View (It is past the end of the MIB tree)``" message at the end of the file. Transfer the file to Hyperview support by uploading it to the applicable support ticket.

Utilities like scp/sftp or `winscp <https://winscp.net/>`_ can be used to transfer files around if there is a need.

If these options are not possible then contact Hyperview Support.

====================================================
Downloading the Linux Data Collector via Artifactory
====================================================

`Artifactory <https://jfrog.com/artifactory/>`_ is an artifact repository that some organizations use to centralize the management of their software supply chain. If you have a requirement to use this solution for your Linux Data Collector then you will have to customize the **docker-compose.yaml** file that ships with the Linux Data Collector installation package.

Once the **docker-compose.yaml** file is configured to your satisfaction then follow the :ref:`standard installation instructions <Setup-data-collectors>`.

Note that once you are running with a customized docker-compose file you will need to maintain that between releases. Hyperview may add new services, change default environment variables or default startup options for the various containers that compose the Linux Data Collector.

Standard Linux command line tools such as **diff** and **vim** can be used to access any differences between the two files and the applicable changes can be ported to your environment.

Please note that Hyperview only tests and supports the default configuration that ships with the installation package.

============================
Docker daemon log management
============================

By default, the Docker software does not perform log rotation for the "local/JSON" logging driver. For a standard installation of the Linux Data Collector on a standalone machine, it is easy to miss setting that up and risk potential disk space exhaustion. This is especially relevant if you are running the Data Collector software in **trace mode** while troubleshooting an issue.

`Docker documentation <https://docs.docker.com/config/containers/logging/>`_ recommends configuring log rotation for the local/JSON logging driver by editing **/etc/docker/daemon.json**. A configuration example is below.

.. code::

    {
      "log-driver": "json-file",
      "log-opts": {
        "max-size": "100m",
        "max-file": "3"
      }
    }
