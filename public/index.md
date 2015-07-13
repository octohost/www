## What is octohost?

Simple web focused [Docker](http://www.docker.io) based mini-PaaS server. `git push` to deploy your websites as needed.

## What is it used for?

Hosting any web site required by adding a Dockerfile to your app's source repository.

## What languages are supported?

We have more than 10 languages supported already, and many frameworks. Detailed information [available here](/languages.html).

## How do I use it?

There are several ways to get it:

1. An already prepared [Vagrant box](https://github.com/octohost/octovagrant). With [integrated wildcard dns](http://octodev.io) for easy reference.

2. An [Amazon AMI](https://github.com/octohost/octohost-cookbook) - [Installation Instructions](/aws-install.html)

3. Digital Ocean [Droplet](https://github.com/octohost/octohost-cookbook) - [Installation Instructions](/digital-ocean-install.html)

4. Rackspace [OpenStack Image](https://github.com/octohost/octohost-cookbook)

5. Build your own somewhere else using [Packer](http://www.packer.io) and a set of [Chef cookbooks](https://github.com/octohost/octohost-cookbook).

6. Build your own using `knife solo` - just need SSH access - [more info](/knife-solo.html). Works on [Azure](http://azure.microsoft.com/en-us/) too!

7. Terraform Code to launch on AWS, DigitalOcean and Rackspace - [more info](https://github.com/octohost/octohost-cookbook/tree/master/terraform)

## Advanced quickstart with AWS

These are the minimum amount of commands needed to get started:

```
ec2-run-instances --key your-key -g group-with-22-and-80-open ami-e8786e80 --region us-east-1
cat ~/.ssh/id_dsa.pub | ssh -i ~/.ssh/your-key.pem ubuntu@ip.address.here "sudo gitreceive upload-key ubuntu"
git clone https://github.com/octohost/harp.git
cd harp && git remote add octohost git@ip.address.here:harp.git
git push octohost master
```

If you want a [full explanation of those commands - take a look here](/aws-install.html).

## Any questions?
