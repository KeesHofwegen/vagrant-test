# Vagrant test

[Vagrant](https://www.vagrantup.com/) is a tool for building and managing virtual machine environments, focussed on development and test environments.

Vagrant images can be found on [https://app.vagrantup.com](https://app.vagrantup.com/boxes/search).

## How to run the demo

1. Install vagrant
2. Install [virtualbox](https://www.virtualbox.org/)
3. Clone this repository
4. Open a cmd shell
5. Run `vagrant up`

## Known issues

### Error
~~~~
There was an error while executing `VBoxManage`, a CLI used by Vagrant
for controlling VirtualBox. The command and stderr is shown below.

Command: ["hostonlyif", "create"]

Stderr: 0%...
Progress state: E_FAIL
VBoxManage.exe: error: Failed to create the host-only adapter
VBoxManage.exe: error: Querying NetCfgInstanceId failed (0x00000002)
VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component HostNetworkInterfaceWrap, interface IHostNetworkInterface
VBoxManage.exe: error: Context: "enum RTEXITCODE __cdecl handleCreate(struct HandlerArg *)" at line 94 of file VBoxManageHostonly.cpp
~~~~

### Solution
Reboot the host.

### Error
~~~~
==> acs: Waiting for machine to boot. This may take a few minutes...
    acs: SSH address: 127.0.0.1:2222
    acs: SSH username: vagrant
    acs: SSH auth method: private key
Timed out while waiting for the machine to boot. This means that
Vagrant was unable to communicate with the guest machine within
the configured ("config.vm.boot_timeout" value) time period.

If you look above, you should be able to see the error(s) that
Vagrant had when attempting to connect to the machine. These errors
are usually good hints as to what may be wrong.

If you're using a custom box, make sure that networking is properly
working and you're able to connect to the machine. It is a common
problem that networking isn't setup properly in these boxes.
Verify that authentication configurations are also setup properly,
as well.

If the box appears to be booting properly, you may want to increase
the timeout ("config.vm.boot_timeout") value.
~~~~

### Solution

Reboot and enable virtualization in bios.

## Notes

Note that it provisions the machine one after the other. Vagrant is able to do it in parallel, but VirtualBox isn't: https://www.vagrantup.com/docs/virtualbox/usage.html
Other providers that are supported out of the box are Docker and Hyper-V.
I couldn't get Hyper-V to run on my laptop so I couldn't try. Docker on Windows only supports either Windows or Linux, not both at the same time.

Command `vagrant status`:

~~~~
Current machine states:

acs                       running (virtualbox)
web                       running (virtualbox)
db                        running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
~~~~

To access machine: `vagrant ssh acs` (no login necessary, if asked, use vagrant/vagrant)
Also: ssh to host machine on mapped port (use 'vagrant port' to find the port)

Memory usage is really good. A 512MB memory VM linux machine takes up 40MB of memory.

Disk usage is ok. The vmdk file of each VM (I only tried Ubuntu and CentOS) is 1,6 GB.
The downloaded vagrant boxes are located in c:\Users\Sebastiaan\.vagrant.d\boxes, the linux ones are about 500 MB in size.
A Windows Server 2016 box (andriy-koval/win-server-2016r2) is more than 5 GB. I couldn't get the image to work (vagrant times out, VirtualBox says 'failed to open a session').
The Windows Server Nano (mwrock/WindowsNano) box is 580 MB, the vmbx is 1,7 GB. Memory usage is 55 MB. This one is running correctly, you can run powershell on the machine
using `vagrant powershell win` or in a powershell `Enter-PSSession -credential (get-credential) -computername localhost -port 55985` (retrieve the port using `vagrant port <machine>`. 

To suspend the machines: vagrant suspend. To resume: vagrant resume.

When you don't suspend the machine before you shut down the host, the status of the VMs after booting the host will be

~~~~
Current machine states:

acs                       aborted (virtualbox)
web                       aborted (virtualbox)
db                        aborted (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
~~~~

To completely remove the machines: `vagrant destroy`.

Directly accessing the VMs by their private IP-addresses works directly for linux VMs, but not for the Windows VM. To fix it, change the network settings of
Ethernet 2 in the VirtualBox VM Windows:
- DHCP: disabled
- IPv4 address: same address as specified in the Vagrantfile (f.e. 192.168.33.40)
- Subnet mask: 255.255.255.0
- Gateway: same address as specified in the Vagrantfile, but last part 0 (f.e. 192.168.33.0).
Now you can ping the Windows VM from the hosts and from other Vagrant VMs.

This Windows box seems to work correctly: StefanScherer/windows_2016_docker, it has the correct IP, Vagrant directory is correctly shared. However the firewall blocks
ping and winrm from the host, so run this on powershell prompt in the guest (either in the machine console or through `vagrant powershell win2016`):
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`
Vagrant box size is 10 GB. Memory usage is about 60 MB.

The directory containing the Vagrantfile will be shared with the guest VM as /vagrant.
