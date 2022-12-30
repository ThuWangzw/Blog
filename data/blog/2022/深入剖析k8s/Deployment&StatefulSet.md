---
title: 2022秋招八股文笔记《云原生4-Deployment&StatefulSet》
date: '2022-07-11'
tags: ['云原生', '笔记', '秋招', '八股文']
draft: false
summary: 2022秋招八股文笔记《云原生4-Deployment&StatefulSet》
---

1. deployment、replicaset 和 pod 是三层关系：

   ![dp](/static/images/dp.jpg)

2. 为什么要有三层关系：
   1. Replica set 控制同一版本的 pod 数目，同一个 deployment 下的不同 replicaset 控制不同的版本
   2. 即：deployment 控制版本，replicaset 控制副本数
3. deployment 可以做两件事
   1. HPA：这个事情完全可以由 replicaset 来做
   2. Rolling update：滚动更新每次升级 deployment 时，它都会生成一个新版本的 replicaset，然后逐步减少旧 replicaset 的副本数、增加新 replicaset 的副本数
4. statefulset 主要管理有状态应用，它在设计上将有状态应用抽象出两个功能：
   1. 拓扑状态：pod 之间有启动顺序
   2. 存储状态：pod 的重建不会影响 pod 存储的信息丢失
5. Stateful set 直接管理 pod
6. 什么是 headless service
   1. 首先，什么是 service？service 是一种可以将一组 pod 暴露给外界访问的方式(通过 labelselector 来选择 pod)
   2. service 的工作方式有两种
      1. VIP：service 不是真实存在的，而是一种抽象的概念。每个 service 有一个虚拟 IP，也就是 VIP，可以通过访问 VIP，访问 service 下的 pod
      2. DNS：通过访问 service 的 DNS 来访问 service 下的 pod，DNS 方式也包括两种
         1. Normal service。DNS 解析的结果正好是 service 的 VIP，因此后续和 VIP 方式一样
         2. Headless service。DNS 解析到的是某个 Pod 的 ip，service 不需要有 VIP，因此 service 的 clusterIP 是 None。
            1. service 下的每个 pod 的 ip 都会分配一个 podname.svcname.namespace.sv3.cluster.local 的域名。
7. Stateful set 如何使用 headless service 中每个 ip 的域名来维持拓扑状态的？
   1. Stateful set 会按顺序创建 pod，分别为 statefulname+index
      1. 使用 index 来保持每个 pod 的启动顺序
      2. 使用 headless service 来保证每个 pod 可以被访问
   2. 简单的讲：statefulset 的相比于 deployment 的唯一改变，就是为每个 pod 确定了一个编号，明确了 pod 之间的启动顺序；同时使用 headless service 为每个 pod 都创建了一个稳定的解析记录
8. Stateful set 如何管理存储状态？
   1. 需要保证两件事情
      1. 当 pod 删除时，原本的存储不应该被删除
      2. 当 pod 重建是，应该与原本的存储绑定，而非创建一份新的存储
   2. Stateful set 使用 persistent volume(PV)和 persistent volume claim(PVC)来确保第一点，即 pod 删除后，volume 不被删除
   3. 对于每个 volume 也有一个编号，当 pod 被重建时，挂载的是相同编号的 pod。
