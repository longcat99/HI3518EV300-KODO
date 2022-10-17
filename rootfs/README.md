# 制作文件系统镜像（64MB nor flash）：

-> cd rootfs
-> ./mkfs.jffs2 -d ./rootfs_uclibc -l -e 0x10000 -o rootfs.jffs2

