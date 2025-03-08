# VPCs and subnets

A subnet is a collection of IP addresses that will correspond to compute instances in close physical proximity.

A subnet is much like a LAN. Just like a LAN party, the computers will be near each other, physically, i.e. they'll be in the same AZ.

They are also used to sort and organize compute instances. For example, it is best practice to keep db instances in on subnet and app instances in another. Even if they don't need to be separated for technical reasons, keeping them in different subnets helps keep things tidy. For example, you may want your db and app instances to both be private and in the same AZ. You would still put them in different subnets to help organize them. Subnets are also important for things like audits and compliance in larger institutions. It's best practice to use subnets to sort your compute instances in this fashion.

Networking architecture can be applied to subnets. A subnet is by default private. This means it has no inbound connection to the internet, but it has an outbound one. In order to set up networking, you will need to create an Internet Gateway for your VPC. The igw is like the cable modem in your house. All traffic in and out of the VPC goes through it. The subnet has its own router (like a LAN). The router has a routing table. The routing table can customize the traffic. If you want your subnet to be fully public, you would set up the route table to send all outgoing traffic to the igw. You can limit this traffic to certain IP ranges in the route table.

Here is an example of an entry in a route table that directs all traffic to the igw:

```bash
0.0.0.0/0  -->  my-igw
```

Another way to control access to subnets is by applying a NACL. This is like a non-stateful firewall and can be set to block traffic from certain IP blocks or ports.

## Instances

EC2 compute instances are created in a subnet. If you have not explicitly created a subnet, it will be created in you default VPC and subnet. (The default VPC and subnet are created for you as a convenience when you set up your AWS account.)

It is a good idea to create a VPC for each project you have in your account. A small project can have a single subnet or even a single instance. Projects that will grow and scale should have a subnets planned out. A common way to lay out subnets for a web app is having a subnet for each of web, app, and db plus one more for reserved future use. A web app that requires higher availability could duplicate this pattern across two or more AZ. In this case, you would have 8 subnets: web-a, app-a, db-a, reserved-a, web-b, app-b, db-b, and reserved-b. Having your instances redundant across AZ's will ensure you can have uptime even if an AZ becomes unavailable.

Instance networking is restricted by the subnet. It is also further restricted by the Security Group applied directly to an EC2 instance. Security groups can be created automatically with the security group wizard, or they maybe be created before hand. Security groups will explicitly allow access in or out to certain IPs or AWS assets on defined ports. It is like a stateful firewall.

NACLs (on the subnet) and sg's (on the instances) work in tandem to control network access to your EC2 instances.

## How to use VPC subnets


The idea is to create a group of subnets within a VPC.

Cantrill has 3 sets of subnets, each in it's own AZ. Each set has 4 subnets: reserved, web, app, and db.

![VCP subnets](./img/VCP-subnets/subnet-diagram.png)

Steps:

Create the VPC with a CIDR block of 10.16.0.0/16

```bash
aws ec2 create-vpc --cidr-block 10.16.0.0/16
```

Create the subnets

[subnet list from Cantrill course](https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0059-aws-mixed-vpcsubnets/subnets.txt)

```bash
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.16.0.0/20 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.16.16.0/20 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.16.32.0/20 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.16.48.0/20 --availability-zone us-east-1a
```

Then do the same for the other AZs.

```bash
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.16.64.0/20 --availability-zone us-east-1b
...
```

For fun, here is a list of numbers, counting by 16.
```
16, 32, 48, 64, 80, 96, 112, 128, 144, 160, 176, 192, 208, 224, 240, 255
10, 20, 30, 40, 50, 60, 70,  80,  90,  a0,  b0,  c0,  d0,  e0,  f0,  ff
```

```JavaScript
parseInt("10", 16)
parseInt("20", 16)
parseInt("ff", 16)
```

Then create the route table for the web subnet

You will need to add routes to open the subnet to the whole internet.

IPv4

0.0.0.0/0

IPv6

::/0






