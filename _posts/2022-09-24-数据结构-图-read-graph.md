---
layout:     post
title:      "数据结构-图-read graph"
subtitle:   ""
date:       2022-09-24
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---

## 读取一张图

使用 nodejs 本身自带的文件读取模块 read-line

代码实现

```js
const fs = require('fs');
const readline = require('readline');
// const DenseGraph = require('./dense-graph');
const SparseGraph = require('./sparse-graph');
const DFSComponent = require('./dfs');
const Path = require('./find-a-path');
const BFCComponent = require('./bfs');

const rl = readline.createInterface({
  input: fs.createReadStream('/Users/da.liu/person/graph/testG1.txt'),
  output: process.stdout,
  terminal: false
});

const length = 13;
const sg = new SparseGraph(length, false);
let index = 0;
rl.on('line', (line) => {
  if (index === length - 1) {
    // const dfsc = new DFSComponent(sg);
    // console.log(dfsc.count());
    // const path = new Path(sg, 0);
    // path.showPath(3);
    const path = new BFCComponent(sg, 0);
    path.showPath(3);
  }

  if (index !== 0) {
    const [v, w] = line.split(/\s/);
    // dg.addEdge(v, w);
    sg.addEdge(v, w);
  }
  index++;
  // console.log(dg.g);
});
```
