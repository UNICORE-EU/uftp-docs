.. _uftpd-manual:


|uftpd-user-guide-img| User Manual
==================================

.. |uftpd-user-guide-img| image:: ../../_static/user-guide.png
	:height: 32px
	:align: middle

This is the user manual providing information on running and using the UNICORE UFTPD server.

.. important:: **IMPORTANT SECURITY NOTE**

   The UNICORE UFTPD server is running with **elevated privileges**, it can set its UID and GID 
   to that of any user except *root*. 
   Make sure to read and understand the section below on `Protecting the Command socket`_. 
   Otherwise, users logged on to the UFTPD machine can possibly read and write other user's files.


|settings-img| Installation and operation
-----------------------------------------

.. |settings-img| image:: ../../_static/installer.png
	:height: 32px
	:align: middle

.. _uftpd-prerequsites:

Prerequisites
~~~~~~~~~~~~~

- Python 3.6.0 or later

- the server's *listen* port needs to be accessible through your firewalls, declaring it 
  an *FTP* port (FTP connection tracking). Alternatively a fixed range of open ports can be 
  configured and used.

- the server's command port needs to be accessible from the Auth server(s)

- the UFTPD server needs access to the target file systems

- a server certificate for the UFTPD server is a **MUST** for production use in a multi-user 
  environment (see the section on SSL below)

- the data encryption feature requires the Python "Crypto" module, which can be installed via
  ``python3 -m pip install pycryptodome``


.. attention::

 A functional UFTP installation requires also the :ref:`authserver`
 or a full :ref:`UNICORE/X server
 <unicore-docs:unicorex>` .

Installation
~~~~~~~~~~~~~

The UNICORE UFTPD server is distributed either as a platform independent and portable 
``tar.gz`` or ``zip`` bundle or as an installable, platform dependent package such as ``RPM``
avalable at `GitHub 
<https://github.com/UNICORE-EU/uftpd/releases>`__.

.. important:: 
  **IMPORTANT NOTE ON PATHS**
    
  Depending on the installation package, the paths to various files are different. 
  
  If installing using distribution-specific package 
  the following paths are used::

	CONF=/etc/unicore/uftpd
	BIN=/usr/share/unicore/uftpd/bin
	LIB=/usr/share/unicore/uftpd/lib
  
  If installing using the portable bundle, all UFTPD files are installed
  under a single directory. Path prefixes are as follows, where `INST` is the directory where 
  UFTPD was installed::
  
	CONF=INST/conf
	BIN=INST/bin
	LIB=INST/lib

  These variables (`CONF`, `BIN` and `LOG`) are used throughout the rest of this manual.

Note that after installation UFTPD is **NOT** automatically enabled as a ``systemd`` service, 
since you will need to edit the configuration and provide a server certificate.


Starting and stopping the UFTPD server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 
If using the Linux packages, uftpd is integrated as a service via systemd, and
you can stop/start it via ``systemctl``. Also, logging is (by default) done via 
systemd, and you can look at the logs via ``journalctl``.

To do things manually, you can use the start/stop and status scripts that are
provided in the BIN directory.

 - ``unicore-uftpd-start.sh`` starts the server
 - ``unicore-uftpd-stop.sh`` stops the server
 - ``unicore-uftpd-status.sh`` checks the server status

The parameters such as server host/port, control host/port, and others are
configured in the ``CONF/uftpd.conf`` file.

In a production scenario with multiple users, the uftpd server
needs to be started as *root*. This is necessary to be able to
access files as the correct user/group and set correct file permissions.


To enable UFTPD as a systemd service (after configuring and adding a server 
certificate), you can use ``systemctl``:

.. code:: console

  $ sudo systemctl add-wants multi-user.target unicore-uftpd


.. _config-parameters:

Configuration parameters
~~~~~~~~~~~~~~~~~~~~~~~~

The following variables can be defined in the configuration file (``uftpd.conf``):


:CMD_HOST: the interface where the server listens for control commands

:CMD_PORT: the port where the server listens for control commands

:SERVER_HOST: the interface where the server listens for client data connections

:SERVER_PORT: the port where the server listens for client data connections
                    
:ADVERTISE_HOST: (*optional, only used in the PASV implementation*) Advertise this server as 
 having the following IPv4 address in the control connection. This is useful if the server is 
 behind a NAT firewall and the public address is different from the address(es) the server has 
 bound to

:SSL_CONF: File containing SSL settings for the command port

:ACL: File containing the list of server DNs that are allowed access to the command port 

:MAX_CONNECTIONS: the maximum number of concurrent control connections per user (default: ``16``)

:MAX_STREAMS: the maximum number of parallel TCP streams per FTP session (default: ``4``)

:PORT_RANGE: (*optional*) server-side port range in the form \'lower:upper\' that will be used to 
 accept data connections. By default, any free ports will be used. *Example*: set to 
 \'50000:50050\' to limit the port range.

:DISABLE_IP_CHECK: (*optional*) in some situations, the client IP can be different from the one 
 that was sent to the UFTPD server by the Auth server. This will lead to rejected transfers. 
 Setting this variable to `true` will disable the IP check. Only the one-time password will be 
 checked.

