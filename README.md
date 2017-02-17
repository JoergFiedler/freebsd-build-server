freebsd-build-server
=========

Creates a FreeBSD server which provides a ready to run 'poudriere' installation. See [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/ports-poudriere.html) for further information.

I wanted to have a build server which I don't have to keep running all the time. To accomplish this, the package repository and the build option for all the packages are synced to S3. This allows me to destroy the build server after the packages have been build and synced. During the next installation the the build options are synced back.

Before you start you may provide you own files for by setting the path for the corresponding Ansible variable.

1. `make.conf`, (`poudriere_build_options_file`)
1. `port-list`, (`poudriere_port_list_file`)
1. `poudriere.key`, (`poudriere_key_file`)

You may do so by providing you own version of the file and setting the path to it to the corresponding Ansible variable.

With all that in mind the typical workflow looks like this.

1. Spawn a new server (there is a [Vagrant file](https://github.com/JoergFiedler/freebsd-build-machine/) for this)
1. Apply this ansible role
1. Log in
1. Run: `sudo build-ports`
1. Run: `sudo upload-to-s3`
1. Log out
1. Destroy the server

Easy as pie.

Requirements
------------

This role is intent to be used with a fresh FreeBSD 11.0 install with some minor modifications. There is a Vagrant Box with providers for VirtualBox and EC2 you may use. I created [this Vagrant project](https://github.com/JoergFiedler/freebsd-build-machine) to create Virtualbox and EC2 machines.

Role Variables
--------------

##### poudriere_enable_s3
When enabled, AWS S3 will be configured on the host using the following
configuration parameters. Default: `'yes'`.

##### aws_default_region
S3 region to use. Default: `''`.

##### aws_access_key_id
S3 access key. Default: `''`.

##### aws_secret_access_key
S3 secret key. Default: `''`.

##### s3_bucket_name
The bucket to use to store the packages and build options. Default: `''`.

##### s3_upload_path
The path within the S3 bucket where to put `packages` and `build-options` folder. Default: `'/public/FreeBSD'`.

##### freebsd_mirror_server
The FreeBSD mirror server used to set up the jails. Default: `'ftp://ftp.freebsd.org'`.

##### poudriere_ssl_prefix
The path where the package signing key should be saved. Default: `'/usr/local/etc/ssl'`.

##### poudriere_key_file
The private key used to sign the packages. Please change this to use your own key. Default: `'poudriere.key.default'`.

##### poudriere_port_list_file
The list of ports to build. Default: `'port-list.default'`.

##### poudriere_build_options_file
Build options `make.conf` used to build to ports. Default: `'make.conf.default'`.

##### poudriere_jails
The jails which should be created.

    poudriere_jails:
    - { jail_name: 'freebsd-11_0_x64', version: '11.0-RELEASE' }

Default: `''`.

##### poudriere_zpool
Name of the ZFS `ZPOOL` used by poudriere. Default: `'tank'`.

##### poudriere_enable_cron
Enable a daily cron job which runs a poudriere bulk build. Default: `no`.

Dependencies
------------

None.

Example Playbook
----------------

    ---
    - hosts: default
      sudo: true

      vars:
        aws_access_key_id: '{{ lookup("env","AWS_ACCESS_KEY_ID") }}'
        aws_secret_access_key: '{{ lookup("env","AWS_SECRET_ACCESS_KEY") }}'
        aws_default_region: '{{ lookup("env","AWS_DEFAULT_REGION") }}'
        s3_bucket_name: 'repo.bucket.name'
        poudriere_port_list_file: './files/port.list'
        poudriere_key_file: './files/poudriere.key'
        poudriere_build_options_file: './files/make.conf'
        poudriere_jails:
          - { jail_name: 'freebsd-10_2_x64', version: '10.2-RELEASE' }
          - { jail_name: 'freebsd-10_3_x64', version: '10.3-RELEASE' }
          - { jail_name: 'freebsd-11_0_x64', version: '11.0-RELEASE' }
        poudriere_zpool: 'tank'
        poudriere_enable_cron: yes

      roles:
      - { role: JoergFiedler.freebsd-build-server }

License
-------

BSD

Author Information
------------------

If you like it or do have ideas to improve this project, please open an issue on Github. Thanks.

