---
layout: post
title: Setting up arch linux!
---

I got a new laptop couple of weeks back and spent almost 2 days setting up Arch Linux (yeah, it's my first time and am still a noob!) but it was completely worth it.
[Arch wiki](https://wiki.archlinux.org/index.php/) is a great place to start since they have the answers to almost everything. Installation is generally strightforward if you follow the instructions in the [installation guide](https://wiki.archlinux.org/index.php/Installation_guide) properly.
Since I did not have an ethernet cable to connect to internet (step 3 in above instructions), I needed to use the wifi to install the packages. This is where I got stuck for quite a while figuring out how to connect to Georgia Tech student wifi. So here I am explaining it.

I ended up using [netctl](https://wiki.archlinux.org/index.php/netctl) package for connecting to the wifi. `netctl` lets you configure multiple profiles each of which are stored in `etc/netctl` directory. In my case I'm going to create a new profile called `GTwifi` with
`touch /etc/netctl/GTwifi`.
As an alternative you could create one by selecting one of the available networks interactively using 
`wifi-menu -o`.
Note that this would create a file called `/etc/netctl/<ssid_of_your_network>`.

Now we need to configure the `GTwifi` profile by editing the file. In my case I needed to setup a *wpa-enterprise* connection, so I copied the boilerplate configuration from  `/etc/netctl/examples/wireless-wpa-configsection` to `/etc/netctl/GTwifi`.

    cat /etc/netctl/examples/wireless-wpa-configsection > /etc/netctl/GTwifi

*Check out the `/etc/netctl/examples/` directory for other configuration examples.*

Now to modify the profile to suit GaTech wifi settings.
    
    nano /etc/netctl/GTwifi

Here's the modified config file for GTwifi.

    Description='GTwifi'
    Interface='wlp1s0'
    Connection='wireless'
    IP='dhcp'
    Security='wpa-configsection'
    WPAConfigSection=(
    'ssid="GTwifi"'
    'key_mgmt=WPA-EAP'
    'eap=PEAP'
    'anonymous_identity="asyouwish"'
    'identity="yourgatechlogin"'
    'password="yourpassword"'
    'phase2="auth=MSCHAPv2"'
    )

**NOTE** that `wlp1s0` is the name of my wireless interface - remember to replace it with the one on your pc. You can find yours with 
    
    iw dev 

Now restart GTwifi (just in case `netctl` tried running it already).

    netctl stop GTwifi
    netctl start GTwifi

You should now be connected to GTwifi and be able to access the internet.
Check your connection status with
    
    iw wlp1s0 link

It should say connected.

Check if you can access the internet with
    
    ping www.google.com