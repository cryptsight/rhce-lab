# RHCE-Lab
## Install qemu/libvirt
```bash
dnf install qemu libvirt
```
## Install Vagrant
```bash
dnf install vagrant
```
## Import rhce-lab Network
```bash
virsh net-define rhce-lab.xml && virsh net-start rhce-lab
```
## Install vagrant-libvirt Plugin
```bash
vagrant plugin install vagrant-libvirt
```
## Start Machines 
```bash
vagrant up --no-parallel
```
