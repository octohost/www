## CNAME on octohost

Adding an assigned domain name to an octohost website automagically.

This is easy enough, add a domain name to a file called CNAME to your root directory :

1. `echo my.example.com >> CNAME`
2. `git add CNAME`
3. `git commit -m “added my.example.com into CNAME”`
4. `git push octo master`
5. log into the octohost and `octo domains:get YOUR_PUSHED_REPO`
returns something similar to:
`YOUR_PUSHED_REPO.192.168.62.86.xip.io,my.example.com`

TADA! now your octohost responds to your assigned domain name
