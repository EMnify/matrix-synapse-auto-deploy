# Auto-deployment for matrix-org/synapse

Auto-deployment process for the matrix-org/synapse (https://github.com/matrix-org/synapse) homeserver and turnserver using  ansible, this will automatically deploy a ready-to-go matrix server on any server.

The playbook will try to install latest master from https://github.com/matrix-org/synapse/tarball/master

## Pre-requirements
* Git
* Ansible

## Clone auto-deploy repo

    git clone https://github.com/hugoShaka/matrix-synapse-auto-deploy

## Adopt vars file as needed or just go with these defaults

    ---
    username: synapse # under wich user the server should be installed and run
    git_repo: https://github.com/matrix-org/synapse/tarball/master # URL to Git Repo you want to install
    hostname: 10.99.99.230 # FQDN to be used
    enable_registration: true # this will open registration by default, take care if you run a public server!
    enable_registration_captcha: false
    recaptcha_private_key: YOURPRIVATEKEYHERE
    recaptcha_public_key: YOURPUBLICKEYHERE
    turn_shared_secret: YOURSHAREDSECRETHERE
    make_migration : true # will shut down the the server to migrate from sqlite to postgresql.
    database_secret: YOURDATABASESECRETHERE


## Run the ansible playbook

If you are not familiar with ansible, the easiest way is to lauch from the server you want to install : `ansible-playbook playbook.yml -c local` from a sudoer user.


## Getting safe

Get an SSL certificate (you can use let's encrypt : (https://matrix.org/docs/guides/lets-encrypt.html)), put the symlinks where they should be and be sure the nginx and synapse users have the right to read certs (610 with nginx and synapse in the group).

## Add your DNS entry

You should have a SRV entry like that (in order to tell other HomeServers on which port they will speak).

`_matrix._tcp.yourdomain.tld.	3600 IN	SRV 10 5 443 machine.yourdomain.tld.`


## Enjoy

You can now connect to your HomeServer, with the built-in web client ( http://machine.yourdomain.tld ) or by specifying your HomeServer on any other client.

# Not Working ?

## Check your firewall options

With this configuration you should allow :
- outbound : 8008 and 8448
- inbound : 80 and 443

## Check your DNS entry

With the commands :
`dig _matrix._tcp.yourdomain.tld SRV` and `dig machine.yourdomain.tld A`

## Check if synapse and nginx can access certs

They should both read the file. Become the app user and test if you can read the files with `sudo -u synapse/www-data cat /path/to/the/certs/cert.crt`.

If everything is fine for all certs and keys and all users, check the certs location in the conf (`/etc/nginx/sites-available and /home/{{username}}/.synapse/homeserver.yml`). If you don't know what files you should link, get a look at this tutorial (https://matrix.org/docs/guides/lets-encrypt.html).

## Still not working ? Come and ask for help on matrix:matrix.org using [Riot client](http://riot.im).


# Credits :
This is a fork of Martin Giess on [this original repo](https://github.com/EMnify/matrix-synapse-auto-deploy).
