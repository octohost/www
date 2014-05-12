## Install via Chef-Solo

Initially contributed by [Steffen Müller](https://github.com/steffenmllr) - added to the project as a Rake task.

1. Clone the [octohost-cookbook](https://github.com/octohost/octohost-cookbook)
2. `cd octohost-cookbook && bundle install`
3. `rake knife_solo user=root ip=555.55.555.5` # Use your ip address.
4. Once it's done, you'll need to add your keys to the git user: `cat ~/.ssh/id_rsa.pub | ssh root@IP "sudo gitreceive upload-key your-name-here"`
5. `wget https://raw.githubusercontent.com/octohost/octohost/master/user-data-file/setup # Edit and run at least the last 2 commands.

After that's setup - you can start to push sites to your octohost:

```
git clone https://github.com/octohost/harp.git
cd harp
git remote add octohost git@IP:harp.git
git push octohost master
```
