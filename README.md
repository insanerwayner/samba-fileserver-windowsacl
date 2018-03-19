## Configure Samba File server in Ubuntu 16.04 with Windows ACL Suppot

1. Install Ubuntu 16.04 with Samba during install
1. Update & Upgrade packages: ```sudo apt update && sudo apt upgrade -y```
1. Modify included files with correct domain name and host name of computer(**/etc/hosts**, **/etc/samba/smb.conf**, **/etc/krb5.conf**, **/etc/nsswitch.conf**) and copy in place.
1. Set static IP Address in **/etc/network/interfaces** (included in files just make sure to change adapter name and appropriate network information.)
1. Check DNS config, see if it matches close to the included **/etc/resolv.conf**, if not then modify
1. Reload Networking Service, or reboot. ```sudo systemctl restart networking.service```
1. Install other required packages: ```sudo apt install krb5-user libnss-winbind libpam-winbind``
1. Reload Samba Config and restart services ```sudo smbcontrol all reload-config
sudo systemctl restart winbindd.service smbd.service nmbd.service```
1. Join the domain: ```net ads join -U administrator```
1. Restart services again: ```sudo systemctl restart winbindd.service smbd.service nmbd.service```
1. Send a **Winbddd Ping**: ```# wbinfo --ping-dc
checking the NETLOGON for domain[SAMDOM] dc connection to "DC.SAMDOM.EXAMPLE.COM" succeeded
```
1. Look up Domain Users and Groups to verif you're getting that information back: ```
# getent passwd SAMDOM\\demo01
SAMDOM\demo01:*:10000:10000:demo01:/home/demo01:/bin/bash
```
1. For your filestore verify that your fstab specifies **xattr_user**
1. Grant **SeDiskOperatorPrivilege** to **Domain Admins** (This is what allows you to manage permission in Windows) ```
# net rpc rights grant "SAMDOM\Domain Admins" SeDiskOperatorPrivilege -U "SAMDOM\administrator"
Enter SAMDOM\administrator's password:
Successfully granted rights.
```
1. Create your share directory and set rights for **Domain Admins**```
# chown root:"Domain Admins" /srv/samba/Demo/
# chmod 0770 /srv/samba/Demo/
```
1. Make sure you make a new entry for each share at the bottom of your **smb.conf** file.

