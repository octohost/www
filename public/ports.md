## PORTS on octohost

### This is another "magic" Dockerfile comment.

Adding non-http/https assigned ports to an octohost container automagically.
The magic comment PORTS\_FROM\_HOST will assign ports from the host to the container.

e.g. to forward all traffic from port 9090 to the container add this line to your Dockerfile:

`# PORTS_FROM_HOST 9090 9090`

i.e.

`# PORTS_FROM_HOST X Y`

corresponds to an command line option of:

`-p 0.0.0.0:X:Y`
