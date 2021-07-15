卸载分区目录出现target is busy

```bash
umount /home/data1/

umount: /home/data1: target is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
```

使用fuser命令查看进程用户占用

```bash
fuser  -cu /home/data1/

/home/data1:          3510c(root)
```
kill相关进程

```bash
fuser  -ck /home/data1/

/home/data1:          3510c
```

正常卸载目录

```bash
umount /home/data1/
```

关于fuser的介绍

```
Usage: fuser [-fMuvw] [-a|-s] [-4|-6] [-c|-m|-n SPACE] [-k [-i] [-SIGNAL]] NAME...
       fuser -l
       fuser -V
Show which processes use the named files, sockets, or filesystems.

  -a,--all              display unused files too
  -i,--interactive      ask before killing (ignored without -k)
  -k,--kill             kill processes accessing the named file
  -l,--list-signals     list available signal names
  -m,--mount            show all processes using the named filesystems or block device
  -M,--ismountpoint     fulfill request only if NAME is a mount point
  -n,--namespace SPACE  search in this name space (file, udp, or tcp)
  -s,--silent           silent operation
  -SIGNAL               send this signal instead of SIGKILL
  -u,--user             display user IDs
  -v,--verbose          verbose output
  -w,--writeonly        kill only processes with write access
  -V,--version          display version information
  -4,--ipv4             search IPv4 sockets only
  -6,--ipv6             search IPv6 sockets only
  -                     reset options

  udp/tcp names: [local_port][,[rmt_host][,[rmt_port]]]
```