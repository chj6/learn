# linux添加硬盘

1.查看现有的硬盘情况
```
df -h
```

2.查看硬盘的详细信息，找到未初始化的硬盘名称，比如这里是：/dev/vdb
```
fdisk -l
```

3.用fdisk对/dev/vdb这块硬盘分区
```
fdisk /dev/vdb
# 依次输入m命令，显示菜单
# 因为要新建分区，所以再输入n
# 第一次建立选择主分区p
# 建立第一个分区，所以再输入1
# 后面一直回车即可
```

4.内核重新读取分区表
```
partprobe /dev/vdb
```

5.创建文件系统，可以通过`vim /etc/fstab`，查看现有的文件系统格式
```
mkfs.ext4 /dev/vdb
```

6.挂载
```
#这里/mnt/vdbdata是自己手动创建的一个目录，用于存储这个硬盘的数据
mount /dev/vdb /mnt/vdbdata/
```

7.更新fstab文件，以达到永久挂载的效果
```
vim /etc/fstab
# 在最后一行添加
#/dev/vdb     /mnt/vdbdata      ext4       defaults         1 1
```

8.最后检查
```
mount -a
# 如果没出现任何信息，表示挂载正常，否则出错。
# 再reboot一下
# 重启之后 运行df -h 看看是否出现了vdb。
```
