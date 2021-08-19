I recently got the latest iPad Pro with the M1 chip and have really enjoyed the screen and using it as a second
monitor, but I really wanted to be able to write code on it in VS Code like my MacBook Pro.  There are several
articles on the web about how to do this, but I just wanted to document what worked for me at this date.

Install Raspberry OS to micro SD Card
---
Followed this page to install the raspberry pi os to the cd card: https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/2

Headless Setup of Raspberry OS
---
Then configured it headless by putting a blank ssh file at the root of the sd card (/Volumes/boot)

Also configured wifi by creating a wpa_supplicant.conf file, second link is what I used

```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
scan_ssid=1
ssid="your_wifi_ssid"
psk="your_wifi_password"
}
```

Docs:
* https://www.raspberrypi.org/documentation/computers/configuration.html#setting-up-a-headless-raspberry-pi
* https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html

Connect to Raspberry PI
---
After that I can ssh like so with the default password of `raspberry` `ssh pi@raspberry.local` the `.local` is required

Highly recommend changing the password with `passwd` after logging in

Update Raspberry PI
---
`sudo apt update && sudo apt upgrade` this updated the kernel or something so when installing docker and trying to run it I got an error.
A reboot fixed the issue so I highly suggest rebooting after this step, it boots up surprisingly quickly.

Install Docker
---
This is the point where most tutorials have you install code-server on raspberry pi, but I prefer not installing software and instead
running containers for software.

Run code-server container
---
I originally started creating a `Dockerfile` for code-server which came with its own problems.  Anything
over alpine 3.12 as a base image doesn't have working DNS on raspberry pi os because
https://gitlab.alpinelinux.org/alpine/aports/-/issues/12091  But after that it failed to install because it needs node and so I switched
to the node base image and then it failed because the latest was too new and it wanted version 14 so I switched to version 14 base image
of node and it succeeded but didn't end.  It was then that I discovered there is already a docker hub image for code-server so I switched
to that and I was up and running.  https://hub.docker.com/r/linuxserver/code-server
`docker run -d --name=code-server -p 8443:8443 ghcr.io/linuxserver/code-server`

Connect to code-server
---
In a browser on any machine on the network (iPad, macbook pro, etc) go to http://raspberrypi.local:8443 and you should see VS Code.

Setup git, etc in code-server container
---
In VS Code start a terminal.  Then you can configure git, put your ssh key in, clone repos, etc

ssh key goes in `/config/.ssh`

I setup the git config like mentioned on the docker hub page
```
git config --global user.name "username"
git config --global user.email "email address"
```

Next Steps
---
That so far is where I am at and it gets it going.  I'd really like to have flake8 and pylint working.
The container should also be set to restart if it stops.  Possibly also create `Dockerfile` with this code-server
as the base and do the config in it but keep it local since it will have my key, etc.
