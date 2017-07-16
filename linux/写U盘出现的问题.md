在写U盘的时候，报了这样的错，记录如下:

```bash
umount /dev/sdb    // 提示未挂载
mkfs.vfat /dev/sdb // device or resource busy
umount /dev/sdb1  // ok
mkfs.vfat /dev/sdb  // 提示整块磁盘，需要-I
mkfs.vfat -I /dev/sdb
```