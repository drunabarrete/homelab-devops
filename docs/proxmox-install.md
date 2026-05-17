## Install Notes
* Purchased Lenovo Thinkcentre M920S used on ebay with the idea in mind to add more desktop ram I had laying around. Did not realize how tight the space was inside the computer, corsair rgb ram did not fit. If I want to add more memory, I will need to purchase low profile RAM.
* Formatted the proxmox.iso file on a USB using rufus on my main pc to install proxmox on the thinkcentre.
* Powered on thinkcentre, adjusted bios settings to boot into proxmox installer, enabled Intel VT-x / VT-d, and configured proxmox to use it as a type 1 bare-metal hypervisor.
## Configure No-Subscription Repo
* Post installation I disabled the repositories that were default in the proxmox shell & changed it to the free open source community repo using the following commands
```bash
mv /etc/apt/sources.list.d/pve-enterprise.sources /etc/apt/sources.list.d/pve-enterprise.sources.disabled
mv /etc/apt/sources.list.d/ceph.sources /etc/apt/sources.list.d/ceph.sources.disabled
echo "deb http://download.proxmox.com/debian/pve trixie pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
apt update
apt full-upgrade -y
