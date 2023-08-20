```
crosh> vsh termina
(termina) chronos@localhost ~ $ lxc list
+---------+---------+-----------------------+----------+-----------+-----------+
|  NAME   |  STATE  |         IPV4          | IPV6     |   TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+----------+-----------+-----------+
| penguin | RUNNING | 100.115.92.199 (eth0) | 2601:... | CONTAINER | 0         |
+---------+---------+-----------------------+----------+-----------+-----------+

(termina) chronos@localhost ~ $ lxc delete penguin
(termina) chronos@localhost ~ $ lxc list
+------+-------+------+------+------+-----------+
| NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
+------+-------+------+------+------+-----------+

(termina) chronos@localhost ~ $ lxc version
Client version: 4.0.7
Server version: 4.0.7
(termina) chronos@localhost ~ $ ls
(termina) chronos@localhost ~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       392M  262M  123M  69% /
devtmpfs        1.6G     0  1.6G   0% /dev
tmp             1.6G     0  1.6G   0% /tmp
tmpfs           1.6G     0  1.6G   0% /mnt/external
run             1.6G   24K  1.6G   1% /run
shmfs           1.6G     0  1.6G   0% /dev/shm
var             1.6G  4.0K  1.6G   1% /var
none            1.6G     0  1.6G   0% /sys/fs/cgroup
/dev/vdb         57M   57M     0 100% /opt/google/cros-containers
fonts           2.6G  1.9G  721M  73% /mnt/fonts
9p              3.9G  600K  3.9G   1% /mnt/shared
/dev/vdc         16G  1.5G   15G  10% /mnt/stateful
tmpfs           100K     0  100K   0% /mnt/stateful/lxd/shmounts
tmpfs           100K     0  100K   0% /mnt/stateful/lxd/devlxd
/dev/vdc         16G  1.5G   15G  10% /mnt/stateful/lxd/storage-pools/default

(termina) chronos@localhost ~ $ lxc image list
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+-------------------------------+
| ALIAS | FINGERPRINT  | PUBLIC |              DESCRIPTION               | ARCHITECTURE |   TYPE    |   SIZE   |          UPLOAD DATE          |
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+-------------------------------+
|       | 061937d12bfd | no     | Debian bullseye arm64 (20230523_09:28) | aarch64      | CONTAINER | 256.41MB | Aug 20, 2023 at 12:50am (UTC) |
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+-------------------------------+

(termina) chronos@localhost ~ $ lxc image info 061937d12bfd
Fingerprint: 061937d12bfdaf35c3d2dd90f17f8711b848a90a617d5bc7a1f8abbeb9c4c6e8
Size: 256.41MB
Architecture: aarch64
Type: container
Public: no
Timestamps:
    Created: 2023/05/23 00:00 UTC
    Uploaded: 2023/08/20 00:50 UTC
    Expires: never
    Last used: 2023/08/20 00:50 UTC
Properties:
    architecture: arm64
    description: Debian bullseye arm64 (20230523_09:28)
    os: Debian
    release: bullseye
    serial: 20230523_09:28
    type: squashfs
Aliases:
Cached: yes
Auto update: enabled
Source:
    Server: https://storage.googleapis.com/cros-containers/115
    Protocol: simplestreams
    Alias: debian/bullseye
Profiles:
    - default
(termina) chronos@localhost ~ $ lxc image show 061937d12bfd
auto_update: true
properties:
  architecture: arm64
  description: Debian bullseye arm64 (20230523_09:28)
  os: Debian
  release: bullseye
  serial: "20230523_09:28"
  type: squashfs
public: false
expires_at: 1970-01-01T00:00:00Z
profiles:
- default
(termina) chronos@localhost ~ $ lxc image delete 061937d12bfd

(termina) chronos@localhost ~ $ lxc image list  
+-------+-------------+--------+-------------+--------------+------+------+-------------+
| ALIAS | FINGERPRINT | PUBLIC | DESCRIPTION | ARCHITECTURE | TYPE | SIZE | UPLOAD DATE |
+-------+-------------+--------+-------------+--------------+------+------+-------------+

(termina) chronos@localhost ~ $ lxc storage list
+---------+-------------+--------+-----------------------------------------+---------+
|  NAME   | DESCRIPTION | DRIVER |                 SOURCE                  | USED BY |
+---------+-------------+--------+-----------------------------------------+---------+
| default |             | btrfs  | /mnt/stateful/lxd/storage-pools/default | 1       |
+---------+-------------+--------+-----------------------------------------+---------+

(termina) chronos@localhost ~ $ lxc storage info default
info:
  description: ""
  driver: btrfs
  name: default
  space used: 30.04MB
  total space: 16.00GB
used by:
  profiles:
  - default
  
```  


