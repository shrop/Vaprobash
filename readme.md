# Vaprobash

**Va**grant **Pro**visioning **Bash** Scripts

[View the site and extended docs.](http://fideloper.github.io/Vaprobash/index.html)

[![Build Status](https://travis-ci.org/fideloper/Vaprobash.png?branch=master)](https://travis-ci.org/fideloper/Vaprobash)

## Goal

The goal of this project is to create easy to use bash scripts in order to provision a Vagrant server.

1. This targets Ubuntu LTS releases, currently 12.04.*
2. This project will give users various popular options such as LAMP, LEMP
3. This project will attempt some modularity. For example, users might choose to install a Vim setup, or not.

Some further assumptions and self-imposed restrictions. If you find yourself needing or wanting the following, then other provisioning tool would better suited ([Chef](http://www.getchef.com), [Puppet](http://puppetlabs.com), [Ansible](http://www.ansibleworks.com)).

* If other OSes need to be used (CentOS, Redhat, Arch, etc).
* If dependency management becomes complex. For example, installing Laravel depends on Composer. Setting a document root for a project will change depending on Nginx or Apache. Currently, these dependencies are accounted for, but more advanced dependencies will likely not be.

## Dependencies

* Vagrant `1.4.3`+
    * Use `vagrant -v` to check your version
* Vitualbox or VMWare Fusion

## Instructions

**First**, Copy the Vagrantfile from this repo. You may wish to use curl or wget to do this instead of cloning the repository.

```bash
# curl
$ curl -L http://bit.ly/vaprobash > Vagrantfile

# wget
$ wget -O Vagrantfile http://bit.ly/vaprobash
```

> The `bit.ly` link will always point to the master branch version of the Vagrantfile.

**Second**, edit the `Vagrantfile` and uncomment which scripts you'd like to run. You can uncomment them by removing the `#` character before the `config.vm.provision` line.

> You can indeed have [multiple provisioning](http://docs.vagrantup.com/v2/provisioning/basic_usage.html) scripts when provisioning Vagrant.

**Third** and finally, run:

```bash
$ vagrant up
```

**Screencast**

Here's a quickstart screencast!

[<img src="https://secure-b.vimeocdn.com/ts/463/341/463341369_960.jpg" alt="Vaprobash Quickstart" style="max-width:100%"/>](http://vimeo.com/fideloper/vaprobash-quickstart)

> <strong>Windows Users:</strong>
>
>You will likely not be able to use NFS for sharing files. If you delete the 4 lines for `config.vm.synced_folder`, Vagrant will default back to its usual Windows-friendly file sharing.

## Install Scripts

The following setups are installable via the separate bash scripts of this repository.


### Base Items
---

### Base Packages

This will install some base items.

* git-core and this [.gitconfig](https://gist.github.com/fideloper/3751524)
* ack-grep
* vim, tmux
* curl, wget
* build-essential, python-software-properties

### PHP

This will install PHP 5.5.

### Oh-My-Zsh

Installs the `zsh` shell and `oh-my-zsh`. It also makes it the default shell of the `vagrant` user.

### Vim

This will install [a Vim setup](https://gist.github.com/fideloper/a335872f476635b582ee), including:

* Vundle
* Bundle 'altercation/vim-colors-solarized'
* Bundle 'plasticboy/vim-markdown'
* Bundle 'othree/html5.vim'
* Bundle 'scrooloose/nerdtree'
* Bundle 'kien/ctrlp.vim'

See [the .vimrc file](https://gist.github.com/fideloper/a335872f476635b582ee) for more details and configuration.


### Web Servers
---

### Apache Base

This will install:

* Apache 2.4.*
* PHP 5.5 mod_php5
* [This vhost](https://gist.github.com/fideloper/2710970) bash script is installed to get you started with setting up a virtual host. This will make use of [xip.io](http://xip.io), creating a virtual host for [192.168.33.10.xip.io](192.168.33.10.xip.io).

By default, the web root will the `/vagrant`, which I suggest you change as needed (within `/etc/apache2/sites-available/192.168.33.10.xip.io.conf`). The Laravel and Symfony installation script will change the document root.

To create a new virtual host:

```bash
# See also: `vhost -h`
$ sudo vhost -s example.com -d /path/to/example/web/root

# You can then use `a2ensite` or `a2dissite` to enable or disable this vhost.
# `vhost` will enable it for you.
$ sudo a2dissite example.com
```

### HHVM

This will install HHVM. If provisioned, composer commands will utilize HHVM instead of the installed PHP version.

### Nginx Base

This will install:

* Nginx 1.4.* (latest stable)
* PHP 5.5 via php5-fpm
* [These virtual host configuration](https://gist.github.com/fideloper/8261546) enable/disable scripts, which work just like Apache2's `a2ensite` and `a2dissite`.

This makes use of [xip.io](http://xip.io), creating a virtual host for [192.168.33.10.xip.io](192.168.33.10.xip.io).

By default, the web root will the `/vagrant`, which I suggest you change as needed (within `/etc/nginx/sites-available/vagrant`). The Laravel and Symfony installation script will change the document root.

To enable or disable a site configuration (note that `vhost` above creates a new configuration. Below only shows enabling or disabling a site configuration):

```bash
# Enable a site config:
$ sudo ngxen example.com

# Disable a site config:
$ sudo ngxdis example.com

# Reload config after any change:
$ sudo service nginx reload
```

### Databases
---

### CouchDB

This will install the CouchDB database.

To create a new database:

```bash
# Execute this command inside the Vagrant box
$ curl -X PUT localhost:5984/name_of_new_database
```

You may access the "Futon" web interface for administering CouchDB at: `http://192.168.33.10:5984/_utils/`

### MySQL

This will install the MySQL 5.5 or 5.6 depending on what you choose on the Vagrantfile.

* Host: `localhost` or `192.168.33.10`
* Username: `root`
* Password: `root`

In order to create a new database to use:

```bash
# SSH into Vagrant box
$ vagrant ssh

# Create Database
# This will ask you to enter your password
$ mysql -u root -p -e "CREATE DATABASE your_database_name"
```

### PostgreSQL

This will install the PostgreSQL 9.3 database.

* Host: `localhost`
* Username: `root`
* Password: `root`

In order to create a new database to use:

```bash
# SSH into vagrant box
$ vagrant ssh

# Create new database via user user "postgres"
# and assign it to user "root"
$ sudo -u postgres /usr/bin/createdb --echo --owner=root your_database_name
```

### SQLite

This will install the SQLite server.

SQLite runs either in-memory (good for unit testing) or file-based.


### In-Memory Stores
---

### Memcached

This will install Memcached, which can be used for things like caching and session storage.

### Redis

This will install Redis (server). There are two options:

1. Install without journaling/persistence
2. Install with journaling/persistence

You can choose between the two by uncommenting one provision script or the other in the `Vagrantfile`.

### Search
---
### ElasticSearch

This will install the ElasticSearch search engine.


### Utility (queues)
---

### Beanstalkd

This will install the Beanstalkd work queue.

This will configure Beanstalkd to start when the server boots.

* Host: `localhost` (`0.0.0.0` default)
* Port: `11300` (default)


### Additional Languages
---

### NodeJS

NodeJS will be installed using [Node Version Manager](https://github.com/creationix/nvm). Type `$ nvm help` in the console/terminal or read [this](https://github.com/creationix/nvm/blob/master/README.markdown) for more info on NVM.

You can configure the NodeJS version and the Global Node Packages within the Vagrantfile.
```ruby
nodejs_version   = "latest" # By default "latest" will equal the latest stable version
nodejs_packages  = [        # List any global NodeJS packages that you want to install
  #"grunt-cli",
  #"bower",
  "yo",                     # This is uncommented and shall be installed globally
  # ... add more packages or delete all packages if you don't want any
]
```
You can have as many packages installed or choose to not install any packages at all (just comment or delete the lines). Type `$ nvm ls-remote` in the console/terminal to display the full list of available NodeJS versions.

The script will also set global NPM packages to be installed in `home/vagrant/npm/bin`.

### RVM/Ruby

This will install Ruby via RVM. You can decide which version of ruby via the configuration variable found in the `Vagrantfile`. Default is `latest`.


### Frameworks and Tooling
---

### Composer

This will install composer and make it globally accessible.

### Laravel

This will install a base Laravel (latest stable) project within `/vagrant/laravel`. It depends on Composer being installed.

This will also attempt to change the Apache or Nginx virtual host to point the document root at `/vagrant/laravel/public`.

### Symfony

This will install a base Symfony (latest stable) project within `/vagrant/symfony`. It depends on Composer being installed.

This will also attempt to change the Apache or Nginx virtual host to point the document root at `/vagrant/symfony/web`.

### Yeoman

This will install Yeoman globally for you to use in your front-end projects.

### PHPUnit

This will install PHPUnit and make it globally accessible.

### Screen

This will install Screen on the Vagrant machine.

### MailCatcher

This will install mailcatcher and set the php.ini path to catchmail.

## The Vagrantfile

The vagrant file does three things you should take note of:

1. **Gives the virtual machine a static IP address of 192.168.33.10.** This IP address is again hard-coded (for now) into the LAMP, LEMP and Laravel/Symfony installers. This static IP allows us to use xip.io for the virtual host setups while avoiding having to edit our computers' `hosts` file.
2. **Uses NFS instead of the default file syncing.** NFS is reportedly faster than the default syncing for large files. If, however, you experience issues with the files actually syncing between your host and virtual machine, you can change this to the default syncing by deleting the lines setting up NFS:

  ```ruby
  config.vm.synced_folder ".", "/vagrant",
            id: "core",
            :nfs => true,
            :mount_options => ['nolock,vers=3,udp,noatime']
  ```
3. **Offers an option to prevent the virtual machine from losing internet connection when running on Ubuntu.** If your virtual machine can't access the internet, you can solve this problem by uncommenting the two lines below:

  ```ruby
    #vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    #vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  ```

  Don't forget to reload your Vagrantfile running `vagrant reload --no-provision`, in case your virtual machine already exists.


## Contribute!

Do it! Any new install or improvement on existing ones are welcome! Please see the [contributing doc](/contributing.md), which only asks that pull requests be made to the `develop` branch.
