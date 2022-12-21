---
layout:     post
title:      "线段树"
date:       2022-11-12
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - tree
    - Segment Tree
---

## 线段树实现及应用

线段树的定义，本质是一颗平衡二叉搜索树，也就是每个节点的左右节点高度不超过1，并且左节点的值小于根节点，根节点的值小于右节点

线段树的操作包括：创建线段树、搜索、查询

下标
如果下标从0开始，那么左节点就是 `2 * i + 1`，右节点就是 `2 * i + 2`，父节点就是 `(i - 1)  /  2`
如果小标从1开始，那么左节点就是 `2 * i`，右节点就是 `2 * i + 1`，父节点就是 `i / 2`

线段树表示如下：

![segment-tree](/images/posts/tree/segment-tree.svg)

线段树主要实现方法：buildSegmentTree query set

构建线段树的主要思路是，采用自顶向下的方式，对于树上的每一个节点，先构建其左右子树，然后根据当前的左右子节点构建当前节点。对于叶子节点（单点区间），不可再分，可以直接赋值即可。

区间查询的思路：
递归查询子区间的结果，再通过合并子区间的结果，来得到期望区间的结果。
● 从根节点开始递归查找
● 如果查询范围区间刚好落在节点区间，直接返回聚合数据
● 如果查询范围区间刚好落在左子树区间，那么递归在左子树区间查找
● 如果查询范围区间刚好落在右子树区间，那么递归在右子树区间查找
● 如果查询范围区间落在左右子树区间，那么需要拆分两次递归查找，查询完毕之后合并结果返回

单点更新的思路：
递归查询修改子区间的结果，可以通过合并子区间的结果来更新当前节点的结果
● 从根节点开始更新值，递归执行步骤
● 如果叶子节点，直接更新
● 如果更新节点正好落在左子树区间范围，那么递归左子树更新
● 如果更新节点正好落在右子树区间范围，那么递归右子树更新
● 如果更新节点落在左右子树区间范围，那么递归操作左右子树，通过合并左右子树的节点来更新

### 代码

```js
function merge(e1, e2) {
  if (!e1) {
    return e2;
  } else if (!e2) {
    return e1;
  } else {
    return e1 + e2;
  }
}

// segment tree implement
class SegmentTree {
  constructor(data, merge) {
    this.data = data;
    this.merge = merge;
    this.init();
  }

  init() {
    this.tree = new Array(4 * this.data.length).fill(null);
    this.buildSegmentTree(0, 0, this.data.length - 1);
  }

  parentIndex(index) {
    return (index - 1) >> 1;
  }

  leftChildIndex(index) {
    return 2 * index + 1;
  }

  rightChildIndex(index) {
    return 2 * index + 2;
  }

  buildSegmentTree(treeIndex, dataLeft, dataRight) {
    if (dataLeft === dataRight) {
      this.tree[treeIndex] = merge(this.data[dataLeft], null);
      return;
    }

    const mid = (dataLeft + dataRight) >> 1;
    const leftChild = this.leftChildIndex(treeIndex);
    const rightChild = this.rightChildIndex(treeIndex);
    this.buildSegmentTree(leftChild, dataLeft, mid);
    this.buildSegmentTree(rightChild, mid + 1, dataRight);
    this.tree[treeIndex] = this.merge(this.tree[leftChild], this.tree[rightChild]);
  }

  set(index, value) {
    this.data[index] = value;
    this.recursiveSet(0, 0, this.data.length - 1, index, value);
  }

  recursiveSet(treeIndex, dataLeft, dataRight, index, value) {
    if (dataLeft === dataRight) {
      this.tree[treeIndex] = value;
      return;
    }
    const mid = (dataLeft + dataRight) >> 1;
    const leftChild = this.leftChildIndex(treeIndex);
    const rightChild = this.rightChildIndex(treeIndex);
    if (index <= mid) {
      this.recursiveSet(leftChild, dataLeft, mid, index, value);
    } else if (index >= mid + 1) {
      this.recursiveSet(rightChild, mid + 1, index, value);
    }
    this.tree[treeIndex] = this.merge(this.tree[leftChild], this.tree[rightChild]);
  }

  get(index) {
    return this.data[index];
  }

  query(left, right) {
    return this.recursiveQuery(0, 0, this.data.length - 1, left, right);
  }

  recursiveQuery(treeIndex, dataLeft, dataRight, left, right) {
    if (dataLeft === left && dataRight === right) {
      return this.tree[treeIndex];
    }
    const mid = (dataLeft + dataRight) >> 1;
    const leftChild = this.leftChildIndex(treeIndex);
    const rightChild = this.rightChildIndex(treeIndex);
    if (right <= mid) {
      return this.recursiveQuery(leftChild, dataLeft, mid, left, right);
    }
    if (left >= mid + 1) {
      return this.recursiveQuery(rightChild, mid + 1, dataRight, left, right);
    }
    const leftResult = this.recursiveQuery(leftChild, dataLeft, mid, left, mid);
    const rightResult = this.recursiveQuery(rightChild, mid + 1, dataRight, mid + 1, right);
    return merge(leftResult, rightResult);
  }
}

const data = [1, 2, 3, 4, 5];
const st = new SegmentTree(data, merge);
// query interval range [0, 2]
console.log(st.query(0, 3));
// udate
st.set(0, 2);
console.log(st.query(0, 3));
```

复杂度分析:

- 时间复杂度: O(logN)
- 空间复杂度：O(4 * N)
