---
title: 2022秋招八股文笔记《云原生3-Pod》
date: '2022-05-02'
tags: ['云原生', '笔记', '秋招', '八股文']
draft: false
summary: 2022秋招八股文笔记《云原生3-Pod》
---

1. Pod 有什么作用
   1. 一个 Pod 中有多个容器运行时，因此 pod 的目的就是让几个关联密切的容器能更方便的交换信息
   2. pod 中的容器在同一台宿主机上，共享一个 network namespace，并且可以通过 volume 来共享某一区域的文件
   3. pod 的实现方式是先有一个非常简单的 infra 容器，再把其余容器的 network ns 加入到 infra 中
      1. 这样做的目的是避免容器之间的启动存在拓扑关系
      2. pod 的生命周期与 infra 容器相同
2. pod 在设计上功能类似于虚拟机，因此在配置 pod 时，和调度、网络、存储、安全、namespace 相关的配置都是属于 pod 的，例如
   1. NodeSelector：选择具有某个 label 的 node，如 disktype: ssd
   2. NodeName：本来由调度器负责调度后，给该属性赋值，意为该 Pod 被调度到哪个 node 上，也可以人工指定，表示手动调度到某个指定节点上
   3. HostAliases：/etc/hosts 文件中的内容。注意，如果需要在 pod 中设置 host 文件，一定要通过这个方法，不然一旦 pod 被重建，host 中的内容将会丢失，因为 host 文件属于 init 层
   4. shareProcessNamespace：pod 中的容器是否共享同一个 pid namespace。
   5. hostIPC, hostPID, hostNetwork：共享宿主机的 IPC, PID, Network
3. Pod 中的 container 包括哪些属性呢：
   1. Image，Command，workingDir，Ports，volomeMounts
   2. ImagePullPolicy：always 表示每次创建 pod 都要重新拉取镜像，这是默认方式。如果镜像被指定为 latest，也是 always；还有 IfNotPresent，表示如果宿主机上不存在才会拉取镜像；Never 表示宿主机不会自动拉取镜像
   3. lifeCycle：表示容器生命周期的一系列钩子
      1. postStart，容器创建好之后（entrypoint 执行之后；和 entryPoint 并发执行
      2. preStop，容器被杀死之前执行，该过程是同步的，一定会在执行完之后才被杀死
4. Pod 的生命周期
   1. Status 字段
      1. Pending pod 中的某些容器创建不成功。例如调度失败
      2. Running pod 已经与 node 绑定，容器至少有一个正在运行中
      3. succeeded 在一次性任务中，所有容器已经正确退出了
      4. Failed 至少一个容器非正常退出
      5. Unknown pod 的状态没有被 kubelet 成功的汇报给 apiserver，可能是通信出了问题
   2. condition 字段，表示更细节的状态
      1. podscheduled，ready，initialized，unscheduled
      2. ready 表示不仅已经启动，而且可以对外服务了
5. Projected volume，1.11 版本的新特性。是一种特殊的 volume，可以将一些信息以绑定挂载的方式投射到 pod 中。包括
   1. secret，secret 是 k8s 的一种资源，可以通过 kubectl create secret generic xxx --from-file 或者通过 yaml 文件的方式创建 secret，不过 yaml 文件的方式需要先将信息用 base64 编码
   2. configmap，和 secret 一样，只不过用来存储一些非加密的信息
   3. projectedvolume 相比于环境变量注入，好处是可以自动管理，即如果修改了 secret 的值，projected volume 也会对应修改。但是要注意到这种修改可能会有延迟，因此要写好重试和超时的逻辑
   4. Downward api，可以让 pod 获取本身的信息，如 label 以及 cpu、内存等的限制
   5. serviceaccounttoken，可以以此访问 k8s 的 api server，这种称为 inclusterconfig，把 k8s 的客户端运行在容器中，并通过 service account token 的方式。
6. pod 的恢复机制：restartpolicy
   1. Always，一旦 pod 不是 running，就会重启，包括 succeeded
   2. onfailure，一旦为 failed 就重启，
   3. Never
   4. always 会保证 pod 一直为 running 状态。因此，如果是一次性的计算操作，应该使用 onfailure。如果想要收集日志，应该使用 never，否则重启之后就会更新。
7. restartpolicy 与 pod 的状态的关系
   1. pod 中所有容器都 failed 之后，才会触发重启
8. 单个 pod 的 deployment 和单个 pod 的区别：
   1. 单个 pod 无论如何都不会离开绑定的宿主机，即使宿主机已经宕机了
9. Podpreset
   1. 由运维人员编写，开发人员只需要写很少一部分，其余的内容可以由 preset 补充。
10. 控制器：deployment 等资源都遵循相同的控制器逻辑：包括一个理想状态、实际状态和编排方式。
11. deployment 对象这类的资源在定义时分为两个部分：一个是控制器的定义（包括控制器类型、理想状态），另一个是所控制的对象的模板，例如 deployment 中的 podtemplate，当需要创建 pod 时，就会从 template 中创建。
