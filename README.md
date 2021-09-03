# SRE_AWS_CLOUD-Week-6
![image](https://user-images.githubusercontent.com/17476059/131913417-060aa0ef-b5c7-43ff-ae4e-bc923cb08d54.png)

# Goal:
    - Set up AWS and run the app web file on port 3000

# Tasks: 
    - Create and Configure a AWS instance 
    - Load the App file into the instance 
    - Install packages
    - Add the 3000 port rule to allow users to access the port




 log into AWS and create an instance 

click on the EC2 Dashboard and create a `New instance`. 



seletc the 
>Ubuntu Server 16.04 LTS (HVM), SSD Volume Type

![image](https://user-images.githubusercontent.com/17476059/131913268-af55f864-dcb1-45e9-b7f6-481bb5e97b06.png)
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

## Step 6:  Configure Security Group
#### you should be able to add them and in the inbound rules by selecting the `security groups link` or by edditing inbound groups 


![image](https://user-images.githubusercontent.com/17476059/131913801-064b58ac-4773-433d-87aa-912b191c2745.png)


|Type        | Port range | Protocol | Source |Security groups |
|------------|------------|----------|--------|----------------|
|  HTTP| 80 | TCP | 0.0.0.0/0 |sre_michael_app |
| SSH |22 | TCP | my IP option |sre_michael_app |
| Custom TCP  |3000| TCP | 0.0.0.0/0 |sre_michael_app |


port 22 = allows the ssh to work
___
# Step 7: 
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

>`scp -ri ~/.ssh/sre_key.pem` == the file that validate the connection  \
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
```
echo"DB_HOST=mongodb://[ DB IP ]:27017/posts"| sudo tee -a /etc/environment
```



END
___

# Done week-6 AWS intro


# Thrusday Work AMI 
## Goal
```
- Load the Database to AWS and connect this one with the App instance. 
- Save both AWS instances as a AMI and load them
```


## Load the Database to AWS

In order to allows the database to work on the aws, create an instance from yesterday.

## From yesterday, follow the from steps 1 - 6


Get to the `security configurations`  setup of the site and 

### Configure Security Group
#### you should be able to add them and in the inbound rules by selecting the `security groups link` or by edditing inbound groups 

|Type        | Port range | Protocol | Source |Security groups |
|------------|------------|----------|--------|----------------|
|  custom TCP |  | TCP | APP public IP Address  |sre_michael_app |
| SSH |22 | TCP | my IP option |sre_michael_app |




# Final Part
once eveything install, log back into the aws intance. 

> `cd ~/.ssh` \
> ssh -i "sre_key.pem" ubuntu@ec2-3-250-169-19.eu-west-1.compute.amazonaws.com

once inside the instance navigate to the APP file, you sould see this once you type `ls` into the terminal you should start installing evrything for the mongodb file. 

```
sudo apt-get update -y
sudo apt-get upgrade -y
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add 
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
sudo apt-get update -y
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl status mongod
sudo systemctl enable mongod

sudo nano /etc/nginx/sites-available/default

```
> type this into the file:

```
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;      
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade'; 
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;      
    }
}
```

> Save the file CTRL-O and CTRL-X
```
sudo nginx -t 
sudo systemctl restart nginx
```

> sudo nano /etc/mongod.conf 
```
change the ip from 127.0.0.1 to 0.0.0.0 
```

> sudo systemctl restart mongod
 \
> sudo systemctl status mongod

## Task 2:
#### Save both AWS instances as a AMI and load them up again

![image](https://user-images.githubusercontent.com/17476059/131913172-b62ae1a1-09bf-43d1-9002-63bcc25fca15.png)




# step 1 
Go to the instance that you want to copy and left click on it, you should see an `image and instance` hovering over that option, you will get an create image option - click that. 
![image](https://user-images.githubusercontent.com/17476059/131913059-bc94a225-7688-481d-9a82-c43bba2e9b0a.png)

____

you should get to the `create image` page

### Creating the instance 

![image](https://user-images.githubusercontent.com/17476059/131913089-09f27222-a0ba-46d6-82d8-b15612dab9b3.png)


> set the name /
e.g. SRE_Michael_app/
> set the mage description 

> add tag
e.g. 
Name, SRE_michael_app

Create the image and click image.


___
## Initialising the image instance 

Go onto the AMI tab in the images dropdown.
follow the 

seletc the insance that you want to launch and then `Lanuch` it with the button on the top.

follow the first 1 - 7 steps for creating an instance as previously mentioned.

# END








