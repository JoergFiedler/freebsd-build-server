freebsd-build-server
=========

Creates a FreeBSD server which may be used as a build server. 'poudriere' is used to build packages.

    upload-to-s3

See (FreeBSD Handbook)[https://www.freebsd.org/doc/handbook/ports-poudriere.html] for further information.

Requirements
------------

This role is intent to be used with a fresh FreeBSD 10.2 install with some minor modifications. There is a Vagrant Box with providers for VirtualBox and EC2 you may use.

Role Variables
--------------

##### s3_bucket_name
The bucket to use to store the packages. Default: `''`.

##### s3_upload_path
The path within the S3 bucket where the packages are stored. Default: `'/public/FreeBSD/packages/'`.

##### freebsd_mirror_server
The FreeBSD mirror server used to set up the jails. Default: `'ftp://ftp.de.freebsd.org'`.

##### poudriere_ssl_prefix
The path where the package signing should be saved. Default: `'/usr/local/etc/ssl'`.

##### poudriere_key_file
The private key used to sign the packages. Please change this to use your own key. Default: `'poudriere.key'`.

##### poudriere_jails
The jails which should be created.

    poudriere_jails:
    - { jail_name: 'freebsd-10_2_x64', version: '10.2-RELEASE' }

Default: `''`.

Dependencies
------------

None.

Example Playbook
----------------


    ---
    - hosts: default
      sudo: true

      vars:
        s3_bucket_name: 's3.bucket.name'
        poudriere_jails:
        - { jail_name: 'freebsd-10_2_x64', version: '10.2-RELEASE' }

      roles:
      - { role: JoergFiedler.freebsd-build-server }

License
-------

BSD

Author Information
------------------

If you like it or do have ideas to improve this project, please open an issue on Github. Thanks.

