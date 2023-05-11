Taken from https://www.cyberciti.biz/faq/how-to-configure-a-static-ip-address-on-rhel-8/

```bash
sudo nmcli con mod ens3 ipv4.addresses 192.168.122.20/24
sudo nmcli con mod ens3 ipv4.gateway 192.168.122.1
sudo nmcli con mod ens3 ipv4.method manual
sudo nmcli con mod ens3 ipv4.dns "192.168.2.254"
sudo nmcli con up ens3
```

## nmtui
Use `nmtui` to bring up a text based GUI