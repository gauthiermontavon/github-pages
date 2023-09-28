---
title: Sniffing Wifi Probe Requests with Kali
date : 2023-09-19
---

# Intro

Ce post décrit comment sniffer facilement les Probe Requests émises par des Wifi devices à promixité avec Kali.

# Pré requis
## hardware

Wifi adapter avec support monitor mode. Si utilisation de Kali en machine virtuelle, il faut une usb wifi adapter, car le built-in wifi adapter sera vu par VirtualBox comme un adapter Ethernet.
Ensuite, il faut que le chipset de l'adapter wifi soit supporté par Kali.

Some of the chipset supported by Kali Linux include:

    Realtek RTL8812AU
    Realtek 8187L
    Ralink RT5370N
    Ralink RT3572
    Ralink RT5572
    Ralink RT3070
    Ralink RT307
    Atheros AR9271
    MT7610U
    MT7612U
(source:https://nooblinux.com/connecting-a-wireless-adapter-to-kali-linux-virtual-machine/)
L'un des meilleurs chipsets pour le hacking:  Atheros AR9271


### Installation drivers sous Kali
#### Installing kernel linux header
with apt
update repos
sudo apt-get update
sudo apt-get dist-upgrade (take over 15-20 minutes)
reboot and install kernel linux headers
sudo apt-get install –y linux-headers-$(uname -r)

####Generating and installing driver

http://linuxwireless.sipsolutions.net/en/users/Download/__v115.html
download archive compat-wireless
generate your driver
./scripts/driver-select atheros (example for atheros)
make
sudo make install


