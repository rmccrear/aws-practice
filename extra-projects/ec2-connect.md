# Connecting to EC2 instances in private subnets

video: [Connect to EC2 with Session Manager and EC2 Instance Connect](https://www.youtube.com/watch?v=3tKB947rT5Q)

## Methods

* EC2 Instance Connect
* Session Manager
* Bastion host or jump box
* Directly connecting via SSH will not work with an instance on a private network
* Alternative: Create am AMI that does not require modification. Use automation to destroy and rebuild instances instead of accessing them manually.

### EC2 Instance Connect

Public: Open by default

Private: Must setup endpoints in your private subnet

#### Steps for setting up the instance connect method

* Start the instance
  * Start the instance in your VPC
  * No public IP
  * In your security group, set allow inbound traffic on port 22 from your any IP address (even though the instance is in a private subnet)
  * Create an endpoint in your VPC
    * Go to VPC dashboard
    * Endpoints
    * Create endpoint
    * Service name: com.amazonaws.region.ec2
    * VPC: select your VPC
    * NOTE: This launches an ENI, which has a charge.

### System Manager: Session Manager

You usually connect to an EC2 instance by initiating a connection via SSH. This method requires the instance to listen on port 22. The security is provided by keys.

Session Manager works differently. The security is provided by IAM roles and policies. And instead of you connecting to the instance, the instance has an SSM Agent running which initiates contact with the SSM service.

In a private subnet, this will require endpoints to be created in your VPC for the SSM Agent to connect to.

Session manager will require...

* A role for SSM attached to the instance
* Endpoints in your VPC (added cost)
* A security group that allows inbound traffic on port 443 to attach to the endpoints
* The instance is not required to have any inbound traffic on any port, but will require outbound traffic on port 443 to the endpoints.

Trouble shooting...

* In your VPC actions, enable...
  * DNS resolution (enableDnsSupport)
  * DNS hostnames (enableDnsHostnames)

Benefits

* CloudWatch logs can be enables and you can keep a record of all commands run on the instance.
* No need to open port 22 on the instance.
* No need to manage keys.
* Uses IAM roles and policies for security.
* Can use other SSM features like Run Command and Patch Manager to manage a fleet.

#### Steps for setting up the session manager method

* Create ROLE for SSM
  * Go to IAM
  * Roles
  * Create role
  * Select EC2 then EC2 for System Manager
  * Attach policy: AmazonSSMManagedInstanceCore
* Attach role to the desired instance
* Create an

* Create these 3 endpoints in your VPC [youtube](https://youtu.be/lYKFvuwGri8?si=9Q_H9Lt5RqedMBW2&t=350)
  * com.amazonaws.region.ssm
  * com.amazonaws.region.ssmmessages
  * com.amazonaws.region.ec2messages
  * Endpoints have a charge of $0.01 per hour.
  * Endpoints need a security group that allows outbound traffic on port 443 to the range of the subnet you wish to connect to.

Note

* SSM Login
  * SSM will log you in as the SSM user, not the ec2-user. You can switch to the ec2-user with `sudo su - ec2-user`.
  * Requires the SSM agent to be installed on the instance (this is installed by default on Amazon Linux 2 and Ubuntu instances).
