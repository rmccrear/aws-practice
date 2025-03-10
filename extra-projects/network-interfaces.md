# Network Interfaces

## Elastic Network Interfaces (ENI)

### Physical NIC

A network card (NIC) is a physical device that connects a computer to a network.

Some features of a physical NIC are:

* provides a physical connection to a network
* has a MAC address
* has an IP address
* can be connected to a switch or router
* attaches to a computer via a PCI slot
* a computer can have multiple NICs
  * for example, a computer could have a NIC for a wired connection and a NIC for a wireless connection
  * or a computer could have a NIC for a public network and a NIC for a private network
  * a single server could host two websites, each with its own NIC and own IP address

### ENI

An Elastic Network Interface (ENI) is a virtual network card that can be attached to an EC2 instance. Just like a network card, an instance must have an ENI to connect to a network. It can have more than one ENI.

Some features of an ENI are:

* provides a virtual connection to a network
* has a MAC address, just like a physical NIC
* has a private IP address within the subnet
* can have a public IP address
* can be assigned multiple IP addresses
  * for example, running two small websites on a single instance
* One instance can have multiple ENIs
  * isolating traffic such as web and database traffic
  * running your own NAT gateway instance to tunnel traffic to the internet