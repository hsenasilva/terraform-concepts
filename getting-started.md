# TERRAFORM CONCEPTS

- Declarative approach
- Idempotent and Consistent
- Push Configuration


## IaC Benefits
- Automated Deployment
- Consistent Environments
- Repeatable process
- Reusable components (DRY concept)
- Documented architecture


## Automating Infrastructure Deployment Flow

Provisioning Resources > Planning Updates > Using Source Control > Reusing Templates


## Terraform Components

- Terraform executable (written in Go)
- Terraform files (.tf files)
- Terraform plugins (to interact with Cloud Providers)
- Terraform state (To keep track what it on)


### Variables
``` 
variable "aws_access_key" {}
variable "aws_secret_key" {}
variable "aws_region" {
     default = "us-east-1"
}
```


### Provider
``` provider "aws" {
     access_key = "var.access_key"
     secret_key = "var.secret_key"
     region = "var.aws_region"
}
```

### Data
```
data "aws_ami" "alx" {
     most_recent = true
     owners = ["amazon"]
     filters {}
}
```


### Resource
```
resource "aws_instance" "ex" {
     ami = "data.aws_ami.alx.id"
     instance_type = "t2.micro"
}
```


### Output
```
output "aws public_ip" {
     value = "aws_instance.ex.public_dns"
}
```
