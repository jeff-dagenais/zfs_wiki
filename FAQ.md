### What is ZFS on Linux

The ZFS on Linux project is an implementation of [OpenZFS][OpenZFS] designed to work in a Linux environment.  OpenZFS is an outstanding storage platform that encompasses the functionality of traditional filesystems, volume managers, and more, with consistent reliability, functionality and performance across all distributions  Additional information about OpenZFS can be found in the [OpenZFS wikipedia article][wikipedia].

### Hardware Requirements

Because ZFS was originally designed for Sun Solaris it was long considered a filesystem for large servers and for companies that could afford the best and most powerful hardware available. But since the porting of ZFS to numerous OpenSource platforms (The BSDs, Illumos and Linux - under the umbrella organization "OpenZFS"), these requirements have been lowered.

The suggested hardware requirements are:
  * ECC memory. This isn't really a requirement, but it's highly recommended.
  * 8GB+ of memory for the best performance.  It's perfectly possible to run with 2GB or less (and people do), but you'll need more if using deduplication.

### Do I have to use ECC memory for ZFS?

Using ECC memory for OpenZFS is strongly recommended for enterprise environments where the strongest data integrity guarantees are required. Without ECC memory rare random bit flips caused by cosmic rays or by faulty memory can go undetected. If this were to occur OpenZFS (or any other filesystem) will write the damaged data to disk and be unable to automatically detect the corruption.

Unfortunately, ECC memory is not always supported by consumer grade hardware. And even when it is ECC memory will be more expensive. For home users the additional safety brought by ECC memory might not justify the cost. It's up to you to determine what level of protection your data requires.

### Installation

ZFS on Linux is available for all major Linux distributions.  Refer to the [[getting started]] section of the wiki for links to installations instructions for many popular distributions.  If your distribution isn't listed you can always build ZFS on Linux from the latest official [tarball][releases].

### Supported Architectures

ZFS on Linux is regularly compiled for the following architectures: x86_64, x86, aarch64, arm, ppc64, ppc.

### Supported Kernels

The [notes][releases] for a given ZFS on Linux release will include a range of supported kernels.  Point releases will be tagged as needed in order to support the *stable* kernel available from [kernel.org][kernel].  The oldest supported kernel is 2.6.32 due to its prominence in Enterprise Linux distributions.

### 32-bit vs 64-bit Systems

You are **strongly** encouraged to use a 64-bit kernel.  ZFS on Linux will build for 32-bit kernels but you may encounter stability problems.

ZFS was originally developed for the Solaris kernel which differs from the Linux kernel in several significant ways.  Perhaps most importantly for ZFS it is common practice in the Solaris kernel to make heavy use of the virtual address space.  However, use of the virtual address space is strongly discouraged in the Linux kernel.  This is particularly true on 32-bit architectures where the virtual address space is limited to 100M by default. Using the virtual address space on 64-bit Linux kernels is also discouraged but the address space is so much larger than physical memory it is less of an issue.

If you are bumping up against the virtual memory limit on a 32-bit system you will see the following message in your system logs. You can increase the virtual address size with the boot option `vmalloc=512M`.

```
vmap allocation for size 4198400 failed: use vmalloc=<size> to increase size.
```

However, even after making this change your system will likely not be entirely stable. Proper support for 32-bit systems is contingent upon the OpenZFS code being weaned off its dependence on virtual memory. This will take some time to do correctly but it is planned for OpenZFS. This change is also expected to improve how efficiently OpenZFS manages the ARC cache and allow for tighter integration with the standard Linux page cache.

### Booting from ZFS

Booting from ZFS on Linux is possible and many people do it.  However, because it often requires the latest versions of grub and is distribution specific we don't recommend it. Instead we suggest using ZFS on Linux as your root file system. There are excellent walk throughs available for [Debian][debian-root], [Ubuntu][ubuntu-root] and [Gentoo][gentoo-root].

### Selecting /dev/ names when creating a pool

There are different /dev/ names that can be used when creating a ZFS pool. Each option has advantages and drawbacks, the right choice for your ZFS pool really depends on your requirements. For development and testing using /dev/sdX naming is quick and easy. A typical home server might prefer /dev/disk/by-id/ naming for simplicity and readability. While very large configurations with multiple controllers, enclosures, and switches will likely prefer /dev/disk/by-vdev naming for maximum control. But in the end, how you choose to identify your disks is up to you.

* **/dev/sdX, /dev/hdX:** Best for development/test pools
  * Summary: The top level /dev/ names are the default for consistency with other ZFS implementations. They are available under all Linux distributions and are commonly used. However, because they are not persistent they should only be used with ZFS for development/test pools.
  * Benefits:This method is easy for a quick test, the names are short, and they will be available on all Linux distributions.
  * Drawbacks:The names are not persistent and will change depending on what order they disks are detected in. Adding or removing hardware for your system can easily cause the names to change. You would then need to remove the zpool.cache file and re-import the pool using the new names.
  * Example: `zpool create tank sda sdb`

