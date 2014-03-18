## Install via Chef-Solo

Contributed by [Steffen Müller](https://github.com/steffenmllr)

1. Install [Knife Solo](http://matschaffer.github.io/knife-solo/)
2. `knife solo init myPaas && cd myPaas`
3. `git clone https://github.com/octohost/octohost-cookbook.git site-cookbooks/octohost`
4. Add the dependencies to the Berksfile (Berkshelf doesn't do nested dependencies!):
5. `cp site-cookbooks/octohost/Berksfile Berksfile`
6. Go into your `Berksfile` and remove the line `metadata`
7. `echo '{"run_list":["octohost::default"]}' > nodes/YOUR.SERVER.IP.json`
8. Edit the `site-cookbooks/octohost/user-data-file/setup`. Make sure to run these commands after the build.
9. Run `knife solo bootstrap user@myip.com`
10. Once it's done, you'll need to add your keys to the git user: `cat ~/.ssh/id_rsa.pub | ssh root@IP "sudo gitreceive upload-key your-name-here"`

After that's setup - you can start to push sites to your octohost:

```
git clone https://github.com/octohost/harp.git
cd harp
git remote add octohost git@IP:harp.git
git push octohost master
```
