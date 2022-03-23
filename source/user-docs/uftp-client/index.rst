
.. _uftp-client:

UFTP client
***********

The UFTP commandline client enables to 

* list remote directories

* upload/download files
* sync files

* make remote directories

* delete remote files or directories

* manage shares and access shared data

* perform authentication to help integrate
  UFTP with other tools

The UFTP client will connect to an authentication server (either a UNICORE/X server or the :ref:`authserver`) to authenticate and then to
the :ref:`uftpd` for transferring data or making a file operation.

The UFTP client supports username/password authentication, OIDC
token authentication and ssh-key authentication. 
It supports multiple concurrent FTP connections for highly efficient data transfers in high-performance environments.


.. topic:: User Documentation

  :doc:`installing`
      How to install and configure the UNICORE Client.

  :doc:`basic-usage`
      Basic usage of the UNICORE Client.

  :doc:`manual`
      User Manual with detailed instructions and examples for using the UNICORE Client.

  :doc:`changelog`
    The UNICORE Client changelog.



.. topic:: Administrator Documentation

  :doc:`building`
      Building the UNICORE Client distribution packages.



.. toctree::
	:maxdepth: 2
	:caption: UFTP Client Documentation
	:hidden:
      
	installing
	basic-usage
	manual
	building
	
	
.. toctree::
	:maxdepth: 1
	:hidden:

	changelog   

