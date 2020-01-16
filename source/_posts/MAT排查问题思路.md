---
title: MAT排查问题思路
date: 2020-01-16 13:50:39
tags: [jvm,mat]
---

### MAT分析jvm内存转储文件思路及操作：

1. 查看leak suspect
2. 查看Histogram分布  (按retainSize降序)
3. merge shortest path to GC root  (按retainSize降序)
4. list objects with incoming/outcoming  (按retainSize降序)
5. 在big map上执行java collection >  collections group by size  (按retainSize降序)
6. 1-5按情况交叉执行