* **/dev/disk/by-id/:** Best for small pools (less than 10 disks)
  * Summary: This directory contains disk identifiers with more human readable names. The disk identifier usually consists of the interface type, vendor name, model number, device serial number, and partition number. This approach is more user friendly because it simplifies identifying a specific disk.
  * Benefits: Nice for small systems with a single disk controller. Because the names are persistent and guaranteed not to change, it doesn't matter how the disks are attached to the system. You can take them all out, randomly mixed them up on the desk, put them back anywhere in the system and your pool will still be automatically imported correctly.
  * Drawbacks: Configuring redundancy groups based on physical location becomes difficult and error prone.
  * Example: `zpool create tank scsi-SATA_Hitachi_HTS7220071201DP1D10DGG6HMRP`

* **/dev/disk/by-path/:** Good for large pools (greater than 10 disks)
  * Summary: This approach is to use device names which include the physical cable layout in the system, which means that a particular disk is tied to a specific location. The name describes the PCI bus number, as well as enclosure names and port numbers. This allows the most control when configuring a large pool.
  * Benefits: Encoding the storage topology in the name is not only helpful for locating a disk in large installations. But it also allows you to explicitly layout your redundancy groups over multiple adapters or enclosures.
  * Drawbacks: These names are long, cumbersome, and difficult for a human to manage.
  * Example: `zpool create tank pci-0000:00:1f.2-scsi-0:0:0:0 pci-0000:00:1f.2-scsi-1:0:0:0`

* **/dev/disk/by-vdev/:** Best for large pools (greater than 10 disks)
  * Summary: This approach provides administrative control over device naming using the configuration file /etc/zfs/vdev_id.conf. Names for disks in JBODs can be generated automatically to reflect their physical location by enclosure IDs and slot numbers. The names can also be manually assigned based on existing udev device links, including those in /dev/disk/by-path or /dev/disk/by-id. This allows you to pick your own unique meaningful names for the disks. These names will be displayed by all the zfs utilities so it can be used to clarify the administration of a large complex pool. See the vdev_id and vdev_id.conf man pages for further details.
  * Benefits: The main benefit of this approach is that it allows you to choose meaningful human-readable names. Beyond that, the benefits depend on the naming method employed. If the names are derived from the physical path the benefits of /dev/disk/by-path are realized. On the other hand, aliasing the names based on drive identifiers or WWNs has the same benefits as using /dev/disk/by-id.
  * Drawbacks: This method relies on having a /etc/zfs/vdev_id.conf file properly configured for your system. To configure this file please refer to section 1.10 How do I setup the /etc/zfs/vdev_id.conf file? As with benefits, the drawbacks of /dev/disk/by-id or /dev/disk/by-path may apply depending on the naming method employed.
  * Example: `zpool create tank mirror A1 B1 mirror A2 B2`

### Setting up the /etc/zfs/vdev_id.conf file

In order to use /dev/disk/by-vdev/ naming the `/etc/zfs/vdev_id.conf` must be configured. The format of this file is described in the vdev_id.conf man page. Several examples follow.

A non-multipath configuration with direct-attached SAS enclosures and an arbitrary slot re-mapping.

```
            multipath     no
            topology      sas_direct
            phys_per_port 4

            #       PCI_SLOT HBA PORT  CHANNEL NAME
            channel 85:00.0  1         A
            channel 85:00.0  0         B

            #    Linux      Mapped
            #    Slot       Slot
            slot 0          2
            slot 1          6
            slot 2          0
            slot 3          3
            slot 4          5
            slot 5          7
            slot 6          4
            slot 7          1
```

A SAS-switch topology. Note that the channel keyword takes only two arguments in this example.

```
            topology      sas_switch

            #       SWITCH PORT  CHANNEL NAME
            channel 1            A
            channel 2            B
            channel 3            C
            channel 4            D
```

A multipath configuration. Note that channel names have multiple definitions - one per physical path.

```
            multipath yes

            #       PCI_SLOT HBA PORT  CHANNEL NAME
            channel 85:00.0  1         A
            channel 85:00.0  0         B
            channel 86:00.0  1         A
            channel 86:00.0  0         B
```

A configuration using device link aliases.

```
            #     by-vdev
            #     name     fully qualified or base name of device link
            alias d1       /dev/disk/by-id/wwn-0x5000c5002de3b9ca
            alias d2       wwn-0x5000c5002def789e
```

After defining the new disk names run udevadm trigger to prompt udev to parse the configuration file. This will result in a new /dev/disk/by-vdev directory which is populated with symlinks to /dev/sdX names. Following the first example above, you could then create the new pool of mirrors with the following command:

