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

## Terraform Provisioners

Terraform only do well provision/update/delete resources,
then if you need to check for example a internal process inside a EC2 instance,
is recommended to use a Configuration Manager Tool like Chef, Puppet

- Provisioners can run local or remote
- Provisioners can run on creation or destruction
- Multiple provisioners
- If something goes wrong terraform will just log where the problem is, and you need to correct

#### Provisioner Example

```terraform
provisioner "file" {
  connection {
    type = "ssh"
    user = "root"
    private_key = "var.private_key"
    host = "var.hostname"
  }
  source = "/local/path/to/file.txt"
  destination = "/path/to/file.txt"
}
```

```terraform
provisioner "local-exec" {
  command = "local command here"
}

provisioner "remote-exec" {
  scripts = ["list", "of", "local", "scripts"]
}
```

## Terraform Functions

### Most useful

- Numeric - min(42, 14, 7)
- String - lower("TACOS")
- Collection - merge(map1, map2)
- Filesystem - file(path)
- IP network - cidrsubnet()
- Date and time - timestamp()

### Functions

#### Configure networking
```terraform
variable network_info {
  default = "10.1.0.0/16" # type, default, description
}

# Returns 10.1.0.0/24
cidr_block = cidrsubnet(var.network_info, 8, 0)

# Returns 10.1.0.5
host_ip = cidrhost(var.network_info, 5)
```
#### Create ami map
```terraform
variable "amis" {
  type = "map"
  default = {
    us-east-1 = "ami-1234"
    us-west-1 = "ami-5678"
  }
}
ami = lookup(var.amis, "us-east-1", "error")
```

