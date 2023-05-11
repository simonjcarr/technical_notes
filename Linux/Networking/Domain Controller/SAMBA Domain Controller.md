Instructions taken from here https://adamtheautomator.com/domain-controller-on-linux/

## Edit /etc/hosts

Edit the hosts file, ensure that only the IPv4 address for the VM points to the FQDN

```bash
sudo nano /etc/hosts
```

![[Pasted image 20230503204202.png]]

![[Pasted image 20230503204224.png]]

Confirm the hostname

```bash
# verify server FQDN
hostname -A

# verify FQDN resolves to your server IP address
ping -c1 oddjobs-dc.oji.com

```

![[Pasted image 20230503204358.png]]

## Disable Network Name Resolution Service

Ubuntu has a service called `systemd-resolved`, which takes care of the DNS resolution requests. This service is unsuitable for Samba, and you must disable it and manually configure the DNS resolver instead.

```bash
sudo systemctl disable --now systemd-resolved
```

![[Pasted image 20230503204658.png]]

Remove the symbolic link to the file /etc/resolv.conf

```bash
sudo unlink /etc/resolv.conf
```

Create a new /etc/resolv.conf file in your text editor. This example uses nano.

```bash
sudo nano /etc/resolv.conf
```

Populate the /etc/resolv.conf file with the following information. Replace 192.168.8.10 with your server’s IP address and oji.com with your domain. Leave the nameserver 1.1.1.1 as the fallback DNS resolver, which is the [public DNS resolver by Cloudflare](https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/).

```bash
# your Samba server IP Address
nameserver 192.168.8.10

# fallback resolver
nameserver 1.1.1.1

# your Samba domain
search oji.com
```

![[Pasted image 20230503204854.png]]

Save and exit the file

## Install Samba

```bash
sudo apt-get update
```

```bash
sudo apt install -y acl attr samba samba-dsdb-modules samba-vfs-modules smbclient winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user dnsutils chrony net-tools
```

On the Configuring Kerberos Authentication step, type the DNS domain in uppercase. In this example, the default realm domain is OJI.COM. Highlight Ok, and press Enter to accept the value.


![[Pasted image 20230503205018.png]]

On the next screen asking for the Kerberos servers for your realm, type the server hostname and press Enter.

![[Pasted image 20230503205034.png]]

On the next screen asking for the Administrative server for your Kerberos realm, type the server hostname and press Enter.
![[Pasted image 20230503205050.png]]

After the configuration, disable the unnecessary services (winbind, smbd, and nmbd).

```bash
sudo systemctl disable --now smbd nmbd winbind

```

![[Pasted image 20230503205120.png]]

## Enable the SAMBA-AD-DC Service
Enable and activate the samba-ad-dc service. This service is what Samba needs to act as an Active Directory domain controller Linux server.


```bash
# unmask the samba-ad-dc service
sudo systemctl unmask samba-ad-dc

# enable samba-ad-dc service
sudo systemctl enable samba-ad-dc
```

![[Pasted image 20230503205211.png]]


## Provisioning the Domain Controller Linux Server

Using the samba-tool binary, you can now provision the domain controller upon your Samba installation. _Samba-too_l is a configuration tool to interact with and configure various aspects of a Samba-based AD.

Backup existing files

```bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
sudo mv /etc/krb5.conf /etc/krb5.conf.bak
```

Run the below command to promote the Samba to an Active Directory domain controller Linux server.

The –use-rfc2307 switch enables the [Network Information Service (NIS)](https://www.linux.com/news/introduction-nis-network-information-service/) extension, which allows the DC to manage UNIX-based user accounts appropriately.

```bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```

Answer the prompts as follows.

-   **Realm** – the tool automatically detects your Kerberos realm. In this example, the realm is `OJI.COM`. Press Enter to accept the default.

-   **Domain** – the tool automatically detects the [NetBIOS](https://www.lifewire.com/netbios-software-protocol-818229) domain name. In this example, the NetBIOS is `OJI`. Press Enter to continue.

-   **Server role** – the tool automatically populates the server role as a domain controller (`dc`). Press Enter to continue.

-   **DNS backend** – the default is `SAMBA_INTERNAL`. Press Enter to accept the default.

-   **DNS forwarder IP address** – type the fallback resolver address you specified in `resolve.conf` earlier, which is `1.1.1.1`. Press Enter to continue.

-   **Administrator password** – set the password of the default domain administrator. The password you specify must meet [Microsoft’s minimum complexity requirements](https://technet.microsoft.com/en-us/library/cc786468%28v=ws.10%29.aspx). Press Enter to proceed.

-   Retype password – retype the default domain administrator password and press Enter.

![[Pasted image 20230503205406.png]]

At the end you will get the following confirmation

![[Pasted image 20230503205436.png]]

The samba-tool command generated the Samba AD Kerberos configuration file at /var/lib/samba/private/krb5.conf. You must copy this file to /etc/krb5.conf. To do so, run the following command.

```bash
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```

Start the samba-ad-dc service

```bash
sudo systemctl start samba-ad-dc
sudo systemctl status samba-ad-dc
```

## Testing the Domain Controller Linux Server

The Samba AD DC server is now running. In this section, you will perform a few post-installation tests to confirm key components are functioning as desired. One such test is to attempt logging into the default network shares on the DC.

Run the smbclient command to log on as the default administrator account and list (ls) the contents of the netlogon share.

```bash
smbclient //localhost/netlogon -U Administrator -c 'ls'
```

Enter the default admin password. The share should be accessible without errors if the DC is in a good state. As you can see below, the command listed the netlogon share directory.

![[Pasted image 20230503205626.png]]

### Verifying DNS Resolution for Key Domain Records

Run the commands below to look up the following DNS records.

-   TCP-based LDAP [SRV](https://www.cloudflare.com/learning/dns/dns-records/dns-srv-record/) record for the domain.
-   UDP-based Kerberos SRV record for the domain.
-   A record of the domain controller.

```bash
host -t SRV _ldap._tcp.oji.com
host -t SRV _kerberos._udp.oji.com
host -t A oddjobs-dc.oji.com
```

Each command should return the following results, indicating that the DNS resolution works.

![[Pasted image 20230503205719.png]]


### Testing Kerberos

The last test is to attempt to issue a Kerberos ticket successfully.

1. Execute the kinit command for the administrator user. The command automatically appends the realm to the user account. For example, the administrator will become administrator@OJI.com, where OJI.com is the realm.
```bash
kinit administrator
```

Type the administrator password on the prompt and press Enter. If the password is correct, you’ll see a Warning message about the password expiration, as shown below.

![[Pasted image 20230503205759.png]]

Run the klist command below to list all tickets in the ticket cache.

```bash
klist
```

The screenshot below shows that the Kerberos ticket for the administrator account is in the ticket cache. This result indicates that Kerberos authentication works on your domain controller Linux server.

![[Pasted image 20230503205830.png]]

## Conclusion

Congratulations on reaching the end of this tutorial. You have now learned to stand up an Active Directory domain controller Linux server quickly. Deepen your knowledge on the subject by learning to [create users and join client computers in the domain](https://adamtheautomator.com/samba-active-directory).