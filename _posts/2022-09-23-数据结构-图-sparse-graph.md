---
layout:     post
title:      "数据结构-图-sparse graph"
subtitle:   ""
date:       2022-09-23
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---


## 图论基础-稀疏图

稀疏图实现过程

定义一个 n * x 的数组，x 表示每一个顶点里面相连顶点的集合

注意：稀疏图存在平行边，也就是 1 - 2 可能出现多次

代码实现

```js
// 稀疏图
class SparseGraph {
  constructor(n, directed) {
    this.n = n; // 表示顶点个数
    this.m = 0; // 表示边的个数
    this.directed = directed; // 表示是否是有向图
    this.g = new Array(n).fill().map(() => new Array()); // 邻接表，表示有 n 行数据，每一行开始都为空，默认一开始都没有连接
  }

  // 返回图中顶点的个数
  V() {
    return this.n;
  }

  // 返回图中边的个数
  E() {
    return this.m;
  }

  // 两个顶点相连成边
  addEdge(v, w) {
    if (!(v >= 0 && v < this.n)) {
      throw new Error('out of bounded');
    }
    if (!(w >= 0 && w < this.n)) {
      throw new Error('out of bounded');
    }

    this.g[v].push(w);
    if (v !== w && !this.directed) {
      this.g[w].push(v);
    }

    this.m++;
  }

  // 验证两个顶点是否构成边
  hasEdge(v, w) {
     if (!(v >= 0 && v < this.n)) {
      throw new Error('out of bounded');
    }
    if (!(w >= 0 && w < this.n)) {
      throw new Error('out of bounded');
    }

    for (const item of this.g[v]) {
      if (item === w) {
        return true;
      }
    }

    return false;
  }
}

// TEST
const sg = new SparseGraph(5, false);

sg.addEdge(1, 2);
sg.addEdge(1, 3);
sg.addEdge(1, 4);

console.log(dg.V());
console.log(dg.E());
console.log(dg.g);

for (let i=0; i<sg.g[1].length; i++) {
  console.log(sg.g[1][i]);
}

```
