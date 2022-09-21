---
layout:     post
title:      "数据结构-图-read weight graph"
subtitle:   ""
date:       2022-10-01
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---


## 读取有权图

读取有权图跟读取一般图没有什么大的区别，只是在添加边的时候，需要把边的权重传入而已。（稠密图使用矩阵实现，稀疏图使用邻接表实现）

代码实现

```js
const fs = require('fs');
const readline = require('readline');
const SparseGraph = require('./sparse-graph');

const rl = readline.createInterface({
  input: fs.createReadStream('./testG1.txt'),
  output: process.stdout,
  terminal: false
});

const length = 8;
const sg = new SparseGraph(length, false);
let index = 0;
rl.on('line', (line) => {
  if (index === length - 1) {
    console.log(sg.g);
  }

  if (index !== 0) {
    const [v, w, weight] = line.split(/\s/);
    sg.addEdge(v, w, weight);
  }
  index++;
});
```
