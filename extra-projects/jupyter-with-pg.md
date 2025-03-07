

Created...

* VPC
* Two subnets - private and public
* Internet Gateway
  * attach it to VPC 
* Route Table
  * name: public-jupyter-pg-rt
  * add route to Internet Gateway
  * create routes for 0.0.0.0/0 and IPv6
  * associate with public subnet
    * only associate with jupyter subnet, not db subnet

* Security Groups 
  * Create security groups for the jupyter and db instances
  * for jupyter-sg, allow inbound traffic on port 22 and 8888 from anywhere
  * for db-sg, allow inbound traffic on port 5432 from the jupyter-sg security group
  * for both, allow outbound traffic on all ports

Next steps:

* Create an EC2 instance for the jupyter server
* Create an EC2 instance for the PostgreSQL database
* Connect the two instances