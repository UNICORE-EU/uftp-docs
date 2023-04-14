.. _auth-server-update:

|update-img| Update procedure
-----------------------------

.. |update-img| image:: ../../_static/update.png
	:height: 32px
	:align: middle

As a first step and precaution, you should make backups of your 
existing config files and put them in a safe place.

In the following, *LIB* refers to the directory containing the jar files for the component, and 
*CONF* to the config directory of the existing installation.

It is assumed that you have unpacked the ``tar.gz`` file somewhere, e.g. to ``/tmp/``. In the 
following, this location will be denoted as *$NEW*:

.. code:: console

	$ export NEW=/tmp/unicore-authserver-2.5.0

* Stop the server. If not yet done, make a backup of the config files.

* Update the jar files:

  .. code:: console

   $ rm -rf LIB/*
   $ cp $NEW/lib/*.jar LIB
   
* Check for other changes

While rarely changed, sometimes the XACML policy files are updated for new releases.
These can be found in *$NEW/conf/xacml2Policies/*
If necessary, copy these to your installation:

  .. code:: console

   $ rm -rf conf/xacml2Policies/*
   $ cp $NEW/conf/xacml2Policies/* conf/xacml2Policies/


* Start the server.

* Check the logs for any **ERROR** or **WARN** messages and if necessary correct them.