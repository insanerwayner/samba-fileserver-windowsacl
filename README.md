## Configure Samba File server in Ubuntu ###.04 with Windows ACL Suppot

### Install Ubuntu ###.04 with Samba during install
### Update & Upgrade packages: ```sudo apt update && sudo apt upgrade -y```
### Modify included files with correct domain name and host name of computer(**/etc/hosts**, **/etc/samba/smb.conf**, **/etc/krb5.conf**, **/etc/nsswitch.conf**) and copy in place.
### Set static IP Address in **/etc/network/interfaces** (included in files just make sure to change adapter name and appropriate network information.)
### Check DNS config, see if it matches close to the included **/etc/resolv.conf**, if not then modify
### Reload Networking Service, or reboot. ```sudo systemctl restart networking.service```
### Install other required packages: ```sudo apt install krb5-user libnss-winbind libpam-winbind``
### Reload Samba Config and restart services ```sudo smbcontrol all reload-config
sudo systemctl restart winbindd.service smbd.service nmbd.service```
### Join the domain: ```net ads join -U administrator```
### Restart services again: ```sudo systemctl restart winbindd.service smbd.service nmbd.service```
### Send a **Winbddd Ping**: ```# wbinfo --ping-dc
checking the NETLOGON for domain[SAMDOM] dc connection to "DC.SAMDOM.EXAMPLE.COM" succeeded
```
### Look up Domain Users and Groups to verif you're getting that information back: ```
# getent passwd SAMDOM\\demo01
SAMDOM\demo0###*:###000:###000:demo0###/home/demo0###/bin/bash
```
### For your filestore verify that your fstab specifies **xattr_user**
### Grant **SeDiskOperatorPrivilege** to **Domain Admins** (This is what allows you to manage permission in Windows) ```
# net rpc rights grant "SAMDOM\Domain Admins" SeDiskOperatorPrivilege -U "SAMDOM\administrator"
Enter SAMDOM\administrator's password:
Successfully granted rights.
```
### Create your share directory and set rights for **Domain Admins**```
# chown root:"Domain Admins" /srv/samba/Demo/
# chmod 0770 /srv/samba/Demo/
```
### Make sure you make a new entry for each share at the bottom of your **smb.conf** file.

