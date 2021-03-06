Commit exceptions used to explicitly reference a given Linux commit.
These exceptions are useful for a variety of reasons.

**This page is used to generate [OpenZFS Tracking](http://build.zfsonlinux.org/openzfs-tracking.html) page.**

#### Format:
- `<openzfs issue>|-|<comment>` - 
The OpenZFS commit isn't applicable to Linux, or 
the OpenZFS -> ZFS on Linux commit matching is unable to associate
the related commits due to lack of information (denoted by a -).
- `<openzfs issue>|<commit>|<comment>` - 
The fix was merged to Linux prior to their being an OpenZFS issue.
- `<openzfs issue>|!|<comment>` - 
The commit is applicable but not applied for the reason described in the comment.

OpenZFS issue id | status/ZFS commit | comment
---|---|---
9672|29445fe3|
9626|59e6e7ca|
9621|305bc4b3|
9539|5228cf01|
9512|b4555c77|
9487|48fbb9dd|
9421|64c1dcef|
9284|!       |Needs evaluation, similar to the existing mechanism on Linux
9237|-       |Introduced by 8567 which was never applied to Linux
9194|-       |Not applicable the '-o ashift=value' option is provided on Linux
9077|-       |Not applicable to Linux
9027|4a5d7f82|
9018|!       |Needs evaluation, similar to the existing mechanism on Linux.
8984|-       |Under Linux POSIX ACLs are used and largely handled by the VFS. This change can be ported to minimize code drift but are not required.
8969|-       |Not applicable to Linux
8942|650258d7|
8941|390d679a|
8858|-       |Not applicable to Linux
8856|-       |Not applicable to Linux due to Encryption (b525630) 
8713|871e0732|
8661|1ce23dca|
8648|f763c3d1|
8602|a032ac4|
8590|935e2c2|
8569|-      |This change isn't relevant for Linux.
8567|-      |An alternate fix was applied for Linux.
8552|935e2c2|
8521|ee6370a7|
8502|!      | Apply when porting OpenZFS 7955
8477|92e43c1|
8454|-      |An alternate fix was applied for Linux.
8408|5f1346c|
8379|-      |This change isn't relevant for Linux.
8376|-      |This change isn't relevant for Linux.
8311|!      |Need to assess applicability to Linux.
8304|-      |This change isn't relevant for Linux.
8300|44f09cd|
8265|-      |The large_dnode feature has been implemented for Linux.
8168|78d95ea|
8138|-      |This man update will be picked up when the mandoc pages are merged.
8108|-      |An equivalent Linux specific fix was made.
8064|-      |This change isn't relevant for Linux.
8021|7657def|
8022|e55ebf6|
8013|-      |The change is illumos specific and not applicable for Linux.
7982|-      |The change is illumos specific and not applicable for Linux.
7970|c30e58c|
7956|cda0317|
7955|!      |Need to assess applicability to Linux.
7869|df7eecc|
7816|-      |The change is illumos specific and not applicable for Linux.
7803|-      |This functionality is provided by `update_vdev_config_dev_strs()` on Linux.
7801|0eef1bd|Commit f25efb3 in openzfs/master has a small change for linting which is being ported.
7779|-      |The change isn't relevant, `zfs_ctldir.c` was rewritten for Linux.
7740|32d41fb|
7739|582cc014|
7730|e24e62a|
7710|-      |None of the illumos build system is used under Linux.
7602|44f09cd|
7591|541a090|
7586|c443487|
7570|-      |Due to differences in the block layer all discards are handled asynchronously under Linux.  This functionality could be ported but it's unclear to what purpose.
7542|-      |The Linux libshare code differs significantly from the upstream OpenZFS code.  Since this change doesn't address a Linux specific issue it doesn't need to be ported.  The eventual plan is to retire all of the existing libshare code and use the ZED to more flexibly control filesystem sharing.
7512|-      |None of the illumos build system is used under Linux.
7497|-      |DTrace is isn't readily available under Linux.
7446|!      |Waiting on PR #6277
7430|68cbd56|
7402|690fe64|
7345|058ac9b|
7278|-      |Dynamic ARC tuning is handled slightly differently under Linux and this case is covered by arc_tuning_update()
7238|-      |zvol_swap test already disabled in ZoL
7194|d7958b4|
7164|b1b85c87|
7041|33c0819|
7016|d3c2ae1|
6914|-      |Under Linux the arc_meta_limit can be tuned with the zfs_arc_meta_limit_percent module option.
6875|-      |Not currently applicable to Linux where POSIX ACLs are primarily used.
6843|f5f087e|
6841|4254acb|
6781|15313c5|
6765|-      |Under Linux POSIX ACLs are used and largely handled by the VFS.  This change can be ported to minimize code drift but are not required.  
6764|-      |Under Linux POSIX ACLs are used and largely handled by the VFS.  This change can be ported to minimize code drift but are not required.  
6763|-      |Under Linux POSIX ACLs are used and largely handled by the VFS.  This change can be ported to minimize code drift but are not required.  
6762|-      |Under Linux POSIX ACLs are used and largely handled by the VFS.  This change can be ported to minimize code drift but are not required.  
6648|6bb24f4|
6578|6bb24f4|
6577|6bb24f4|
6575|6bb24f4|
6568|6bb24f4|
6528|6bb24f4|
6494|-      |The `vdev_disk.c` and `vdev_file.c` files have been reworked extensively for Linux.  The proposed changes are not needed.
6468|6bb24f4|
6465|6bb24f4|
6434|472e7c6|
6421|ca0bf58|
6418|131cc95|
6391|ee06391|
6390|85802aa|
6388|0de7c55|
6386|485c581|
6385|f3ad9cd|
6369|6bb24f4|
6368|2024041|
6346|058ac9b|
6334|1a04bab|
6290|017da6 |
6250|-      |Linux handles crash dumps in a fundamentally different way than Illumos.  The proposed changes are not needed.
6249|6bb24f4|
6248|6bb24f4|
6220|-      |The b_thawed debug code was unused under Linux and removed.
6209|-      |The Linux user space mutex implementation is based on phtread primitives. 
6095|f866a4ea|
6091|c11f100|
5984|480f626|
5966|6bb24f4|
5961|22872ff|
5815|-      |This patch could be adapted if needed use equivalent Linux functionality.
5770|c3275b5|
5769|dd26aa5|
5768|-      |The change isn't relevant, `zfs_ctldir.c` was rewritten for Linux.
5766|4dd1893|
5693|0f7d2a4|
5692|!      |This functionality should be ported in such a way that it can be integrated with `filefrag(8)`.
5684|6bb24f4|
5410|0bf8501|
5409|b23d543|
5379|-      |This particular issue never impacted Linux due to the need for a modified zfs_putpage() implementation.
5316|-      |The illumos idmap facility isn't available under Linux.  This patch could still be applied to minimize code delta or all HAVE_IDMAP chunks could be removed on Linux for better readability.
5313|ec8501e|
5312|!      |This change should be made but the ideal time to do it is when the spl repository is folded in to the zfs repository (planned for 0.8).  At this time we'll want to cleanup many of the includes.
5219|ef56b07|
5179|3f4058c|
5149|-      |Equivalent Linux functionality is provided by the `zvol_max_discard_blocks` module option.
5148|-      |Discards are handled differently under Linux, there is no DKIOCFREE ioctl.
5136|e8b96c6|
4752|aa9af22|
4745|411bf20|
4698|4fcc437|
4620|6bb24f4|
4573|10b7549|
4571|6e1b9d0|
4570|b1d13a6|
4391|78e2739|
4465|cda0317|
4263|6bb24f4|
4242|-      |Neither vnodes or their associated events exist under Linux.
4206|2820bc4|
4188|2e7b765|
4181|44f09cd|
4161|-      |The Linux user space reader/writer implementation is based on phtread primitives.
4128|!      |The ldi_ev_register_callbacks() interface doesn't exist under Linux.  It may be possible to receive similar notifications via the scsi error handlers or possibly a different interface.
4072|-      |None of the illumos build system is used under Linux.
3947|7f9d994|
3928|-      |Neither vnodes or their associated events exist under Linux.
3871|d1d7e268|
3747|090ff09|
3705|-      |The Linux implementation uses the lz4 workspace kmem cache to resolve the stack issue.
3606|c5b247f|
3580|-      |Linux provides generic ioctl handlers get/set block device information.
3543|8dca0a9|
3512|67629d0|
3507|43a696e|
3444|6bb24f4|
3371|44f09cd|
3311|6bb24f4|
3301|-      |The Linux implementation of `vdev_disk.c` does not include this comment.
3258|9d81146|
3254|-      |The `aclmode` property cannot be supported under Linux.
3246|cc92e9d|
2933|-      |None of the illumos build system is used under Linux.
2897|fb82700|
2665|32a9872|
2130|460a021|
1974|-      |This change was entirely replaced in the ARC restructuring.
1898|-      |The zfs_putpage() function was rewritten to properly integrate with the Linux VM.
1618|ca67b33|
1337|2402458|
1126|e43b290|
763 |3cee226|
742 |-      |The `aclmode` property cannot be supported under Linux.
701 |460a021|
348 |-      |The Linux implementation of `vdev_disk.c` must have this differently.
243 |-      |Manual updates have been made separately for Linux.
184 |-      |The zfs_putpage() function was rewritten to properly integrate with the Linux VM.