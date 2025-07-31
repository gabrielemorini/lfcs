## 5. Storage

<details>
<summary><strong>What is partitioning?</strong></summary>
Partitioning is the process of dividing a storage device, such as a hard disk or SSD, into separate sections (partitions) that the operating system can manage individually.
</details>

<details>
<summary><strong>What is swap space?</strong></summary>
Swap space is a portion of a storage device used as virtual memory when the system's physical RAM is full. It temporarily holds data that would otherwise be kept in RAM.
</details>

<details>
<summary><strong>What is filesystem?</strong></summary>
A filesystem is a method and data structure used by an operating system to store, organize, and retrieve files on a storage device.
</details>

<details>
<summary><strong>What is dump and fsck in the fstab? Which is the fsck number for the root filesystem and which is for the other filesystems?</strong></summary>
The <strong>dump</strong> field in the fstab file indicates whether a filesystem should be backed up by the dump utility.<br>
The <strong>fsck</strong> field determines the order in which filesystems are checked at boot.<br>
The root filesystem is typically set to 1, and other filesystems are usually set to 2.
</details>

<details>
<summary><strong>What is MBR? What are its limits?</strong></summary>
<strong>MBR (Master Boot Record):</strong> A legacy partitioning scheme stored in the first sector of a disk.

<strong>Limits:</strong>
<ul>
<li>Maximum of 4 primary partitions.</li>
<li>Maximum disk size ~2 TB (because of 32-bit addressing).</li>
</ul>
</details>

<details>
<summary><strong>What is the difference between a primary partition and an extended partition?</strong></summary>
<ul>
<li>You can have up to 4 <strong>primary partitions</strong> on an MBR disk.</li>
<li>To have more, you create <strong>one extended partition</strong>, inside which you can create multiple <strong>logical partitions</strong> (e.g., 5th, 6th, etc.).</li>
</ul>
</details>

<details>
<summary><strong>What is GPT? Which command is used to partition with GPT?</strong></summary>
<strong>GPT (GUID Partition Table):</strong> A modern partitioning scheme that supports large disks and many partitions. You can manage GPT partitions with tools like gdisk, parted, or gparted.
</details>

<details>
<summary><strong>What is the difference between ext4 and XFS in LVM?</strong></summary>
<ul>
<li><strong>ext4:</strong> Supports both growing and shrinking.</li>
<li><strong>XFS:</strong> Supports growing only (cannot be shrunk).</li>
</ul>
</details>

<details>
<summary><strong>Added a second disk and partitioned /dev/sdb into /dev/sdb1 (5G) and /dev/sdb2 (2G). Show the list of all block devices.</strong></summary>

```bash
fdisk /dev/sdb
lsblk
```
</details>

<details>
<summary><strong>Show all swap. Create a swap filesystem on /dev/sdb2. Enable the new swap. Disable the new swap and erase the filesystem.</strong></summary>

```bash
swapon –show
mkswap /dev/sdb2
swapon /dev/sdb2
swapoff /dev/sdb2
wipefs -a /dev/sdb2
blkid /dev/sdb2
```
</details>

<details>
<summary><strong>Added an ext4 filesystem on /dev/sdb1 with the label 'BackupVolume'. Check the integrity of the filesystem.</strong></summary>

```bash
mkfs.ext4 -l 'BackupVolume' /dev/sdb1
fsck /dev/sdb1
```
</details>

<details>
<summary><strong>Create the directory /mybackups and mount the 'BackupVolume' on /mybackups. Then mount the filesystem permanently, update the configuration, and verify that the filesystem is mounted.</strong></summary>

```bash
mkdir /mybackups
mount /dev/sdb1 /mybackups

echo "/dev/sdb1 /mybackups ext4 defaults 0 2" >> /etc/fstab
systemctl daemon-reload
findmnt -x -s
```
</details>

---

<details>
<summary><strong>What is NFS?</strong></summary>
NFS (Network File System) is a protocol that allows a system to access files over a network as if they were on local storage.
</details>

