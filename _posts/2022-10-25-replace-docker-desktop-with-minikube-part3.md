I get a new mac for work and its the fancy 64GB memory m1 mac book pro.  So I went to install minikube to setup
docker and found the setup won't work for m1 macs as VirtualBox doesn't run on them and I needed VirtualBox
to connect to the VPN resources.  Thats when I found colima and this article which actually lead to a better
setup: https://everythingdevops.dev/how-to-run-minikube-on-apple-m1-chip-without-docker-desktop/
