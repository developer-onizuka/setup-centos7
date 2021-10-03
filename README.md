# setup-centos7

[onizuka@localhost ~]$ cat /etc/default/grub 
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto spectre_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet intel_iommu=on vfio_iommu_type1.allow_unsafe_interrupts=1 iommu=pt vfio-pci.ids=10de:1cb1,10de:0fb9"
GRUB_DISABLE_RECOVERY="true"


[onizuka@localhost ~]$ sudo ls -l /boot/efi/EFI/centos/grub.cfg
-rwx------. 1 root root 4876 Oct  3 11:01 /boot/efi/EFI/centos/grub.cfg

[onizuka@localhost ~]$ sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-1160.42.2.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.42.2.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1160.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-277d0a7f3e6741c79fe8f42aa3bec61b
Found initrd image: /boot/initramfs-0-rescue-277d0a7f3e6741c79fe8f42aa3bec61b.img
done
