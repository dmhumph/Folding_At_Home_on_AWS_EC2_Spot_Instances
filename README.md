# Folding at Home on Amazon EC2 Spot Instances using Ansible to automate configuration
This will walk through how to request the proper spot instance in AWS, then configure the instance to run Foldng at Home!

https://foldingathome.org/

## Download this repo to use and modify for your information
git clone this repo to view and modify and run ansible playbook.

You will need some information for your client to use Fold at Home and will use this information in your config.xml.  https://apps.foldingathome.org/getpasskey.  In the name field whatever you username will be, and then enter your email.  You will then receive an email with your passkey.  Use the same username and then the passkey in the config.xml file in this repo.  You also need a team number, and if you want to join our Red Hat team you can use the team number 11812 in your config.xml.

Make sure to go through the foldathome_AWS.yaml file and edit any fields needed such as custom passwords, and folder paths of where your config.xml and xstartup files are located on your host machine.

## Request AWS EC2 Spot instance
Login to AWS Console first: https://aws.amazon.com/console/ and click on the "Sign in to the Console" button in the upper right of the screen.

Once logged in, type EC2 in the text box and hit enter.  This will take you to your EC2 dashboard.

Now on the left side of the screen you will see a group called "Instances" under that there is a section called "Spot Requests" click on that.

Next, click on the Blue "Request Spot Instances" button.

On the next page select the far right radio button "Defined duration workloads" and the select the duration you would like... I typically go with 6 hours.  Now select tha Amazon AMI by choosing the "Search for AMI" button and search for "ami-04e6cde49c31596d5" and select Community AMIs.  Then hit the "Select" button.  Now you want to choose your instance type by selecting "Change Instance type" button.  In the "Instance type" dropdown select "GPU instances" and then select the first check box for the instance type "g4dn.xlarge" This instance type provides a high power GPU for use on your Fold at Home work units. Now hit the "Select" button. Now just accept the rest of the default values unless you need to change anything specific (Make sure you have the "Key pair" and if not generate a new one to use). Now to finish click "Submit", which will issue your spot request and depending on availablity of that instance type it will be fulfilled.  Now go to your EC2 Dashboard and look at your instances, and you should see your running instance now there.

You will want to copy the "Public DNS (IPv4)" information so we can use with our Ansible playbook and ssh to the host.

## Run the Ansible playbook 
I run my playbook using Ansible tower, but it can be modified easily to run as a standalone playbook.  Just make sure to edit the Hosts file in this repo and add your AWS EC2 instance DNS name to the file.  Also update the included ansible.cfg with your AWS key pair location.

The playbook can now be ran:
```
anisble-playbook -i hosts foldathome_AWS.yaml
```

Once the playbook runs (takes about 5 minutes) it will install Fold at Home, configure it with your information, install VNC, and all the dependancies needed.  

You may need to ssh to the host and sudo su - so you can be root and first kill vncserver:
```
vncserver -kill :1
```
And then restart the vncserver:
```
vncserver
```

## Access your VNC session

Now use a VNC client and use your DNS name and port 5901 to connect to it.  You will need to use your password from earlier in the configuration.  Once logged in you can access the Folding at Home controler through the top left icon and under "Science". 

If you can't access your host, you may need to check the security group and allow inbound/outbound traffic to allow VNC to work properly.
