---
title: AWS Glue Iceberg tables schema can't be updated with Pulumi
date: 2024-10-09 09:46:22
categories: Cloud
tags: [Cloud, IaC, AWS, Pulumi, Python]
---

## Context
Recently, we're planing to use `Pulumi` to manage all current existing `AWS Glue Datacatalog` tables which are `Iceberg` format. For the `Iceberg` tables, I have post a blog before to talk about what is iceberg and what's feature of it. Here is post link: https://stonefishy.github.io/2020/05/23/what-is-apache-iceberg/

To manage the AWS Glue Iceberg tables with `Pulumi`, due to our catalog table schemas are continue changes base on requirements. We need to do some technical POC whethere the pulumi can also support to update the iceberg metadata schema as well.

## Create Glue Iceberg Table
We're using `Pulumi` to manage the AWS Cloud Infrastructure. Before create glue table, a glue database is indeed.

``` python
import pulumi
import pulumi_aws as aws

pulumi_database_test = aws.glue.CatalogDatabase("pulumi_database_test",
    create_table_default_permissions=[aws.glue.CatalogDatabaseCreateTableDefaultPermissionArgs(
        permissions=["ALL"],
        principal=aws.glue.CatalogDatabaseCreateTableDefaultPermissionPrincipalArgs(
            data_lake_principal_identifier="IAM_ALLOWED_PRINCIPALS",
        ),
    )],
    name="pulumi_database_test")
```

Above code is to create a glue database named pulumi_database_test. Next Step is to create a glue table with `Iceberg` format.

``` python
import pulumi
import pulumi_aws as aws

pulumi_external_table_test = aws.glue.CatalogTable("pulumi_external_table_test",
    database_name="pulumi_database_test",
    name="pulumi_external_table_test",
    storage_descriptor=aws.glue.CatalogTableStorageDescriptorArgs(
        additional_locations=["s3://xxx/pulumi_external_table_test/data"],
        columns=[
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test1",
                type="string",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test2",
                type="string",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test3",
                type="boolean",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test4",
                type="string",
            )
        ],
        location="s3://xxx/pulumi_external_table_test",
    ),
    table_type="EXTERNAL_TABLE",
        open_table_format_input=aws.glue.CatalogTableOpenTableFormatInputArgs(
        iceberg_input=aws.glue.CatalogTableOpenTableFormatInputIcebergInputArgs(
            metadata_operation="CREATE"
        )
    ),
    opts=pulumi.ResourceOptions(protect=False)
)
```

There is important thing to notice here is that we need to set `open_table_format_input` with `iceberg_input` and set `metadata_operation` as `CREATE`. This is because we want to create a new Iceberg table with new schema. 

Below is glue iceberg table created screenshot. You can see the 4 fields is added in schema and table format is `Apache Iceberg`.

{% image /assets/images/aws/aws-glue-table.png, alt=AWS Glue Table Schema Created by Pulumi %}

Next, let's check the important file that is `Apache Iceberg` metadata file which is located in `s3://xxx/pulumi_external_table_test/metadata/`.  Download this json file `00006-fd122b03-a7aa-42cf-8fec-001535a9fcf5.metadata.json` from `S3`. The 4 fields are defined in metadata json file. That is good. The metadata json is created as well when creating glue table.

{% image /assets/images/aws/aws-glue-iceberg-metadata.png, alt=AWS Glue Table Iceberg metadata%}

## Insert new data in Glue iceberg table
Let's using `AWS Athena` to insert a test data in the table.

``` SQL
INSERT INTO pulumi_database_test.pulumi_external_table_test(test1,test2,test3,test4) VALUES('1a', '2a', true, '4a')
```

The data is insert success and we can use `SELECT` sql to query the data.

{% image /assets/images/aws/aws-glue-iceberg-table-query.png, alt=Query inserted data in AWS Glue Iceberg table %}

In Iceberg table, we can `insert`, `update`, `delete` data as well.

## Update Glue Iceberg table schema
Let's add a new field `test5` in the glue iceberg table base on previous code.

``` python
import pulumi
import pulumi_aws as aws

pulumi_external_table_test = aws.glue.CatalogTable("pulumi_external_table_test",
    database_name="pulumi_database_test",
    name="pulumi_external_table_test",
    storage_descriptor=aws.glue.CatalogTableStorageDescriptorArgs(
        additional_locations=["s3://xxx/pulumi_external_table_test/data"],
        columns=[
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test1",
                type="string",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test2",
                type="string",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test3",
                type="boolean",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test4",
                type="string",
            ),
            aws.glue.CatalogTableStorageDescriptorColumnArgs(
                name="test5",
                type="string",
            )
        ],
        location="s3://xxx/pulumi_external_table_test",
    ),
    table_type="EXTERNAL_TABLE",
        open_table_format_input=aws.glue.CatalogTableOpenTableFormatInputArgs(
        iceberg_input=aws.glue.CatalogTableOpenTableFormatInputIcebergInputArgs(
            metadata_operation="CREATE"
        )
    ),
    opts=pulumi.ResourceOptions(protect=False)
)
```

Execute `pulumi up` command to update the glue table schema.

{% image /assets/images/aws/aws-glue-iceberg-update-by-pulumi.png, alt=Pulumi update AWS Glue Iceberg table schema %}

After that, we can check the glue table schema is updated to add a new field `test5`.

{% image /assets/images/aws/aws-glue-iceberg-table-new-field.png, alt=AWS Glue iceberg table new field %}

Let's insert new data in the table with new field `test5` and run it in `AWS Athena`.

``` SQL
INSERT INTO pulumi_database_test.pulumi_external_table_test(test1,test2,test3,test4,test5) VALUES('1b', '2b', true, '4b', '5b')
```

The Athena execute show below errors:
```
COLUMN_NOT_FOUND: Insert column name does not exist in target table: test5. If a data manifest file was generated at 's3://xxxxxx/4c346103-60d2-45ea-9813-d7060bd5efe9/Unsaved/2024/10/09/37f67a67-4604-43cb-b113-af351c363a51-manifest.csv', you may need to manually clean the data from locations specified in the manifest. Athena will not delete data in your account.
This query ran against the "pulumi_database_test" database, unless qualified by the query. Please post the error message on our forum  or contact customer support  with Query Id: 37f67a67-4604-43cb-b113-af351c363a51
```
{% image /assets/images/aws/aws-glue-iceberg-athena-insert-failed.png, alt=AWS Athena insert glue iceberg table failed %}

But when we check the `Apache Iceberg` metadata file again. The new field `test5` is not added in the new metadata file. That's why the insert new data with new field failed.

{% image /assets/images/aws/aws-glue-iceberg-metadata-not-updated.png, alt=AWS Glue iceberg table metadata not updated %}


## Conclusion
In Pulumi documentation. The `metadata_operation` of `iceberg_input` in `open_table_format_input` is only support `CREATE` value. It seems it only can create the iceberg metadata file when glue table created.

{% image /assets/images/aws/aws-glue-iceberg-pulumi-doc.png, alt=Pulumi API Doc %}

It seems this is `pulumi` issue. It is not updating the iceberg metadata file when the glue table schema is updated. I've raised a issue to pulumi,  here is issue link: https://github.com/pulumi/pulumi/issues/17516. Hope this issue can be fixed soon.

Mean while, I found there is same issue in `Terraform` which also can not update the iceberg metadata file when the glue table schema is updated. Terraform issue link here https://github.com/hashicorp/terraform-provider-aws/issues/36641.
