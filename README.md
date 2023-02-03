# server-installation-manual
Private physical server installation manual (network driver, cuda driver &amp; toolkits)

## network driver (Realtek R8125 NIC chip)
ubuntu-20.04.5-live-server-amd64 dose not provide r8125 driver by default, we need to download manually without network connection.

<https://www.realtek.com/en/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-pci-express-software>

#### Check Ethernet controller & logical name (interface, e.g. enp7s0)

```
lspci | grep Ethernet
```
or with options
```
lspci -knn | grep Eth -A3
lshw -C network
```

0. Before installing the network driver, we need `make` and `gcc` since ubuntu-20.04 does not provide them by default.

Download `gcc` and `make` in other virtual environment (easy to solve dependency problems) with 

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


###### appendix
check cpu info

    grep -m 1 'model name' /proc/cpuinfo

control process

`jobs -s` list stopped process by `SIGTSTP`, not `SIGSTOP`.

difference between `SIGSTOP` and `SIGTSTP` are showed below link.

<https://stackoverflow.com/questions/11886812/what-is-the-difference-between-sigstop-and-sigtstp/11888074#11888074>

show stopped process

<https://stackoverflow.com/questions/32859493/linux-command-to-show-stopped-and-running-processes>

```
jobs -s
ps -A -o stat,command,pid | grep '^T '
kill -9 `jobs -ps`
```

## cuda driver & toolkits
<https://developer.nvidia.com/cuda-downloads>

```
wget -P [prefix] https://developer.download.nvidia.com/compute/cuda/12.0.1/local_installers/cuda_12.0.1_525.85.12_linux.run
sudo sh cuda_12.0.1_525.85.12_linux.run
```

## cuDNN
<https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html>

## Anaconda
