## DNS Settings
Ensure that the primary DNS address is pointing to the Domain Controller IP Address

## Install required packages
```bash
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## Join the domain
```bash
realm join dc01.soxprox.lan -U simon
```

if you get an error that files are not installed and you have installed the packages above , you can change the above command as so

```bash
realm join dc01.soxprox.lan -U simon --install=/
```

When prompted enter your password.

## Edit /etc/sssd/sssd.conf

Open /etc/sssd/sssd.conf  and comment out the line that starts `access_provider` so it looks like this
```bash
#access_provider = ad
```

Check that you are logged into the domain

```bash
id simon@soxprox.lan
```