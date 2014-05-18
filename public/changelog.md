## Changelog

### NOTE: All AMIs are in USW-2.

## Unreleased

* Added [usage instrutions](https://github.com/octohost/octohost/commit/bfa7c79b220b853b441313fdb24ca0a25d306265) to the octo tool.
* Added `octo update` to pull the current octo command down from github.
* Added help text when you just run `octo`
* Got Rackspace support working properly with PRIVATE_IP and firewall.
* Added `knife solo` support - `rake knife_solo user=root ip=555.55.555.5` - works on Linode - should work on others.
* Fix quoting when adding config variables with [Bash special characters](https://github.com/octohost/octohost/commit/0752bfec0af38947c38b1544ce0bee18b8608261).
* Added [PHP5-Apache](https://github.com/octohost/php5-apache).
* Added [WordPress](https://github.com/octohost/wordpress).
* Added [logspout](https://github.com/progrium/logspout) - controlled through `octo logspout {pull|start|stop}` - [commit](https://github.com/octohost/octohost/commit/855e26ec1c69808e1a1c1d04c644f0b8eecde339)
* Added `octo logs nginx:start` - send nginx logs to remote syslog through [octohost/remote_syslog](https://github.com/octohost/remote_syslog)
* Added `octo logs docker:start` - send Docker logs to remote syslog through [octohost/remote_syslog](https://github.com/octohost/remote_syslog)
* Added 'octo logs start {full-path} {mount}' - to send any logs to remote syslog through [octohost/remote_syslog](https://github.com/octohost/remote_syslog)
* Re-added [package that enables AUFS](https://github.com/darron/docker-cookbook/commit/23d3af52bf1af46d5661c53ad6dad4ece7b99e23) instead of Devicemapper for 14.04.

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
