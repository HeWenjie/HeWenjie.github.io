---
layout:     post
title:      "集合操作"
subtitle:   "Set Operation"
date:       2018-01-25 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### STL集合操作

* **并集：**set_union

求A和B的并集

```
set_union(A.begin(), A.end(), B.begin(), B.end(), inserter(X, X.begin()))
```

* **交集：**set_intersection

求A和B的交集

```
set_intersection(A.begin(), A.end(), B.begin(), B.end(), inserter(X, X.begin()))
```

* **差集：**set_difference

求A和B的差集

```
set_difference(A.begin(), A.end(), B.begin(), B.end(), inserter(X, X.begin()))
```