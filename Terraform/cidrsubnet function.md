In Terraform, the `cidrsubnet` function is used to generate a list of subnet information based on a given IP address and CIDR netmask. This function takes two arguments:

-   `iprange`: the IP address and CIDR netmask in CIDR notation. For example, "192.0.2.0/24" represents the IP address 192.0.2.0 with a netmask of 24.
-   `newbits`: the number of additional bits to use for subnetting. For example, if `newbits` is set to 2, the function will create a subnet with 4 subnets (2^2).

Here's an example of how you might use the `cidrsubnet` function in Terraform:



``` 
variable "ip_range" {   default = "192.0.2.0/24" }
variable "newbits" {   default = 2 }

subnets = cidrsubnet(var.ip_range, var.newbits) 
output "subnets" {   value = subnets }

```

This code would define two variables, `ip_range` and `newbits`, and then use the `cidrsubnet` function to generate a list of subnets based on the `ip_range` and `newbits` variables. The resulting list of subnets would be output as an output variable called `subnets`.

Each element in the list of subnets is a map with the following keys:

-   `cidr_block`: the CIDR notation for the subnet.
-   `network_address`: the first IP address in the subnet.
-   `broadcast_address`: the last IP address in the subnet.



<br/>

Suppose you have an existing subnet with network address `192.0.2.0/24`, and you want to create a new subnet with a prefix length of `26` using the `cidrsubnet` function. You can use the `netnum` argument to specify the subnet number for the new subnet.

```
# Create the first subnet in the 192.0.2.0/24 network with a prefix length of 26 
subnet1 = cidrsubnet("192.0.2.0/24", 26, 0) 
# Create the second subnet in the 192.0.2.0/24 network with a prefix length of 26
subnet2 = cidrsubnet("192.0.2.0/24", 26, 1) 
# Create the third subnet in the 192.0.2.0/24 network with a prefix length of 26 
subnet3 = cidrsubnet("192.0.2.0/24", 26, 2)
```

The resulting subnets would have the following network addresses:

-   `subnet1`: `192.0.2.0/26`
-   `subnet2`: `192.0.2.64/26`
-   `subnet3`: `192.0.2.128/26`

Each of these subnets would contain 64 host addresses.

> Here "newbit" defines the order. subnet1 has newbit set as 0, hence will get 1st subnet, subnet2 has newbit set as 1, hence will get the 2nd subnet and so on