```
(termina) chronos@localhost ~ $ curl https://raw.githubusercontent.com/ptorre/crostini-lxc-cloud-containers/remove-sftp-service/cloud-cfg.yaml > /tmp/cloud-cfg.yaml

(termina) chronos@localhost ~ $ lxc launch images:debian/12/cloud penguin < ^C//raw.githubusercontent.com/ptorre/crostini-lxc-cloud-containers/remove-sftp-service/cloud-cfg.[1;3Dyaml
(termina) chronos@localhost ~ $ set -o vi
(termina) chronos@localhost ~ $ lxc launch images:debian/12/cloud penguin < /tmp/cloud-cfg.yaml 
Creating penguin
Starting penguin                              
(termina) chronos@localhost ~ $ lxc list
+---------+---------+-----------------------+----------+-----------+-----------+
|  NAME   |  STATE  |         IPV4          | IPV6     |   TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+----------+-----------+-----------+
| penguin | RUNNING | 100.115.92.205 (eth0) | 2601:... | CONTAINER | 0         |
+---------+---------+-----------------------+----------+-----------+-----------+

(termina) chronos@localhost ~ $ lxc exec penguin /bin/bash
root@penguin:~# cloud-init status
status: done
root@penguin:~# ps -ef | grep garcon
ptorre    1028   985  0 02:51 ?        00:00:00 /opt/google/cros-containers/bin/../lib/ld-linux-aarch64.so.1 --argv0 /opt/google/cros-containers/bin/garcon --library-path /opt/google/cros-containers/bin/../lib --inhibit-rpath  /opt/google/cros-containers/bin/garcon.elf --server
root      1035  1031  0 02:51 pts/1    00:00:00 grep garcon
root@penguin:~# 
root@penguin:~# 
exit
(termina) chronos@localhost ~ $ exit
logout
crosh> exit
```

```
crosh> vsh termina
(termina) chronos@localhost ~ $ lxc storage list
+---------+-------------+--------+-----------------------------------------+---------+
|  NAME   | DESCRIPTION | DRIVER |                 SOURCE                  | USED BY |
+---------+-------------+--------+-----------------------------------------+---------+
| default |             | btrfs  | /mnt/stateful/lxd/storage-pools/default | 3       |
+---------+-------------+--------+-----------------------------------------+---------+
(termina) chronos@localhost ~ $ lxc storage show default
config:
  source: /mnt/stateful/lxd/storage-pools/default
  volatile.initial_source: /mnt/stateful/lxd/storage-pools/default
description: ""
name: default
driver: btrfs
used_by:
- /1.0/images/94d9683004ff224230d0095fcc61fc4978d3abc6d81509883bfcb840db5e54a8
- /1.0/instances/penguin
- /1.0/profiles/default
status: Created
locations:
- none
(termina) chronos@localhost ~ $ lxc storage info default
info:
  description: ""
  driver: btrfs
  name: default
  space used: 886.75MB
  total space: 16.00GB
used by:
  images:
  - 94d9683004ff224230d0095fcc61fc4978d3abc6d81509883bfcb840db5e54a8
  instances:
  - penguin
  profiles:
  - default
(termina) chronos@localhost ~ $ lxc image list
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+------------------------------+
| ALIAS | FINGERPRINT  | PUBLIC |              DESCRIPTION               | ARCHITECTURE |   TYPE    |   SIZE   |         UPLOAD DATE          |
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+------------------------------+
|       | 94d9683004ff | no     | Debian bookworm arm64 (20230819_05:24) | aarch64      | CONTAINER | 118.13MB | Aug 20, 2023 at 2:48am (UTC) |
+-------+--------------+--------+----------------------------------------+--------------+-----------+----------+------------------------------+
(termina) chronos@localhost ~ $ lxc image delete 94d9683004ff
(termina) chronos@localhost ~ $ lxc image list
+-------+-------------+--------+-------------+--------------+------+------+-------------+
| ALIAS | FINGERPRINT | PUBLIC | DESCRIPTION | ARCHITECTURE | TYPE | SIZE | UPLOAD DATE |
+-------+-------------+--------+-------------+--------------+------+------+-------------+
(termina) chronos@localhost ~ $ lxc storage info default
info:
  description: ""
  driver: btrfs
  name: default
  space used: 886.76MB
  total space: 16.00GB
used by:
  instances:
  - penguin
  profiles:
  - default

# Wait about 10 seconds ...

(termina) chronos@localhost ~ $ lxc storage info default
info:
  description: ""
  driver: btrfs
  name: default
  space used: 744.79MB
  total space: 16.00GB
used by:
  instances:
  - penguin
  profiles:
  - default
(termina) chronos@localhost ~ $ exit 
logout
crosh> exit
```

```
crosh> vsh termina
vmc stop termina
crosh> exit
```
