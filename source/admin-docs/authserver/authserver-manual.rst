.. _authserver-manual:

|auth-guide-img| User Manual
============================

.. |auth-guide-img| image:: ../../_static/user-guide.png
	:height: 32px
	:align: middle

This manual focuses on the configuration items specific to the Auth server. If you need more 
in-depth information on general configuration issues, please refer to the :ref:`UNICORE/X manual 
<unicore-docs:unicorex>`.


|settings-img| Installation
---------------------------

.. |settings-img| image:: ../../_static/installer.png
	:height: 32px
	:align: middle

.. _auth-prerequsites:

Prerequisites
~~~~~~~~~~~~~

The Auth server should be run as a non-root user (e.g. *unicore*). It requires

 * Java 11
 * an installed :ref:`uftpd`

The Auth server needs an X.509 certificate and truststore
for communicating with the :ref:`uftpd`.

Users must be able to access the Auth server's https port. It is
possible to deploy the Auth server behind a :ref:`UNICORE Gateway
<unicore-docs:gateway>` 
(please see :ref:`auth-behind-gateway` below).


Installation
~~~~~~~~~~~~

The UFTP Auth service is distributed either 
as a platform independent and portable ``tar.gz`` or ``zip`` bundle available at
`GitHub <https://github.com/UNICORE-EU/uftp/releases>`__.
It comes with all required scripts and config files to be run as a standalone application. 
To install, unzip the downloaded package into a directory of your choice.

.. note::
 You can run the service in an existing :ref:`UNICORE/X server 
 <unicore-docs:unicorex>`.  Please see
 :ref:`auth-uxdeploy` below for details.


Basic server configuration (memory etc)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``startup.properties`` configuration file contains basic settings
such as the Java command, JVM memory etc. Please review it.

The Auth server host and port are configured in the ``container.properties``
configuration file::

	container.host=uftp.yoursite.com
	container.port=9000

	# if running behind a UNICORE Gateway or a NAT router, 
	# set the baseurl
	container.sitename=AUTH
	container.baseurl=gateway.yoursite.com:2222/AUTH/services

Also in the ``container.properties`` configuration file, the server's X.509
private key and the truststore settings need to be configured.


Starting and stopping the service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use the shell scripts in the ``bin`` folder to start or stop the service.


|configuration-img| Configuration
---------------------------------

.. |configuration-img| image:: ../../_static/configuration.png
	:height: 32px
	:align: middle

The following items need to be configured in the Auth 
server's ``container.properties`` file:

 * :ref:`UFTPD server(s) <auth-uftpd>` to be accessed

 * :ref:`User authentication <auth-user>`: configure the Auth server to authenticate
   users using :ref:`username/password <auth-user-pass>`, :ref:`ssh key <ssh-key-auth>` 
   or via :ref:`Unity <auth-unity>`
   
 * :ref:`Attribute sources <attr-sources>` (XUUDB, map file, ...) for assigning 
   local attributes like UNIX user name to authenticated 
   users


Features
~~~~~~~~

This service provides two features

 * AuthServer
 * DataSharing

both are enabled by default. To disable data sharing, set
::

	container.feature.DataSharing.enable=false

There are no further configuration options for these features.


.. _auth-uftpd:

UFTPD server(s) configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For each :ref:`UFTPD server <uftpd>` that should be accessed, you'll need
to configure the relevant properties in the Auth service's config file.

The ``authservice.servers`` property is a list of server names. These
should be meaningful, since users will need to use them, too.  The
other properties are used to configure the UFTPD command address and
the UFTPD listen address. Please refer to the :ref:`UFTPD manual 
<config-parameters>` for more information about these ports.

 :description: human-readable description of the UFTPD server

 :host: the IP address of the UFTPD *listen* socket

 :port: the port of the UFTPD *listen* socket

 :commandHost: the IP address of the UFTPD *command* socket
 
 :commandPort: the port of the UFTPD *command* socket

 :ssl: whether SSL is used to connect to the command socket. This MUST be set to its default 
  of ``true`` in a production environment!

 :reservations.enable: whether to enable the :ref:`reservations <auth-reservations>` feature 

 :reservations.file: JSON file containing reservations definitions

