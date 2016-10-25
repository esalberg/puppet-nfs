#nfs

####Table of Contents

1. [Overview - What is the nfs module?](#overview)
2. [Module Description - What does this module do?](#module-description)
3. [Setup - The basics of getting started with nfs](#setup)
4. [Usage - The class and available configurations](#usage)
5. [Requirements](#requirements)
6. [Limitations - OS compatibility, etc.](#limitations)
7. [Contributing to the nfs module](#contributing)

##Overview

Github Master: [![Build Status](https://secure.travis-ci.org/derdanne/puppet-nfs.png?branch=master)](https://travis-ci.org/derdanne/puppet-nfs)

This module installs, configures and manages everything on NFS clients and servers.

This module is a complete refactor of the module haraldsk/nfs, because Harald Skoglund sadly is not
maintaining his module actively anymore. It is stripped down to use only the class 'nfs'
and parametrized to act as a server, client or both with the parameters 'server_enabled'
and 'client_enabled'. It also has some dependencies on newer stdlib functions like 'difference'.

It supports the OS Families Ubuntu, Debian, Redhat, SUSE, Gentoo and Archlinux. It supports also Strict Variables, so if you pass all
OS specific parameters correctly it should work on your preferred OS too. Feedback, bugreports,
and feature requests are always welcome, visit https://github.com/derdanne/puppet-nfs or send me an email.

If you want to contribute, please do a fork on github, create a branch "feature name" with your
features and do a pull request.

##Module Description

This module can be used to simply mount nfs shares on a client or to configure your nfs servers.
It makes use of storeconfigs on the puppetmaster to get its resources. You can also easily use the
create_resources function when you store your exports i.e. via hiera.

##Setup

This Module depends on puppetlabs-stdlib >= 4.5.0 and puppetlabs-concat >= 1.1.2. It is tested till
Puppet Version 4.2.

##Reference

###Classes

####Public Classes

* `nfs`: Main class, includes all other classes

####Public Defines

* `nfs::client::mount`: Handles all mounts on a nfs client.
* `nfs::server::export`: Handles all nfs exports on a nfs server.

####Private Classes

* `nfs::client`: Includes all relevant classes for configuring as a client.
* `nfs::client::config`: Handles the configuration files.
* `nfs::client::package`: Handles the packages.
* `nfs::client::service`: Handles the services.

* `nfs::server`: Includes all relevant classes for configuring as a server
* `nfs::server::config`: Handles the configuration files.
* `nfs::server::package`: Handles the packages.
* `nfs::server::service`: Handles the services.

####Private Defines

* `nfs::bindmount`: Creates the bindmounts of non nfs 3 exports.
* `nfs::nfsv4_bindmount`: Creates the bindmounts of non nfs 4 exports.
* `nfs::create_export`: Creates the nfs exports.
* `nfs::mkdir`: Creates directories recursive.

### Parameters

The following parameters are available in the `::nfs` class:

####`ensure`
  String. Controls if the managed resources shall be <tt>present</tt> or
  <tt>absent</tt>. If set to <tt>absent</tt>:
  * The managed software packages are being uninstalled.
  * Any traces of the packages will be purged as good as possible. This may
    include existing configuration files. The exact behavior is provider
    dependent. Q.v.:
    * Puppet type reference: {package, "purgeable"}[http://j.mp/xbxmNP]
    * {Puppet's package provider source code}[http://j.mp/wtVCaL]
  * System modifications (if any) will be reverted as good as possible
    (e.g. removal of created users, services, changed log settings, ...).
  * This is thus destructive and should be used with care.
  Defaults to <tt>present</tt>.

####`server_enabled`
  Boolean. If set to <tt>true</tt>, this module will configure the node
  to act as a nfs server.

####`client_enabled`
  Boolean. If set to <tt>true</tt>, this module will configure the node
  to act as a client server, you can use the exported mount resources
  from configured servers.

####`nfs_v4`
  Boolean. If set to <tt>true</tt>, this module will use nfs version 4
  for exporting and mounting nfs resources.

####`nfs_v4_client`
  Boolean. If set to <tt>true</tt>, this module will use nfs version 4
  for mounting nfs resources. If set to <tt>false</tt> it will use nfs
  version 3 to mount nfs resources. It defaults to the setting of `nfs_v4`

####`exports_file`
  String. It defines the location of file with the nfs export resources used
  by the nfs server.

####`idmapd_file`
  String. It defines the location of th file with the idmapd settings.

####`defaults_file`
  String. It defines the location of th file with the nfs settings.

####`manage_packages`
  Boolean. It defines if the packages should be managed through this module

####`server_packages`
  Array. It defines the packages needed to be installed for acting as
  a nfs server

####`server_package_ensure`
  String. It defines the packages state - any of present, installed,
  absent, purged, held, latest

####`client_packages`
  Array. It defines the packages needed to be installed for acting as
  a nfs client

####`client_package_ensure`
  String. It defines the packages state - any of present, installed,
  absent, purged, held, latest

####`manage_server_service`
  Boolean. Defines if module should manage server_service

####`manage_server_servicehelper`
  Boolean. Defines if module should manage server_servicehelper

####`manage_client_service`
  Boolean. Defines if module should manage client_service

####`server_service_name`
  String. It defines the servicename of the nfs server service

####`server_service_ensure`
  Boolean. It defines the service parameter ensure for nfs server services.

####`server_service_enable`
  Boolean. It defines the service parameter enable for nfs server service.

####`server_service_hasrestart`
  Boolean. It defines the service parameter hasrestart for nfs server service.

####`server_service_hasstatus`
  Boolean. It defines the service parameter hasstatus for nfs server service.

####`server_service_restart_cmd`
  String. It defines the service parameter restart for nfs server service.

####`server_nfsv4_servicehelper`
  String. It defines the service helper like idmapd for servers configured with
  nfs version 4.

####`client_services`
  Nested Hash. It defines the servicenames need to be started when acting as a nfs client

####`client_nfsv4_services`
  Nested Hash. It defines the servicenames need to be started when acting as a nfs client
  version 4.

####`client_service_hasrestart`
  Boolean. It defines the service parameter hasrestart for nfs client services.

####`client_service_hasstatus`
  Boolean. It defines the service parameter hasstatus for nfs client services.

####`client_idmapd_setting`
  Array. It defines the Augeas parameter added in `defaults_file` when acting as a nfs
  version 4 client.

####`client_nfs_fstype`
  String. It defines the name of the nfs filesystem, when adding entries to /etc/fstab
  on a client node.

####`client_nfs_options`
  String. It defines the options for the nfs filesystem, when adding entries to /etc/fstab
  on a client node.

####`client_nfsv4_fstype`
  String. It defines the name of the nfs version 4 filesystem, when adding entries
  to /etc/fstab on a client node.

####`client_nfsv4_options`
  String. It defines the options for the nfs version 4filesystem, when adding entries
  to /etc/fstab on a client node.

####`nfs_v4_export_root`
  String. It defines the location where nfs version 4 exports should be bindmounted to
  on a server node. Defaults to <tt>/export</tt>.

####`nfs_v4_export_root_clients`
  String. It defines the clients that are allowed to mount nfs version 4 exports and
  includes the option string. Defaults to
  <tt>*.${::domain}(ro,fsid=root,insecure,no_subtree_check,async,root_squash)</tt>.

####`nfs_v4_mount_root`
  String. It defines the location where nfs version 4 clients find the mount root
  on a server node. Defaults to <tt>/srv</tt>.

####`nfs_v4_idmap_domain`
  String. It defines the name of the idmapd domain setting in `idmapd_file` needed
  to be set to the same value on a server and client node to do correct uid and gid
  mapping. Defaults to <tt>$::domain</tt>.

####`nfs_v4_root_export`
  Vary. These settings define the options of the exported resource of the export root.


##Examples

### Simple NFSv3 server and client example

This will export /data/folder on the server and automagically mount it on client.

```puppet
  node server {
    class { '::nfs':
      server_enabled => true
    }
    nfs::server::export{ '/data_folder':
      ensure  => 'mounted',
      clients => '10.0.0.0/24(rw,insecure,async,no_root_squash) localhost(rw)'
    }
  }

  # By default, mounts are mounted in the same folder on the clients as
  # they were exported from on the server
  node client {
    class { '::nfs':
      client_enabled => true,
    }
    Nfs::Client::Mount <<| |>>
  }
```

### Simple NFSv4 client example

This will mount /data on client in /share/data.

```pupppet
  class { '::nfs':
    server_enabled => false,
    client_enabled => true,
    nfs_v4_client => true,
    nfs_v4_idmap_domain => $::domain,
  }

  nfs::client::mount { '/share/data':
      server => '192.168.0.1',
      share => 'data',
  }
```


### NFSv3 multiple exports, servers and multiple node example

```puppet
  node server1 {
    class { '::nfs':
      server_enabled => true
    }
    nfs::server::export{
      '/data_folder':
        ensure  => 'mounted',
        clients => '10.0.0.0/24(rw,insecure,async,no_root_squash) localhost(rw)'
      # exports /homeexports and mounts them om /srv/home on the clients
      '/homeexport':
        ensure  => 'mounted',
        clients => '10.0.0.0/24(rw,insecure,async,root_squash)',
        mount   => '/srv/home'
  }

  node server2 {
    class { '::nfs':
      server_enabled => true
    }
    # ensure is passed to mount, which will make the client not mount it
    # the directory automatically, just add it to fstab
    nfs::server::export{
      '/media_library':
        ensure  => 'present',
        nfstag     => 'media'
        clients => '10.0.0.0/24(rw,insecure,async,no_root_squash) localhost(rw)'
  }

  node client {
    class { '::nfs':
      client_enabled => true,
    }
    Nfs::Client::Mount <<| |>>
  }

  # Using a storeconfig override, to change ensure option, so we mount
  # all shares
  node greedy_client {
    class { '::nfs':
      client_enabled => true,
    }
    Nfs::Client::Mount <<| |>> {
      ensure => 'mounted'
    }
  }


  # only the mount tagged as media
  # also override mount point
  node media_client {
    class { '::nfs':
      client_enabled => true,
    }
    Nfs::Client::Mount <<|nfstag == 'media' | >> {
      ensure => 'mounted',
      mount  => '/import/media'
    }
  }

  # All @@nfs::server::mount storeconfigs can be filtered by parameters
  # Also all parameters can be overridden (not that it's smart to do
  # so).
  # Check out the doc on exported resources for more info:
  # http://docs.puppetlabs.com/guides/exported_resources.html
  node single_server_client {
    class { '::nfs':
      client_enabled => true,
    }
    Nfs::Client::Mount <<| server == 'server1' |>> {
      ensure => 'absent',
    }
  }
```

### NFSv4 Simple example


```puppet
  # We use the $::domain fact for the Domain setting in
  # /etc/idmapd.conf.
  # For NFSv4 to work this has to be equal on servers and clients
  # set it manually if unsure.
  #
  # All nfsv4 exports are bind mounted into /export/$mount_name
  # and mounted on /srv/$mount_name on the client.
  # Both values can be overridden through parameters both globally

  # and on individual nodes.
  node server {
    class { '::nfs':
      server_enabled => true,
      nfs_v4         => true,
      nfs_v4_export_root_clients =>
        '10.0.0.0/24(rw,fsid=root,insecure,no_subtree_check,async,no_root_squash)'
    }
    nfs::server::export{ '/data_folder':
      ensure  => 'mounted',
      clients => '10.0.0.0/24(rw,insecure,no_subtree_check,async,no_root_squash) localhost(rw)'
  }

  # By default, mounts are mounted in the same folder on the clients as
  # they were exported from on the server

  node client {
    class { '::nfs':
      client_enabled  => true,
      nfs_v4_client   => true,
      nfs_v4_export_root_clients =>
        '10.0.0.0/24(rw,fsid=root,insecure,no_subtree_check,async,no_root_squash)'
    }
    Nfs::Client::Mount <<| |>>
  }

  # We can also mount the NFSv4 Root directly through nfs::client::mount::nfsv4::root.
  # By default /srv will be used for as mount point, but can be overriden through
  # the 'mounted' option.

  node client2 {
    $server = 'server'
    class { '::nfs':
      client_enabled => true,
      nfs_v4_client  => true,
    }
    Nfs::Client::Mount::Nfs_v4::Root <<| server == $server |>> {
      mount => "/srv/$server",
    }
  }
```

### NFSv4 insanely overcomplicated reference example


```puppet
  # and on individual nodes.
  node server {
    class { '::nfs':
      server_enabled      => true,
      nfs_v4              => true,
      # Below are defaults
      nfs_v4_idmap_domain => $::domain,
      nfs_v4_export_root  => '/export',
      # Default access settings of /export root
      nfs_v4_export_root_clients =>
        "*.${::domain}(ro,fsid=root,insecure,no_subtree_check,async,root_squash)"


    }
    nfs::server::export{ '/data_folder':
      # These are the defaults
      ensure  => 'mounted',
      # rbind or bind mounting of folders bindmounted into /export
      # google it
      bind    => 'rbind',
      # everything below here is propogated by to storeconfigs
      # to clients
      #
      # Directory where we want export mounted on client
      mount     => undef,
      remounts  => false,
      atboot    => false,
      #  Don't remove that option, but feel free to add more.
      options   => '_netdev',
      # If set will mount share inside /srv (or overridden mount_root)
      # and then bindmount to another directory elsewhere in the fs -
      # for fanatics.
      bindmount => undef,
      # Used to identify a catalog item for filtering by by
      # storeconfigs, kick ass.
      nfstag     => undef,
      # copied directly into /etc/exports as a string, for simplicity
      clients => '10.0.0.0/24(rw,insecure,no_subtree_check,async,no_root_squash)'
  }

  node client {
    class { '::nfs':
      client_enabled      => true,
      nfs_v4_client       => true,
      nfs_v4_idmap_domain => $::domain
      nfs_v4_mount_root   => '/srv',
    }

    # We can as you by now know, override options set on the server
    # on the client node.
    # Be careful. Don't override mount points unless you are sure
    # that only one export will match your filter!

    Nfs::Client::Mount <<| # filter goes here # |>> {
      # Directory where we want export mounted on client
      mount     => undef,
      remounts  => false,
      atboot    => false,
      #  Don't remove that option, but feel free to add more.
      options   => '_netdev',
      # If set will mount share inside /srv (or overridden mount_root)
      # and then bindmount to another directory elsewhere in the fs -
      # for fanatics.
      bindmount => undef,
    }
  }
```

### Simple create_resources with hiera example

#### HIERA:

```yaml
  nas::nfs_exports_global:
    /var/www: {}
    /var/smb: {}
```

#### PUPPET:

```puppet
  $nfs_exports_global = hiera_hash('nas::nfs_exports_global', false)

  class { '::nfs':
    server_enabled => true,
    client_enabled => false,
    nfs_v4 => true,
    nfs_v4_idmap_domain => $::domain,
    nfs_v4_export_root => '/share',
    nfs_v4_export_root_clients => '192.168.0.0/24(rw,fsid=root,insecure,no_subtree_check,async,no_root_squash)',
  }

  $defaults_nfs_exports = {
    ensure => 'mounted',
    clients => '192.168.0.0/24(rw,insecure,no_subtree_check,async,no_root_squash)
  }

  if $nfs_exports_global {
    create_resources('::nfs::server::export', $nfs_exports_global, $defaults_nfs_exports)
  }
```

##Requirements

###Modules needed:

puppetlabs/stdlib >= 4.5.0
puppetlabs/concat >= 1.1.2

###Software versions needed:

facter > 1.6.2
puppet > 3.2.0

###Ruby Gems needed:

augeas

##Limitations
If you want to have specific package versions installed you may manage the needed packages outside of this 
module (use manage_packages => false). It is only tested to use 'present', 'installed', 'absent',
'purged', 'held' and 'latest' as argument for the parameters server_package_ensure and client_package_ensure.

##Contributing

Derdanne modules are open projects. So if you want to make this module even better,
you can contribute to this module on [Github](https://github.com/derdanne/puppet-nfs).

This module based on Harald Skoglund <haraldsk@redpill-linpro.com> from
https://github.com/haraldsk/puppet-module-nfs/ but has been fundementally refactored
