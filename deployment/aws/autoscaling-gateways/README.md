# Auto-Scaling Appgate SDP Gateways on aws

This directory contains example how to setup and provision appgate sdp controller and gateways.

## Requirements
- [Terraform](https://www.terraform.io/downloads.html) >= v0.14.5
- [terraform-provider-appgatesdp](https://github.com/appgate/terraform-provider-appgatesdp/releases) >= v0.6.6

## Setup Environment

- Generate RSA keys

  ```bash
  ssh-keygen -t rsa -f appgate_rsa -b 4096 
  # hit <enter> twice to ensure the key does not have a password
  ```
  
  - Record the RSA key locations

    - `realpath appgate_rsa` => **PRIVATE_KEY**
    - `realpath appgate_rsa.pub` => **PUBLIC_KEY**

- Create a VPC on AWS

  1. Log in to AWS account
  1. Open VPC page
  1. Click `Create VPC`
     1. Select `VPC Only`
     1. Name tag
        - `appgate-vpc`
     1. IPv4 CIDR block
        - `10.123.0.0/24`
     1. IPv6 CIDR block
        - `No IPv6 CIDR block`
     1. Create VPC
  1. Record the `VPC ID` => **VPC_ID**
  1. Record `CIDR block` => **CIDR_BLOCK**

- Create an Internet Gateway

  1. Log in to AWS account
  1. Open VPC page
  1. Click `Internet Gateways` in left menu under `Virtual private cloud`
  1. Click `Create internet gateway`
     1. Name tag
        - `appgate-igw`
     1. Create internet gateway
  1. Record the `IGW ID` => **IGW_ID**
  1. Attach the gateway to your vpc.
     1. Click `Actions` in the top right.
     1. Find your vpc in the `Available VPCs` search box.
     1. Click `Attach internet gateway.

## Create the controller

- Setup an empty config file. 

  ```bash
  # we just create an empty config file at first, this file will
  # be automatically populated by the controller module.
  echo "{}" > appgateprovider.config.json
  ```

- Create an auto.tfvars file

  ```hcl
  private_key          = "<PRIVATE_KEY>"
  public_key           = "<PUBLIC_KEY>"
  appliance_cidr_block = "<CIDR_BLOCK>/24"

  ingress_cidr_blocks = [
    "__your_IP__/32",
  ]

  internet_gateway_id = "<IGW_ID>"
  vpc_id = "<VPC_ID>"
  ```

- Initialize Terraform

  ```bash
  terraform init
  ```

- Apply Terraform

  ```bash
  terraform apply -var-file auto.tfvars -auto-approve
  ```

## Initial Setup

Default Admin UI login is `admin`/`adminadmin`. You should change this immediately.

