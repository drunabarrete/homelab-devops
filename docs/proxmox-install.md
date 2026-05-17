## Install Notes
* Purchased a Lenovo Thinkcentre M920S used on eBay, with the intent to add more desktop RAM I had lying around. Attempted to install old DDR4 Corsair RGB RAM and it did not fit. If I want to add more memory, I will need to purchase low-profile RAM.
* Formatted the proxmox.iso file on a USB using Rufus on my main pc to install Proxmox on the ThinkCentre.
* Powered on ThinkCentre, adjusted BIOS settings to boot into Proxmox installer, enabled Intel VT-x / VT-d, and configured Proxmox to use it as a type 1 bare-metal hypervisor.
## Configure No-Subscription Repo
* Post installation, I disabled the repositories that were default in the Proxmox shell & changed it to the free open source community repo, then upgraded all packages using the following commands
```bash
mv /etc/apt/sources.list.d/pve-enterprise.sources /etc/apt/sources.list.d/pve-enterprise.sources.disabled
mv /etc/apt/sources.list.d/ceph.sources /etc/apt/sources.list.d/ceph.sources.disabled
echo "deb http://download.proxmox.com/debian/pve trixie pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
apt update
apt full-upgrade -y
