# server-installation-manual
Private physical server installation manual (network driver, cuda driver &amp; toolkits)

## network driver (Realteck R8125 NIC chip)
ubuntu-20.04.5-live-server-amd64 dose not provide r8125 driver by default, we need to download manually without network connection.

<https://www.realtek.com/en/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-pci-express-software>

#### Check Ethernet controller & logical name (interface, e.g. enp7s0)
lspci | grep Ethernet
lshw -C network

0. Before installing the network driver, we need `make` and `gcc` since ubuntu-20.04 does not provide them by default.

Download `gcc` and `make` in other virtual envrionment (easy to solve dependency problems) with 

    apt-get install --download-only <package_name>

    cd /var/cache/apt/archives

move them into usb, mount to the target server install with

    dpkg --force-all -i *.deb

1. download r8125
2. tar -xvf r8125-9.005.06.tar.bz2
3. sudo ./autorun.sh

edit /etc/netplan/*.yaml

```
network:
  ethernets:
    enp7s0:
      dhcp4: true
  version: 2
```

sudo netplan apply


## cuda driver & toolkits