## Changelog

### NOTE: All AMIs are in USW-2.

## Unreleased

* Update to Docker 1.7
* Add ability to specify a custom Nginx template on a per container basis. [Original PR](https://github.com/octohost/octohost/pull/125), [Merged PR](https://github.com/octohost/octohost/pull/130) - Thanks [Joshua!](https://github.com/joshuacox)
* Instaled [Vault](https://www.vaultproject.io/) - want to secure the ENV vars stored in Consul.
* [Better removal](https://github.com/octohost/octohost/commit/0819f6ce833f72e91f05546d09875f3b0a08ac27) of exited containers and untagged images.
* [Always restart](https://github.com/octohost/octohost/commit/5a689433e9a5978366122d8dfc812f40689869ea) all logging containers.
* Remove spurious [extra lines](https://github.com/octohost/octohost-cookbook/commit/05f9167248709746a153d9e23626af9ea9a8ef15) from `consulkv`.

## 1.6 - ami-67e0d057 \(ami-e8786e80 in USE-1\)

* Update to Docker 1.6.
* Update to Consul 0.5.0 (compiled from master) and Consul Template 0.9.
* Update to HVM AMI.
* Use Consul http check rather than curl script by default - more efficient.
* Fix a whole bunch of issues around pushing branches and the '/' character in branch names.
* Fix issues around NO_HTTP_PROXY
* Add `octo version` command. [Example output](https://gist.github.com/darron/a911b63169bea012fd22).
* Add some [simple logging](https://github.com/octohost/octohost/commit/c8c044d19e83b0f2163b140e4aefeaf99f3233a9).
* Fixed some bugs related to the use of a private registry.
* Force SSL for certain hosts - thanks to [Joshua Cox](https://github.com/octohost/octohost/issues/101).
* Add Upstart configuration to [restart containers on reboot](https://github.com/octohost/octohost/issues/109).
* Removed Openresty in favor of nginx 1.8.0.
* Update all packages during the build so that every install is fully patched when built.
* Use Chef 12 during the build.
* Builds and operates on Azure. Use the [`knife solo`](http://www.octohost.io/knife-solo.html) instructions if the [Packer Azure plugin](https://github.com/MSOpenTech/packer-azure/releases/tag/prerelease) isn't working for you.
* Works with [Docker Swarm](https://github.com/docker/swarm) for some basic clustering. [Instructions](https://github.com/octohost/www/issues/6#issuecomment-95068509), [Demo](https://asciinema.org/a/19043).
* Added mod_pagespeed to the [nginx build](https://github.com/darron/nginx-build) - not enabled by default. Configuration to enable is [located here](https://gist.github.com/darron/5604cefb982b8bfcc338).

## 1.4.1 - ami-ff9ac4cf \(ami-5e334b36 in USE-1\)

* Added MOUNT\_FROM\_HOST to mount the host filesystem into the docker container. [More info.](/data-stores.html) [Thanks Brandl!](https://github.com/octohost/octohost/issues/68)
* Update to Docker 1.4.1.
* Update to Consul 0.4.1.
* Add Docker repository in a more reliable way.
* Change to newer Consul cookbook.
* Merge [bugfix](https://github.com/octohost/octohost/pull/74) from lorello.
* Set memory limits on containers via [config env](https://github.com/octohost/octohost/commit/72cfb2ed350c4d0628dcfe02b0a7240bf549518c).
* Move all octohost config vars into an `/octohost/` prefix - will allow better integration with larger Consul clusters and allow ACL for `/octohost/` prefix.
* Add ability to specify a custom check for the registered service in `/octohost/$service/CUSTOM_CHECK` - [Commit](https://github.com/octohost/octohost/commit/73f5b175d0248402584cb0ec7384c50901940009) and [screenshot](http://shared.froese.org/2014/bk3bu-15-12.jpg)
* Add ability to specify a custom check interval for the registered service in `/octohost/$service/CUSTOM_CHECK_INTERVAL` - [Commit](https://github.com/octohost/octohost/commit/e2b1172bb8ba2e95fd3d928a2ada1f8b426e48e8)
* Removed octoconfig and added Consul Template for configuration files.
* Added a [Consul Watch](http://www.consul.io/docs/agent/watches.html) to update the nginx config whenever a container changes state. [Consul Watch Template](https://github.com/octohost/octohost-cookbook/commit/a8970569764acf523ea5a98f4bbee198c89c4078)
* `octo reload {container}` now starts the number of containers specified in `/octohost/{container}/CONTAINERS`
* ~~Consul KV watch now reloads the container when anything changes inside of the KV space.~~ Disabled because of: [![hashicorp/consul/issues/571](https://github-shields.cfapps.io/github/hashicorp/consul/issues/571.svg)](https://github-shields.cfapps.io/github/hashicorp/consul/issues/571)
* Cleaned up stray services when stopping a container. [Commit](https://github.com/octohost/octohost/commit/1f8ad3c474130d38084922862f16816dd6f15dca)
* If you're using the vagrant box, it installs the Consul web interface at [http://consul.octodev.io](http://consul.octodev.io)

## 1.2.1 - ami-ef81c0df - \(ami-dc6cc1b4 in USE-1\)

* Adjusted `octo move sites` to `octo move:sites`.
* Added `octo move:config` to grab config from `octo config:export` on remote server and import locally. Great when you're moving / replacing an octohost.
* Added `octo move:redis to grab Redis keys from remote Redis server.`
* Update to Consul 0.4.
* Bugfix from [Brandl](https://github.com/octohost/octohost/pull/67).

## 1.2 - ami-075e1837 - \(ami-7265c31a in USE-1\)

* Update to [Docker 1.2.0](https://blog.docker.com/2014/08/announcing-docker-1-2-0/).
* Fully patched and updated with all current Ubuntu updates.
* The `octo` MySQL plugin will no longer try and restart [non-running containers](https://github.com/octohost/mysql-plugin/commit/a4c6bb0add8c37064ed6e716ec9addb89368c38f).
* Added [nsenter and docker-enter](https://github.com/darron/docker-cookbook/commit/f48a83edf9918550bc4b43081ef5d6886b899172) to default install.
* Added additional folder for [manually configured nginx virtualhosts](https://github.com/octohost/octohost-cookbook/commit/294b06f6692bc8ac11be388cf6afa0a1e355bb88). A great way to expose [Consul](http://www.consul.io) if you'd like to. [Example nginx config here.](https://gist.github.com/darron/3f19aa600abf044b3918)
* Don't have to use Xip.io if [you don't want to](https://github.com/octohost/octohost/commit/41c8d045b14788436ce4c236da12e1c7f5dbf67d).
* Added more commonly used `octo -h` and `octo --help`.
* Added [LINK_PREFIX to make URLs configureable](https://github.com/octohost/octohost/commit/d8ab8c5253bff2f1c4cbc445d132853ab0227126).
* Use sudo wherever it's needed. [Don't have to be root](https://github.com/octohost/octohost/commit/0e80005e86a591d2dc8b9e5bca06b8414b3b0dfe).
* Only [register services that match](https://github.com/octohost/octohost/commit/86456265412bb966029fb13ce77891161fb3f3bf) $BUILD_ORG_NAME.
* Fixed `octo services:clear` to [properly clear out the services](https://github.com/octohost/octohost/commit/0c9a5737e4270507e253a5fd079038865aeef6c4) from abnormally stopped containers.
* Fixed the `octo status` command to use Consul health check data rather than an additional, unreliable http check.
* Handle environment variables with spaces in them. Thanks to [Bruno Lara Tavares](https://github.com/bltavares).

## 1.1 - ami-cd1b62fd

* Docker 1.1.1
* Moved from etcd to Consul for ENV variables.
* Registered each container as a Consul Service - using `octo service:set`
* Automatically tag all Consul Services with 'http' unless they're set in the CONSUL_TAGS ENV variable. Used the same ENV variable as [docksul](https://github.com/progrium/docksul).
* Show all Consul Services on a host - using `octo services`
* Show all Consul Services in the cluster - using `octo services:catalog`
* Added `octo start|stop` that work on containers with source and images you have built but haven't "pushed" over.
* `octo start {container} {n}` will launch n number of containers.
* Added `octo reload` to stop and start images while reloading any ENV vars and configuration.
* Clarified `octo restart` - it rebuilds from source and restarts the container.
* Added `octo config:env` - to grab ENV vars from Consul.
* If the 'http' tag is present, add a simple http health check.
* Added the ability to [push and pull from a private registry](https://github.com/octohost/octohost/blob/master/bin/octo#L541-L567).
* If PRIVATE_REGISTRY is defined, [push the image automatically after you're done building](https://github.com/octohost/octohost/blob/master/bin/receiver.sh#L138-L141).
* Building nginx configuration from Consul Service information. [octoconfig does this](https://github.com/octohost/octoconfig)
* Integrating multiple containers with nginx. octoconfig generates config files that use all active containers.
* For a container called 'website' if you set an environment config `octo config:set website/CONTAINERS 5` it will launch `5` containers for you on each push. It launches the new containers first, then kills the old containers one by one.

## 1.0 - ami-6799eb57

* [Docker 1.0](http://blog.docker.com/2014/06/its-here-docker-1-0/)
* Add `octo config:export` command to [get all config variables](https://github.com/octohost/octohost/commit/7849e530cc80149d05b004c94bcc49bec49ecd47). Can update with `octo update`

## 0.12 - ami-8d6416bd

* [Docker 0.12](https://github.com/dotcloud/docker/blob/master/CHANGELOG.md)
* Updated Ubuntu with OpenSSL CCS patched.
* Added apparmor and apparmor-utils - seems to help with strange Apparmor problems on Ubuntu 14.04LTS.
* Containers without any exposed ports weren't able to launch properly. [Now they are.](https://github.com/octohost/octohost/commit/747ec50bbaef5ab3622d1a3e70d61cf874acd07b)
* Containers without any exposed ports weren't killing the old container. [Now they are.](https://github.com/octohost/octohost/commit/7afd14a75ec9a13ef8c23258d39849c811d04004)
* Add updated etcd init file so that it starts from scratch the next time it boots.

## 0.11.1.1 - ami-cd1161fd

* Added `octo update` to pull the current octo command down from github.
* Added help text when you run `octo help`
* Got Rackspace support working properly with PRIVATE_IP and firewall.
* Added `knife solo` support - `rake knife_solo user=root ip=555.55.555.5` - works on Linode - should work on others.
* Fix quoting when adding config variables with [Bash special characters](https://github.com/octohost/octohost/commit/0752bfec0af38947c38b1544ce0bee18b8608261).
* Added [PHP5-Apache](https://github.com/octohost/php5-apache).
* Added [WordPress](https://github.com/octohost/wordpress).
* Added [logspout](https://github.com/progrium/logspout) - controlled through `octo logspout {pull|start|stop}` - [commit](https://github.com/octohost/octohost/commit/855e26ec1c69808e1a1c1d04c644f0b8eecde339)
* Added 'octo logs start {full-path} {mount}' - to send any logs to remote syslog through [octohost/remote_syslog](https://github.com/octohost/remote_syslog)
* Added 'octo logs octologs:start|octologs:stop' - sends nginx and Docker logs to remote syslog in a single container.
* Re-added [package that enables AUFS](https://github.com/darron/docker-cookbook/commit/23d3af52bf1af46d5661c53ad6dad4ece7b99e23) instead of Devicemapper for 14.04.
* Added the first pass at a plugin system for the 'octo' command. A [mysql](https://github.com/octohost/mysql-plugin) plugin is first - it doesn't really do anything yet.
* Fixed the [@digitalocean](https://www.digitalocean.com/) build by [updating the nginx.conf](https://github.com/darron/fpm-recipes/commit/79f5b4d6a7fb569f96f0fc47576072cc1dc2188c) inside the openresty deb file. Thanks to [@philholden](https://github.com/philholden) for filing the bug and taking a stab at fixing it.

## 0.11.1 - ami-7d2a5c4d

* Update to Docker 0.11.1.
* Update to [Ubuntu 14.04LTS](https://github.com/octohost/ubuntu-14.04).
* Added SPDY support to nginx.
* Added GeoIP databases and configure geoip module.
* Added additional [open file caching and headers](https://github.com/octohost/octohost-cookbook/commit/49381260b7505aadc5be4ae0fb3120a32bdd0ef3).
* Added X-Request-Id header to nginx. Using [ngx_txid](https://github.com/octohost/ngx_txid).
* Added firewall to non EC2 builds. Ports 22, 80 and 443 are open by default.
* Add hostname and X-Request-Id to proxy logs.
* Extract duplicated variables from /usr/bin/octo and /home/git/receiver to /etc/default/octohost.
* If Docker [doesn't launch the container, try again](https://github.com/octohost/octohost/commit/d3b95255f279c4e8e4a867fd23b58bc32ca43866).

## 0.10.0.1 - ami-8ca8c2bc - \(ami-e5cfd18c in USE-1\)

* Actually use proper Heartbleed proof base image. If you are using 0.10.0 or 0.9.1.1 - please 'apt-get update; apt-get upgrade; service hipache restart' to be sure.
* Remove Hipache and add nginx as the proxy. Massive speed increases \(15x on an m3.medium\). Huge thanks to [@alkema](https://github.com/alkema) who made this happen.
* Enable PFS for nginx SSL.
* Addition of `octo proxy:get`, `octo proxy:set` and `octo proxy:rm` for nginx config paves the way for clusters of octohosts.
* Move nginx virtualhost api interface to [octohost/tentacles](https://github.com/octohost/tentacles) container.
* Add `octo tentacles` command to start|stop|pull octohost/tentacles container.
* Addition of gzip encoding to outbound proxied requests.
* Addition of [ngxtop](https://github.com/lebinh/ngxtop).
* Actually install [sysdig](https://github.com/octohost/octohost-cookbook/commit/380402075c5f45955299a816387247511a6e81de).
* Removed Serf - not being used.
* Made Redis listen on all interfaces so that it can be used from the octohost/tentacles container.
* Update Docker cookbook to fix startup workarounds. \(Had to re-add the workarounds.\)

## 0.10.0 - ami-86fc97b6

* Update to [Docker 0.10](https://github.com/dotcloud/docker/blob/release-0.10/CHANGELOG.md).
* Update bin/octo to [look for proper name](https://github.com/octohost/octohost/commit/e99572058e87f4c3cd1bd4fc6ca5243a4b340848).

## 0.9.1.1 - ami-ba8be08a

* Update base Ubuntu VM with all updates and patches.
* Added [Sysdig](http://www.sysdig.org/)
* Updated OpenSSL that fixes [Heartbleed](http://heartbleed.com).

## 0.9.1 - ami-e29ff7d2

* Update to [Docker 0.9.1](https://github.com/dotcloud/docker/commit/d36176652ef8f0220a1cff5dc00933400c69a562#diff-4ac32a78649ca5bdd8e0ba38b7006a1e)
* [Add](https://github.com/octohost/octohost-cookbook/commit/a934ce86eeaa8f2fc66713d6fa8f7d4fb110ccb0) vagrant, ubuntu and git users to docker group - so that they can do things without sudo.
* [Fix](https://github.com/octohost/octohost/commit/0e3aa7f838e92139ef13a8b15b331baf1f93752e) `octo config` to properly cut apart strings with : characters in them.
* Rename images built on octohost with different prefix '[octoprod](https://github.com/octohost/octohost/commit/a48a3fdc8af62448088619059084ac5ea466714a)' so that you can't accidentally overwrite a base image.
* Clean up some octo commands so that you can run them as a non-root user.
* Add some more output to deploys so that you can see ENV vars are being added.
* Add [CONTAINER_NAME as an ENV var](https://github.com/octohost/octohost/commit/0c768f547740665429c41f7eec783ccfc8204931) inside each container.
* Added [HHVM](https://github.com/octohost/hhvm) and [Hack](https://github.com/octohost/hack) containers - [example site](https://github.com/octohost/hack-example-site)
* If you're using etcd and want to transfer your env vars over, you'll need to make sure to setup the cluster properly. Take a look [here](https://github.com/octohost/octohost/blob/master/user-data-file/setup).

## 0.9 - ami-da3c52ea

* Update to Docker 0.9.
* Added [etcd](https://github.com/coreos/etcd) for environment variables.
* Addition of [octo config](/octo-cli.html) - etcd backed environment variables injected at runtime.
* Removal of container memory stats - the LXC source of those stats was removed as default.

## 0.8.1 - ami-68dcb158

* Update to Docker 0.8.1.
* Sped up all sorts of containers using nginx and static content.
* Looking at replacing Hipache with nginx. Work in progress - likely not for this release.
* Working on a way to setup, provision and link a persistent data store to your pushed applications with "magic comments" in your Dockerfile. [More detail here](/data-stores.html). [Redis](/data-stores-redis.html) and [Memcached](/data-stores-memcached.html) are working. Still very experimental.
* Make `octo clean` [not remove](https://github.com/octohost/octohost/commit/ba875ccfb55110409da69c718b7cb94edde3b55c) stopped "\_data" containers.

## 0.8 - ami-2a80e31a

* Add container [memory usage](https://github.com/octohost/octohost/commit/4e9276d8ea2efa7e0203637ef86f15e5a5fe542d) to `octo status` listing.
* Fix Docker running out of file descriptors at [203 containers](https://github.com/darron/docker-cookbook/commit/3038d964e0afc63745b925f64586c641dee707ea).
* [Add Google DNS](https://github.com/darron/docker-cookbook/commit/1872e8302b799147e45c9615c17736264d255084) to Docker containers.
* Update to [Docker 0.8](http://blog.docker.io/2014/02/docker-0-8-quality-new-builder-features-btrfs-storage-osx-support/).
* Update to newer [Base Image](https://github.com/octohost/ubuntu-12.0.4-3.8).
* Added SSL snake-oil cert as standard. \(You need to replace the key and cert in /etc/hipache/\)
* Fix octo restart to pass branch along.

## 0.7.6 - ami-78eb8a48

* Update to Docker 0.7.6.
* Make sure to [remove Hipache route](https://github.com/octohost/octohost/commit/b5d23f433812f3cf9ce2b5cc19770d668d0889a1) when `octo remove` is used.
* [Comment out unused PUBLIC_IP](https://github.com/octohost/octohost-cookbook/commit/49a6a01528dece21104b7ab7d00c44471073c095) when building Vagrant.
* Added proper [OpenJDK7](https://github.com/octohost/openjdk7) and [Play Framework](https://github.com/octohost/play-app).
* Added [Vagrant build](https://github.com/octohost/octovagrant) for local development.
* Added [octodev.io](http://octodev.io/) wildcard dns for local development.
* Added a more consistent [Ubuntu base cookbook](https://github.com/darron/ubuntu_base-cookbook) to the build.
* Added [sysstat](https://github.com/darron/octobase-cookbook/commit/c32167fe8fb044af52c9689caae0efef7fbac152) for some basic server metrics.
* [Properly set en_US.UTF-8](https://github.com/darron/ubuntu_base-cookbook/commit/dbd45aefd79d3c67af01fc886ca9c67cf6ee57e8) as the locale.
* Set [ulimit](https://github.com/darron/octobase-cookbook/commit/6def19aca5abe4e74fbaeba1c55ff3a20d7f7cf0) to 64000 open files.
* Remove old init file and properly use [/etc/default/docker](https://github.com/darron/docker-cookbook/commit/77309c615a848173ad4db3ba110e6bfe3fd0979c).
* Remove the extra .git from the [branch url](https://github.com/octohost/octohost/commit/83ec3c690faed7f3c9abfbadc0f9e043b384f95b).

## 0.7.5 - ami-7e29484e

#### Changes

* Update to [Docker 0.7.5](https://github.com/dotcloud/docker/blob/c348c04fdfb00e013be9db15d37728e04fb94b76/CHANGELOG.md)
* Added [Perl](https://github.com/octohost/perl), [Perl Dancer](https://github.com/octohost/perldancer-app), [Mojolicious](https://github.com/octohost/mojolicious-app) and [Slim](https://github.com/octohost/slim).

## 0.7.3 - ami-48147278

#### Changes

* Update to [Docker 0.7.3](https://github.com/dotcloud/docker/blob/8502ad4ba7b5410eb55f3517a801b33f61b1f625/CHANGELOG.md)
* Added [Ruby 2.1](https://github.com/octohost/ruby-2.1.0), [Ramaze](https://github.com/octohost/ramaze), [Erlang](https://github.com/octohost/erlang), and [Sails.js](https://github.com/octohost/sails)

## 0.7.2 - ami-74690e44

#### Changes

* Update to [Docker 0.7.2](https://github.com/dotcloud/docker/blob/master/CHANGELOG.md)
* Built with new Chef based [octohost-cookbook](https://github.com/octohost/octohost-cookbook)

## 0.7 - ami-2c187c1c

#### Changes

* Updates to Node.JS, Ruby and Go base containers. Lots of small updates to frameworks.
* Updated [base image](https://github.com/octohost/ubuntu-12.0.4-3.8)
* If you push a branch other than master, the domain name is suffixed with -branch.
* If `docker build` fails, stop the rest of bin/receiver.sh
* Always register Xip.io domain names tied to IP address.

## 0.6 - ami-5c5e3b6c

#### Changes

* Update to [Docker 0.7.1](https://github.com/dotcloud/docker/blob/v0.7.1/CHANGELOG.md)
* Fixed a bug when --force pushing that wouldn't update the site.
* Added image cleaning to `octo clean`
* Removed Shipyard.
* Updated to Serf 0.3 - fixed bug with event handler discovery.
* Fixed some bugs with octo cli.

## 0.5 - ami-e876ecd8

#### Changes

* Update to Docker 0.7
* Add `/usr/bin/octo move` to pull sites from old octohost and relaunch them.
* Allowing SSH Forwarding and keeping SSH\_AUTH\_SOCK so that `/usr/bin/octo move` works.
* Added way to install ssh keys and configure domain name at AMI launch using [user-data](https://github.com/octohost/octohost/blob/master/user-data-file/setup).
* Added `/usr/bin/octo remove $site` - removes active sites / repos.
* Added [documentation](https://github.com/octohost/octohost/blob/master/docs/octo-cli.md) around octo cli.
* Add `/usr/bin/octo clean` - removes old stopped containers.

## 0.4 - ami-da910bea

#### Changes

* Add /usr/bin/octo status|restart - shows status and can reload from an uploaded git repo.
* Update to Serf 0.2.1
* Don't build without a Dockerfile.
* Add a little better error checking on push.
* Added Lynx.

## 0.3 - ami-06fa6036

#### Changes

* Update to Docker 0.6.7.
* Remove apt-get upgrade which was causing Ansible service module requests to fail.

## 0.2 - ami-26d84216

#### Changes

* Make curl in receiver always return IPv4.

## 0.1 - Removed.

* First release.
