## AWS Install

If the Quickstart on the [homepage](/) doesn't work - take a look here.

To Install Octohost
---------

1\. Build your AMI - clone the [octohost-cookbook](https://github.com/octohost/octohost-cookbook) repo and build using [Packer](http://www.packer.io/):

```
bundle install
berks install
rake build_ami
```

NOTE: The AMI in [template.json](https://github.com/octohost/octohost-cookbook/blob/master/template.json) has all of the proper required kernel extensions for Docker as well as [Chef](http://www.opscode.com/chef/) \(currently unused\) and [Ansible](https://github.com/ansible/ansible) provisioners. If you supply your own AMI, make sure it's got those items. You can rebuild build your own from [this repo](https://github.com/octohost/ubuntu-12.0.4-3.8).

2\. Create an AWS security group with port 80 and 443 open to the world, port 22 open to you and all ports open to other members of that group.

```
ec2-create-group -K your-key octohost -d "Octohost Group" --region us-west-2
ec2-authorize octohost -P tcp -p 80 -s 0.0.0.0/0 --region us-west-2
ec2-authorize octohost -P tcp -p 443 -s 0.0.0.0/0 --region us-west-2
ec2-authorize octohost -P tcp -p 22 -s 0.0.0.0/0 --region us-west-2
# Not totally required - but helpful with Serf.
ec2-authorize octohost -P tcp -p 0-65535 -o sg-groupid --region us-west-2
ec2-authorize octohost -P udp -p 0-65535 -o sg-groupid --region us-west-2
```

In the end - your group should look like this:

```
ec2-describe-group octohost --region us-west-2
GROUP	sg-groupid	0000000000	octohost	Octohost Group
PERMISSION	457992882886	octohost	ALLOWS	tcp	0	65535	FROM	USER	0000000000	NAME octohost	ID sg-groupid	ingress
PERMISSION	457992882886	octohost	ALLOWS	udp	0	65535	FROM	USER	0000000000	NAME octohost	ID sg-groupid	ingress
PERMISSION	457992882886	octohost	ALLOWS	tcp	22	22	FROM	CIDR	0.0.0.0/0	ingress
PERMISSION	457992882886	octohost	ALLOWS	tcp	80	80	FROM	CIDR	0.0.0.0/0	ingress
PERMISSION	457992882886	octohost	ALLOWS	tcp	443	443	FROM	CIDR	0.0.0.0/0	ingress
```

3\. Create a running instance using your AMI \(or use ami-ff9ac4cf\) and security group:

`ec2-run-instances --key your-key -g sg-groupid ami-yourAMI --user-data-file user-data-file/setup --region us-west-2`

_Make sure to edit_ the [user-data-file/setup](https://github.com/octohost/octohost-cookbook/blob/master/user-data-file/setup) with the correct information if you're:

1. Adding additional ssh keys during AMI creation.
2. Using your own domain name instead of the xip.io default.

You can safely leave that part out if you're not using the user-data-file.

4\. Once it's launched - visit that ip address with your web browser - it should say:

"No Application Configured - This domain is not associated with an application."

5\. Add your private key to gitreceive:

`cat ~/.ssh/id_dsa.pub | ssh -i ~/your-key.pem ubuntu@ip.address.here "sudo gitreceive upload-key ubuntu"`

6\. Now you can push one of our example repos:

```
git clone git@github.com:octohost/harp.git
cd harp
git remote add octohost git@ip.address.here:harp.git
git push octohost master
```

It will pull the base Docker container, build your repo and launch your site. The last "remote:" line should look like this:

`Your site is available at: http://harp.ip.address.here.xip.io`

7\. Visit that site:

http://harp.ip.address.here.xip.io

NOTE: Whatever you name your git repo is what the website URL is - for example:

`git remote add octohost git@ip.address.here:octohost-test.git`

would be located at:

http://octohost-test.ip.address.here.xip.io

8\. Take a look around at all of the frameworks and languages available at [https://github.com/octohost](https://github.com/octohost).

There's lots to do, this is nowhere near done - but it's working as the backend for a service of ours.

Got a change? Send us a pull request - we'll look at adding whatever is needed.

A few notes
--------

1. The key to octohost is the Dockerfile in the root of any repo. That's what determines how the site is built and runs.
2. Currently there is only a single exposed port working per container.
3. Only websites can be pushed via git - any additional services - Redis, MySQL, Postgresql, etc. will need to be built and installed on the server. We're using external managed MySQL and Memcache at the moment.
4. If you want to use your own domain name, just point a wildcard record to the server and edit DOMAIN_SUFFIX in [/etc/default/octohost](https://github.com/octohost/octohost/blob/master/bin/default).
5. If you want to add an additional domain name record for your website - add a CNAME text file to the root directory. Here's an [example file](https://gist.github.com/darron/7571573). If it's not a wildcard - make sure to point the DNS there - it won't work otherwise. You can set this at deploy using `--user-data-file user-data-file/setup` - be sure to edit that file with your domain name.

To Build the VM's and other Cloud Providers
--------

[Take a look at the README.](https://github.com/octohost/octohost-cookbook/blob/master/README.md)
