## Install via Chef-Solo

Contributed by [Steffen Müller](https://github.com/steffenmllr)

1. Install [Knife Solo](http://matschaffer.github.io/knife-solo/)
2. `knife solo init myPaas && cd myPaas`
3. `git clone https://github.com/octohost/octohost-cookbook.git site-cookbooks/octohost`
4. Add the dependencies to the Berksfile (Berkshelf doesn't do nested dependencies!):

```
site :opscode

cookbook 'ubuntu_base', git: 'https://github.com/darron/ubuntu_base-cookbook.git'
cookbook 'sysstat', git: 'https://github.com/retr0h/cookbook-sysstat.git'
cookbook 'octobase', git: 'https://github.com/darron/octobase-cookbook.git'
cookbook 'docker', git: 'https://github.com/darron/docker-cookbook.git'
cookbook 'redis', git: 'https://github.com/darron/redis-cookbook.git'
cookbook 'nodejs', git: 'https://github.com/darron/nodejs-cookbook.git'
cookbook 'hipache', git: 'https://github.com/darron/hipache-cookbook.git'
cookbook 'serf', git: 'https://github.com/darron/serf-cookbook.git'
cookbook 'gitreceive', git: 'https://github.com/darron/gitreceive-cookbook.git'
```

5. `echo '{"run_list":["octohost::default"]}' > nodes/YOUR.SERVER.IP.json`
6. Edit the `site-cookbooks/octohost/user-data-file/setup`. Make sure to run these commands after the build.
7. Run `knife solo bootstrap user@myip.com`
8. Once it's done, you'll need to add your keys to the git user: `cat ~/.ssh/id_rsa.pub | ssh root@IP "sudo gitreceive upload-key your-name-here"`

After that's setup - you can start to push sites to your octohost:

```
git clone https://github.com/octohost/harp.git
cd harp
git remote add octohost git@IP:harp.git
git push octohost master
```
