# setup-centos7

# 1. Chrome install
```
$ cat <<EOF > /etc/yum.repos.d/google.chrome.repo
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
EOF

$ yum search google chrome
$ sudo yum install google-chrome-stable
```

# 2. Disable Nouveau driver
```
$ sudo su
# mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r)-nouveau.img
# dracut --omit-drivers nouveau /boot/initramfs-$(uname -r).img $(uname -r)
# echo 'blacklist nouveau' >> /etc/modprobe.d/modprobe.conf
# echo 'blacklist nouveau' >> /etc/modprobe.d/nouveau_blacklist.conf
# exit
$ sudo systemctl disable gdm.service
$ reboot
```

# 3. Install Nvidia-Driver
Before this step, download the Nvidia driver from https://www.nvidia.co.jp/Download/index.aspx?lang=jp .
```
$ sudo yum -y update
$ sudo yum -y install kernel-devel kernel-headers gcc make git pciutils tmux
$ sudo bash NVIDIA-Linux-x86_64-470.74.run

$ sudo systemctl enable gdm.service
$ reboot
```

# 4. Enable IOMMU and Load vfio-pci driver instead of Nouveau driver
```
$ sudo su
# vi /etc/default/grub 
...
GRUB_CMDLINE_LINUX="crashkernel=auto spectre_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet intel_iommu=on vfio_iommu_type1.allow_unsafe_interrupts=1 iommu=pt"
...

# cat <<EOF > /etc/modprobe.d/vfio.conf
options vfio-pci ids=10de:1cb1,10de:0fb9
EOF

# cat <<EOF > /etc/modules-load.d/vfio-pci.conf
pci_stub
vfio
vfio_iommu_type1
vfio_pci
kvm
kvm_intel
EOF

# grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
# reboot

$ lspci -nnk -d 10de:1cb1
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP107GL [Quadro P1000] [10de:1cb1] (rev a1)
	Subsystem: NVIDIA Corporation Device [10de:11bc]
	Kernel driver in use: vfio-pci
	Kernel modules: nouveau, nvidia_drm, nvidia

$ lspci -nnk -d 10de:0fb9
01:00.1 Audio device [0403]: NVIDIA Corporation GP107GL High Definition Audio Controller [10de:0fb9] (rev a1)
	Subsystem: NVIDIA Corporation Device [10de:11bc]
	Kernel driver in use: vfio-pci
	Kernel modules: snd_hda_intel
```

# 5. Install KVM and Vagrant
```
$ sudo yum install qemu-kvm qemu-img libvirt virt-install
$ sudo yum install qemu libvirt-devel ruby-devel

$ sudo systemctl start libvirtd
$ virsh list
$ sudo yum install virt-manager
$ sudo virt-manager 

$ sudo yum install -y yum-utils
$ sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
$ sudo yum -y install vagrant
$ vagrant plugin list
$ CONFIGURE_ARGS="with-libvirt-include=/usr/include/libvirt with-libvirt-lib=/usr/lib64" vagrant plugin install vagrant-libvirt
```