<details>
<summary><strong>In NFS, what is the difference between mount and automount?</strong></summary>
<ul>
<li><strong>mount:</strong> A share is mounted immediately and stays mounted until unmounted or reboot.</li>
<li><strong>automount:</strong> Managed by the autofs service; mounts a share on demand when accessed and unmounts it after inactivity. This doesn't require constant root intervention.</li>
</ul>
</details>

<details>
<summary><strong>What is NBD?</strong></summary>
NBD (Network Block Device) is a protocol that allows a system to use remote storage devices over a network as if they were local block devices.
</details>

<details>
<summary><strong>When should I choose NFS and when should I choose NBD?</strong></summary>
Use NFS when you want to share directories and files over a network.<br>
Use NBD when you need remote block storage that behaves like a local disk.
</details>

<details>
<summary><strong>Configure NFS on the server and client. The directory /nfs/share should be read-only and accessible from 192.168.1.0/24. On the client, mount the NFS share /nfs/share to /nfs/terminal/share.</strong></summary>
<em>Configuration steps for NFS server and client setup.</em>
</details>

<details>
<summary><strong>Configure NBD on the server and client. Create two disks on the server and map them on the client.</strong></summary>
<em>Configuration steps for NBD server and client setup.</em>
</details>

<details>
<summary><strong>What is LVM? Explain how it works with app.diagrams.net.</strong></summary>
LVM (Logical Volume Manager) is a storage management technology that allows flexible disk management by creating logical volumes from physical storage devices. It supports resizing, snapshots, and efficient space allocation.
</details>

<details>
<summary><strong>What are physical extents?</strong></summary>
<strong>Physical Extents (PEs)</strong> are the <strong>smallest unit of space</strong> that LVM uses to manage and allocate storage on a physical volume.

<ul>
<li>They allow LVM to allocate and rearrange storage flexibly.</li>
<li>When you create or extend a <strong>Logical Volume</strong>, LVM assigns it enough <strong>Logical Extents (LEs)</strong>, which map directly to free <strong>PEs</strong> in the Volume Group.</li>
</ul>

<strong>Intuitive example:</strong><br>
Think of the Physical Extent (PE) as the size of the "bricks" you use to build your logical volumes:

<ul>
<li><strong>Small bricks (small PE size):</strong><br>
You can build walls of almost any length with very little waste, but if you want to build a skyscraper you'll need a <em>huge number</em> of bricks.</li>
<li><strong>Large bricks (large PE size):</strong><br>
You can build massive structures very quickly because each brick covers more space, but you can't make small, precise walls without wasting a lot of material.</li>
</ul>
</details>

<details>
<summary><strong>Create a 2-GiB volume group named myvg using 8-MiB physical extents. In this volume group, create a 500-MiB logical volume named mydata and mount it persistently on the directory /mydata.</strong></summary>

```bash
pvcreate /dev/sdb1
vgcreate -s 8Mib myvg /dev/sdb1
lvcreate -L 500m –name mydata myvg

mkfs.ext4 /dev/myvg/mydata
mkdir /mydata
/dev/myvg/mydata /mydata ext4 defaults 0 2
```
</details>

<details>
<summary><strong>Create a 4-GiB volume group using a physical extent size of 2 MiB. In this volume group, create a 1-GiB logical volume named myfiles, format it with the Ext3 filesystem, and mount it persistently on /myfiles.</strong></summary>

```bash
pvcreate /dev/sdb1
vgcreate -s 2Mib myvol /dev/sdb1
lvcreate -L 1G –name myfiles myvg

mkfs.ext3 /dev/myvg/myfiles
mkdir /myfiles
/dev/myvol/myfiles /myfiles ext3 defaults 0 2
```
</details>

<details>
<summary><strong>Use iostat and iotop to monitor storage performance.</strong></summary>
<em>Commands for monitoring storage performance using iostat and iotop tools.</em>
</details>

---

<details>
<summary><strong>What is ACL?</strong></summary>
ACL (Access Control List) is a permission mechanism that provides more fine-grained control over file access than the standard user/group/other model.
</details>

<details>
<summary><strong>Create a user named jeremy and allow read and write permissions to a file. Retrieve the ACL of the file and then remove it.</strong></summary>

```bash
useradd jeremy
touch file
getfacl file
setfact -m user:jeremy:rw- file
getfacl file
setfacl -x user:jeremy file
```
</details>