stackhpc.os-keypair-login
=========================

[![Build Status](https://travis-ci.org/stackhpc/ansible-role-os-keypair-login.svg?branch=master)](https://travis-ci.org/stackhpc/ansible-role-os-keypair-login)

Add OpenStack keypairs into the ``authorized_keys`` file.

Requirements
------------

Requires the following python packages to be installed:

* ``os-client-config``

Role Variables
--------------

Variables you should always set are:

* ``os_keypair_login_cloud`` which cloud in ``/etc/openstack/clouds.yaml``
  should be used to fetch keypairs.
* ``os_keypair_login_project_name`` which OpenStack project your users are in
* ``os_keypair_login_users`` which users to create and inject keys into

Other variables you can use to customize things can be found in the role
defaults.

Dependencies
------------

This depends on openstack configuration being placed at
`/etc/openstack/clouds.yaml`
One way to do that is using the role ``stackhpc.os-config`.

Example Playbook
----------------

This example playbook combines this role and ``stackhpc.os-config`` so users
are able to login via their nova ssh key, if they have one:

    ---
    - hosts: all
      vars:
        my_cloud_config: |
          ---
          clouds:
            mycloud:
              auth:
                auth_url: http://openstack.example.com:5000
                project_name: p3
                username: user
                password: secretpassword
              region: RegionOne
        allowed_users:
          - name: "John Smith"
            user: johnsmith
            uid: 2001
            groups: wheel
          - name: "James Bond"
            user: jbond7
            uid: 2007
      roles:
        - { role: stackhpc.os-config,
            os_config_content: "{{ my_cloud_config }}" }
        - { role: stackhpc.os-keypair-login,
            os_keypair_login_cloud: "mycloud",
            os_keypair_login_project_name: "p3",
            os_keypair_login_users: "{{ allowed_users }}" }

An easy way to this example is:

.. code-block::

    sudo yum install python-virtualenv libselinux-python

    virtualenv .venv --system-site-packages
    . .venv/bin/activate
    pip install -U pip
    pip install -U ansible

    ansible-galaxy install stackhpc.os-keypair-login \
                           stackhpc.os-config

    ansible-playbook -i "localhost," -c local test.yml

License
-------

Apache 2

Author Information
------------------

http://www.stackhpc.com
