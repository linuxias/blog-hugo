+++
title = "FileSystem"
+++

### free(1)

```bash
$free -th
              total        used        free      shared  buff/cache   available
Mem:            31G        1.5G         28G         79M        1.1G         29G
Swap:          2.0G          0B        2.0G
Total:          33G        1.5G         30G

```

### top(1)


```bash
...

KiB Mem : 32832784 total, 30064040 free,  1579628 used,  1189116 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used. 30760488 avail Mem 

...
```
**1189116 buff/cache**


### vmstat 

buff and cache column -> buffer cache

```bash
vmstat -w 1      
procs -----------------------memory---------------------- ---swap-- -----io---- -system-- --------cpu--------
 r  b         swpd         free         buff        cache   si   so    bi    bo   in   cs  us  sy  id  wa  st
 0  0            0     30052984       119008      1078384    0    0    66     6  792 1632   2   1  98   0   0
 0  0            0     30052724       119008      1078388    0    0     0     0 7244 14004   0   1  99   0   0
 0  0            0     30055024       119008      1076084    0    0     0     0 10043 19655   0   1  99   0   0

```


### slabtop

slabtop  displays detailed kernel slab cache information in real time.  
It displays a listing of the top caches sorted by one of the listed sort criteria.  
It also displays a statistics header filled with slab layer information.

```bash
sudo slabtop -o
 Active / Total Objects (% used)    : 881272 / 898900 (98.0%)
 Active / Total Slabs (% used)      : 24976 / 24976 (100.0%)
 Active / Total Caches (% used)     : 102 / 151 (67.5%)
 Active / Total Size (% used)       : 224446.49K / 230393.23K (97.4%)
 Minimum / Average / Maximum Object : 0.01K / 0.26K / 16.81K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME                   
...    
 88620  82099   0%    0.19K   4220       21     16880K dentry                    
 31185  30925   0%    0.58K   1155       27     18480K inode_cache            
 21330  19517   0%    1.05K    711       30     22752K ext4_inode_cache                  
 17184  16972   0%    0.65K    716       24     11456K proc_inode_cache       
...
```

- dentry : dentry cache
- inode_cache : inode cache
- ext4_inode_cache : inode cache(ext4)

### Reference

[리눅스의 페이지 캐시와 버퍼 캐시](https://brunch.co.kr/@alden/25)
