---
layout:     post
title:      "数据结构-图-weight sparse graph"
subtitle:   ""
date:       2022-09-30
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---


## 有权图-实现稀疏图

有向图的稀疏图实现也跟一般的稀疏图实现类似，主要是把邻接表里面的每个元素换成了 `edge`

代码实现

```js
const Edge = require("./edge");

class SparseGraph {
  constructor(n, directed) {
    this.n = n;
    this.m = 0;
    this.directed = directed;

    this.g = new Array(n).fill().map(() => new Array());
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
    for (const item of this.g[v]) {
      if (item.w === w) {
        return true;
      }
    }

    return false;
  }

  addEdge(v, w, weight) {
    const edge = new Edge(v, w, weight);
    this.g[v].push(edge);
    if (v !== w && !this.directed) {
      const edge1 = new Edge(w, v, weight);
      this.g[w].push(edge1);
    }
    this.m++;
  }

  show() {
    for (let i = 0; i < this.n; i++) {
      for (let j = 0; j < this.n; j++) {
        if (this.g[i][j]) {
          console.log('v: ', this.g[i][j].v, 'w: ', this.g[i][j].w, 'weight: ', this.g[i][j].weight);
        }
      }
    }
  }
}

module.exports = SparseGraph;

const sg = new SparseGraph(13, false);
sg.addEdge(0, 1, 1);
sg.addEdge(0, 2, 2);
sg.addEdge(0, 3, 3);
sg.show();
```
