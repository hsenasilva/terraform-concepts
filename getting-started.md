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

## Provisioning Resources

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


## Planning Updates

### Terraform State
- JSON format (do not touch!)
- Resource mappings and metadata
- Locking
- Location
    - Local
    - Remote: AWS, Azure, NFS, Terraform Cloud
- Workspaces

#### State File
```json
{
  "version": 4,
  "terraform_version": "0.12.5",
  "serial": 30,
  "lineage": "",
  "outputs": {},
  "resources": []
}
```

- State File is the truth

#### Fist rule of Terraform?
- Make all changes in Terraform


### Terraform Planning

- Inspect state
- Dependency graph
- Additions, updates, and deletions
- Parallel execution
- Save the plan

