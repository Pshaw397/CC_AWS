# Cloud Computing and AWS

## Creating an Instance
- Login to AWS and enter the EC2 Page
- Press `Launch Instance` button
- Select your AMI, for this example select `Ubuntu Server 16.04 LTS (HVM), SSD Volume Type` and then move to the next step
- Choose your instance type, for this example select `t2 micro` and then move to the next step
- Select your preferred Network, for this example select: `vpc-07e47e9d90d2076da | DevOps VPC DO NOT DELETE (default)`
- Select your preferred Subnet, for this example select: `subnet-0e9b6138ff1ce18f2 | DevOpsStudentSubnet | eu-west-1b`
- Set auto assigne public IP to `Enable` and move onto the next step
- Leave storage as it is, and move onto the next step
- Add a tag, with the desired name of your instance, and move onto the next step
- Change the name of the security group to your desiered name, for this example use: `devops_bootcamp_securitygroup_phill`
- Set the source of the first rule to: `My IP`
- Create a new rule and set the Type to `HTTP`, then set the Source to `Anywhere`
- Create a new rule, leaving the Type as default, then set the Port Range to `3000`, then move onto the next step.
- Place a new, or existing security group .pem file into your .ssh file (c/users/name/.shh)
- Select that file as your security group key pair
- Finish instance creation

## Starting the node application with your EC2 Instance
1. After creating your EC2 instance, copy the app folder to the virtual machine, to do this use: `scp -i devop_bootcamp.pem -r [app location path] ubuntu@[remote client]:[remote location path]` This will copy the files over, make sure to be in your ssh folder when you run this.
2. Run a provision file containing the commands required to install nodejs, this will contain the following commands:
```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install nginx -y
sudo systemctl start nginx
sudo apt-get install python-software-properties -y
sudo apt-get install npm -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y
sudo npm install pm2 -g
cd app
npm install
```
3. In order to ssh into the remote virutal machine, use `ssh -i devop_bootcamp.pem ubuntu@[remote client]`, from there you can use the virutal machine.
4. You can now run the application as normal by using `cd app` and then `node app.js`. You should be able to access the Port 3000 page by using your public IP (available by accessing `Connect to instance -> EC2 Instance connect` on the instance page.
5. To reverse proxy you need change the default file in /etc/nginx/sites-available/default and replace with this:
```
server {
        listen 80;
        server_name _;
        location / {
                proxy_pass http://[public IP]:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}
```
6. Restart nginx and run `node app.js` again and the reverse port should redirect from the default nginx page to the 3000 port.

[remote client] - example: `ubuntu@ec2-54-78-54-144.eu-west-1.compute.amazonaws.com` (available on `Connect to instance -> SSH client`)
[remote location path] - example: `/home/ubuntu/app`
