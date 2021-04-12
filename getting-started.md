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

## Terraform Syntax

- Human readable and editable
- Configuration syntax and expressions
- Conditionals, functions, templates

### Blocks

- Basic block

``` terraform
block_type label_one label_two {
  key = value
  embedded_block {
    key = value
  }
}
```


- Example block
``` terraform
resource "aws_route_table" "route-table" {
  vpc_id = "id12345"
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = "id4343434"
  }
}
```

### Object Types

- Different value types
``` terraform
string = "taco"
number = 5
bool = true
list = ["bean-taco", "beef-taco"]
map = {name = "Ned", age = 42, loves_tacos = true}
```

### References

- Keyword reference
```terraform
var.taco_day
aws_instance.taco_truck.name
local.taco_toppings.cheeses
module.taco_hut.locations
```

- Interpolation

```terraform
taco_name = "neds-${var.taco_type}"
```

- Strings, numbers, and bools
```terraform
local.taco_count #returns the number
```

- Lists and maps
```terraform
local.taco_toppings[2] # returns element 3
local.taco_map["like-tacos"] # returns value at keyname
```

- Resource values
```terraform
var.region # returns us-east-1
data.aws_availability_zones.azs.names[1] # returns 2nd AZ
```

### Terraform Provisioners

Terraform only provision/update/delete resources, 
then if you need to check for example a internal process inside a EC2 instance,
You will need an Configuration Manager Tool like Chef, Puppet

- Provisioners can run local or remote
- Provisioners can run on creation or destruction
