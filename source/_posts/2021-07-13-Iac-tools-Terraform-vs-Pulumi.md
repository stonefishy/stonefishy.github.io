---
title: 浅谈IaC工具Terraform vs Pulumi
date: 2021-07-13 17:20:13
tags: [Pulumi, Terraform]
categories: [DevOps]
---

`基础架构即代码（Infrastructure as Code, IaC）`是一种管理和提供计算基础架构的方式, 它使得基础架构的创建、修改和管理变得更加高效。`Terraform`和`Pulumi`是两个流行的IaC工具, 各自具有独特的工作原理和优势。

## Terraform

### 工作原理
Terraform是由`HashiCorp`开发的开源IaC工具。它使用一种`声明性配置语言（HashiCorp Configuration Language, HCL）`来定义基础架构的状态。不是完全开源的。有以下特征:

**声明式配置**: 用户通过HCL编写配置文件, 描述所需的基础架构状态。用户并不需要关心如何实现这些状态的细节。
**执行计划**: 运行`terraform plan`命令时, Terraform会读取配置文件和当前的基础架构状态, 并生成一个执行计划, 指出将要执行的操作, 以使当前状态转变为期望状态。
**应用更改**: 运行`terraform apply`命令后, Terraform将根据执行计划实际创建、更新或删除资源。
**状态管理**: Terraform会维护一个状态文件（terraform.tfstate）, 用于记录当前基础架构的状态。此文件使Terraform能够识别哪些资源已经存在、哪些资源需要创建或更新。
**图形化依赖关系**: Terraform会自动处理资源之间的依赖关系, 从而确保以正确的顺序创建和更新资源。

### 优势
**平台无关性**: 支持多种云服务提供商（`AWS`、`Azure`、`GCP`等）, 用户可以使用相同的工具管理不同平台的资源。
**模块化**: Terraform支持模块化, 用户可以将常用的资源配置封装成模块, 提高重用性。
**社区支持**: 拥有丰富的社区和大量的第三方模块, 方便用户快速上手。

### 示例
Terraform创建AWS S3 Bucket的示例代码:

``` tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name"
  acl    = "private"

  tags = {
    Name        = "My S3 Bucket"
    Environment = "Dev"
  }
}
```

### Command

``` bash
# Initialize Terraform project. This sets up the backend, downloads necessary providers, etc.
terraform init

# Run a Terraform plan to preview the changes that will be made to the infrastructure.
# This step is a dry-run that shows what will happen when you actually apply the changes.
terraform plan

# Apply the planned changes to the infrastructure.
# This step will actually create, update, or delete resources to match the configuration.
# It will show a preview of these actions and ask for confirmation before proceeding.
terraform apply

# Destroy the infrastructure.
# This step will delete all resources that were created by Terraform.
terraform destroy
```

## Pulumi

### 工作原理
Pulumi是一个现代化的IaC工具, 允许用户使用多种编程语言（如`JavaScript`、`Python`、`Go`和`C#`, `Java`）或者标记语言`YAML`来定义基础架构。是完全开源的，与Terraform的声明性方法不同, Pulumi采用了`命令式编程模型`。

**命令式编程**: 用户使用编程语言编写代码, 通过构建逻辑和控制流来描述基础架构的创建过程。例如, 可以使用条件语句和循环来动态地生成资源。
**Pulumi CLI**: 通过Pulumi CLI, 用户可以运行`pulumi up`命令来预览和应用更改。Pulumi会分析代码并生成执行计划。
**状态管理**: Pulumi将状态存储在云中（如AWS S3）或本地, 以便跟踪基础架构的当前状态。
**实时更新**: Pulumi支持实时更新, 当基础架构发生变化时, 用户可以立即看到影响, 并迅速响应。

### 优势
**灵活性**: pulumi支持多种编程语言, 允许用户使用熟悉的编程工具和库。
**可重用性**: 可以通过函数和类等编程特性提高基础架构代码的可重用性和组织性。
**动态配置**: 能够利用编程语言的特性, 动态生成和配置资源, 使得基础架构管理更加灵活。

### 示例
Pulumi创建AWS S3 Bucket的示例代码:

``` javascript
const aws = require("@pulumi/aws");

// 创建一个S3 Bucket
const bucket = new aws.s3.Bucket("my-bucket", {
    acl: "private",  // 设置访问控制列表
    tags: {
        Name: "My S3 Bucket",
        Environment: "Dev"
    }
});

// 导出Bucket的名字和URL
exports.bucketName = bucket.id;
exports.bucketUrl = bucket.websiteEndpoint;
```

### Command
``` bash
# create a new Pulumi project
pulumi new

# preview the changes that will be made to the infrastructure
pulumi preview

# apply the changes to the infrastructure
pulumi up

# destroy the infrastructure
pulumi destroy
``` 

## Terraform与Pulumi的比较

| 特性               | Terraform                       | Pulumi                             |
|--------------------|---------------------------------|------------------------------------|
| 配置语言           | HCL（声明性）                   | 多种编程语言（命令式）            |
| 学习曲线           | 相对较低, 易于上手              | 依赖于熟悉的编程语言, 学习曲线较高 |
| 状态管理           | 状态文件（terraform.tfstate）, 支持远程存储 | 状态存储在云中或本地, 支持更灵活的状态管理 |
| 社区与模块         | 拥有丰富的模块和社区支持        | 社区相对较小, 模块支持较少         |
| 资源依赖管理       | 自动处理资源之间的依赖关系      | 需要手动管理依赖关系               |
| 动态特性           | 受限于声明性配置, 动态配置较为困难 | 使用编程语言的特性, 支持动态生成和配置 |
| 可重用性           | 支持模块化                     | 高度可重用性, 利用编程语言特性     |


### 选择Terraform: 

- 如果团队对声明性语言（如HCL）更熟悉, 并且希望快速上手。
- 需要丰富的社区支持和成熟的模块。
- 对基础架构的定义不需要复杂的逻辑。

### 选择Pulumi: 

- 如果团队更熟悉编程语言, 并希望利用现有的编程技能。
- 需要动态生成基础架构配置, 或者希望实现复杂的逻辑。
- 希望在IaC中充分利用编程语言的特性（如函数、类、模块）。

## 总结
`Terraform`和`Pulumi`都是强大的`IaC工具`, 各有优劣。Terraform以其简单易用和广泛的社区支持而受到欢迎, 而Pulumi则因其灵活性和对多种编程语言的支持而逐渐崭露头角。选择哪种工具可以从团队的技能、项目需求和未来的维护策略方面来综合考虑。更多详细区别可以参见`pulumi`官网对比文章https://www.pulumi.com/docs/iac/concepts/vs/terraform/