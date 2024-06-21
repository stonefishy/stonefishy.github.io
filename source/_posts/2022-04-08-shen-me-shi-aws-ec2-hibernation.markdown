---

title: "什么是AWS EC2 Hibernation"
date: 2022-04-08 17:00:59 +0800
comments: true
categories: Cloud
tags: [Cloud, AWS]
---

`EC2 Hibernation` 是 Amazon Elastic Compute Cloud (EC2) 服务的一个功能，它允许您将 EC2 实例从运行状态转换为休眠状态，然后再次唤醒它，而不会丢失实例的内存中的数据。这个功能非常适合那些需要保持应用程序状态或长时间运行进程的工作负载。
![](/assets/images/aws/aws-ec2-hibernation-flow.webp)
这里详细说明 EC2 Hibernation 的**工作原理**
<!--more-->
### 工作原理

**1.启用 Hibernation：** 要使用 EC2 Hibernation，首先需要在 EC2 实例上启用它。这可以通过使用支持 Hibernation 的实例类型，并在启动时启用 Hibernation 选项来完成。并且EBS卷必须要开启加密。
![](/assets/images/aws/aws-ec2-hibernation-enable.webp)

**2.创建 Hibernation 快照：** 在启用 Hibernation 后，EC2 实例的内存状态将被定期保存到 Amazon Elastic Block Store (EBS) 卷上的 Hibernation 快照中。这确保了在实例休眠时不会丢失内存中的数据。

**3.休眠实例：** 一旦 Hibernation 启用并创建了快照，您可以通过执行 hibernate 命令或使用 AWS 管理控制台将实例转换为休眠状态。在休眠期间，实例的状态和数据将保存在 EBS 卷上。

**4.唤醒实例：** 当您希望恢复实例的运行状态时，只需执行唤醒命令或使用控制台，EC2 实例将从 Hibernation 快照中还原内存状态，并继续运行。


### 使用场景

EC2 Hibernation 可以应用于多种使用场景，包括但不限于：

**长时间运行的进程：** 如果您的 EC2 实例运行有状态的应用程序或长时间运行的进程，您可以使用 Hibernation 将实例休眠，以节省计算成本，而无需中断应用程序。

**工作负载保持状态：** 某些工作负载需要保持状态，例如内存中的会话数据或数据库连接。通过 Hibernation，您可以在需要时恢复工作负载，而无需重新创建状态。

**成本优化：** 对于不需要全天候运行的工作负载，您可以使用 Hibernation 在闲置时降低成本，然后在需要时快速恢复。

**实例维护：** 在 EC2 实例需要进行操作系统更新或其他维护操作时，可以使用 Hibernation 将实例休眠，然后在完成维护后唤醒它，以减少维护期间的停机时间。

==请注意==，<u>不是所有的 EC2 实例类型都支持 Hibernation</u>，您需要选择支持该功能的实例类型并启用它。此外，<u>使用 Hibernation 可能会产生额外的 EBS 存储费用，因为需要存储 Hibernation 快照</u>。所以，在使用 Hibernation 时，需要权衡成本和性能需求。
