---
layout:     post
title:      "数据结构-图-find a path"
subtitle:   ""
date:       2022-09-26
author:     "franki"
header-img: ""
catalog: true
tags:
    - Graph
---

## 图的深度优先遍历

通过 dfs 得到每个顶点经过的路径，然后通过遍历得到结果

代码实现

```js
class Path {
  constructor(g, s) {
    this.g = g;
    this.s = s;
    this.from = new Array(g.V()).fill(-1);
    this.visited = new Array(g.V()).fill(false);

    this.dfs(s);
  }

  dfs(v) {
    this.visited[v] = true;

    for (const i of this.g.adj(v)) {
      if (!this.visited[i]) {
        this.from[i] = v;
        this.dfs(i);
      }
    }
  }

  hasPath(w) {
    if (!(w >= 0 && w < this.g.V())) {
      throw new Error('Out of boundary!');
    }
    return this.visited(w);
  }

  showPath(w) {
    // path: s -> w
    const stack = [];
    let p = w;
    while (p !== -1) {
      stack.push(p);
      p = this.from[p];
    }

    const queue = [];
    while (stack.length) {
      queue.push(stack.pop());
    }
   
    console.log('path: ', queue.join('->'));
  }
}
```
