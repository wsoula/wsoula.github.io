Replace Docker Desktop with Minikube: Part 2
---

About a month ago I decided to try replacing docker desktop with minikube based
on an article I reposted on here.  While this process requires a little more
work than docker desktop, which offered a solution that felt like docker on
linux, I've mostly been able to get by with my day to day work.

The minikube post has follow ups by the other on how to handle mounts and now
I just start a process mounting `$HOME:$HOME` when I need to run a container
that uses my local file system.  And I have to make sure to run the eval
command so docker works.

The biggest issue has been when I use the hyperkit driver, because of its small
memory footprint, and need to connect to the work vpn.  I believe my work vpn
is taking over the CIDR range for the minikube vm and thus I can no longer
access it.  Docker Desktop uses hyperkit as well and they solve this problem
by using vpnkit.  The vpnkit binary can be hard to find as it is on circle ci
and those artifacts are deleted after 30 days:
https://github.com/moby/vpnkit/issues/380

Once I got the vpnkit binary and ran it the minikube start command would fail
with not a lot of output.  I finally found I needed to install the latest
hyperkit binary and then, at least, minikube would start.

First start vpnkit::
`Contents/Resources/bin/vpnkit --ethernet /tmp/vpnkit.socket --port /tmp/vpnkit.port.socket --diagnostics /tmp/vpnkit.diag.socket --vsock-path /tmp/connect --debug`

Then minikube pointing to the vpnkit socket:
`minikube start --profile vpnkit --kubernetes-version=v1.22.1 --driver=hyperkit --container-runtime=docker --hyperkit-vpnkit-sock=/tmp/vpnkit.socket`

But even with this setup, I could not connect to the minikube vm when on vpn.
So, for now, I have a virtualbox profile I use when I need to be on the vpn,
as that driver works fine on the vpn.  And then the default profile uses
hyperkit with no vpnkit.  And lastly, I created a profile for using vpnkit
so I can continue to try and figure this out.
