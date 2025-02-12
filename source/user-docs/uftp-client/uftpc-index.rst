
.. _uftp-client:

UFTP client
***********

The UFTP commandline client enables users to 

* :ref:`list remote directories <uftpc-ls-command>` (``ls``)
 
* :ref:`upload\/download files <uftpc-cp-command>` (``cp``)

* :ref:`compute checksums for remote files <uftpc-checksum-command>` (``checksum``)

* :ref:`sync remote\/local files <uftpc-sync-command>` (``sync``)

* make remote directories (``mkdir``)

* delete remote files or directories (``rm``)

* :ref:`manage shares and access shared data <uftpc-data-sharing>` (``share``)

* :ref:`perform authentication <uftpc-auth>` to help integrate UFTP with other tools 

* :ref:`launch server-to-server copy operations <uftpc-rcp-command>` (``rcp``)


The UFTP client will connect to an authentication server (either a :ref:`UNICORE/X server 
<unicore-docs:unicorex>` or the :ref:`authserver`) to authenticate and then to
the :ref:`uftpd` for transferring data or making a file operation.

.. image:: ../../_static/uftp-client.png
  :width: 400
  :alt: UFTP Client

The UFTP client supports username/password authentication, OIDC
token authentication and ssh-key authentication. 

The UFTP client supports multiple concurrent FTP connections for highly efficient 
data transfers in high-performance environments.

.. topic:: Features

 * :ref:`Commands <uftp-client-basic-usage>` (UNIX-like semantics) 
 
 * Supports :ref:`multi-threaded transfers <uftpc-multiple-connections>`, 
   :ref:`encryption and compression <uftpc-encrypt-compress>` of the data streams

 * :ref:`Flexible authentication <uftpc-auth>`

   * sshkey incl. support for ssh-agent (on Linux only)
   * OIDC via oidc-agent
   * Username/password


|usage-img| :doc:`uftpc-basic-usage`
    Basic usage of the UFTP Client.

.. |usage-img| image:: ../../_static/usage.png
	:height: 22px
	:align: middle

|user-guide-img| :doc:`uftpc-manual`
    User Manual with detailed instructions and examples for using the UFTP Client.

.. |user-guide-img| image:: ../../_static/user-guide.png
	:height: 22px
	:align: middle

|app-package-img| :doc:`uftpc-building`
    Building the UFTP Client distribution packages.

.. |app-package-img| image:: ../../_static/app-package.png
	:height: 22px
	:align: middle


.. toctree::
	:maxdepth: 6
	:caption: UFTP Client Documentation
	:hidden:
      
	uftpc-basic-usage
	uftpc-manual
	uftpc-building

