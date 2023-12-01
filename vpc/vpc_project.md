# VPC with public-private subnet in production

* This example demonstrate how to create a VPC that you can use for servers in a production environment
* To improve resilienct, deploy the servers in two availability zones by using Auto Scaling and Load Balancer
* For additional security we deploy the servers in private subnets, and the servers receive requests through the Load Balancer
* The servers can connect to the Internet by using a NAT gateway, to improve resiliency we deploy the NAT gateway in both Availability Zones.

**Overview**

* The VPC has public subnet and private subnet in two Availability Zones, Each public subnet contains NAT gateway and a Load Balancer node.
* The servers run in the private subnets, are launched and terminated by using and Auto Scaling group, and receive traffic from the Load Balancer
* The servers can connect to the internet by using the NAT gateway.

## Steps to create above requirement
**Step-1:** Create VPC <br>
**Step-2:** Create Auto Scaling group <br>
**Step-3:** Create Bastion Host of Jump server <br>
**Step-4:** Create Load Balancer <br>

## Step-1: Create VPC 
* Go to VPC and select create VPC
   - Select `VPC and more`
   - Auto-generate `Enter a value for the Name tag. This value will be used to auto-generate Name tags for all resources in the VPC.`
   - IPv4 CIDR block `Determine the starting IP and the size of your VPC using CIDR notation.` use default one.
   - Tenancy `Default`
   - Number of Availability Zones (AZs) - `2`
   - Number of public subnets - `2`
   - Number of private subnets - `2`
   - NAT gateways - `1 per AZ`
   - VPC endpoints - `None`
   - Finally, click on `Create VPC` button

## Step-2: Create Auto Scaling
* Go to EC2, and select Auto Scaling
  -  Auto Scaling group `Auto scaling group can not create directly, we should create Launch Template, and then we can use template for multiple Auto Scaling groups`
* Create a launch template
  - Launch template name - required
  - Recently launched - select
  - Instance type  Info - `Free t2.micro`
  - Key pair (login)  Info - `Select existin one or create new`
  - Network settings  Info
     - Create security group
     - Security group name - required
     - VPC - required  Info
     - Inbound Security Group Rules `Add security group rule`
        - Add `ssh'
        - Add `HTTP` - 8000 port and allow 
  - Finally, click on `Create launch template`
* Now, Create Auto Scaling group
  - Provide `Auto Scaling group name`
  - Select `Launch template` which we create above step
  - Click `Next` button
  - Under Network Info section
     - Select `VPC` which we create above step
     - Availability Zones and subnets `Select 2 private subnets`
     - Click `Next` button
  - Under Load balancing Info section
     - Select `No load balancer'
     - Click `Next` button
  - Under Group size Info section
     - Desired capacity - `2`
     - Min desired capacity - `1`
     - Max desired capacity - `4`
     - Automatic scaling - optional
         - No scaling policies
     - Click `Next`
     - Click `Next`
     - Cilck `Create Auto Scaling group`
**Note:** Check EC2 instances are created or not, and here we don't have `Public IPv4 address` address for those two insances.

## Step-3: Create Bastion Host or Jump Server
It enables the access between private and public subnets
* Go to EC2 instance, and Launch instance
* Make sure we have ro choos security group for `ssh`
* Edit network setting
    - VPC
    - Auto-assign public IP  Info - `Enable`
    - Inbound Security Group Rules - `ssh`
    - Click `Launch instance`
* Connect to Bastion-host server, and copy the key value pair from local machine to Bastion-host server using `scp` command.
* Copy command `scp -i <local_path>/project1.pem <local_path>/project1.pem  ubuntu@<Bastion-host-pulicIP>:/home/ubuntu`
* Connect to Bastion-host server `ssh -i project1.pem ubuntu@<Bastion-host-pulicIP>`
* Check the .pem file `ls -ltr`
* Now, Try to connect to EC2 instance through Bastion-host using `ssh`
  `ssh -i project1.pem ubuntu@<PrivateIP>`
* Create `index.html` sample web page
```
<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>

<h1>Response from Server-1</h1>

</body>
</html>
```
* Run python http server `python3 -m http.server 8000`

## Step-4: Create Load Balancer 
Create Load Balancer and attach to EC2 instances
* Go to EC2 instance, and create Load Balancer
  - Application Load Balancer
     - Load balancer name
     - Internet-facing
     - IPv4
     - VPC
     - Mappings `Select both Availability Zones and subnets should be public
     - Security groups
     - Listeners and routing
        - Create target group
           - Target group name
           - Protocol : Port - HTTP - 80
           - Click `Next`
    - Available instances
       - Select instances
       - Click `Include as pending below`
       - Click `Create target group
* Now, add target group to the Load Balancer
* Click 'Create Load Balancer` It will take few mins to provisioning, the status should be `Active`
* Take the DNS name of the Load Balancer and try to access from the browser
* Enable security port 80 and try again
