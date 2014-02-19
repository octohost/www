## Redis on octohost
### NOTE: This is still unreleased.
#### Please send feedback to [Darron](mailto:darron@froese.org

Adding a Redis data store to an octohost website.

*TLDR: 3 git pushes - three linked single use containers.*

To make this happen, we will utilize 3 containers in total:

1. Data only container to store the Redis dump.rdb file.
2. Redis application container that uses the volume from #1 to persist the data between container upgrades.
3. The web application container that is linked to the Redis application container.

We use a simple naming convention to make the linking and volume sharing easier:

1. appname\_datastore\_data - for the data storage container.
2. appname\_datastore - for the storage daemon.
3. appname - for the actual web application that links to the storage daemon.

This way - we always know what container name we're looking for when we're linking them up.

First, let's create the [data](http://www.tech-d.net/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) [only](http://www.offermann.us/2013/12/tiny-docker-pieces-loosely-joined.html) container.

Here's the Dockerfile:

```
FROM          busybox
VOLUME        ["/var/lib/redis"]
CMD           ["true"]
# NO_HTTP_PROXY
# ADD_NAME
```

The last two lines are important, they tell the [receiver script](https://github.com/octohost/octohost/blob/master/bin/receiver.sh) on octohost to:

1. Do not add an entry into the HTTP proxy - this won't be accessed from outside the machine.
2. Add a name [so that we can share the volume](http://docs.docker.io/en/latest/use/working_with_volumes/)

When that's pushed - you should see this:

```
[master] darron@/data: git remote add octo git@server.octohost.io:testing_redis_data.git
[master] darron@/data: gp octo master
Warning: remote port forwarding failed for listen port 52698
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 294 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: Put repo in src format somewhere.
remote: Building Docker image.
remote: Base: testing_redis_data
remote: Nothing running - no need to look for a port.
remote: Uploading context  2.56 kB
remote: Uploading context 
remote: Step 0 : FROM          busybox
remote:  ---> 769b9341d937
remote: Step 1 : VOLUME        ["/var/lib/redis"]
remote:  ---> Running in fedc60e7bec5
remote:  ---> 5a03e36a4866
remote: Step 2 : CMD           ["true"]
remote:  ---> Running in f0ba0503a3c6
remote:  ---> dcb61d3a1cf7
remote: Successfully built dcb61d3a1cf7
To git@server.octohost.io:testing_redis_data.git
 * [new branch]      master -> master
```

The data container is complete  - let's add the Redis application container:

```
FROM octohost/redis
WORKDIR /var/lib/redis
EXPOSE 6379
# NO_HTTP_PROXY
# ADD_NAME
# VOLUMES_FROM
CMD ["/usr/bin/redis-server"]
```

Like the data container, there are a few important commented items at the end of the Dockerfile:

1. Do not add an entry into the HTTP proxy - this won't be accessed from outside the machine.
2. Add a name [so that we can link to this container](http://docs.docker.io/en/latest/use/working_with_volumes/)
3. Use the data only container we already created as the data storage location.

Here's what happens on the push:

```
[master] darron@/server: git remote add octo git@server.octohost.io:testing_redis.git
[master] darron@/server: gp octo master
Warning: remote port forwarding failed for listen port 52698
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 310 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: Put repo in src format somewhere.
remote: Building Docker image.
remote: Base: testing_redis
remote: Nothing running - no need to look for a port.
remote: Uploading context  2.56 kB
remote: Uploading context 
remote: Step 0 : FROM octohost/redis
remote:  ---> 4e23407f3917
remote: Step 1 : EXPOSE 6379
remote:  ---> Running in 63558f94ef39
remote:  ---> 7005893a2eb5
remote: Step 2 : CMD ["/usr/bin/redis-server"]
remote:  ---> Running in a85c9ceee561
remote:  ---> 778ee46b9697
remote: Successfully built 778ee46b9697
remote: Port: 49240
To git@server.octohost.io:testing_redis.git
 * [new branch]      master -> master

 ```
 
We've provisioned a Redis server and it's ready to go.

Now let's link up the final application container - here's the Dockerfile:
 
 ```
 FROM octohost/ruby-1.9
 ADD . /srv/www
 RUN cd /srv/www; bundle install --deployment --without test development
 # LINK_SERVICE redis
 EXPOSE 5000
 CMD ["/usr/local/bin/foreman","start","-d","/srv/www"]
 ```
 
There's only 1 special comment in this Dockerfile - on push, octohost links this container to the Redis container we created in step 2. Let's try it:
 
 ```
 [master] darron@/app: git push octo master
 Warning: remote port forwarding failed for listen port 52698
 Counting objects: 9, done.
 Delta compression using up to 8 threads.
 Compressing objects: 100% (6/6), done.
 Writing objects: 100% (9/9), 1.09 KiB | 0 bytes/s, done.
 Total 9 (delta 0), reused 0 (delta 0)
 remote: Put repo in src format somewhere.
 remote: Building Docker image.
 remote: Base: testing
 remote: Nothing running - no need to look for a port.
 remote: Uploading context 8.192 kB
 remote: Uploading context 
 remote: Step 0 : FROM octohost/ruby-1.9
 remote:  ---> 9bc0da4bad25
 remote: Step 1 : ADD . /srv/www
 remote:  ---> e0ebe5e4cb78
 remote: Step 2 : RUN cd /srv/www; bundle install --deployment --without test development
 remote:  ---> Running in ee460166f37a
 remote: Fetching gem metadata from https://rubygems.org/..........
 remote: Fetching gem metadata from https://rubygems.org/..
 remote: Installing dotenv (0.9.0) 
 remote: Installing thor (0.18.1) 
 remote: Installing foreman (0.63.0) 
 remote: Installing rack (1.5.2) 
 remote: Installing rack-protection (1.5.2) 
 remote: Installing redis (3.0.6) 
 remote: Installing tilt (1.4.1) 
 remote: Installing sinatra (1.4.4) 
 remote: Using bundler (1.3.5) 
 remote: Your bundle is complete!
 remote: Gems in the groups test and development were not installed.
 remote: It was installed into ./vendor/bundle
 remote:  ---> b947b5e625bc
 remote: Step 3 : EXPOSE 5000
 remote:  ---> Running in d144ac1800c5
 remote:  ---> 3a4c2ffc1538
 remote: Step 4 : CMD ["/usr/local/bin/foreman","start","-d","/srv/www"]
 remote:  ---> Running in 1998c2793974
 remote:  ---> 0e4f5afbad3e
 remote: Successfully built 0e4f5afbad3e
 remote: Adding http://testing.54.244.116.169.xip.io
 remote: Adding http://testing.octohost.io
 remote: Not killing any containers.
 remote: Your site is available at: http://testing.54.244.116.169.xip.io
 remote: Your site is available at: http://testing.octohost.io
 To git@server.octohost.io:testing.git
  * [new branch]      master -> master
```

And here's the live site - with a Redis INCR counter: [http://testing.octohost.io/](http://testing.octohost.io/)

3 pushes - 3 containers:

1. [Data only container.](https://github.com/octohost/redis_data)
2. [Redis container running and using #1 for data storage.](https://github.com/octohost/redis_container)
3. [Small Sinatra app linked to the Redis container.](https://github.com/octohost/redis_app)

Take a look at the Sinatra code here: [https://github.com/octohost/redis_app](https://github.com/octohost/redis_app)