.. note::
	The listen socket address will be communicated to clients, who will
	attempt to connect to that address. Therefore, this has to be a public
	interface. For example, if you are running UFTPD behind a NAT router,
	you have to use the IP configured as the ``ADVERTISE_HOST`` in the UFTPD configuration.

For example, we want to configure two UFTPD servers named *CLUSTER* and *TEST*::

	# configured UFTPD server(s)
	authservice.servers=CLUSTER TEST
	
	# configuration for 'CLUSTER' server
	authservice.server.CLUSTER.description=Production UFTPD server
	authservice.server.CLUSTER.host=cluster.your.org
	authservice.server.CLUSTER.port=64433
	authservice.server.CLUSTER.commandHost=cluster-	internal.your.org
	authservice.server.CLUSTER.commandPort=64434
	authservice.server.CLUSTER.ssl=true
	  
	# configuration for 'TEST' server
	authservice.server.TEST.description=Test UFTPD server
	authservice.server.TEST.host=localhost
	authservice.server.TEST.port=64433
	authservice.server.TEST.commandHost=localhost
	authservice.server.TEST.commandPort=64434
	authservice.server.TEST.ssl=false

To allow the Auth server access to the command port of UFTPD, you
need to add an entry to UFTPD's ACL file. This is explained in the :ref:`UFTPD manual 
<acl-setup>`.


Round-robin use / grouping of UFTPD servers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can configure multiple UFTPD servers to form a *logical*
UFTPD server.  The idea is that multiple UFTPD servers are used in a round robin fashion to 
provide better performance.

Also, this mode of operation will provide fail-over if one of the
UFTPD servers is down for maintenance or upgrades (or because of some error).

In this case the configuration for the logical server has multiple blocks numbered *1*, *2*, 
...

Each block configures one physical server. For example::

	# configuration for multiple UFTPD instances
	# providing the logical 'CLUSTER' server
	
	authservice.servers=CLUSTER
	
	authservice.server.CLUSTER.description=Production UFTPD server on CLUSTER
	
	authservice.server.CLUSTER.1.host=cluster1.your.org
	authservice.server.CLUSTER.1.port=64433
	authservice.server.CLUSTER.1.commandHost=cluster-internal-1.your.org
	authservice.server.CLUSTER.1.commandPort=64434
	authservice.server.CLUSTER.1.ssl=true
	
	
	authservice.server.CLUSTER.2.host=cluster2.your.org
	authservice.server.CLUSTER.2.port=64433
	authservice.server.CLUSTER.2.commandHost=cluster-internal-2.your.org
	authservice.server.CLUSTER.2.commandPort=64434
	authservice.server.CLUSTER.2.ssl=true


.. _auth-user:

User authentication
~~~~~~~~~~~~~~~~~~~

The Auth service is a RESTful UNICORE service, and as such all the
configuration details for a UNICORE/X server apply here as well.

We summarise the most important details, please refer to the `UNICORE/X manual 
:ref:<unicore-docs:auth-img-authentication>` if you want to learn about further options.

The enabled authentication options and their order are configured 
in ``container.properties``.
::

	container.security.rest.authentication.order=PASSWORD | SSHKEY | UNITY

The available options can be combined.

.. _auth-user-pass:

Username-password file
^^^^^^^^^^^^^^^^^^^^^^

To use a file containing username, password and the DN,
::

	container.security.rest.authentication.order=PASSWORD
	container.security.rest.authentication.PASSWORD.class=eu.unicore.services.rest.security.FilebasedAuthenticator
	container.security.rest.authentication.PASSWORD.file=conf/rest-users.txt

This configures to use the file ``conf/rest-users.txt``. The file format is
::

	#
	# on each line:
	# username:hash:salt:DN
	#
	demouser:<...>:<...>:CN=Demo User, O=UNICORE, C=EU

i.e. each line gives the username, the hashed password, the salt and the user's DN, separated 
by colons. To generate entries, i.e. to hash the password correctly, the ``md5sum`` utility can 
be used. For example, if your intended password is *test123*, you could do

.. code:: console

	$ SALT=$(tr -dc "A-Za-z0-9_" < /dev/urandom | head -c 16 | xargs)
	$ /bin/echo "Salt is ${SALT}"
	$ /bin/echo -n "${SALT}test123" | md5sum

which will output the salted and hashed password. Here we generate a
random string as the salt. Enter these together with the username, and
the DN of the user into the password file.

.. _auth-unity: 

Unity SAML authentication
^^^^^^^^^^^^^^^^^^^^^^^^^

You can also hook up with `Unity <https://unity-idm.eu/>`__, passing on the username/password and
retrieving an authentication assertion.
::

	container.security.rest.authentication.order=UNITY
	
	container.security.rest.authentication.UNITY.class=eu.unicore.services.rest.security.UnitySAMLAuthenticator
	container.security.rest.authentication.UNITY.address=https://localhost:2443/unicore-soapidp/saml2unicoreidp-soap/AuthenticationService
	container.security.rest.authentication.UNITY.validate=true


