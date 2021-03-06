# Using Variables

Last updated: 06.14.2020

## Purpose

The purpose of this document is to show how to use variables after
performing the initial [readme.md](../t1-getting-started/readme.md) steps.

## Prerequisites

You must follow the [readme.md](../t1-getting-started/readme.md) steps.

## Instructions

1. export AWS_ACCESS_KEY_ID="YOUR ACCESS KEY"
1. export AWS_SECRET_ACCESS_KEY="YOUR SECRET KEY"
1. cp ../t1-getting_started/main.tf .
1. vi main.tf
1. add the following block to the top of the file:

    ```hcl-terraform
    variable "region" {
      default = "us-east-1"
    }
    variable "amazon_instance" {
      type = object (
        {
          name = string
          instance_type = string
        }
      )
      default = {
        name = "Instance1",
        instance_type = "t2.medium"
      }

    }
    ```

    The block above shows that we are declaring a string
    variable called **v_region** with a default value of
    **us-east-2** and an object variable called **amazon_instance**.
    The object variable declares two internal variables called
    **name** with a type of **string** and **instance_type** with
    a type of **string**.  The internal values are given **default**
    values of **Instance1** and **t2.medium** respectively.

1. replace the provider section with the following:

    ```hcl-terraform
    provider "aws" {
      region = var.region
    }
    ```

   The provider is now using the variable **var.region** which we
   defined as the variable **region**.

1. replace the resource "aws_instance" "master1_centos" section
with the following:

   ```hcl-terraform
   resource "aws_instance" "master1_centos" {
     ami           = "ami-0ff750889570c0406"
     instance_type = var.amazon_instance.instance_type
     vpc_security_group_ids = [aws_security_group.instance.id]
     tags          = { Name = var.amazon_instance.name }

   }
    ```

    :warning: The **AMI** describes the image to use in your region.  The
    **AMI** might not be available in your Amazon Region.  Please find the
    **AMI** by performing the steps in the
    [getting started with aws](https://github.com/bretmullinix/getting-started-working-with-aws) repo.

   
   The block above shows that we are using our object variable
   **amazon_instance**.  We are assigning the resource instance_type
   to **amazon_instance.instance_type** and the resource instance tag "Name"
   to **amazon_instance.name**.

1. save your file
1. terraform init
1. Run `terraform plan` to find any errors and review
your deployment.
1. Run `terraform apply` to deploy your ec2 instance.