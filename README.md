A procedure for creating a Cisco IOSvL2 Vagrant box for the [libvirt](https://libvirt.org) provider.

## Prerequisites

  * [Cisco VIRL](http://virl.cisco.com) account
  * [Git](https://git-scm.com)
  * [Python](https://www.python.org)
  * [Ansible](https://docs.ansible.com/ansible/latest/index.html)
  * [libvirt](https://libvirt.org) with client tools
  * [QEMU](https://www.qemu.org)
  * [Expect](https://en.wikipedia.org/wiki/Expect)
  * [Telnet](https://en.wikipedia.org/wiki/Telnet)
  * [Vagrant](https://www.vagrantup.com) <= 2.2.9
  * [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Steps

0. Verify the prerequisite tools are installed.

```
which git python ansible libvirtd virsh qemu-system-x86_64 expect telnet vagrant
vagrant plugin list
```

1. Clone this GitHub repo and _cd_ into the directory.

```
git clone https://github.com/mweisel/cisco-iosvl2-vagrant-libvirt
cd cisco-iosvl2-vagrant-libvirt
```

2. Log in and download the IOSvL2 disk image file from your [Cisco VIRL](http://virl.cisco.com) account.

3. Copy (and rename) the disk image file to the `/var/lib/libvirt/images` directory.

```
sudo cp $HOME/Downloads/vios_l2-adventerprisek9-m.SSA.high_iron_20180619.qcow2 /var/lib/libvirt/images/cisco-iosvl2.qcow2
```

4. Modify the file ownership and permissions. Note the owner will differ between Linux distributions. A couple of examples:

> Arch Linux
```
sudo chown nobody:kvm /var/lib/libvirt/images/cisco-iosvl2.qcow2
sudo chmod u+x /var/lib/libvirt/images/cisco-iosvl2.qcow2
```

> Ubuntu 18.04
```
sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/cisco-iosvl2.qcow2
sudo chmod u+x /var/lib/libvirt/images/cisco-iosvl2.qcow2
```

5. Start the `vagrant-libvirt` network (if not already started).

```
virsh -c qemu:///system net-list
virsh -c qemu:///system net-start vagrant-libvirt
```

6. Run the Ansible playbook. 

```
ansible-playbook main.yml
```

7. Add the Vagrant box. 

```
vagrant box add --provider libvirt --name cisco-iosvl2-15.2.1 ./cisco-iosvl2.box
```

## Debug

To view the telnet session output for the `expect` task:

```
tail -f ~/iosvl2-console.explog
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