Unity OAuth bearer token authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To have Unity check the client's OAuth token::

	container.security.rest.authentication.order=UNITY-OAUTH
	container.security.rest.authentication.UNITY-OAUTH.class=eu.unicore.services.rest.security.UnityOAuthAuthenticator
	container.security.rest.authentication.UNITY-OAUTH.address=https://localhost:2443/unicore-soapidp.oidc/saml2unicoreidp-soap/AuthenticationService
	container.security.rest.authentication.UNITY-OAUTH.validate=true


.. _ssh-key-auth:

SSH Key validation
^^^^^^^^^^^^^^^^^^

This authentication option is based on the validation of a token using the user's public SSH 
key. The token will be checked, and if successful, the user will be assigned a distinguished 
name for later authorisation.

SSH keys are read from the user's ``~/.ssh/authorized_keys`` file, but can also be managed 
manually in a dedicated ssh keys file.

.. note::
	SSH key validation will not work for users on Windows, since the UFTP stand-alone client 
	does not yet support SSH keys on Windows.
	We recommend adding a username/password option for Windows users.

SSH key validation is configured as follows:
::

	# authN
	container.security.rest.authentication.order=SSHKEY
	
	container.security.rest.authentication.SSHKEY.class=eu.unicore.uftp.authserver.authenticate.SSHKeyAuthenticator

When used like this, the users get an automatically assigned DN. By
default, the DN is `CN=<username>, OU=ssh-local-users`. Using the *PAM
attribute source* (see :ref:`below <attr-sources>`), authenticated users can be assigned the
*user* role automatically without further configuration.

The user DN can be modified by configuring the DN template like this::

	#DN template used for SSH key mapping. The %s is replaced by the username 
	container.security.rest.authentication.SSHKEY.dnTemplate=CN=%s, OU=ssh-local-users


Manual SSH key mapping
++++++++++++++++++++++

If you want to map ssh keys to DNs manually, a file is used. Entries in the file
override the keys read from ``~/.ssh/authorized_keys``.
::

	# configure SSH keys file 
	container.security.rest.authentication.SSHKEY.file=conf/ssh-users.txt

It contains the mappings and the ssh public keys in a simple format::

	# Example SSH users file used with the SSHKEY authentication method
	
	#
	#format: username:sshkey:DN
	#
	demouser:ssh-rsa keydata_was_omitted testkey:CN=Demo User, O=UNICORE, C=EU

The SSH key is in the same one-line format used in the ``.ssh/authorized_keys`` file.

