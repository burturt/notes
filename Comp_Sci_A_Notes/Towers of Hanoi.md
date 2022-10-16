Algorithm:

Start with largest disk, src = rod 1. dst = rod 3, empty = rod 2
```
If disk is smallest one:
- move disk to dst
If disks exist above:
- run function move using disk above but with empty => dst, dst => empty, and src => src
- Move disk to dst
- Run function move using disk above but with empty => src, dst => dst