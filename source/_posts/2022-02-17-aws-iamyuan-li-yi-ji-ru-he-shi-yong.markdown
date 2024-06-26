---

title: "AWS IAM原理以及如何使用"
date: 2022-02-17 23:22:57 +0800
comments: true
categories: Cloud
tags: [Cloud, AWS]
---

AWS Identity and Access Management（`IAM`）是Amazon Web Services（AWS）提供的身份和访问管理服务，用于安全地管理对AWS资源的访问权限。IAM使您能够精细控制用户、组、角色等实体对AWS服务和资源的访问权限，以确保安全性和合规性。

### IAM的原理
IAM基于一些核心概念：

**用户（Users）：** 代表实际的个人用户，可以分配安全凭证（如访问密钥、密码）来访问AWS资源。
![](/assets/images/aws/aws-iam-users.webp)
<!--more-->
**组（Groups）：** 用于将一组用户组合在一起，并将一组权限分配给该组，从而简化权限管理。
![](/assets/images/aws/aws-iam-groups.webp)

**角色（Roles）：** 类似于用户，但不是直接分配给人，而是分配给AWS资源（例如EC2实例）。角色可以用于临时授权。
![](/assets/images/aws/aws-iam-roles.webp)

**权限策略（Permissions Policies）：** 
定义了用户、组或角色可以执行的特定操作的权限。AWS提供了许多预定义的权限策略，同时也可以创建自定义策略。
![](/assets/images/aws/aws-iam-policies.webp)

### 如何使用IAM

**创建用户和组：** 在IAM中，您可以创建用户，并将用户组合成组。然后，为用户分配安全凭证和权限。

**定义和分配权限：** 您可以使用AWS提供的预定义权限策略，也可以创建自定义策略，然后将这些策略附加到用户、组或角色上，以控制他们对资源的访问。

**使用角色：** 如果您的应用程序需要访问AWS服务，可以创建角色，并将这些角色附加到您的应用程序或服务中。这样，您可以避免将敏感的凭证嵌入到应用程序代码中。

**多因素认证（MFA）：** 您可以启用多因素认证来增加账户的安全性，要求用户在登录时提供额外的身份验证信息。

**访问审计：** IAM提供了日志记录功能，可记录用户在AWS资源上执行的操作，以便进行审计和安全分析。

### 最佳实践
在使用IAM时，建议采用以下最佳实践，例如：

**最小权限原则：** 为用户和角色提供所需的最小权限，以减少潜在的安全风险。

**使用身份提供商：** 将身份提供商（如企业目录）与IAM集成，以便集中管理用户和权限。

**定期审查权限：** 定期审查用户、组和角色的权限，确保权限保持最新且合规。

