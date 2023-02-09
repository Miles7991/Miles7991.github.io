---
title: Turn Your IPAD to The Screen of Raspberry Pi 
author: shiyu
date: 2023-02-08 10:00:00 -0600 
categories: [Tools]
tags: [Jekyll]
math: true
image:
  path: /postpic/20230208/ipadscreen.png
  width: 800
  height: 500
  alt: use IPad as the screen of Raspberry pi
---
In this post I will introduce how to use IPad to be the screen of Raspberry pi and how to use PI as code server so that you can use VScode on your IPad.

## Background
I guess you will have the same question when you see this post: Why do we want to use a cable connection rather than a WIFI connection which is easier to set up? The cable can give a more reliable connection between the IPad and Raspberry Pi. For example, last Christmas I have been to Cuba, where tourists cannot easily get Internet access, and you have to be in a specific area to use WIFI, and you also need an account to access it. So under this scenario, the cable connection can save much time. Next, I will introduce how to connect the iPad to the Pi via USB-C cable, power the Pi from the iPad, and create a network between the iPad and Pi so that the iPad can access Pi's system via SSH and Remote Desktop.


## Prerequisite 
1. IPad Pro
2. Raspberry Pi 4b
3. Two-sided type-C cable

## Set up the cable connection 
### Enable the Ethernet through USB-C on Raspberry pi
First, adding `dtoverlay=dwc2, dr_mode=peripheral` at the end of `/boot/config.txt`{: .filepath}, where `dw2` driver allow the Pi to switch the mode of its USB-C port from normal host mode, which can used to access downstream device like hard drive, to peripheral mode.
Next, loading the dwc2 driver by adding `modules-load=dwc2, g_ether` at the end of `/boot/cmdline.txt`{: .filepath}. 

### Create a network over the Ethernet connection 
Now we need to create a network interface on the usb-c port by creating a new file
```console
$ sudo nano /etc/network/interfaces.d/usb0
```
and adding
```
auto usb0
allow-hotplug usb0
iface usb0 inet static
  address 192.168.59.1
  netmask 255.255.255.248
```
the `netmask` can control the size of the network we're creating. 

### Configure Pi to hand out IP address to IPad.  

Now we need to install `dnsmaq` used to hand out IP address to IPad.
```console
$ sudo apt install dnsmasq
```
and create new file `/etc/dnsmasq.d/usb0`{: .filepath} with following code
```
interface=usb0
dhcp-range=192.168.59.2,192.168.59.6,255.255.255.248,1h
dhcp-option=3
leasefile-ro
```
{: file='/etc/dnsmasq.d/usb0'}
should be noted that `192.168.59.7` is the broadcast address and cannot be used by devices.

Finally, To stop the default DHCP software from messing with our `usb0` interface we need to add `denyinterfaces usb0` at the end of `/etc/dhcpcd.conf`{: .filepath}

## Using VScode on IPad
### Installing NodeJS
```console
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Installing Code Server 
First, we need to install `yarn` which is a package manager.
```console
sudo npm -g install yarn
```
Then, we install code-server by yarn
```console
yarn global add code-server
```
Some warning may occur about `google-cloud` but it doesn't matter.

### Test the Installation
Start `code-server` by running
```console
~/.yarn/bin/code-server
```
You should see something like this:
```console
[2022-03-04T20:37:35.946Z] info  Wrote default config file to ~/.config/code-server/config.yaml
[2022-03-04T20:37:37.202Z] info  code-server 4.1.0-4946-9e620e90f53fb91338a2ba1aaa2e556d42ae52d5 9e620e90f53fb91338a2ba1aaa2e556d42ae52d5
[2022-03-04T20:37:37.206Z] info  Using user-data-dir ~/.local/share/code-server
[2022-03-04T20:37:37.253Z] info  Using config file ~/.config/code-server/config.yaml
[2022-03-04T20:37:37.253Z] info  HTTP server listening on http://127.0.0.1:8080/ 
[2022-03-04T20:37:37.253Z] info    - Authentication is enabled
[2022-03-04T20:37:37.254Z] info      - Using password from ~/.config/code-server/config.yaml
[2022-03-04T20:37:37.254Z] info    - Not serving HTTPS 
```

### Configuring code-server
By default, `code-server` is only accessible from the machine it is installed on. If we want to used it on Safari on IPad, we need to edit the `bind-addr` property in the config file at `~/.config/code-server/config.yaml`{: .filepath}.

```console
bind-addr: 0.0.0.0:8080
auth: password
password: mypassword
cert: false
```
### Start Code Server automatically
As the superuser create following file at `/etc/systemd/system/code-server.service`{: .filepath}
```console
[Unit]
Description=code-server
After=network.target

[Service]
User=pi 
Group=pi

WorkingDirectory=/home/pi
Environment="PATH=/usr/bin"
ExecStart=/home/pi/.yarn/bin/code-server

[Install]
WantedBy=multi-user.target
```
{:. file=`/etc/systemd/system/code-server.service`}

you should change the path on `WorkingDirectory`, `ExecStart` and `User`, `Group`  if the user name is other than `'pi'`.

Then run `sudo systemctl enable code-server` to tell your Pi to start the code-server automatically. Now is a good time to reboot and check that auto start actually works.

![Code Server on Safari](/postpic/20230208/codeserver.jpeg)_Code Server on Safari_

For more information please check [**Tech Craft's**](https://techcraft.co/videos/2022/2/vscode-on-ipad-pro-full-setup-guide-with-raspberry-pi/) website.



## Reference
1. <https://www.youtube.com/watch?v=L8r6kMod7Vw>
2. <https://nyac.at/posts/raspi-with-ipad>
3. <https://yiwei.dev/raspberrypi-ipad/>
4. <https://techcraft.co/videos/2022/2/vscode-on-ipad-pro-full-setup-guide-with-raspberry-pi/>

