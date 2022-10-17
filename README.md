# terraform-file.tf
first terraform test

Install terraform:

brew install terraform

Create a new Project

Create a folder with a terraform file

For example as you see it here: https://registry.terraform.io/providers/hashicorp/aws/latest/docs

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Create a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.0.0.0/16"
}


terraform init

Apply Changes (You need credentials!!)

terraform apply

