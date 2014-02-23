## Memcached on octohost
### NOTE: Released at 0.8.1.
#### Please send feedback to [Darron](mailto:darron@froese.org)

Adding a Memcached data store to an octohost website.

*TLDR: 2 git pushes - 2 linked single use containers.*

To make this happen, we will utilize 2 containers in total:

1. Memcached application container.
2. The web application container that is linked to the Memcached application container.

We use a simple naming convention to make the linking and volume sharing easier:

1. appname\_datastore - for the storage daemon.
2. appname - for the actual web application that links to the storage daemon.

This way - we always know what container name we're looking for when we're linking them up.

Let's add the Memcached application container:

```
FROM octohost/memcached
ADD memcached.conf /etc/memcached.conf
USER daemon
EXPOSE 11211
# NO_HTTP_PROXY
# ADD_NAME
CMD ["memcached", "-m", "32"]
```

There are a few important commented items at the end of the Dockerfile:

1. Do not add an entry into the HTTP proxy - this won't be accessed from outside the machine.
2. Add a name [so that we can link to this container](http://docs.docker.io/en/latest/use/working_with_volumes/)

Here's what happens on the push:

```
[master] darron@~/Dropbox/memcached_container: git remote add dev git@server.octodev.io:test_memcached.git
[master] darron@~/Dropbox/memcached_container: git push dev master
Counting objects: 11, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (11/11), 5.60 KiB | 0 bytes/s, done.
Total 11 (delta 2), reused 0 (delta 0)
remote: Put repo in src format somewhere.
remote: Building Docker image.
remote: Base: test_memcached
remote: Nothing running - no need to look for a port.
remote: Uploading context 17.92 kB
remote: Uploading context 
remote: Step 0 : FROM octohost/memcached
remote:  ---> cdbca89ea78d
remote: Step 1 : ADD memcached.conf /etc/memcached.conf
remote:  ---> Using cache
remote:  ---> fca182755319
remote: Step 2 : USER daemon
remote:  ---> Using cache
remote:  ---> c39fbd0799d1
remote: Step 3 : EXPOSE 11211
remote:  ---> Running in b8559f8343bc
remote:  ---> b08edf25611d
remote: Step 4 : CMD ["memcached", "-m", "32"]
remote:  ---> Running in 6493002c4ebc
remote:  ---> 6ca0d81c2ad9
remote: Successfully built 6ca0d81c2ad9
remote: Port: 49155
To git@server.octodev.io:test_memcached.git
 * [new branch]      master -> master
 ```
 
We've provisioned a Memcached server and it's ready to go.

Now let's link up the final application container - here's the Dockerfile:
 
 ```
 FROM octohost/ruby-1.9
 RUN apt-get update && apt-get install -y libsasl2-dev
 ADD . /srv/www
 RUN cd /srv/www; bundle install --deployment --without test development
 # LINK_SERVICE memcached
 EXPOSE 5000
 CMD ["/usr/local/bin/foreman","start","-d","/srv/www"]
 ```
 
There's only 1 special comment in this Dockerfile - on push, octohost links this container to the Memcached container we created in step 2. Let's try it:
 
 ```
 [master] darron@~/Dropbox/memcached_app: git push dev master
 Warning: remote port forwarding failed for listen port 52698
 Counting objects: 5, done.
 Delta compression using up to 8 threads.
 Compressing objects: 100% (3/3), done.
 Writing objects: 100% (3/3), 318 bytes | 0 bytes/s, done.
 Total 3 (delta 2), reused 0 (delta 0)
 remote: Put repo in src format somewhere.
 remote: Building Docker image.
 remote: Base: test
 remote: Uploading context 10.24 kB
 remote: Uploading context 
 remote: Step 0 : FROM octohost/ruby-1.9
 remote:  ---> 9bc0da4bad25
 remote: Step 1 : RUN apt-get update && apt-get install -y libsasl2-dev
 remote:  ---> Using cache
 remote:  ---> db5ea094e329
 remote: Step 2 : ADD . /srv/www
 remote:  ---> 382721008aba
 remote: Step 3 : RUN cd /srv/www; bundle install --deployment --without test development
 remote:  ---> Running in 2662ae1560c2
 remote: Fetching gem metadata from https://rubygems.org/.........
 remote: Fetching gem metadata from https://rubygems.org/..
 remote: Installing dotenv (0.9.0) 
 remote: Installing thor (0.18.1) 
 remote: Installing foreman (0.63.0) 
 remote: Installing memcached (1.7.2) 
 remote: Installing rack (1.5.2) 
 remote: Installing rack-protection (1.5.2) 
 remote: Installing tilt (1.4.1) 
 remote: Installing sinatra (1.4.4) 
 remote: Using bundler (1.3.5) 
 remote: Your bundle is complete!
 remote: Gems in the groups test and development were not installed.
 remote: It was installed into ./vendor/bundle
 remote:  ---> 459da9d79a5e
 remote: Step 4 : EXPOSE 5000
 remote:  ---> Running in b8a147738fda
 remote:  ---> db690947717e
 remote: Step 5 : CMD ["/usr/local/bin/foreman","start","-d","/srv/www"]
 remote:  ---> Running in 75aa6737640b
 remote:  ---> a44ac63898e6
 remote: Successfully built a44ac63898e6
 remote: Linking to: test_memcached:memcached
 remote: Adding http://test.192.168.62.86.xip.io
 remote: Adding http://test.octodev.io
 remote: Your site is available at: http://test.192.168.62.86.xip.io
 remote: Your site is available at: http://test.octodev.io
 To git@server.octodev.io:test.git
  * [new branch]      master -> master
```

And here's the live site - with a Memcached INCR counter: [http://memcached.octohost.io/](http://memcached.octohost.io/)

2 pushes - 2 containers:

1. [Memcached container running.](https://github.com/octohost/memcached_container)
2. [Small Sinatra app linked to the Memcached container.](https://github.com/octohost/memcached_app)

Take a look at the Sinatra code here: [https://github.com/octohost/memcached_app](https://github.com/octohost/memcached_app)