:UFTP_KEYFILES: (*optional*) list of files (relative to current user's ``$HOME``) where uftpd 
 will read public keys for authentication. List is separated by ``:``. This defaults to 
 ``.ssh/authorized_keys``.

:UFTP_NO_WRITE: (*optional*) "``:``"-separated list of file name patters that uftpd should not 
 write to.

:LOG_VERBOSE: set to ``true`` to get (much) more detailed logging

:LOG_SYSLOG: set to ``false`` to print logging output to stdout

As usual if you set the SERVER_HOST to be `0.0.0.0`, the server will bind to all the available 
network interfaces.

If possible, use an *internal* interface for the Command socket. If that
is not possible, make sure the Command socket is protected by a firewall!

.. attention::
 We **VERY STRONGLY** recommend enabling SSL for the Command socket.
 Please refer to the next section.


Protecting the Command socket
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using SSL for the Command port ensures that only trusted parties
(i.e. trusted Auth and/or UNICORE/X servers) can issue commands to the 
UFTPD server. To further limit the set of trusted users, an access control
list (ACL) file is used.

In production settings where users can log in to the UFTPD server
machine, **SSL MUST** be enabled to prevent unauthorized data access!

.. important:: **IMPORTANT SECURITY NOTE**

  Without SSL enabled, users logged in to the UFTPD server can easily create 
  exploits to read or write files with arbitrary user privileges (except *root*).


SSL setup
^^^^^^^^^

To setup SSL, you need a PEM file containing the UFTPD server's
credential, and a PEM file containing certificate authorities that should be trusted.

The following properties can be set in the ``CONF/uftpd-ssl.conf`` file.
::

	credential.path=path/to/keyfile.pem
	credential.password=...
	
	truststore=path/to/ca-cert-file.pem

You can also use separate PEM files for key and certificate::

	credential.key=path/to/key.pem
	credential.password=...
	credential.certificate=path/to/certificate.pem
	
	truststore=path/to/ca-cert-file.pem

The ``credential.password`` is only needed and used if the key is encrypted.

.. note:: **Backwards (in)compatibility to previous versions**

	UFTPD 2.x SSL config is **NOT supported**.

	|:point_right:| If you already have a p12 keystore for UFTPD 2.x, you can use ``openssl`` 
	to convert it to `PEM` format.


.. _acl-setup:

ACL setup
^^^^^^^^^

The access control list contains the distinguished names of those certificates that should be 
allowed access.

The ``ACL`` setting in ``CONF/uftpd.conf`` is used to specify the location of the ACL file::

	export ACL=conf/uftpd.acl

The default ACL contains the certificate DN of the UNICORE/X server from the `UNICORE 
core server bundle <https://github.com/UNICORE-EU/server-bundle/releases/>`__. 
In production, you need to replace this by the actual DNs of 
your :ref:`UNICORE/X server(s) <unicore-docs:unicorex>` 
and :ref:`UFTP Authentication server(s) <authserver>`.

The ACL entries are expected in RFC2253 format. To get the name 
from a certificate in the correct format using ``openssl``, you can use the following OpenSSL 
command:

.. code:: console

	$ openssl x509 -in your_server.pem -noout -subject -nameopt RFC2253

The ACL file can be updated at runtime.


Firewall configuration
~~~~~~~~~~~~~~~~~~~~~~

UFTPD requires

 * an open TCP port for accepting FTP connections
 * additional open TCP ports for accepting data connections
 
The data connections can either be openend dynamically using *FTP connection tracking*, or
you can use a dedicated port range and permanently open those in the firewall.

.. note::
	Please refer to the firewall documentation on how to enable an *FTP* service on your firewall 
	(or operating system).

With Linux ``iptables``, you may use rules similar to the following:

.. code:: console

	$ iptables -A INPUT -p tcp -m tcp --dport $SERVER_PORT -j ACCEPT
	$ iptables -A INPUT -p tcp -m helper --helper ftp-$SERVER_PORT -j ACCEPT

where ``$SERVER_PORT`` is the SERVER_PORT defined in ``uftpd.conf``. The first
rule allows anyone to access port $SERVER_PORT. The second rule
activates the iptables connection tracking FTP module on port $SERVER_PORT.

On some operating systems it may be required to load additional kernel modules to enable 
connection tracking, for example on CentOS:

.. code:: console

    $ modprobe nf_conntrack_ipv4
    $ modprobe nf_conntrack_ftp ports=$SERVER_PORT

If you cannot use connection tracking, you will need to open a port range, and configure
UFTPD accordingly.

For example, in ``uftpd.conf``
::

	export PORT_RANGE=21000:21010

and the iptables rule

.. code:: console

	$ iptables -A INPUT -p tcp -m tcp --dport 21000:21010 -j ACCEPT

would allow incoming data connections on ports 21000 to 21010. 

A fairly small range (e.g. 10 ports) is usually enough, since these are server ports.


Logging
~~~~~~~

By default, UFTPD writes to syslog, and you can use ``journalctl`` to read log messages. 
To print logging output to stdout, set ``export LOG_SYSLOG=false`` in the ``uftpd.conf`` file.


.. _unicore-integration:

|integration-img| UNICORE integration
-------------------------------------

.. |integration-img| image:: ../../_static/integration.png
	:height: 32px
	:align: middle

Please refer to the :ref:`UNICORE/X manual <unicore-docs:ux_uftp>` 
for detailed information on how to configure UFTP based data access and data transfer.


|testing-img| Testing the UFTPD server
--------------------------------------

.. |testing-img| image:: ../../_static/testing.png
	:height: 32px
	:align: middle

You should use the :ref:`uftp client <uftp-client>` to run tests, which contains
many options such as the number of concurrent FTP connections, and can
use ``/dev/null`` and ``/dev/zero`` as data source/sink.

.. raw:: html

   <hr>

