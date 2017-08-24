---
title: Linux 简单命令，防止遗忘
date: 2017-8-24 10:30:08
tags: [Linux]
---
# Linux 简单命令，防止遗忘

标签： [Linux]

---

 1. 解压缩命令
    1. 打包一个目录成为一个`tar`文件: tar cvf archive_name.tar dirname/
    2. 解压一个tar文件: tar xvf archive_name.tar
 2. Grep 命令的使用
    1. 在一个文件中查找一个字符串: grep -i "test" log.txt
    2. 查找文件中匹配的字符串，并打印出当前字符串后面三行的内容: grep -A 3 -i "tets" log.txt
    3. 在一个文件夹中递归查找包含当前字符串的文件: grep -r "test"
 3. Find 命令的使用
    1. 指定目录查找指定的文件: find / -iname "dump.rdb" 
    2. 模糊匹配指定目录的文件: find / -iname "*ump.rdb*"
 4. SSH 命令的使用
    1. 远程登录一个域：ssh -l jsmith remotehost.example.com
 5. Vim 命令的使用
    1. 打开文件并跳到第10行: vim +10 filename.txt
    2. 打开文件跳到第一个匹配的行: vim +/search-term filename.txt
    3. 以只读模式打开文件: vim -R /etc/passwd
 6. Sort 命令的使用
    1. 以升序对文件内容排序：sort name.txt
    2. 以降序对文件内容排序：sort -r name.txt
    3. 以第N列进行排序： sort -t:-k 3n name.txt | more
 7. ls 命令的使用
    1. 以人类可读的方式展示数据：ls -lh
    2. 按最后修改时间排序文件：ls -ltr
    3. 查看文件的编码类型：ls -F
 8. Ftp 的命令的使用：
    1.Both ftp and secure ftp (sftp) has similar commands. To connect to a remote server and download multiple files, do the following。`ftp IP/hostname`
 9. crontab 命令的使用
    1. 使用一个特别的用户，查看一个crontab 的内容: crontab -u name -l
    2. 设置一个10分钟执行一次的任务：*/10 * * * * /home/ramesh/check-disk-space
 10. Top 命令的使用：`top -u username` 只是查看当前用户的进程
 11. rm、cp、mv 命令的使用
    1. 删除之前确认，复制文件已经存在，重命名文件当文件名称已经存在的时候，需要确认：`rm -i filename.txt`,`cp -i file1 file2`,`mv -i file1 file2` 
    2. 删除文件：rm -r example
    3. 拷贝文件1到文件2，并保持文件的权限、属主和时间戳：cp -p file1 file2
    4. -v会输出重命名的过程，当文件名中包含通配符时，这个选项会非常方便：mv -v file1 file2
 12. chmod命令的使用
    1. 给指定文件的属主和属组所有权限(包括读、写、执行)：chmod ug+rwx file.txt
    2. 删除指定文件的属组的所有权限：chmod g-rwx file.txt
    3. 修改目录的权限，以及递归修改目录下面所有文件和子目录的权限：chmod -R ug+rwx file.txt
 13. date 命令的使用，设置系统日期:`date -s "01/31/2010 23:59:53"` ,当你修改了系统时间，你需要同步硬件时间和系统时间:`hwclock –systohc`,`hwclock --systohc –utc`