#### Managing Virtual Machines (VMs) with Ansible.

##### Managing KVM virtual machines.

These are VMs that are deployed on top of the Linux internal KVM hypervisor.

The machines are managed by the `libvirtd` daemon that must be running on the managed hosts. 
They have configuration files stored on `/etc/libvirt/qemu` and a disk file on `/var/lib/libvirt/images`.

The `virt` module can be used to create KVM based virtual machines. This comes from the `communit.libvirt` collection, we install with `ansible-galaxy collection install community.libvirt`. Since am running my lab on a VM, it won't be possible to show how to use this module without installing ansible on my ProxMox hypervisor. Use `ansible-doc virt` to see example of the usage of the `virt` module. 


