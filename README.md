Vagrant LNPP
============

Vagrant LNPP gives you everything you need for developing PHP applications locally. It consists of a Ubuntu 12.04 box provisioned with Nginx, Percona and PHP using Puppet. Librarian-Puppet takes care of downloading the required Puppet modules after which Puppet sets you up with an environment instantly ready for cutting code. The idea came from the need of having an OS-agnostic and virtualized alternative to the great [MNPP](http://getmnpp.org/) stack as regular LAMP stacks quite simply can't keep up with the Nginx + PHP-FPM combo in terms of performance. I hope you'll find it as useful an addition to your dev-arsenal as I've found it!

## What's inside

* [Ubuntu 12.04](http://releases.ubuntu.com/precise/)
* [Nginx](http://nginx.org/)
* [Percona](http://www.percona.com/)
* [PHP-FPM](http://php-fpm.org/)

### PHP modules

Vagrant LNPP comes with the following PHP modules installed and enabled out-of-the-box:

* php-apc
* php5-curl
* php5-memcache
* php5-mysql
* php5-mcrypt
* php5-sqlite

If you need other modules, you can define these in [`puppet/manifests/provision.pp`](puppet/manifests/provision.pp). `.ini` configuration file templates go in [`puppet/templates/php`](puppet/templates/php) and will be automatically included if they're named `module-name.ini.erb` and your module definitions are written like this (replace `module-name` with the name of the module leaving out the `php5-` prefix):

```puppet
php::module { "module-name":
  notify  => $notify_services,
  content => $php_ini_dir
}
```

### phpMyAdmin

[phpMyAdmin](http://www.phpmyadmin.net/) is included with Vagrant LNPP for easy database management and can be accessed on all your server blocks at `/phpmyadmin`.

## Getting started

To install Vagrant LNPP, simply download or clone the repo and do a `vagrant up`. You might want to grap a coffee as provisioning the box can take a little while.

### Server blocks

When you've got your box up and running, navigate to [192.168.33.10](http://192.168.33.10/) to see the default server block which contains information about PHP. To set up a new server block, make a new folder in [`public`](public) and define it in [`puppet/manifests/provision.pp`](puppet/manifests/provision.pp):

```puppet
nginx::vhost { "domain":
  root     => "${sites_dir}/directory",
  index    => "index.php",
  template => "${nginx_dir}/default.conf.erb"
}
```

Now make sure you've added an entry for `domain` to your host file, do a `vagrant reload` and you should be all set with your own server block.

### Databases

Databases can be added either through a definition or directly in [phpMyAdmin](#phpmyadmin). To add a new database through a definition, add something like this to [`puppet/manifests/provision.pp`](puppet/manifests/provision.pp):

```puppet
percona::database { "database":
  ensure => present
}
```

Database users are managed in [`puppet/manifests/provision.pp`](puppet/manifests/provision.pp) and can be added through this definition:

```puppet
percona::rights { "user@localhost":
  database => "database",
  password => "password",
  priv     => "all"
}
```

The default Percona admin user has the following credentials: `root:vagrant`.

---
Copyright 2013 @ [Kasper Kronborg Isager](http://webhutt.com). Licensed under the terms of the [MIT License](LICENSE.md)
