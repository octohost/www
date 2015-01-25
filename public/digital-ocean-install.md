## Digital Ocean Install

1. Sign into the Digital Ocean dashboard and click on [Apps &amp; API](https://cloud.digitalocean.com/settings/applications).

2. Create a read and write Personal Access Token.

3. Set that token as an Environment Variable:

  `export DIGITALOCEAN_API_TOKEN="really-long-string-that-i-am-not-going-to-actually-show"`

4. Make sure you have [Packer](https://www.packer.io/downloads.html) installed. You should see similar output:

  ```
  octohost$ packer version
  Packer v0.7.5
  ```

5. Download the main cookbook:

  `git clone https://github.com/octohost/octohost-cookbook.git`

6. Install the [Chef Development Kit](https://downloads.chef.io/chef-dk/):

  Add the ChefDK to your path:

  `eval "$(chef shell-init bash)"`

7. Install the cookbooks we require:

  ```
  cd octohost-cookbook
  berks install # Sample output: https://gist.github.com/anonymous/90f895eca5fbbe731303
  ```

8. Build the "image" on Digital Ocean.

  ```
  cd octohost-cookbook
  berks vendor vendor/cookbooks
  packer build -only=digitalocean template.json
  ```

  If it all goes well, you should see output something like this:

  ```
  digitalocean: Running handlers:
  digitalocean: Running handlers complete
  digitalocean: Chef Client finished, 159/195 resources updated in 252.479616963 seconds
  ==> digitalocean: Gracefully shutting down droplet...
  ==> digitalocean: Creating snapshot: octohost-chef 1422149922
  ==> digitalocean: Waiting for snapshot to complete...
  ==> digitalocean: Destroying droplet...
  ==> digitalocean: Deleting temporary ssh key...
  Build 'digitalocean' finished.

  ==> Builds finished. The artifacts of successful builds are:
  --> digitalocean: A snapshot was created: 'octohost-chef 1422149922' in region 'New York 3'
  ```

  You should see an 'octohost-chef' image on your [Images](https://cloud.digitalocean.com/images) page. It should look something like this:

  <img src="http://shared.froese.org/2015/octohost-do-image.png" border="0" />

9. In order to use octohost you need to create a droplet using the image Packer just built for you. We will add some "User data" to help setup Consul for your new octohost. [Create a droplet](https://cloud.digitalocean.com/droplets/new) - give it a name, pick any size 1GB or above, select the same region and scroll down to here:

  <img src="http://shared.froese.org/2015/octohost-do-userdata.png" border="0" />

  Click on "Enable User Data" and enter the following:

  ```
  #!/bin/bash

  export PUBLIC_IPV4=$(curl -s http://169.254.169.254/metadata/v1/interfaces/public/0/ipv4/address)
  export CONSUL_KEY=$(consul keygen)
  service consul stop
  rm -rf /var/cache/octohost/*
  sudo cat > /etc/consul.d/default.json << EOL
  {
    "data_dir": "/var/cache/octohost",
    "server": true,
    "bootstrap": true,
    "client_addr": "0.0.0.0",
    "advertise_addr": "$PUBLIC_IPV4",
    "datacenter": "dc1",
    "node_name": "octohost",
    "enable_syslog": true,
    "encrypt": "$CONSUL_KEY"
  }
  EOL
  service consul start
  ```

  Click on "My Snapshots" and select the image you just built, then select your SSH key if you've got one setup.

  Click "Create Droplet" and wait approximately 60 seconds.

10. You should now have an octohost running on DigitalOcean. Let's add your key to the octohost:

  ```
  octohost$ cat ~/.ssh/id_rsa.pub | ssh -i ~/.ssh/id_rsa root@ip.address.here "sudo gitreceive upload-key your-name"
  0c:d6:da:7e:50:7d:45:cd:1a:4f:6b:57:96:7c:81:7b # Example output - won't be identical.
  ```

11. Now, let's try a simple html website to test that it's working:

  ```
  cd ~/Desktop
  octohost$ git clone https://github.com/octohost/html.git
  Cloning into 'html'...
  remote: Counting objects: 21, done.
  remote: Total 21 (delta 0), reused 0 (delta 0)
  Unpacking objects: 100% (21/21), done.
  Checking connectivity... done.
  octohost$ cd html/
  octohost$ git remote add octohost git@ip.address.here:html.git
  octohost$ git push octohost master
  ```

  The output at the end should look like this:

  ```
  remote: Successfully built 948b59533f91
  remote: Adding http://html.104.131.166.122.xip.io
  remote: Adding http://html.104.131.166.122.xip.io
  remote: Not killing any containers.
  remote: Your site is available at: http://html.104.131.166.122.xip.io
  remote: Your site is available at: http://html.104.131.166.122.xip.io
  ```

  When you visit the website - it should look like this:

  <img src="http://shared.froese.org/2015/octohost-do-html-site.png" border="0" />

Congratulations - you have successfully built and setup an octohost!
