NMap IP Lookup
---

You can always `whois <ip>` to get all the information about an IP address
and even `dig <ip>` will get some information, but another really helpful
command is `nmap <ip> --script whois-ip` which will also tell you what
ports are open.  This helped me once when it would return open ports when
I was on the VPN but not when I was off the VPN.  I then knew the machine
was one of ours and not a random one so I was able to track it done.  NMap
output looks like below:
```
nmap ip.ip.ip.ip --script whois-ip
Starting Nmap 7.80 ( https://nmap.org ) at 2020-04-14 13:23 MDT
Nmap scan report for ec2-ip-ip-ip-ip.compute-1.amazonaws.com (ip.ip.ip.ip)
Host is up (0.086s latency).
Not shown: 996 filtered ports
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
3306/tcp closed mysql
3389/tcp closed ms-wbt-server

Host script results:
|_whois-ip: ERROR: Script execution failed (use -d to debug)

Nmap done: 1 IP address (1 host up) scanned in 7.94 seconds
```

To go even further you can geolookup the IP address which further helped
in my case because I then knew which region to look in.  You can use websites
to geolookup the IP or if you want to do it on command line you have to sign
up for a free account with maxmind so you can use `geoipupdate` and
`geoiplookup <ip>`.  I could not get this to work and it looks like it only
returns the country while this website requires no setup and returns the state
which made the region obvious when the stat was Virginia.

https://www.ultratools.com/tools/geoIpResult
