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

1. download r8125 (kernel up to 5.19)

2. `tar -xvf r8125-9.005.06.tar.bz2`

3. `sudo ./autorun.sh`

(This is not installed as a DKMS, only efforts to the current kernel.)

edit /etc/netplan/*.yaml

```
network:
  ethernets:
    enp7s0:
      dhcp4: true
  version: 2
```

sudo netplan apply

## Upgrade kernel on Ubuntu 20.04 [optional]
Before starting, update all system packages
```
uname -r
sudo apt update
sudo apt upgrade
```
then
```
wget -P [prefix] https://raw.githubusercontent.com/pimlie/ubuntu-mainline-kernel.sh/
master/ubuntu-mainline-kernel.sh
sudo cp ubuntu-mainline-kernel.sh /usr/local/bin/
sudo chmod +x /usr/local/bin/ubuntu-mainline-kernel.sh
```
search and find the latest version of kernel by

    ubuntu-mainline-kernel.sh -c

Also able to see the list of available version of Kernel to install

    ubuntu-mainline-kernel.sh -r

install preferred version of the Kernel by

    ubuntu-mainline-kernel.sh -i [version-number]

## cuda driver & toolkits
<https://developer.nvidia.com/cuda-downloads>


Quick start Guide

<https://docs.nvidia.com/cuda/cuda-quick-start-guide/>

```
wget -P [prefix] https://developer.download.nvidia.com/compute/cuda/12.0.1/local_installers/cuda_12.0.1_525.85.12_linux.run
sudo sh cuda_12.0.1_525.85.12_linux.run
```

Please make sure that

 - PATH includes /usr/local/cuda-12.0/bin

 - LD_LIBRARY_PATH includes /usr/local/cuda-12.0/lib64, or, add /usr/local/cuda-12.0/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run cuda-uninstaller in /usr/local/cuda-12.0/bin

To uninstall the NVIDIA Driver, run nvidia-uninstall

Logfile is /var/log/cuda-installer.log

## cuDNN
<https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html>

## Anaconda3
```
wget -P [prefix] https://repo.anaconda.com/archive/Anaconda3-2021.05-Linux-x86_64.sh
bash Anaconda3-2021.05-Linux-x86_64.sh
```

add the below in `/etc/skel/.bashrc` and `/home/{user}/.bashrc`

```
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/opt/anaconda3/21.05/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/opt/anaconda3/21.05/etc/profile.d/conda.sh" ]; then
        . "/opt/anaconda3/21.05/etc/profile.d/conda.sh"
    else
        export PATH="/opt/anaconda3/21.05/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

#### appendix
Add github.com SSH key
    ssh-keygen -t 

check cpu info

    grep -m 1 'model name' /proc/cpuinfo

control process

`jobs -s` list stopped process by `SIGTSTP`, not `SIGSTOP`.

[difference between `SIGSTOP` and `SIGTSTP` are showed below link.](https://stackoverflow.com/questions/11886812/what-is-the-difference-between-sigstop-and-sigtstp/11888074#11888074)

[show stopped process](https://stackoverflow.com/questions/32859493/linux-command-to-show-stopped-and-running-processes)

```
jobs -s
ps -A -o stat,command,pid | grep '^T '
kill -9 `jobs -ps`
```

The types and definitions of Ubuntu Linux Partitions and Directories Explained.

<https://www.dell.com/support/kbdoc/ko-kr/000131456/the-types-and-definitions-of-ubuntu-linux-partitions-and-directories-explained?lang=en>