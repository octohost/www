## KEYS for octohost

You will need a repo with two required files in it’s root
`keys`
`gitreceive-keys`

The keys file is simply a list of keys just like an `authorized_keys` file that you’ll see in .ssh folders

To make an appropriate gitreceive line it needs to be of this format

`command="GITUSER=git /usr/bin/gitreceive run $USER $GITRECEIVEKEY",no-agent-forwarding,no-pty,no-user-rc,no-X11-forwarding,no-port-forwarding ssh-rsa $RSAKEY user@host`

FYI - I'm not even certain I labeled the above variables correctly.  Just a wild guess.
I had to install https://github.com/progrium/gitreceive
and had it create the necessary gitreceive line

In a programmers pursuit of ultimate laziness I have created a docker project that will do the above for you here:
https://github.com/joshuacox/gitreceivelinemaker

