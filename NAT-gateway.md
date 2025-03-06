* [demo lecture](https://learn.cantrill.io/courses/1820301/lectures/42262547)

## NAT Gateway

This lesson includes a stack. It is a one click deploy that recreates the A4L project from the previous lesson. 

The stack has three AZs (A, B, C), each with 4 subnets: reserved, web, app, and db.

The web subnets are public and the others are private.

The stack puts an EC2 instance in the app-A subnet. This instance cannot access the internet. The task is to create a NAT gateway in web-A to allow the instance to access the internet.

You need create the NAT NAT gateway in the public subnet (web-A).

Then you need to create route table, called privateA for the private subnets (app-A, db-A). And set the route for 0.0.0.0/0 to the NAT gateway.

Then associate the privateA route table with the private subnets.

These steps should allow the app-A instance to access the internet. So if you ssh into the instance and ping 1.1.1.1, you should get a response, if successful.
