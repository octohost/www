## octo cli

The `octo` command controls the octohost and is your main interface to the system.

It:

1. Show basic app `status` - does a `curl http://{ip.add.res.s}:{port}`
2. `clean` up unused and stopped containers.
3. Allow you to `stop`, `start`, `restart`, `reload` and `remove` containers.
4. `move` an entire octohost's sites over to a new octohost. Immutable infrastructure FTW!
5. `push` and `pull` images from a private Docker registry.
6. Display, set, get, remove and export configuration variables and service information that is available to your containers.
7. Update the proxy configuration files with all configuration data inside Consul.
8. Send your container and daemon logs offsite into a remote syslog server.
9. Create mysql users, databases and link the configuration data to a container.
10. `update` itself from the current repository.


### `octo help`

```
  octo status                              Show basic app status: OK or DOWN.
  octo clean                               Remove unused and stopped containers.
  octo start {image}                       Start a {container} from an already built image.
  octo stop {container}                    Stop all {containers} that match.
  octo restart {container}                 Rebuild and restart {container} from src stored in $SRC_DIR.
  octo reload {container}                  Reload {container} without a rebuild.
  octo remove {container}                  Remove {container}, the src and the service entry.
  octo move sites {ip-address}             Move sites from targeted octohost, rebuilding all containers from src.

  octo push {image}                        Tag and push {image} to a private registry.
  octo pull {image}                        Pull and re-tag {image} from a private registry.

  octo config {container}                  Show all ENV variables for {container}.
  octo config:set {container/key} {var}    Set an ENV variable for {container}.
  octo config:get {container/key}          Get an ENV variable for {container}.
  octo config:rm {container/key}           Remove an ENV variable for {container}.
  octo config:export                       Get all config variables for all containers.
  octo config:proxy                        Update the proxy configuration from Consul data.

  octo domains:set {container} {domains}   Put list of comma separated domains into Consul for a {container}
  octo domains:get {container}             Get list of domains from Consul for a {container}

  octo service:set {container} {port}      Add service to Consul.
  octo service:rm {container} {port}       Remove a service from Consul.
  octo service:tags {container}            Get service tags from CONSUL_TAGS ENV in a container.

  octo services                            List all services that the local Consul agent knows about.
  octo services:catalog                    List all services that the Consul cluster knows about.
  octo services:clear                      Remove all services from Consul.
  octo services:register                   Register all running containers with Consul.

  octo logspout {pull|start|stop}          Pull/start/stop progrium/logspout image.

  octo logs octostart                      Use remote_syslog to send Docker and nginx logs to remote syslog.
  octo logs octostop                       Stop sending Docker and nginx logs to remote syslog.
  octo logs start {full-log-path} {mount}  Use remote_syslog to send {full-log-path} to remote syslog.

  octo update                              Update octo command from $OCTO_BIN.

  octo mysql:create {container}            Create MySQL user and database and link environment variables to {container}
  octo mysql:delete {container}            Delete MySQL user and database and link environment variables to {container}
```

Examples to come.

Bash helper function to come.
