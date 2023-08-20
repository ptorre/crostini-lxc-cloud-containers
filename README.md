# Installing and using cloud containers on ChromeOS instead of the default supplied by Google

## This is a work in progress that explains how to install cloud containers with *minimal* ChromeOS integration.
Ideally it would be possible to launch a custom vm using vmc.  I'd even be happy to have the default termina kernel and initrd with custom rw rootfs.

Since that's not possible without putting the whole device into developer mode, this is the best alternative I have come up with.

Alternate cloud images can be used and this cloud-cfg.yaml can be used as a possible
starting point. [Found Here](https://images.linuxcontainers.org/) The `cloud` should be 
relatively painless to get working.

See the **[Warning](#warning)** below with regard to alternate container names. 

### Remove and reinstall the linux development environment to ensure you have a clean start
1. In the Settings application (Advanced -> Developers -> Linux development environment)
Click the "Remove Linux development environment" button if present and wait for it to complete.
1. Restart the computer
1. In the Settings application (Advanced -> Developers)
Next to "Linux development environment," select Turn On.
1. Wait till this completes

_while this is time consuming it should only need to be done once and will save a lot of greif later_

### Remove the default penguin image completely before installing something else

1. Open crosh with the shortcut `Ctrl+Alt+T` and execute the following commands
2. `crosh> vsh termina`
3. `(termina) chronos@localhost ~ $ lxc list`
```
+---------+---------+-----------------------+-------------------------------------------+-----------+-----------+
|  NAME   |  STATE  |         IPV4          |                   IPV6                    |   TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+-------------------------------------------+-----------+-----------+
| penguin | RUNNING | 100.115.92.199 (eth0) | 2601:646:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx (eth0) | CONTAINER | 0         |
+---------+---------+-----------------------+-------------------------------------------+-----------+-----------+
```
4. `(termina) chronos@localhost ~ $ lxc delete penguin`
5. `(termina) chronos@localhost ~ $ lxc list`
```
+------+-------+------+------+------+-----------+
| NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
+------+-------+------+------+------+-----------+
```

*This is enough to ensure a relatively clean start, but will lose some storage (around 250M) for the initial image that was downloaded.*

This file contains the commands that will completely cleanup the storage used in use by lxd for the removed container [lxc-commands.md](lxc-commands.md)

### Launch the debian based linux container in the VM

_Google only provides packages for specific versions of Debian, so set up essential services and software using cloud-init_

1. Save a copy of cloud-cfg.yaml 
`curl https://raw.githubusercontent.com/ptorre/crostini-lxc-cloud-containers/main/cloud-cfg.yaml > /tmp/cloud-cfg.yaml`

1.  Edit `cloud-cfg.yaml` 
    - change 'default_user.name` to what you would like to use
    - change the `- loginctl enable-linger ptorre` line at the to use the chosen user name
    - you can also add to the list of packages that will be installed during instance creation.
    - make the editied file available in the termina vm, so you can pass it as input to
        the `lxc launch ...` command 
        * If you ran the above curl command you can use sed to edit the file in place (there is no
        real editor installed in termina):
        `sed -i.bak -e 's/ptorre/whatever/' /tmp/cloud-cfg.yaml`

1. Run `lxc launch images:debian/12/cloud penguin < .../cloud-cfg.yaml` at the termina command line
   - _you must use the name 'penguin' so that the terminal application will detect the container_
   - You can wait for cloud-init completion with the command
   `lxc exec penguin -- cloud-init status --long --wait`

You can attempt debugging from a shell in termina by connecting to containers directly:
Open a crosh termina (Ctrl+Alt+T) type `vsh termina` then
`lxc exec <container-name> -- bash`

After this point you should be able to create more containers in termina manually with `lxc launch ...`

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
- etc..


# Warning

***Major issues with dissapearing containers and leaking storage to unreferenceable images/files***

**Termina seems to sometimes lose containers and images from the lxd database**

_Only creating containers called `penguin` **may** prevent this_

_It is also wise to manually delete images when you are finished installing with them_
See the notes in [lxc-commands.md](lxc-commands.md)

_Crostini Reset LXD DB on launch_ set to *Enabled* (chrome://flags/#crostini-reset-lxd-db) is a bad idea.
Because this also means all containers not named `penguin` are LOST whenever the termina vm
is restarted. You should consider any container not named `penguin` to be ephemeral.**
(You may even prefer to add the `-e` or `--ephemeral` option to the `lxc launch ...` command
so that they are marked as such.  
The last time I checked the storage tney are using when they are lost, becomes innaccesable but is not deleted. 

There are other known issues:
https://bugs.chromium.org/p/chromium/issues/detail?id=1146390#c6

The archlinux wiki has more information on how the linux development environment is set up and other issues people have encountered.
https://wiki.archlinux.org/title/Chrome_OS_devices/Crostini
