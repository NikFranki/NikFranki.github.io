---
layout:     post
title:      "数据结构-图-weight dense graph"
subtitle:   ""
date:       2022-09-29
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---

## 有权图-实现稠密图

带权的稠密图，跟普通稠密图类似，只不过现在二维数组里面的每个元素，不再是存放简单的 `true` 或者 `false`，而是存放 edge (包含顶点 v, 顶点 w，weight 权重)

代码实现

```js
const Edge = require('./edge');

class DenseGraph {
  constructor(n, directed) {
    this.n = n;
    this.m = 0;
    this.directed = directed;
    this.g = new Array(n).fill().map(() => new Array(n).fill());
  }

  V() {
    return this.n;
  }

  E() {
    return this.m;
  }

  hasEdge(v, w) {
    if (!(v >= 0 && v < this.n)) {
      throw new Error('Out of boundary!');
    }
    if (!(w >= 0 && w < this.n)) {
      throw new Error('Out of boundary!');
    }
    return !!this.g[v][w];
  }

  addEdge(v, w, weight) {
    if (this.hasEdge(v, w)) {
      delete this.g[v][w];

      if (v !== w && !this.directed) {
        delete this.g[w][v];
      }
    }

    const edge = new Edge(v, w, weight);
    this.g[v][w] = edge;
    if (v !== w && !this.directed) {
      this.g[w][v] = edge;
    }
  }

  show() {
    for (let i = 0; i < this.n; i++) {
      for (let j = 0; j < this.n; j++) {
        if (this.g[i][j]) {
          console.log('v: ', i, 'w: ', j, 'weight: ', this.g[i][j].weight);
        }
      }
    }
  }
}

module.exports = DenseGraph;

const dg = new DenseGraph(13, false);
dg.addEdge(0, 1, 1);
dg.addEdge(0, 2, 2);
dg.addEdge(0, 3, 3);
console.log(dg.hasEdge(0, 1));
dg.show();
```
