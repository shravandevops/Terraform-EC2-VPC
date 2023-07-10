## Terraform Configuration 
Fetch Existing Subnet and Security ID, Create EC2 Instance, and Display Subnet ID Output

## Provider Credentials

1. Create a file named `provider.tf` and add your AWS provider credentials.

## Child Module

1. Create a folder named `data` as a child module.

Inside the `data` folder, create a file named `datafetch.tf` with the following content:

```hcl
# Note: This file must be created inside the data folder

data "aws_subnet" "filtered_subnet" {
  filter {
    name   = "tag:Name"
    values = ["t3"]
  }
}

data "aws_security_group" "filtered_sg" {
  filter {
    name   = "tag:Name"
    values = ["sgseven"]
  }
}

resource "aws_instance" "new_instance" {
  ami           = "ami-0310483fb2b488153"
  instance_type = "t2.micro"

  subnet_id              = data.aws_subnet.filtered_subnet.id
  vpc_security_group_ids = [data.aws_security_group.filtered_sg.id]
}
```

**Note:** Ensure that the subnet and security group tags you provide belong to the same VPC ID. If the tags are not created, create them in the AWS console.

## Root Module

1. In the root module, create a file named `main.tf` and include the path and output commands directly within it.

```hcl
module "datafetch" {
  source = "./data"
}

output "instance_subnet_id" {
  value = module.datafetch.instance_subnet_id
}
```

After executing `terraform apply`, use `terraform refresh` to obtain the output of the subnet ID.
