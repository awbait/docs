# Resize LVM Filesystem with reboot

Другой способ расширения корнегого раздела.

!!! warning "Требуется перезагрузка сервера"

### 1. Проверяем диск и структуру

Смотрим какой диск нужно расширить:
```
[root@server ~]# lsblk
NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                   8:0    0  180G  0 disk
├─sda1                8:1    0    1G  0 part /boot
├─sda2                8:2    0   15G  0 part
│ ├─centos-root     253:0    0   13G  0 lvm  /
│ └─centos-swap     253:1    0    2G  0 lvm
└─sda3                8:3    0  114G  0 part
  └─vg_data-lv_data 253:2    0  114G  0 lvm  /var/lib/pgsql
```

Коневая система, которую я хочу расширить находится в _**/dev/sda2**_

Проверить разделы мы можем так же командой:

```
fdisk -l
```

Получили:
```
[root@server ~]# fdisk -l

Disk /dev/sda: 193.3 GB, 193273528320 bytes, 377487360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x00016994

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    33554431    15727616   8e  Linux LVM
/dev/sda3        33554432   272629759   119537664   8e  Linux LVM

Disk /dev/mapper/centos-root: 14.0 GB, 13958643712 bytes, 27262976 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2143 MB, 2143289344 bytes, 4186112 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/vg_data-lv_data: 122.4 GB, 122402373632 bytes, 239067136 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### 2. Создаем новый раздел

```
fdisk /dev/sda
```

Далее вводим параметры:
```
n # Создать новый раздел
p # тип раздела Основной
4 # номер раздела 4
Enter # Начальный сектор оставляем по умолчанию
Enter # Конечный сектор оставляем по умолчанию
```

Получили следующее:
```
Command (m for help): n
Partition type:
   p   primary (3 primary, 0 extended, 1 free)
   e   extended
Select (default e): p
Selected partition 4
First sector (272629760-377487359, default 272629760):
Using default value 272629760
Last sector, +sectors or +size{K,M,G} (272629760-377487359, default 377487359):
Using default value 377487359
Partition 4 of type Linux and of size 50 GiB is set
```

Изменяем тип раздела с Linux на Linux LVM:

```
Command (m for help): t
Partition number (1-4, default 4): 4
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'
```

Проверяем, что раздел создался:

```
Command (m for help): p

Disk /dev/sda: 193.3 GB, 193273528320 bytes, 377487360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x00016994

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    33554431    15727616   8e  Linux LVM
/dev/sda3        33554432   272629759   119537664   8e  Linux LVM
/dev/sda4       272629760   377487359    52428800   8e  Linux LVM
```

Сохраняем таблицу разделов:

```
Command (m for help): w
The partition table has been altered!
```

### 3. Добавляем раздел к точке монтирования

Просмотреть физических томов:
```
[root@server ~]# pvs
  PV         VG      Fmt  Attr PSize    PFree
  /dev/sda2  centos  lvm2 a--   <15.00g     0
  /dev/sda3  vg_data lvm2 a--  <114.00g     0
```

Создаём физический том:
```
pvcreate /dev/sda4
```

Далее необходимо назначить физическому тому группу.

Просмотреть группы томов (VG) можно командой:
```
[root@server ~]# vgs
  VG      #PV #LV #SN Attr   VSize    VFree
  centos    1   2   0 wz--n-  <15.00g    0
  vg_data   1   1   0 wz--n- <114.00g    0
```

Расширяем группу нашим разделом:
```
[root@server ~]# vgextend centos /dev/sda4
  Volume group "centos" successfully extended
```

Далее необходимо расширить логический том:

Просмотреть логические тома можно командой:

```
[root@server ~]# lvs
  LV      VG      Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root    centos  -wi-ao----   13.00g
  swap    centos  -wi-a-----   <2.00g
  lv_data vg_data -wi-ao---- <114.00g
```

Расширяем логический том:
```
[root@server ~]# lvextend -l +100%FREE /dev/centos/root
  Size of logical volume centos/root changed from 13.00 GiB (3328 extents) to <63.00 GiB (16127 extents).
  Logical volume centos/root successfully resized.
```

### 4. Расширяем файловую систему xfs

```
[root@server ~]# xfs_growfs /dev/centos/root
meta-data=/dev/mapper/centos-root isize=512    agcount=4, agsize=851968 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=3407872, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 3407872 to 16514048
```

Готово:
```
[root@server ~]# df -h
Filesystem                   Size  Used Avail Use% Mounted on
devtmpfs                     7.8G     0  7.8G   0% /dev
tmpfs                        7.8G  240K  7.8G   1% /dev/shm
tmpfs                        7.8G  8.9M  7.8G   1% /run
tmpfs                        7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/mapper/centos-root       63G   12G   52G  19% /
/dev/sda1                   1014M  186M  829M  19% /boot
/dev/mapper/vg_data-lv_data  114G   75G   40G  66% /var/lib/pgsql
tmpfs                        1.6G     0  1.6G   0% /run/user/0
```