```
$ zpool create tank \
	mirror A0 B0 mirror A1 B1 mirror A2 B2 mirror A3 B3 \
	mirror A4 B4 mirror A5 B5 mirror A6 B6 mirror A7 B7

$ zpool status
  pool: tank
 state: ONLINE
 scan: none requested
config:

	NAME        STATE     READ WRITE CKSUM
	tank        ONLINE       0     0     0
	  mirror-0  ONLINE       0     0     0
	    A0      ONLINE       0     0     0
	    B0      ONLINE       0     0     0
	  mirror-1  ONLINE       0     0     0
	    A1      ONLINE       0     0     0
	    B1      ONLINE       0     0     0
	  mirror-2  ONLINE       0     0     0
	    A2      ONLINE       0     0     0
	    B2      ONLINE       0     0     0
	  mirror-3  ONLINE       0     0     0
	    A3      ONLINE       0     0     0
	    B3      ONLINE       0     0     0
	  mirror-4  ONLINE       0     0     0
	    A4      ONLINE       0     0     0
	    B4      ONLINE       0     0     0
	  mirror-5  ONLINE       0     0     0
	    A5      ONLINE       0     0     0
	    B5      ONLINE       0     0     0
	  mirror-6  ONLINE       0     0     0
	    A6      ONLINE       0     0     0
	    B6      ONLINE       0     0     0
	  mirror-7  ONLINE       0     0     0
	    A7      ONLINE       0     0     0
	    B7      ONLINE       0     0     0

errors: No known data errors
```

### Changing /dev/ names on an existing pool

Changing the /dev/ names on an existing pool can be done by simply exporting the pool and re-importing it with the -d option to specify which new names should be used. For example, to use the custom names in /dev/disk/by-vdev:

```
$ zpool export tank
$ zpool import -d /dev/disk/by-vdev tank
```

### The /etc/zfs/zpool.cache file

Whenever a pool is imported on the system it will be added to the `/etc/zfs/zpool.cache file`. This file stores pool configuration information, such as the device names and pool state.  If this file exists when running the `zpool import` command then it will be used to determine the list of pools available for import.  When a pool is not listed in the cache file it will need to be detected and imported using the `zpool import -d /dev/disk/by-id` command.

### Generating a new /etc/zfs/zpool.cache file

The `/etc/zfs/zpool.cache` file will be automatically updated when your pool configuration is changed. However, if for some reason it becomes stale you can force the generation of a new `/etc/zfs/zpool.cache` file by setting the cachefile property on the pool.

```
$ zpool set cachefile=/etc/zfs/zpool.cache tank
```

Conversely the cache file can be disabled by setting `cachefile=none`.  This is useful for failover configurations where the pool should always be explicitly imported by the failover software.

```
$ zpool set cachefile=none tank
```

### Performance Considerations

To achieve good performance with your pool there are some easy best practices you should follow. Additionally, it should be made clear that the ZFS on Linux implementation has not yet been optimized for performance. As the project matures we can expect performance to improve.

* **Evenly balance your disk across controllers:** Often the limiting factor for performance is not the disk but the controller. By balancing your disks evenly across controllers you can often improve throughput.
* **Create your pool using whole disks:** When running zpool create use whole disk names. This will allow ZFS to automatically partition the disk to ensure correct alignment. It will also improve interoperability with other OpenZFS implementations which honor the wholedisk property.
* **Have enough memory:** A minimum of 2GB of memory is recommended for ZFS. Additional memory is strongly recommended when the compression and deduplication features are enabled.
* **Improve performance by setting ashift=12:** You may be able to improve performance for some workloads by setting `ashift=12`. This tuning can only be set when block devices are first added to a pool, such as when the pool is first created or when a new vdev is added to the pool. This tuning parameter can result in a decrease of capacity for RAIDZ configuratons.

### Advanced Format Disks

Advanced Format (AF) is a new disk format which natively uses a 4,096 byte, instead of 512 byte, sector size. To maintain compatibility with legacy systems many AF disks emulate a sector size of 512 bytes. By default, ZFS will automatically detect the sector size of the drive. This combination can result in poorly aligned disk accesses which will greatly degrade the pool performance.

Therefore, the ability to set the ashift property has been added to the zpool command. This allows users to explicitly assign the sector size when devices are first added to a pool (typically at pool creation time or adding a vdev to the pool). The ashift values range from 9 to 16 with the default value 0 meaning that zfs should auto-detect the sector size. This value is actually a bit shift value, so an ashift value for 512 bytes is 9 (2^9 = 512) while the ashift value for 4,096 bytes is 12 (2^12 = 4,096).

To force the pool to use 4,096 byte sectors at pool creation time, you may run:

```
$ zpool create -o ashift=12 tank mirror sda sdb
```

To force the pool to use 4,096 byte sectors when adding a vdev to a pool, you may run:

