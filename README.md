# Azure Network Terraform module

[![Help Contribute to Open Source](https://www.codetriage.com/innovationnorway/terraform-azurerm-network/badges/users.svg)](https://www.codetriage.com/innovationnorway/terraform-azurerm-network)

Terraform module which creates networking resources on Azure.

These types of resources are supported:

* [Virtual Network](https://www.terraform.io/docs/providers/azurerm/r/virtual_network.html)
* [Subnet](https://www.terraform.io/docs/providers/azurerm/r/subnet.html)
* [Route](https://www.terraform.io/docs/providers/azurerm/r/route.html)
* [Route table](https://www.terraform.io/docs/providers/azurerm/r/route_table.html)

## Usage

```hcl
module "network" {
  source = "innovationnorway/network/azurerm"

  # Resource group
  create_resource_group = true
  resource_group_name   = "my-dev"
  location              = "westeurope"

  # Virtual network
  name           = "my-dev-network"
  address_spaces = ["10.0.0.0/16"]
  dns_servers    = ["20.20.20.20"]

  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  private_subnets = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]

  # Tags
  tags = {
    Terraform = "true"
    Environment = "dev"
  }
}
```

## Create resource group or use an existing one

By default this module will not create a resource group and a name of an existing one should be provided in an argument `resource_group_name`.
If you want to create it using this module, set argument `create_resource_group = true`.

## Tagging

All network resources which support tagging can be tagged by specifying key-values in arguments like `resource_group_tags`, `virtual_network_tags`, `public_route_table_tags`, `private_route_table_tags`, `tags`. Tag `Name` is added automatically on all resources. For eg, you can specify virtual network tags like this: 

```hcl
module "network" {
  source = "innovationnorway/network/azurerm"

  # ... omitted
  virtual_network_tags = {
    Owner     = "test-user"
    Terraform = "true"
  }
}
```

## Conditional creation

Sometimes you need to have a way to create network resources conditionally but Terraform does not allow to use `count` inside `module` block, so the solution is to specify argument `create_network`.

```hcl
# This network will not be created
module "network" {
  source = "innovationnorway/network/azurerm"

  create_network = false
  # ... omitted
}
```

## Examples

* [Complete network](https://github.com/innovationnorways/terraform-azurerm-network/tree/master/examples/complete)

## Other resources

* [Virtual network documentation (Azure docs)](https://docs.microsoft.com/en-us/azure/virtual-network/)

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| address\_spaces | List of address spaces to use for virtual network | list | `[]` | no |
| create\_network | Controls if networking resources should be created (it affects almost all resources) | string | `"true"` | no |
| create\_resource\_group | Whether to create resource group and use it for all networking resources | string | `"false"` | no |
| dns\_servers | List of dns servers to use for virtual network | list | `[]` | no |
| location | Location where resource should be created | string | `""` | no |
| name | Name to use on resources | string | `""` | no |
| private\_route\_table\_disable\_bgp\_route\_propagation | Boolean flag which controls propagation of routes learned by BGP on private route table. True means disable. | string | `"false"` | no |
| private\_route\_table\_suffix | Suffix to append to private route table name | string | `"private"` | no |
| private\_route\_table\_tags | Additional tags for the private route table | map | `{}` | no |
| private\_subnet\_suffix | Suffix to append to private subnets name | string | `"private"` | no |
| private\_subnets | A list of private subnets inside the VPC | list | `[]` | no |
| private\_subnets\_service\_endpoints | The list of Service endpoints to associate with the private subnets  . Possible values include: Microsoft.AzureActiveDirectory, Microsoft.AzureCosmosDB, Microsoft.EventHub, Microsoft.KeyVault, Microsoft.ServiceBus, Microsoft.Sql and Microsoft.Storage. | list | `[]` | no |
| private\_vnetlocal\_route\_suffix | Suffix to append to private VnetLocal route table name | string | `"private-vnetlocal"` | no |
| public\_internet\_route\_next\_hop\_in\_ip\_address | Contains the IP address packets should be forwarded to when destination is 0.0.0.0/0 for the public subnets. Next hop values are only allowed in routes where the next hop type is VirtualAppliance. | string | `""` | no |
| public\_internet\_route\_next\_hop\_type | The type of Azure hop the packet should be sent when reaching 0.0.0.0/0 for the public subnets. Possible values are VirtualNetworkGateway, VnetLocal, Internet, VirtualAppliance and None. | string | `"Internet"` | no |
| public\_internet\_route\_suffix | Suffix to append to public internet route name | string | `"public"` | no |
| public\_route\_table\_disable\_bgp\_route\_propagation | Boolean flag which controls propagation of routes learned by BGP on public route table. True means disable. | string | `"false"` | no |
| public\_route\_table\_suffix | Suffix to append to public route table name | string | `"public"` | no |
| public\_route\_table\_tags | Additional tags for the public route table | map | `{}` | no |
| public\_subnet\_suffix | Suffix to append to public subnets name | string | `"public"` | no |
| public\_subnets | A list of public subnets inside the VPC | list | `[]` | no |
| public\_subnets\_service\_endpoints | The list of Service endpoints to associate with the public subnets. Possible values include: Microsoft.AzureActiveDirectory, Microsoft.AzureCosmosDB, Microsoft.EventHub, Microsoft.KeyVault, Microsoft.ServiceBus, Microsoft.Sql and Microsoft.Storage. | list | `[]` | no |
| resource\_group\_name | Name to be used on resource group | string | `""` | no |
| resource\_group\_tags | Additional tags for the resource group | map | `{}` | no |
| tags | A map of tags to add to all resources | map | `{}` | no |
| virtual\_network\_tags | Additional tags for the virtual network | map | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| private\_route\_table\_id | ID of private route table |
| private\_route\_table\_subnets | List of subnets associated with private route table |
| private\_subnet\_address\_prefixes | List of address prefix for private subnets |
| private\_subnet\_ids | List of IDs of private subnets |
| public\_route\_table\_id | ID of public route table |
| public\_route\_table\_subnets | List of subnets associated with public route table |
| public\_subnet\_address\_prefixes | List of address prefix for public subnets |
| public\_subnet\_ids | List of IDs of public subnets |
| this\_resource\_group\_id | The ID of the resource group in which resources are created. |
| this\_resource\_group\_location | The location of the resource group in which resources are created |
| this\_resource\_group\_name | The name of the resource group in which resources are created |
| this\_virtual\_network\_address\_space | List of address spaces that are used the virtual network. |
| this\_virtual\_network\_id | The virtual NetworkConfiguration ID. |
| this\_virtual\_network\_name | The name of the virtual network. |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Authors

Module is maintained by [Anton Babenko](https://github.com/antonbabenko) with help from [these awesome contributors](https://github.com/innovationnorway/terraform-azurerm-network/graphs/contributors).

## License

Apache 2 Licensed. See LICENSE for full details.
