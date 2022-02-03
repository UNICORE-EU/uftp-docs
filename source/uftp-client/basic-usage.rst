.. _uftp-client-basic-usage:


Basic Usage
===========

In this manual, we use the following format to indicate commands
that you can type on the command line:

.. code:: console

	$> some_command

and assume that the bin directory of the UFTP client is on your path.

* Invoking uftp without any arguments,

  .. code:: console

	$> uftp

  will list the available commands.

.. .note::
	On Windows, the script is called ``uftp.bat``

* Invoking
 
  .. code:: console

	$> uftp <command> -h

  will show help for a particular command

* Invoking 

  .. code:: console

	$> uftp -version

  will show version information.

* For password authentication, use the ``-P`` option. The password can be written into the URL, for example

  .. code:: console

	$> uftp ls -u demo:password https://localhost:9000/rest/auth/TEST:/home/demo/

  If not given on the command line, the password will be queried interactively.


.. seealso::

	For detailed usage instructions and examples, refer to the :ref:`uftp-client-manual` available in the doc directory or online.