You can enter multiple lines per username, to accommodate the case that a user has different
SSH keys available. For example
::

	# Example SSH users file with multiple keys per user
	
	demouser:ssh-rsa <...omitted keydata...>:CN=Demo User, O=UNICORE, C=EU
	demouser:ssh-dss <...omitted keydata...>:CN=Demo User, O=UNICORE, C=EU
	otheruser:ssh-rsa <...omitted keydata...>:CN=Other User, O=UNICORE, C=DE


.. _attr-sources:

Attribute sources
~~~~~~~~~~~~~~~~~

Please refer to the :ref:`UNICORE/X manual 
<unicore-docs:unicorex-manual>` 
on how to set up and configure attribute sources like :ref:`map file 
<unicore-docs:file-attr-source>` or :ref:`XUUDB <unicore-docs:xuudb-attr>`.

To use the automatic SSH key mapping, please use this config snippet
::

	# attribute source(s)
	container.security.attributes.order=PAM
	container.security.attributes.combiningPolicy=MERGE_LAST_OVERRIDES
	
	container.security.attributes.PAM.class=eu.unicore.services.rest.security.PAMAttributeSource

In this way users that successfully authenticate with their SSH key get the *user*
role automatically.


Attribute mapping
~~~~~~~~~~~~~~~~~

After successful authentication, the user is assigned attributes
such as the Unix account and group which is used for file access.

The Unix account and group are taken from the configured attribute
sources (e.g. :ref:`XUUDB <unicore-docs:xuudb>`). 
Since it is possible to access multiple UFTPD
servers using a single Auth server, it may be required to configure
different attributes for different UFTPD servers. This is easily
possible using the file attribute source (map file).

It is also possible to control which directories and files a user
can access. This is done by configuring the allowed and/or the
forbidden file path patterns.

The following map file entry gives a full example.

