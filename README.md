1. **Install dummy-hcd**
	Source: https://github.com/RushOnline/dummy-hcd
	- Clone git
	```bash
	git clone https://github.com/RushOnline/dummy-hcd.git
	```
	- Update source up to running kernel version
	```bash
	make update
	```
	- Add to dkms
	```bash
	make dkms
	```
	- Load: Emulate one USB2 connected device:
	```bash
	sudo modprobe dummy-hcd is_high_speed=1
	```
	- Remove (optional)
	```bash
	sudo dkms remove dummy-hcd/0.1 --all
	```

2. **Emulate a usb flashdrive**
	Source: https://unix.stackexchange.com/questions/338026/centos-how-to-emulate-a-usb-flashdriveMake 
	- IMG mirroring usb
	```bash
	dd bs=1G count=1 if=/dev/zero of=/tmp/unraid.img
	```
	
	- Loading a gadget driver to emulate USB mass storage
	```bash
	modprobe g_mass_storage file=/tmp/unraid.img idVendor=0x1d6b idProduct=0x0104 iManufacturer=Myself iProduct=VirtualBlockDevice iSerialNumber=1234
	```
	GUID of this virtual USB: *idVendor-idProduct-0000-iSerialNumber(12bit)*:
	**1D6B-0104-0000-000000001234**
	
	- Run `lsusb` to check whether command run successfully 
	`..`
	**`Bus 005 Device 004: ID 1d6b:0104 Linux Foundation Multifunction Composite Gadget`**
	`Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub`
	`..`
	- Check /dev/sdX by using `lssici`
	`..`
	`[7:0:0:0]    disk    Linux    File-Stor Gadget 0408  /dev/sdh`
	`..`

3. **Create partition and format**
	- Create partition by using fdisk command for the specific device that created above.
	```bash
	fdisk /dev/sdh
	```
    - For the first sdX1 as FAT with UNRAID label, all capital, no space.
    ```bash
    mkfs.fat -n UNRAID /dev/sdh1
    ```
1. **Mount**
	Create a directory and mount usb device into it
	```bash
	mkdir /mnt/virt-usb
	mount -t auto /dev/sdh1 /mnt/virt-usb
	```
1. **Follow**  https://docs.unraid.net/unraid-os/getting-started/manual-install-method/
2. **Make bootable proxmox: **
	- Create proxmox VM as normal
	- Proxmox cannot boot from an USB, so, change boot option to ISO from [PlopKexec - Download](https://www.plop.at/en/plopkexec/download.html)
