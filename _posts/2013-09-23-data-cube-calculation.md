---
layout: post
title: "基于关系OLAP的方体计算及其优化"
description: "数据立方体计算是数据仓库实现的一项基本任务..."
keywords: "DM, Algorithm"
category: 数据挖掘
tags: [DM]
---

####数据立方体计算的有效方法

数据立方体计算是数据仓库实现的一项基本任务。

一般有两种基本数据结构用于存储方体。关系表是OLAP（ROLAP）实现的基本数据结构，而多维数组是多维OLAP（MOLAP）实现的基本数据结构。

方体计算的有效方法：

- **基于ROLAP的方体算法**
- **基于MOLAP的方体算法（数组）**
- 自底向上的计算方法
- H-cubing技术

<!-- more -->

####数据立方体有效的优化技术

尽管ROLAP和MOLAP可能使用不同的立方体计算技术，但是某些优化“技巧”可以在不同的数据表示之间共享。

#####优化技术1：排序、散列和分组。

应当对维属性使用排序、散列和分组操作，以便对相关元组重新定序和聚类。在立方体计算中，聚集对共享一组相同的维值的元组（或单元）进行。这样，重要的是利用排序、散列和分组操作一起访问和分组这些数据，以利于聚集的计算。	例如，为了用branch，day和item计算总销量，更有效的方法是先按branch分组，再按day对元组或单元排序，然后按item名分组。

#####优化技术2：同时聚集和缓存中间结果。
	
在立方体计算中，由先前计算的较低层聚集计算较高层聚集，而不是由基本事实表计算是有效的。此外，从缓存的中间计算结果同时聚集可能导致减少开销很大的磁盘I/O操作。例如，为了按branch计算销售，我们可以使用较低层方体（如按branch和day的销售）计算导出的中间结果。这种技术可以进一步扩展，进行来摊扫描（即同时计算尽可能多的方体，缓冲磁盘读）。

#####优化技术3：当存在多个子女方体时，由最小的子女聚集。

当存在子女方体时，由最小的、先前计算的子女方体计算父母方体（即更泛化的方体）通常更有效。例如，为了计算销售方体 $$C_{branch}$$，当存在两个先前计算的方体$$C_{\{branch,year\}}$$和$$C_{\{branch,item\}}$$时,如果不同的商品远比不同的年份多，则使用$$C_{\{branch,year\}}$$比使用$$C_{\{branch,item\}}$$计算$$C_{branch}$$显然更有效。

#####优化技术4：可以使用Apriori剪枝方法有效地计算冰山立方体。

对于数据立方体，Apriori性质表述如下：如果给定的单元不满足最小支持度，则该单元的后代（即更特殊化或更详细的版本）也都不满足最小支持度。使用这种性质可以显著地降低冰山立方体的计算量。



