---
layout:     post
title:      "数据结构-图-dense graph"
subtitle:   ""
date:       2022-09-22
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---


## 图论基础-稠密图

稠密图实现过程使用一个 n*n 的矩阵，一开始矩阵里面的元素都是false

应用场景是：数据较多，场景复杂的图

代码实现

```js
// 稠密图
class DenseGraph {
  constructor(n, directed) {
    this.n = n; // 图的顶点数
    this.m = 0; // 图的边数
    this.directed = directed; // 是否为有向图
    this.g = new Array(n).fill().map(() => new Array(n).fill(false)); // 图的矩阵(二维矩阵)，初始化为每个元素都为 false
  }

  V() {
    return this.n; // 返回顶点个数
  }

  E() {
    return this.m; // 返回边的个数
  }

  // 添加边
  addEdge(v, w) {
    if (!(v >=0 && v < this.n)) {
      throw new Error('out of boundary!');
    }
    if (!(w >=0 && w < this.n)) {
      throw new Error('out of boundary!');
    }

    if (this.hasEdge(v, w)) {
      reutrn;
    }

    this.g[v][w] = true;
    if (v !== w && !this.directed) {
      this.g[w][v] = true;
    }

    this.m++;
  }

  // 判断两个顶点间是否有边
  hasEdge(v, w) {
    if (!(v >=0 && v < this.n)) {
      throw new Error('out of boundary!');
    }
    if (!(w >=0 && w < this.n)) {
      throw new Error('out of boundary!');
    }

    return this.g[v][w];
  }

  // 打印所有节点对应的值
  show() {
    for (let i =0; i < this.n; i++) {
      let str = '';
      for (let j = 0; j < this.n; j++) {
        str += this.g[i][j] + ' ';
      }
      console.log(str);
      console.log("\n");
    }
  }
}


const dg = new DenseGraph(5, false);
dg.addEdge(1, 2);
dg.addEdge(1, 3);
dg.addEdge(1, 4);

dg.show();
console.log(dg.n);
console.log(dg.m);
console.log(dg.g);

for (let i = 0; i < dg.g[1].length; i++) {
    if (dg.g[1][i]) {
        console.log(i);
    }
}
```
