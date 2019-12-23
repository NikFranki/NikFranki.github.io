---
layout:     post
title:      "git commit 提交规范"
subtitle:   ""
date:       2019-05-07
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - git
    - git flow
    - 代码规范
---

* 目录  
{:toc #markdown-toc}

## git commit信息规范

* Angular团队规范

```js
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

通过vim界面编写填写的最终结果如上所示，大致分为三个部分(使用空行分割)

* 标题行：必填，描述具体的类型和内容
* 主题内容：描述为什么修改，做了什么样的修改，以及开发的思路
* 页脚注释：放Breaking changes 或者 Close issues

分别由下面的部分组成

* type: commit 的类型
* feat: 新特性
* fix: 修复bug
* refactor: 代码重构
* docs: 文档修改
* style: 代码格式修改，(注意不是css的修改)
* test: 测试用例修改
* chore: 其他修改，比如构建流程，依赖管理
* scope: commit 影响范围，比如：route，component，utils，build...
* subject: commit 描述，建议符合 50/70 formatting
* body: commit 具体修改内容，可以分为多行，建议 50/70 formatting
* footer: 一些备注，通常是一些breaking change 或 修复的bug的链接

git commit 模板

如果是个人项目的话，抑或你想尝试下如此的规范，那么你可以为git设置commit template，每次git commit的时候在vim带出，时刻提醒自己

修改 ~/.gitconfig, 添加：

```bash
[commit]
template = ~/.gitmessage
```

新建~/.gitmessage 编写以下

```bash
# head: <type>(<scope>): <subject>
# - type: feat, fix, docs, style, refactor, test, chore
# - scope: can be empty (eg. if the change is a global or difficult to assign a single component)
# - subject: start with verb (such as 'change'), 50-character line
#
# body: 72 -character wrapped. This should answer:
# * Why was this change necessary?
# * How does it address the problem?
# * Are there any side effects?
#
# footer:
# - Include a link to the ticket, if any
# - BREAKING CHANGE
#
```

Commitizen: 替代你的git commit

目的是通过工具生成和约束，那么现在start吧！

[commitizen/cz-cli](https://link.zhihu.com/?target=https%3A//github.com/commitizen/cz-cli), 我们需要借助它提供的 git cz 命令替代我们的 git commit 命令, 帮助我们生成符合规范的 commit message.

除此之外, 我们还需要为 commitizen 指定一个 Adapter 比如: [cz-conventional-changelog](https://link.zhihu.com/?target=https%3A//github.com/commitizen/cz-conventional-changelog) (一个符合 Angular团队规范的 preset). 使得 commitizen 按照我们指定的规范帮助我们生成 commit message.

## 全局安装

```bash
npm install -g commitizen cz-conventional-changelog
echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc
```

主要, 全局模式下, 需要 ~/.czrc 配置文件, 为 commitizen 指定 Adapter.

## 项目级安装

```bash
npm install -D commitizen cz-conventional-changelog
```

package.json中配置:

```json
"script": {
    ...,
    "commit": "git-cz",
},
 "config": {
    "commitizen": {
      "path": "node_modules/cz-conventional-changelog"
    }
  }
```

如果全局安装过 commitizen, 那么在对应的项目中执行 git cz or npm run commit 都可以.

效果如下:

![img](https://pic2.zhimg.com/80/v2-b2176482b433c658b5687576f46e0b35_hd.jpg)

## 自定义 Adapter

也许 Angular 的那套规范我们不习惯, 那么可以通过指定 Adapter [cz-customizable](https://link.zhihu.com/?target=https%3A//github.com/leonardoanalista/cz-customizable) 指定一套符合自己团队的规范.

全局 或 项目级别安装:

```bash
npm i -g cz-customizable
or
npm i -D cz-customizable
```

修改 .czrc 或 package.json 中的 config 为:

```json
{ "path": "cz-customizable" }
or
  "config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    }
  }
```

同时在~/ 或项目目录下创建 .cz-config.js 文件

## Commitlint: 校验你的 message

[commitlint](https://link.zhihu.com/?target=https%3A//github.com/marionebl/commitlint): 可以帮助我们 lint commit messages, 如果我们提交的不符合指向的规范, 直接拒绝提交, 比较狠.

同样的, 它也需要一份校验的配置, 这里推荐 [@commitlint/config-conventional](https://link.zhihu.com/?target=https%3A//github.com/marionebl/commitlint/tree/master/%40commitlint/config-conventional) (符合 Angular团队规范).

安装:

```bash
npm i -D @commitlint/config-conventional @commitlint/cli
```

同时需要在项目目录下创建配置文件 .commitlintrc.js, 写入:

```js
module.exports = {
  extends: [
    ''@commitlint/config-conventional''
  ],
  rules: {
  }
};
```

## 针对自定义的 Adapter 进行 Lint

如果你像我一样, 使用的是自定义的 commitizen adapter, 那么你需要:

```bash
npm i -D commitlint-config-cz @commitlint/cli
```

.commitlintrc.js 中写入:

```json
module.exports = {
  extends: [
    'cz'
  ],
  rules: {
  }
};
```

## 结合 Husky

校验 commit message 的最佳方式是结合 git hook, 所以需要配合 [Husky](https://link.zhihu.com/?target=https%3A//github.com/typicode/husky).

```bash
npm i husky@next
```

package.json 中添加:

```json
"husky": {
    "hooks": {
      ...,
      "commit-msg": "commitlint -e $GIT_PARAMS"
    }
  },
```

效果如下:

![img](https://pic1.zhimg.com/80/v2-73ad405e2323eeebcfd0e4ded3146444_hd.jpg)

## standard-version: 自动生成 CHANGELOG

通过以上工具的帮助, 我们的工程 commit message 应该是符合 Angular团队那套, 这样也便于我们借助 [standard-version](https://link.zhihu.com/?target=https%3A//github.com/conventional-changelog/standard-version) 这样的工具, 自动生成 CHANGELOG, 甚至是 语义化的版本号([Semantic Version](https://link.zhihu.com/?target=http%3A//semver.org/lang/zh-CN/)).

安装使用:

```bash
npm i -S standard-version
```

package.json 配置:

```json
"scirpt": {
    ...,
    "release": "standard-version"
}
```

PS: standard-version 有很多其他的特性, 这里不过多涉及, 有兴趣的同学自行尝试.

## 最后

commit message 的规范性很重要, 但是是否需要像本文这样强制限制, 每个团队和个人都有自己的想法, 但是个人认为: 好的习惯, 受益终身。
