Role Name
========

[Ansible](http://www.ansible.com) role to install [OpenDJ](http://opendj.forgerock.org/) LDAP v3 server.

This role downloads and installs the latest nightly build of OpenDJ. If you prefer to install a stable
release, or if for some reason the nightly build is not available, you [must download a copy of the OpenDJ](http://forgerock.com/download-stack/)  zip file
and update the variable *opendj_url* in defaults/main.yml to a location where the guest can access the zip file. Alternatively,
you can copy the OpenDJ zip file to the *download_dir* location.

Requirements
------------

Requires Java SDK (1.7 or later) installed on the target.


This role requires ansible 1.4 or higher


Role Variables
--------------

Default Variables (see defaults/main.yml for a more complete list)


Example
-------


Dependencies
------------

None

License
-------

MIT

Author Information
------------------

Warren Strange.
