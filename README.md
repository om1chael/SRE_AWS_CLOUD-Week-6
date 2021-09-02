# SRE_AWS_CLOUD-Week-6

# Goal:
    - Set up AWS and run the app web file on port 3000

# Tasks: 
    - Create and Configure a AWS instance 
    - Load the App file into the instance 
    - Install packages
    - Add the 3000 port rule to allow users to access the port


 log into AWS and create an instance 

(login picture)

click on the EC2 Dashboard and create a `New instance`. 

seletc the 
>Ubuntu Server 16.04 LTS (HVM), SSD Volume Type

( picture)
___
## configure New Instance  


Step 2: Choose an Instance Type 
> this will be left as a defalt witch is Type: `t2.micro ` aka the "Free tier eligible"


## Step 3: 
### Configure Instance Details
Network :
> leave this on default 

Subnet:
Set this one to:
> DevOpsStudents  default 1a 
 
Auto-assign Public IP:
> Enable
___
## Step 4: The Add Storage is kept as default 
___

## Step 5: Add tags 
> Key: Name\
> Value: sre_michael_app

### Configure Security Group
#### you should be able to add them and in the inbound rules by selecting the `security groups link` or by edditing inbound groups 

|Type        | Port range | Protocol | Source |Security groups |
|------------|------------|----------|--------|----------------|
|  HTTP| 80 | TCP | 0.0.0.0/0 |sre_michael_app |
| SSH |22 | TCP | my IP option |sre_michael_app |
| Custom TCP  |3000| TCP | 0.0.0.0/0 |sre_michael_app |


port 22 = allows the ssh to work
___
# Step 6: 
## final config step: select existing key pair

select choose an existing key pair

### select key pair from drop down
> sre_key | RSA  
To lanuch Instance then press the ID (its clickable)

___
## Getting the App on the AWS instance 
#### for this you need an ssh key. Once you have the ssh key you place that key in the `~/.ssh` Directory 

> Change the chmod 400 sre_key.pem

> cd ~/.ssh

#### In the `~/.ssh` directory, run this:
> ssh -i "sre_key.pem" ubuntu@ec2-3-250-169-19.eu-west-1.compute.amazonaws.com

### - Breakdown -

> ssh -i "sre_key.pem" = this is the key you saved in the ~/.ssh file 

> ubuntu@ec2-3-250-169-19.eu-west-1.compute.amazonaws.com  = the link with a public public address


### Once inside the AWS instance Run These commands:
 ```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install nginx -y 
sudo apt-get install nodejs -y
python-software-properties -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
 ```
___

### In order to run the App you need to install it from your directory  

Exit the AWS instance and go to the **app** file in your local directory (or the file the file that you want to migrate to the instance).

> cd/Desktop/sparta/week-5..etc

then to migrate to the instance, we use this command.

>`scp -ri ~/.ssh/sre_key.pem` *app* `ubuntu@[Public IP address (without square brackets)]:/home/ubuntu/app`

>s`cp -ri ~/.ssh/sre_key.pem` == the file that validate the connection  \
> *app* ==  the file that you want to send to the instance  \
> `ubuntu@[Public IP address]:/home/ubuntu/app` == the ip address with the location that you want to send the *app* file to `/home/ubuntu/app`

In this case, you will have to wait till eveything install properly

# Final Part
once eveything install, log back into the aws intance. 

> `cd ~/.ssh` \
> ssh -i "sre_key.pem" ubuntu@ec2-3-250-169-19.eu-west-1.compute.amazonaws.com

once inside the instance navigate to the APP file, you sould see this once you type `ls` into the terminal you should see the *app* diretory 

> cd app

Inside the *app* directory run these commands:

```
sudo npm install pm2 -y
sudo apt-get install nodejs -y
npm start
```

### Public address should show the sparta logo
# Done week-6 AWS intro




