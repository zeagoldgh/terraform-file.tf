# terraform-file.tf
first terraform test


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
   region = "us-west-2"
 }

 resource "aws_vpc" "my_terraformvpc" {
   cidr_block = "10.0.0.0/16"
   tags = {
     Name = "My VPC"
   }
 }

 resource "aws_subnet" "private_subnet_1" {
   vpc_id     = aws_vpc.my_terraformvpc.id
   cidr_block = "10.0.1.0/24"
   tags = {
     Name = "Private 1"
   }
 }

 resource "aws_subnet" "private_subnet_2" {
   vpc_id     = aws_vpc.my_terraformvpc.id
   cidr_block = "10.0.2.0/24"
   tags = {
     Name = "Private 2"
   }
 }

 resource "aws_internet_gateway" "igw" {
    vpc_id = aws_vpc.my_terraformvpc.id
 }

 resource "aws_route_table" "Public_route_table" {
    vpc_id = aws_vpc.my_terraformvpc.id

    route {
        cidr_block = "0.0.0.0/0"
        gateway_id = aws_internet_gateway.igw.id
    }

    tags = {
        Name = "Public route table"
    }
 }

 resource "aws_route_table_association" "public_subnet_association"{
    subnet_id    = aws_subnet.public_subnet2.id
    route_table_id = aws_route_table.public_route_table.id
 }

 resource  "aws_security_group"  "ssh_http_security" {
    name        =  "allow_http_ssh"
    description = "Allow HTTP and SSH"
    vpc_id      = aws_vpc.myterraformvpc.id

    ingress {
        description = "SSH"
        from_port   = 22
        to_port     = 22
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
 }

 ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
}

engress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
}

tags = {
    Name = "allow_http_ssh"
  }
}
 
resource "aws_instance"  "first_instance" {
   ami                         = "ami-026b57f3c383c2eec"
   instance_type               = "t3.micro"
   associate_public_ip_address = true
   subnet_id                   = aws_subnet.public_subnet2.id
   vpc_security_group_ids      = [aws_security_group.ssh_http_security.id]
   key_name                    = "vockey"
   user_data = file("userdata.sh")
   tags = {
     Name = "Super instance"
   }   
}
