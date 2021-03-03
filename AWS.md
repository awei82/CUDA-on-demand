Since I don't have an Nvidia card to play with and video card prices have been skyrocketing recently, I decided to see if I could get a cloud instance with CUDA set up so I can test my CUDA code without relying the PACE cluster.

I went with AWS since that is what I'm most familiar with, but I know some of the other major cloud providers also offer CUDA compute. I looked up the pricing, and AWS's [G4dn EC2 instances](https://aws.amazon.com/ec2/instance-types/g4/) are the cheapest instances they have available - with [spot pricing](https://aws.amazon.com/ec2/spot/pricing/) for the `g4dn.xlarge` instance at ~$0.20 per hour in us-east-1. I've compiled my notes on getting an on-demand instance running below so that anyone else that's interested can give it a shot.

The instructions below are for creating a spot instance - aka you spin it up when you need it, and then terminate it when you're done. Note that $0.20/hour comes out to <strong>$146 dollars per month</strong> if you leave it running, so it's vitally important to only spin up the instance and have it running while you need it for testing.

<strong>Word of caution:</strong> AWS can be very complex - it is really easy to make mistakes on AWS and have no feedback on what went wrong. Also, costs can quickly run up in AWS, especially if you don't pay attention to what's been created in your account. The instructions below should be adequate to clean up everything you've created, but know that you'll be responsible for managing your spend on AWS.


## Prereqs:
0. Get an AWS account and sign into the console
1. Select your region - I recommend <strong>us-east-1</strong> if it's not already your default
2. Go to the EC2 dashboard
3. Create a key pair - once you create this, a .pem file will be downloaded to your computer
4. Create a security group - limit inbound access to port 22 on your home IP 

## Set up a launch template with the following options:
<strong>Name:</strong> CUDA
<strong>AMI:</strong> Deep Learning AMI (Ubuntu 18.04) Version 40.0
<strong>Instance type:</strong> g4dn.xlarge
<strong>Key pair:</strong> select the key pair you created earlier
<strong>Network settings:</strong>
- <strong>Networking Platform:</strong> VPC
- <strong>Subnet:</strong> Select any one
- <strong>Security Groups:</strong> select the SG that you created earlier 

<strong>Advanced details: </strong>
- Select "Request Spot Instances" 

## Launch the instance:
- Go to Launch Templates and launch one instance from the CUDA template
- Wait for the instance to boot up
- Copy down the ip address of the instance

## SSH into the instance:
- make sure the .pem file is secured: `chmod 400 <yourkeyname>.pem`
- `ssh -i <yourkeyname>.pem ubuntu@<ip-address>`
- from here, you can copy your code onto the instance and compile/test your CUDA code

## Terminate the instance when you're done:
- Very important! You are charged for every minute the instance is left running.
- Save your work off of the instance - once you terminate the instance, all stored data is deleted.
- Go to EC2 -> Instances -> Actions -> Manage Instance State -> Terminate
- This should clean up all the resources you created, including deleting any attached EBS volumes

## Repeat
When you need to test again, just follow the instructions from "Launch the instance" again.


