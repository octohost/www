## Data Stores

We want to make several data stores available through linked Docker containers:

1. Redis - working now.
2. MySQL - in progress.
3. Memcached
4. PostgreSQL

### Redis

Adding a Redis data store to an octohost website.

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
# ADD_A_NAME
```

The last two lines are important, they tell the [receiver script](https://github.com/octohost/octohost/blob/master/bin/receiver.sh) on octohost to:

1. Do not add an entry into the HTTP proxy - this won't be accessed from outside the machine.
2. Add a name [so that we can share the volume](http://docs.docker.io/en/latest/use/working_with_volumes/)

When that's pushed - you should see this:

```
[master] darron@~/Desktop/octo-data/darron_redis_data: git remote add dev git@server.octodev.io:darron_redis_data.git
[master] darron@~/Desktop/octo-data/darron_redis_data: git push dev master
Warning: remote port forwarding failed for listen port 52698
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 522 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
remote: Put repo in src format somewhere.
remote: Building Docker image.
remote: Base: darron_redis_data
remote: Nothing running - no need to look for a port.
remote: Uploading context  2.56 kB
remote: Uploading context 
remote: Step 0 : FROM          busybox
remote:  ---> 769b9341d937
remote: Step 1 : VOLUME        ["/var/lib/redis"]
remote:  ---> Using cache
remote:  ---> b463cd3af8c3
remote: Step 2 : CMD           ["true"]
remote:  ---> Using cache
remote:  ---> 5aa41fe755f9
remote: Successfully built 5aa41fe755f9
To git@server.octodev.io:darron_redis_data.git
 * [new branch]      master -> master
```

The data container is complete  - let's add the Redis application container:

```
FROM octohost/redis
EXPOSE 6379
# NO_HTTP_PROXY
# ADD_A_NAME
# VOLUMES_FROM
CMD ["/usr/bin/redis-server"]
```

Like the data container, there are a few important commented items at the end of the Dockerfile:

1. Do not add an entry into the HTTP proxy - this won't be accessed from outside the machine.
2. Add a name [so that we can link to this container](http://docs.docker.io/en/latest/use/working_with_volumes/)
3. Use the data only container we already created as the data storage location.

Here's what happens on the push:

```
[master] darron@~/Desktop/octo-data/darron_redis: git remote add dev git@server.octodev.io:darron_redis.git
[master] darron@~/Desktop/octo-data/darron_redis: git push dev master
Warning: remote port forwarding failed for listen port 52698
Counting objects: 15, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (10/10), done.
Writing objects: 100% (15/15), 1.21 KiB | 0 bytes/s, done.
Total 15 (delta 4), reused 0 (delta 0)
remote: Put repo in src format somewhere.
remote: Building Docker image.
remote: Base: darron_redis
remote: Nothing running - no need to look for a port.
remote: Uploading context  2.56 kB
remote: Uploading context 
remote: Step 0 : FROM octohost/redis
remote:  ---> 4e23407f3917
remote: Step 1 : EXPOSE 6379
remote:  ---> Running in 958ac09acc36
remote:  ---> 2a5c3ff1355e
remote: Step 2 : CMD ["/usr/bin/redis-server"]
remote:  ---> Running in b8f4195ab936
remote:  ---> a19c53c1abba
remote: Successfully built a19c53c1abba
remote: Port: 49153
To git@server.octodev.io:darron_redis.git
 * [new branch]      master -> master
 ```
 
 We've provisioned a Redis server and it's ready to go.
 
 ```
 [master] darron@~/Desktop/octo-data/darron_redis: redis-cli -h server.octodev.io -p 49153
 server.octodev.io:49153> SET 1 "This is a test."
 OK
 server.octodev.io:49153> SET 2 "octohost is pretty fun."
 OK
 server.octodev.io:49153> GET 1
 "This is a test."
 server.octodev.io:49153> GET 2
 "octohost is pretty fun."
 ```
 
 Now let's link up the final application container - here's the Dockerfile:
 
 ```
 FROM octohost/ruby-1.9
 ADD . /srv/www
 RUN cd /srv/www; bundle install --deployment --without test development
 # LINK_DATA redis
 EXPOSE 5000
 CMD ["/usr/local/bin/foreman","start","-d","/srv/www"]
 ```
 
 There's only 1 special comment in this Dockerfile. 
 
 On push, octohost links this container to the Redis container we created in step 2.