.. code:: xml

  <entry key="CN=Demo User,O=UNICORE,C=EU">
     <attribute name="role">
        <value>user</value>
     </attribute>

     <!-- default Unix account and group -->
     <attribute name="xlogin">
        <value>somebody</value>
     </attribute>
     <attribute name="group">
        <value>users</value>
     </attribute>
     
      <!-- UFTP specific attributes -->

      <attribute name="uftpd.CLUSTER.xlogin">
         <value>user1</value>
      </attribute>
      <attribute name="uftpd.CLUSTER.group">
         <value>hpc</value>
      </attribute>     

      <!-- optional rate limit (bytes per second) -->
      <attribute name="uftpd.CLUSTER.rateLimit">
         <value>10M</value>
      </attribute>     

      <!-- optional includes -->
      <attribute name="uftpd.CLUSTER.includes">
         <value>/tmp/*:/work/*</value>
      </attribute>     
      <!-- optional excludes -->
      <attribute name="uftpd.CLUSTER.excludes">
         <value>/home/*:/etc/*</value>
      </attribute>     
     
   </entry>

Here, the *CLUSTER* must match a configured UFTPD server, see also :ref:`auth-uftpd`. 
Available attributes are

:role: the UNICORE role, usually this will be *user*.

:xlogin, group: Unix account and group to be used for this user.

:rateLimit: the number of bytes per second (per transfer) can be limited. You can use the 
 units "K", "M", and "G" for kilo, mega or gigabytes, respectively.

:includes: file path patterns (separated by ``:``) that are allowed. If not given, all the 
 user's files can be accessed.

:excludes: file path patterns (separated by ``:``) that are forbidden. If not given, no files 
 are explicitely excluded.

Reservations
~~~~~~~~~~~~
.. _auth-reservations:

The Auth server allows to define reservations, i.e. time slots where 
certain users can get more of the available bandwidth for UFTP transfers.
During such a reservation, other users are rate-limited. The Auth server reads
reservations from a local JSON file, which can be edited at runtime by an admin. 

To enable, define the following two settings in the 
:ref:`UFTP configuration section <auth-uftpd>`::


	# configured UFTPD server(s)
	authservice.servers=CLUSTER
	
	# enable reservations feature for 'CLUSTER' server
	authservice.server.CLUSTER.reservations.enable=true
	authservice.server.CLUSTER.reservations.file=/path/to/reservations.json

The ``reservations.json`` file can be added / edited at runtime, and updates
will be read from it. 

The format of the JSON file is the following::

    {
      "reservations": [

         {
         	"name": "reservation1",
         	"from": "2023-08-31 16:00",
         	"to":   "2023-08-31 18:00",
         	"uids": [ "user1", "user2" ],
         	"rateLimit": "10m" 
         },

         {
         	"name": "reservation2",
         	"from": "2023-09-22 08:00",
         	"to":   "2023-09-22 09:00",
         	"uids": [ "user3" ],
         	"rateLimit": "100k" 
         }

      ]
    }
    
and should be self-explanatory. The ``from`` and ``to`` fields give the start/end time
of the reservation in ``yyyy-MM-DD hh:mm`` format, while the ``uids`` lists the Unix
logins of the users that should NOT be limited to the transfer rate given by ``rateLimit``.

The rate limit is optional, and defaults to "10m" i.e. 10MB/sec.

Note that the rate limit can only be applied to new connections, all FTP sessions already
existing at the start time of the reservation will not be affected.

|testing-img| Checking the installation
---------------------------------------

.. |testing-img| image:: ../../_static/testing.png
	:height: 32px
	:align: middle

You can check that the server works using a simple HTTP client such as ``curl`` to access the 
Auth server's base URL, provided you have configured username/password authentication.

The command

.. code:: console

	$ curl -k https://<host:port>/rest/auth \
		-H "Accept: application/json" \
		-u username:password

should produce a JSON document containing information about the
configured UFTPD servers and their status, such as

.. code:: json

	{"TEST": {
	  "availableGroups": [
	    "somebody",
	    "audio",
	    "users"
	  ],
	  "description": "Default UFTPD server for testing",
	  "gid": "users",
	  "href": "https://localhost:9000/rest/auth/TEST",
	  "rateLimit": 209715200,
	  "status": "OK [connected to UFTPD localhost:64435]",
	  "uid": "somebody",
	}}

.. note::
	If you do not get any output, try adding the ``-i`` option to the ``curl`` command, 
	most probably the username/password is incorrect.

 

.. _auth-uxdeploy:

|integration-img| Installing the Auth server in an existing UNICORE/X server
----------------------------------------------------------------------------

.. |integration-img| image:: ../../_static/integration.png
	:height: 32px
	:align: middle

This option is interesting if you are already running a UNICORE
installation and want to allow your users the option of using the
standalone :ref:`UFTP client <uftp-client>`. This requires :ref:`UNICORE/X 
<unicore-docs:unicorex>` version 8.0 or later!

 * copy the ``authserver-*.jar`` file to the ``lib`` directory of UNICORE/X

 * copy the XACML policy file ``30uftpAuthService.xml`` to the
   ``conf/xacml2Policies`` directory

 * edit ``container.properties`` (or ``uas.config``) and setup UFTPD details and, if necessary, 
   RESTful user authentication as described above


.. _auth-behind-gateway:

|gateway-img| Running the Auth server behind a UNICORE Gateway
--------------------------------------------------------------

.. |gateway-img| image:: ../../_static/gateway.png
	:height: 32px
	:align: middle

If you want to place the Auth server behind a :ref:`UNICORE gateway 
<unicore-docs:gateway>`
for easy firewall transversal, you need to configure an entry in the `Gateway
connections :ref:<unicore-docs:configuring-sites-connections-properties>` 
config file, and set the container base URL property
(``container.baseurl``) in the Auth server's ``container.properties``. 
This option is also useful when the server's listen address differs from the 
publicly accessible server address, such as when running the Auth server behind a NAT firewall.


.. raw:: html

   <hr>