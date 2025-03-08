# Public Subnets

To make a public subnet, you need to do the following:

* Create a VPC
* Create an Internet Gateway
* Attach the Internet Gateway to the VPC
* Create a subnet
* Create a route table
  * add routes for 0.0.0.0/0 and IPv6 to send traffic to the Internet Gateway
  * associate the route table with the subnet
* Create a security group
  * allow inbound traffic on desired ports  from anywhere
* Create an EC2 instance
  * associate the instance with the subnet
  * associate the security group with the instance