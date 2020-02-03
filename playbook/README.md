Mongodb Access Playbook
=========

This playbook is ran against MongoDB databases to give access.

Requirements
------------

None

Role Variables
--------------

syslevel - system level of the database

Dependencies
------------



Example Playbook
----------------

ansible-playbook site.yml --extra-vars="syslevel='dev' createUsers='true'"

License
-------

BSD
