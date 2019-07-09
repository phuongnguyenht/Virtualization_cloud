#------------ Tăng ổ cứng sau khi cài máy ảo từ template -----------------
```
TH Template chỉ có 40GB ổ cứng nhưng trong lúc cài máy ảo lại tạo cho nó 100GB ổ cứng.
TH này phải cấu hình để tăng cho VM để VM nhận đúng với dung lượng thực tế.
```
## Sau khi cài xong VM login vào VM  
```
Tạo phân vùng /dev/sda3
$ fdisk /dev/sda
Then:
    type p - to list all your partitions
    type n - to create a new partition
    type l - for "logical"
    then give it a number (e.g. if you got 2 partitions listed as /dev/sda1 & /dev/sda2, for the new partition simply type "3" to create /dev/sda3)
    type t - to change the partition type to "Linux LVM"
    provide the partition number you previously created
    type 8e - for the "Linux LVM" type
    type p - to list the new partition table
    type w - to write changes and exit

- Reboot server:
$ reboot
$ pvcreate /dev/sda3
$ pvresize /dev/sda3
$ vgextend cl /dev/sda3
# TH đối với Centos
$ vgextend centos /dev/sda3
# /dev/mapper/ TAB
Check disk 
	[root@localhost ~]# df -h
	Filesystem               Size  Used Avail Use% Mounted on
	/dev/mapper/centos-root   97G  1.2G   96G   2% /

$ lvextend --extents +100%FREE /dev/mapper/centos-root --resizefs
xfs_growfs /dev/mapper/cl-root
# TH đối với Centos
$  xfs_growfs /dev/mapper/centos-root 
```
## Check lai thong tin hdd
```
[root@localhost ~]# vgdisplay 
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  9
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               98.99 GiB
  PE Size               4.00 MiB
  Total PE              25342
  Alloc PE / Size       25342 / 98.99 GiB
  Free  PE / Size       0 / 0   
  VG UUID               1wuPvJ-AdzC-eHup-YHBs-LwUh-urai-JEiXMa
   
[root@localhost ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               centos
  PV Size               <39.00 GiB / not usable 2.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              9983
  Free PE               0
  Allocated PE          9983
  PV UUID               lB457l-C30d-LYUY-tnCN-dTWh-QzjE-s1heEJ
   
  --- Physical volume ---
  PV Name               /dev/sda3
  VG Name               centos
  PV Size               60.00 GiB / not usable 4.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              15359
  Free PE               0
  Allocated PE          15359
  PV UUID               gQoFj8-r2ry-ZMg3-kQWg-zc9n-ZxKI-9xhnSi
  ```
3. Tham khảo: https://www.joomlaworks.net/blog/item/168-resizing-the-disk-space-on-ubuntu-server-vms-running-on-vmware-esxi-5
4. https://www.linuxquestions.org/questions/linux-newbie-8/vgextend-lvm-resizing-4175463294/