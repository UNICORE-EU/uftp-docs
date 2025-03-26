.. _uftp-client-basic-usage:

|usage-img| Basic Usage
=======================

.. |usage-img| image:: ../../_static/usage.png
	:height: 22px
	:align: middle

In this manual, we use the following format to indicate commands
that you can type on the command line:

.. code:: console

	$ some_command

and assume that the bin directory of the UFTP client is on your path.

* Invoking uftp without any arguments,

  .. code:: console

	$ uftp

  will list the available commands.

.. .note::
	On Windows, the script is called ``uftp.bat``

* Invoking
 
  .. code:: console

	$ uftp <command> -h

  will show help for a particular command

* Invoking 

  .. code:: console

	$ uftp -version

  will show version information.

* For password authentication, the password can be given on the commandline, for example

  .. code:: console

	$ uftp ls -u demo:password https://localhost:9000/rest/auth/TEST:/home/demo/

* When you specify the ``-P`` option, the password/passphrase will be queried interactively

  .. code:: console

	$ uftp ls -u demo -P https://localhost:9000/rest/auth/TEST:/home/demo/



.. seealso::

	For detailed usage instructions and examples, refer to the :ref:`uftp-client-manual`.

.. raw:: html

   <hr>