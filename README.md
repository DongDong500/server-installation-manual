# server-installation-manual
Private physical server installation manual (network driver, cuda driver &amp; toolkits)

## network driver (Realteck R8125 NIC chip)
ubuntu-20.04.5-live-server-amd64 dose not provide r8125 driver, we need to download manually without network connection.

### Check Ethernet controller
lspci | grep Ethernet

## cuda driver & toolkits