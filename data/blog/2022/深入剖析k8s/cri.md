---
title: 2022秋招八股文笔记《云原生7-CRI》
date: '2022-08-16'
tags: ['云原生', '笔记', '秋招', '八股文']
draft: false
summary: 2022秋招八股文笔记《云原生7-CRI》
---

1. cri、kubelet 有 k8s 的 sig-node 小组负责
2. 为什么需要 cri
   1. 最开始 k8s 的运行时只有 docker，后面支持了 rkt，每次 kubelet 升级时，都需要考虑 docker 和 rkt 两种运行时，再后来出现了更多的运行时比如 KaTA Container 的前身 runV，kubelet 如果需要考虑这些运行时，开发将会非常麻烦。
   2. 因此提出了 cri 运行时接口
3. 有了 cri 之后的架构为：

   ![cri1](/static/images/cri1.png)

   kubelet 调用 CRI grpc，经过 CRI shim 翻译为运行时的操作，每个容器项目都可以实现一个自己 cri-shim，用来处理 cri 请求。例如 containerd 创建出 runC 容器

   ![cri2](/static/images/cri2.png)

4. cri 接口主要包括两种
   1. 一个叫 runtime service，主要是启动/停止容器等容器操作
   2. 另一个叫 image service，主要是容器镜像的操作，如拉取、删除镜像