```
$ zpool add -o ashift=12 tank mirror sdc sdd
```

### Using a zvol for a swap device

You may use a zvol as a swap device but you'll need to configure it appropriately.

* Set the volume block size to match your systems page size, for x86_64 systems that is 4k.  This tuning prevents ZFS from having to perform read-modify-write options on a larger block while the system is already low on memory.
* Set the `sync=always` property.  Data written to the volume will be flushed immediately to disk freeing up memory as quickly as possible.
* Disable automatic snapshots of the swap device.

```
$ zfs create -V 4G -b 4K rpool/swap
$ zfs set com.sun:auto-snapshot=false rpool/swap
$ zfs set sync=always rpool/swap
```

### Using ZFS on Xen Hypervisor or Xen Dom0

It is usually recommended to keep virtual machine storage and hypervisor pools, quite separate. Although few people have managed to successfully deploy and run ZFS on Linux using the same machine configured as Dom0. There are few caveats:

  * Set a fair amount of memory in grub.conf, dedicated to Dom0.
    * dom0_mem=16384M,max:16384M
  * Allocate no more of 30-40% of Dom0's memory to ZFS in `/etc/modprobe.d/zfs.conf`.
    * options zfs zfs_arc_max=6442450944
  * Disable Xen's auto-ballooning in `/etc/xen/xl.conf`
  * Watch out for any Xen bugs, such as [this one][xen-bug] related to ballooning

### Licensing

ZFS is licensed under the Common Development and Distribution License ([CDDL][cddl]), and the Linux kernel is licensed under the GNU General Public License Version 2 ([GPLv2][gpl]). While both are free open source licenses they are restrictive licenses. The combination of them causes problems because it prevents using pieces of code exclusively available under one license with pieces of code exclusively available under the other in the same binary. In the case of the kernel, this prevents us from distributing ZFS on Linux as part of the kernel binary. However, there is nothing in either license that prevents distributing it in the form of a binary module or in the form of source code.

Additional reading and opinions:

* [Software Freedom Law Center][lawcenter]
* [Software Freedom Conservancy][conservancy]
* [Free Software Foundation][fsf]
* [Encouraging closed source modules][networkworld]

### Reporting a problem

You can open a new issue and search existing issues using the public [issue tracker][issues]. The issue tracker is used to organize outstanding bug reports, feature requests, and other development tasks. Anyone may post comments after signing up for a github account.

Please make sure that what you're actually seeing is a bug and not a support issue.  If in doubt, please ask on the mailing list first, and if you're then asked to file an issue, do so.

When opening a new issue include this information at the top of the issue:

* What distribution you're using and the version.
* What spl/zfs packages you're using and the version.
* Describe the problem you're observing.
* Describe how to reproduce the problem.
* Including any warning/errors/backtraces from the system logs.

When a new issue is opened it's not uncommon for a developer to request additional information about the problem. In general, the more detail you share about a problem the quicker a developer can resolve it. For example, providing a simple test case is always exceptionally helpful.  Be prepared to work with the developer looking in to your bug in order to get it resolved.  They may ask for information like:

* Your pool configuration as reported by `zdb` or `zpool status`.
* Your hardware configuration, such as
  * Number of CPUs.
  * Amount of memory.
  * Whether your system has ECC memory.
  * Whether it is running under a VMM/Hypervisor.
  * Kernel version.
  * Values of the spl/zfs module parameters.
* Stack traces which may be logged to `dmesg`.

[OpenZFS]: http://open-zfs.org/wiki/Main_Page
[wikipedia]: https://en.wikipedia.org/wiki/OpenZFS
[releases]: https://github.com/zfsonlinux/zfs/releases
[kernel]: https://www.kernel.org/
[debian-root]: https://github.com/zfsonlinux/pkg-zfs/wiki/HOWTO-install-Debian-GNU-Linux-to-a-Native-ZFS-Root-Filesystem
[ubuntu-root]: https://github.com/zfsonlinux/pkg-zfs/wiki/HOWTO-install-Ubuntu-to-a-Native-ZFS-Root-Filesystem
[gentoo-root]: https://github.com/pendor/gentoo-zfs-install/tree/master/install
[xen-bug]: https://github.com/zfsonlinux/zfs/issues/1067
[cddl]: http://hub.opensolaris.org/bin/view/Main/opensolaris_license
[gpl]: http://www.gnu.org/licenses/gpl2.html
[lawcenter]: https://www.softwarefreedom.org/resources/2016/linux-kernel-cddl.html
[conservancy]: https://sfconservancy.org/blog/2016/feb/25/zfs-and-linux/
[fsf]: https://www.fsf.org/licensing/zfs-and-linux
[networkworld]: http://www.networkworld.com/news/2006/120606-closed-modules1.html
[issues]: https://github.com/zfsonlinux/zfs/issues