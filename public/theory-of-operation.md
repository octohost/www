## Theory of Operation

### Just the highlights.

1. `git push git@server.example.com` initiates a ssh connection to the `git` user on the octohost.

1. The sshd server looks for the ssh public key in `/home/git/.ssh/authorized_keys` - if there's no match, it denies access. To add the public key - `cat ~/.ssh/{public_key}.pub | ssh -i ~/your-private-key.pem ubuntu@ip.address.here "sudo gitreceive upload-key {your-name-here}"`

1. It runs the `command` defined in `/home/git/.ssh/authorized_keys` - which is normally: `/usr/bin/gitreceive run {name} {ssh:key:finger:print}`

1. [gitreceive](https://github.com/octohost/octohost/blob/master/bin/gitreceive) takes over and ends up [pushing the code](https://github.com/octohost/octohost/blob/master/bin/gitreceive#L73) through `git archive` to [/home/git/receiver](https://github.com/octohost/octohost/blob/master/bin/receiver.sh).

1. The receiver loads the [main octohost configuration file](https://github.com/octohost/octohost/blob/master/bin/default) by sourcing it. This configuration file is used by `/home/git/receiver` and `/usr/bin/octo`

1. The [receiver](https://github.com/octohost/octohost/blob/master/bin/receiver.sh) takes the bare repo it gets and [checks out a working repo](https://github.com/octohost/octohost/blob/master/bin/receiver.sh#L10) in `/home/git/src/$NAME.git`

1. If the git branch isn't master, [it will create a new container](https://github.com/octohost/octohost/blob/master/bin/receiver.sh#L14-L18) named `$NAME-$BRANCH` - if it's master, it's just named `$NAME`.

1. The receiver looks for [any old containers running](https://github.com/octohost/octohost/blob/master/bin/receiver.sh#L21) with the same name - it will use this information to stop it later. (We're not really using the information anymore, but it's the trigger that helps to know there's something to kill.)

1. The receiver looks for the [image_id of the current running image](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L23). This will help to kill old container images.

1. If there's a Dockerfile inside `/home/git/src/$NAME.git/` then it starts to build the container. Otherwise, we're done here.

1. It [looks inside the Dockerfile](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L28) for an `EXPOSE` command - this is used to run the container and figure out how to connect to it from the outside.

1. Then the Docker [container is built](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L30). If the build fails, it exits here.

1. There are a number of options that are needed to run any container, those are all gathered using the `/usr/bin/octo config:options` command [detailed here](https://github.com/octohost/octohost/blob/31bd0df31b4d3ede55bbdfeb0525803915b048b7/bin/octo#L286-L332).

1. Any config variables stored in Consul's KV storage [are also added to the $RUN_OPTIONS](https://github.com/octohost/octohost/blob/31bd0df31b4d3ede55bbdfeb0525803915b048b7/bin/octo#L271-L284). Config variables can be added with `/usr/bin/octo config:set $NAME/$KEY $VARIABLE`

1. Once we have all of the $RUN_OPTIONS, we can [actually run the container](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L40).

1. Once the container is running, we look for the [exposed port that's connected to the internal port](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L42-L46).

1. If there is no exposed HTTP port, then it [kills the old container and is complete](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L48-L64).

1. [This section will be removed](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L66-L84). It was to work around a specific Docker bug that should be fixed now.

1. Any [tags from the container are grabbed](https://github.com/octohost/octohost/blob/31bd0df31b4d3ede55bbdfeb0525803915b048b7/bin/octo#L362-L372) using `/usr/bin/octo service:tags` - and the [Consul service is created](https://github.com/octohost/octohost/blob/31bd0df31b4d3ede55bbdfeb0525803915b048b7/bin/octo#L334-L350) with `/usr/bin/octo service:set`

1. We [grab all of the possible domains](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L96-L121) and then [set them in Consul](https://github.com/octohost/octohost/blob/31bd0df31b4d3ede55bbdfeb0525803915b048b7/bin/octo#L401-L409) with `/usr/bin/octo domains:set`.

1. If you want to launch more than 1 container when you push, you can set a key/value in Consul at `$NAME/CONTAINERS` - we [check it now and launch more containers as needed](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L123-L131).

1. Any [old containers are killed](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L133-L139).

1. nginx is reconfigured, a new `/etc/nginx/containers.conf` is written and it is reloaded.

1. If you have a private registry defined in `/etc/default/octohost`, [then we push the new image there](https://github.com/octohost/octohost/blob/3ae5abbec0be46ecdbd357611c66392d4f4158fd/bin/receiver.sh#L146-L149).
