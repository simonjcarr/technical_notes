
# Raspberry Pi
If installing on a raspberry pi run the following command before installing K3S

```bash
sudo apt install linux-modules-extra-raspi && reboot
```

## Turn off swap
K3S recommends that you turn off swap. 
Follow the instructions here [[SWAP#Permanently disable swap]]

# First node or Single node cluster

Then follow the normal instructions below

## Install the latest version of K3S
```bash
curl -sfL https://get.k3s.io | sh -
```
 
## Install a specific version of K3S
```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=<k3s version> sh -
```

K3S releases https://github.com/k3s-io/k3s/releases

## Disable traefik
If you want to disable the default ingress class of traefik use the `INSTALL_K3S_EXEC` env var
```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.24.12+k3s1 INSTALL_K3S_EXEC="server --no-deploy traefik" sh -
```

# Multi Node Cluster

First install one node using one of the options above

### Get the node token from the first node you installed.
```bash
cat /var/lib/rancher/k3s/server/node-token
```

**expected result** is the node token
```console
K100beefca05523be76e568cdf5289e2edbc73d568a5558f44fcb3ea0865783cf7e::server:a409bf80429472ae6d02cd7fb19f20f7
```

### Install other nodes
```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=<k3s version> K3S_URL=https://<url_of_first_node>:6443 K3S_TOKEN=<Token from first node>  sh -
```
