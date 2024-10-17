---
title: Using Pulumi to Import the AWS Resources of the Other Region
date: 2024-07-04 10:27:55
categories: Cloud
tags: [Cloud, IaC, AWS, Pulumi, Python]
top: true
swiper: true
---

## Context
By default, the Pulumi import the resource in the region which is specified in the `Pulumi.yaml` or `Pulumi.<stack-name>.yaml` file. If we import the resources which is located in other regions. It will cause the error by using `pulumi import` command.

For example, we have quicksight resources such like DataSource, DataSet located in the `eu-west-1` region, we already manage these resources in the pulumi by using `pulumi import` CLI command. All resources are located in `eu-west-1` region. It is specified in the `Pulumi.yaml` or `Pulumi.<stack-name>.yaml` file like below.

```yaml
config:
  aws:region: eu-west-1
```

Now we also want to import the existing resources such like QuickSight user Groups into the pulumi. But the AWS Quicksight user Groups resources all are located in the `us-east-1` region. The pulumi will give us the error if we try to import the other region resource direclty.

{% image /assets/images/pulumi/pulumi-import-other-region-resource-error.png %}

This is because the Pulumi is using default `provider` for the AWS resources. The default provider is set to the region which is specified in the `Pulumi.yaml` or `Pulumi.<stack-name>.yaml` file. So, if we want to import the resources from other region, we need to specify the provider for that region.

## Pulumi Provider
A `Pulumi provider` is a plugin that enables Pulumi to interact with a specific cloud provider or service. These providers are responsible for translating the Pulumi code into the appropriate API calls for the target cloud platform. 

By default, each provider uses its package’s global configuration settings, which are controlled by your stack’s configuration. You can set information such as your cloud provider credentials with environment variables and configuration files. If you store this data in standard locations, Pulumi knows how to retrieve them. For example, you can run below command to set the AWS region to `eu-west-1` region for the AWS provider configuration.

``` shell
pulumi config set aws:region eu-west-1
```

This command actually will set the `aws:region` configuration value for the AWS provider in your Pulumi stack yaml file. You can also define the provider in your pulumi code, and create related resources in the specified region.

``` python
import pulumi
import pulumi_aws as aws

# Create a new provider for the us-east-1 region
us_east_1_provider = aws.Provider('us-east-1', region='us-east-1')
# Create the Quicksight Groups resources in the us-east-1 region
quicksight_group = aws.quicksight.Group(
    "dev",
    aws_account_id="<aws-account-id>",
    group_name="dev",
    opts=pulumi.ResourceOptions(
        provider=us_east_1_provider
    )
)
```

In above code, we create a new provider for the `us-east-1` region and then create the Quicksight user Groups resources in the `us-east-1` region. The `provider` option is used to specify the provider to use for the resource. Even we have global configuration for the `eu-west-1` region, we can still create the resources in the `us-east-1` region by specifying the provider.

## Importing the AWS Resources of the Other Region
Back to previous topic, if we want to import the AWS Quicksight Users and Groups resources from the `us-east-1` region in current pulumi stack from the command line, we need to specify the provider for the pulumi command line. The Pulumi CLI import command takes an additional `--provider` option to specify the provider to use for the import.

``` shell
pulumi import aws:quicksight/group:Group dev xxxxxx/default/dev --provider name=urn
```

In above command, we are importing the `aws:quicksight/group:Group` resource with the `dev` name in the provider. For the `--provider` option, The `name` is the name of the provider to use for the import, and `urn` is the URN of the provider to use for the import. Typically, the resource urns in pulumis is below format.

``` shell
urn:pulumi:production::acmecorp-website::custom:resources:Resource$aws:s3/bucket:Bucket::my-bucket
           ^^^^^^^^^^  ^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^
           <stack-name> <project-name>   <parent-type>             <resource-type>       <resource-name>
```

If there is no `parent-type` in the resource urn, the urns will be like below format.

``` shell
urn:pulumi:production::acmecorp-website::aws:s3/bucket:Bucket::my-bucket
           ^^^^^^^^^^  ^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^
           <stack-name> <project-name>   <resource-type>       <resource-name>
```
For the details of Pulumi Resources URNs, please refer to the [Pulumi URNs](https://www.pulumi.com/docs/concepts/resources/names/#urns).

In our scenario, we can import the Quicksight Groups resources from the `us-east-1` region by using the provider. {% pbg warning, There is one thing is important to note, the `provider` resource should be created in pulumi before importing the resources of the other region resource. Otherwise, the import other region resources will fail. %} For example, we don't have any `Provider` resources for the `us-east-1` region in our current stack. If we run below command to import the Quicksight Groups resources from the `us-east-1` region, it will fail. Below is an examle of the full import resource with `--provider` option

``` shell
pulumi import aws:quicksight/group:Group dev <aws-account-id>/default/dev --provider us_east_1_provider=urn:pulumi:<pulumi-project-name>::quicksight::pulumi:providers:aws::us_east_1_provider
```

The `<aws-account-id>` and `<pulumi-project-name>` are placeholder just for example. Without the `Provider` resource for the `us-east-1` region, the import command will fail as below error message.

``` error
Preview failed: bad provider reference 'us_east_1_provider=urn:pulumi:<pulumi-project-name>::quicksight::pulumi:providers:aws::us_east_1_provider' is not valid URN'
```

The error full screenshot is below.

{% image /assets/images/pulumi/pulumi-import-without-provider-error.png %}

To fix this issue, we need to create the `Provider` resource for the `us-east-1` region in our current stack. We can do this by adding the `Provider` resource in in our Pulumi code and using `Pulumi up` command to create the resource.

``` python
import pulumi
import pulumi_aws as aws

# Create a new provider for the us-east-1 region
us_east_1_provider = aws.Provider('us-east-1', region='us-east-1')
```

After that, we can run the import command again to import the Quicksight Groups resources from the `us-east-1` region. And now you will see the Quicksight Groups resources of the `us-east-1` region in your pulumi stack.

{% image /assets/images/pulumi/pulumi-import-provider-success.png %}

## Summary
To import the AWS resources of the other region, we need to specify the provider for the pulumi command line. The Pulumi CLI import command takes an additional `--provider` option to specify the provider to use for the import. The `provider` resource should be created in pulumi before importing the resources of the other region resource.





