.. _uftp-howto-test:


|user-guide| How-To Install UFTP for Testing
********************************************

.. |user-guide| image:: _static/user-guide.png
   :height: 32px
   :align: middle


|overview-img| Overview
-----------------------

.. |overview-img| image:: _static/overview.png
   :height: 32px
   :align: middle

This guide explains how to set up a complete UFTP test environment
consisting of a UFTPD server, an Auth server, and a UFTP client using
the provided test certificates. All components can be installed on a
single machine, making the setup suitable for evaluation and functional
testing.

.. warning::
   This setup is intended **for testing only**. The included certificates
   are not suitable for production use. Production deployments must use
   certificates issued by a trusted Certificate Authority (CA).


|checklist-img| Prerequisites
-----------------------------

.. |checklist-img| image:: _static/checklist.png
   :height: 32px
   :align: middle

- Java 17 or later (OpenJDK recommended)

- Python 3.9 or later

- The UFTPD server *listening* port must be reachable through your firewall.
  If stateful firewall inspection is enabled, configure the port for FTP
  connection tracking. Alternatively, configure and open a fixed range of
  data ports.

- The UFTPD command port must be accessible from the Auth server.

- For encrypted data transfers, the Python *Crypto* module is required.
  It can be installed using:

  .. code:: console

     python3 -m pip install pycryptodome


|config-img| Installation and Configuration
-------------------------------------------

.. |config-img| image:: _static/configuration.png
   :height: 32px
   :align: middle

To set up a complete test environment, install the following components:

1. Install and run a UFTPD server as described in
   :ref:`UFTPD Server Installation <uftpd-test-installation>`.

2. Install and run an Auth server as described in
   :ref:`Auth Server Installation <authserver-test-installation>`.

3. Install the UFTP client as described in
   :ref:`UFTP Client Installation <uftpc-installation>`.

All components can be installed on the same machine for testing purposes.


Authentication and File Transfer Flow
-------------------------------------

In this setup, the UFTP client authenticates using only a username and
password. No client certificate is required.


.. figure:: _static/test-uftp-setup.png
   :alt: UFTP Test Installation Authentication
   :width: 700px
   :align: center


The authentication and file transfer process works as follows:

1. The client sends an authentication request containing its username and
   password to the Auth server. The Auth server validates the credentials
   using ``user-authfile.txt`` and maps the authenticated user to a local
   account using ``user-mapfile.json``.

2. If authentication is successful, the Auth server sends a request to the
   UFTPD command port. This request configures the upcoming file transfer and
   includes the following information:

   - a generated one-time password 

   - the local user ID and group ID 

   - the client's IP address

   The UFTPD server stores this information and accepts an incoming client
   connection only if the supplied one-time password matches the expected
   value. The Auth server then replies with ``OK`` and returns the generated
   one-time password to the client.

3. The UFTP client connects to the UFTPD server using the standard FTP
   protocol. It authenticates with the one-time password received from the 
   Auth server. Once authentication succeeds, the client 
   can open data connections, list files, transfer data, and perform other 
   FTP operations.




Testing the Installation
------------------------

To verify that the installation was successful, run the functional and
performance tests described in :ref:`uftpd_test`.

These tests use the UFTP client to connect to the Auth server and the
UFTPD server and verify authentication, file transfers, and performance.


Troubleshooting
---------------


Authentication failures
~~~~~~~~~~~~~~~~~~~~~~~

Check:

* username/password
* ``conf/user-authfile.txt`` und ``conf/user-mapfile``
* Auth Server logs


ACL errors
~~~~~~~~~~

Check:

* certificate DNs in ``conf/uftpd.acl``


Certificate trust problems
~~~~~~~~~~~~~~~~~~~~~~~~~~

Verify:

* ``conf/cacert.pem``
* certificate validity
* certificate subjects
* matching CA certificates


Permission denied errors
~~~~~~~~~~~~~~~~~~~~~~~~

Verify:

* ``conf/uftpd.conf``
* directory permissions
* configured ``USER_NAME``
* Unix user exists

.. raw:: html

   <hr>