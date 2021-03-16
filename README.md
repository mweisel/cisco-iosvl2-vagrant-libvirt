<img alt="Vagrant" src="https://img.shields.io/badge/vagrant%20-%231563FF.svg?&style=for-the-badge&logo=vagrant&logoColor=white"/>

# Cisco IOSvL2 Vagrant box

A procedure for creating a Cisco IOSvL2 Vagrant box for the [libvirt](https://libvirt.org) provider.

## Prerequisites

  * [Cisco Modeling Labs - Personal](https://learningnetworkstore.cisco.com/cisco-modeling-labs-personal) subscription
  * [Git](https://git-scm.com)
  * [Python](https://www.python.org)
  * [Ansible](https://docs.ansible.com/ansible/latest/index.html) >= 2.7
  * [libvirt](https://libvirt.org) with client tools
  * [QEMU](https://www.qemu.org)
  * [Expect](https://en.wikipedia.org/wiki/Expect)
  * [Telnet](https://en.wikipedia.org/wiki/Telnet)
  * [Vagrant](https://www.vagrantup.com) <= 2.2.9
  * [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Steps

0\. Verify the prerequisite tools are installed.

<pre>
$ <b>which git python ansible libvirtd virsh qemu-system-x86_64 expect telnet vagrant</b>
$ <b>vagrant plugin list</b>
vagrant-libvirt (0.3.0, global)
</pre>

1\. Log in and download the CML-P reference platform ISO file to your `Downloads` directory.

2\. Create a mount point directory.

<pre>
$ <b>sudo mkdir -p /mnt/iso</b>
</pre>

3\. Mount the ISO file.

<pre>
$ <b>cd $HOME/Downloads</b>
$ <b>sudo mount -o loop refplat-20201020-fcs.iso /mnt/iso</b>
</pre>

4\. Copy (and rename) the Cisco IOSvL2 disk image file to the `/var/lib/libvirt/images` directory.

<pre>
$ <b>sudo cp /mnt/iso/virl-base-images/iosvl2-2019/vios_l2-adventerprisek9-m.ssa.high_iron_20190423.qcow2 /var/lib/libvirt/images/cisco-iosvl2.qcow2</b>
</pre>

5\. Unmount the ISO file.

<pre>
$ <b>sudo umount /mnt/iso</b>
</pre>

6\. Modify the file ownership and permissions. Note the owner may differ between Linux distributions.

> Ubuntu 18.04

<pre>
$ <b>sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/cisco-iosvl2.qcow2</b>
$ <b>sudo chmod u+x /var/lib/libvirt/images/cisco-iosvl2.qcow2</b>
</pre>

> Arch Linux

<pre>
$ <b>sudo chown nobody:kvm /var/lib/libvirt/images/cisco-iosvl2.qcow2</b>
$ <b>sudo chmod u+x /var/lib/libvirt/images/cisco-iosvl2.qcow2</b>
</pre>

7\. Create the `boxes` directory.

<pre>
$ <b>mkdir -p $HOME/boxes</b>
</pre>

8\. Start the `vagrant-libvirt` network (if not already started).

<pre>
$ <b>virsh -c qemu:///system net-list</b>
$ <b>virsh -c qemu:///system net-start vagrant-libvirt</b>
</pre>

9\. Clone this GitHub repo and _cd_ into the directory.

<pre>
$ <b>git clone https://github.com/mweisel/cisco-iosvl2-vagrant-libvirt</b>
$ <b>cd cisco-iosvl2-vagrant-libvirt</b>
</pre>

10\. Run the Ansible playbook.

<pre>
$ <b>ansible-playbook main.yml</b>
</pre>

11\. Copy (and rename) the Vagrant box artifact to the `boxes` directory.

<pre>
$ <b>cp cisco-iosvl2.box $HOME/boxes/cisco-iosvl2-2019.box</b>
</pre>

12\. Copy the box metadata file to the `boxes` directory.

<pre>
$ <b>cp ./files/cisco-iosvl2.json $HOME/boxes/</b>
</pre>

13\. Change the current working directory to `boxes`.

<pre>
$ <b>cd $HOME/boxes</b>
</pre>

14\. Substitute the `HOME` placeholder string in the box metadata file.

<pre>
$ <b>awk '/url/{gsub(/^ */,"");print}' cisco-iosvl2.json</b>
"url": "file://<b>HOME</b>/boxes/cisco-iosvl2-2019.box"

$ <b>sed -i "s|HOME|${HOME}|" cisco-iosvl2.json</b>

$ <b>awk '/url/{gsub(/^ */,"");print}' cisco-iosvl2.json</b>
"url": "file://<b>/home/marc</b>/boxes/cisco-iosvl2-2019.box"
</pre>

15\. Add the Vagrant box to the local inventory.

<pre>
$ <b>vagrant box add cisco-iosvl2.json</b>
</pre>

## Debug

View the telnet session output for the `expect` task:

<pre>
$ <b>tail -f ~/iosvl2-console.explog</b>
</pre>

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
