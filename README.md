# Installing and using cloud containers on ChromeOS instead of the default supplied by Google

## This is a work in progress that explains how to install cloud containers with *minimal* ChromeOS integration.
Ideally it would be possible to launch a custom vm using vmc.  I'd even be happy to have the default termina kernel and initrd with custom rw rootfs.

Since that's not possible without putting the whole device into developer mode, this is the best alternative I have come up with.



Alternate cloud images can be used and this cloud-cfg.yaml can be used as a possible
starting point. 

The archlinux wiki has more information on how the linux development environment is set up.
https://wiki.archlinux.org/title/Chrome_OS_devices/Crostini


### Launch the termina VM
1. Open crosh with the shortcut `Ctrl+Alt+T`
1. `vmc start termina`

### Launch the debian based linux container in the VM

_Google only provides packages for specific versions of Debian, so set up essential services and software using cloud-init_

1. Save and edit `cloud-cfg.yaml`. You shoud change the `ssh_authorized_keys` and 'default_user.name` to what you would like to use.  You can also add to the list of packages that will be installed during instance creation.
    - Make sure this file is accessable in termina somewhere.
    - You will pass this file as input to the `lxc launch ...` command

1. Run `lxc launch images:debian/12/cloud penguin < .../cloud-cfg.yaml` at the termina command line
   - _you must use the name 'penguin' so that the terminal application will detect the container_
   - You can wait for cloud-init completion with the command
   `lxc exec penguin -- cloud-init status --long --wait`

If you have never set up the _Linux development environment_ in the ChromeOS Settings application,
go through the motions of doing that now.  Just click _next_ on every page, since there is already
a container named penguin and termina is already running, it should be farily quick.

If you already have a _Linux development environment_ you may need to restart the system so that
the terminal app will be able to connect to the new container (still named penguin).

After this point you should be able to create more containers in termina manually with `lxc launch ...`

# Major issues with dissapearing containers and leaking storage to unreferenceable images/files #
**Termina seems to only start lxd reliably IF you have the flag
_Crostini Reset LXD DB on launch_ set to *Enabled* (chrome://flags/#crostini-reset-lxd-db).
Because this also means all containers not named `penguin` are LOST whenever the termina vm
is restarted, you should consider any container not named `penguin` to be ephemeral.**
(You may even prefer to add the `-e` or `--ephemeral` option to the `lxc launch ...` command
so that they are marked as such.  
The last time I checked the storage tney are using when they are lost, becomes innaccesable but is not deleted. 


_When creating subsequent containers you can leave the container-name blank and one will be generated._
Creating multiple containers from the same image will be much faster than different images.
For example:
- `lxc launch -e images:rockylinux/9/cloud < .../cloud-cfg.yaml`  _creating an ephemeral container_
- `lxc launch -e images:rockylinux/9/cloud < .../cloud-cfg.yaml`  _after images is downloaded subsequent containers should start almost immediately_
- `lxc launch -e images:rockylinux/9/cloud < .../cloud-cfg.yaml`
- `lxc launch images:ubuntu/jammy/cloud < /tmp/cloud-cfg.yaml`    _will need to download the jammy image_
- etc..
- check the cloud-init status for each container using the command
    - `lxc exec <container-name> -- cloud-init status`

You can attempt debugging from a shell in termina by connecting to containers directly:
Open a crosh termina (Ctrl+Alt+T) type `vsh termina` then
`lxc exec <container-name> -- bash`

