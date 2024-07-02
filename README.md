# AWS-RESOURCE-MANAGEMENT-SHELL-SCRIPTING-PROJECT
This is a shell scripting project that can be used to track AWS resource usage.
As this is a shell scripting project, you should have access to a Linux system, if you don't have it.
Create an EC2 instance in AWS and use it.

First, it is best to update the packages of the system using the command
sudo apt update

After that, Install AWS CLI using the command.
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

Configure credentials to communicate with AWS using the command 
aws configure 

Then you will be asked to add
AWS Access Key ID:
AWS Secret Access Key:
Default Region:
Default output format[json]:

To get all this information, go to the top right corner where you see the username, under that click Security Credentials, under that go to the Access key section, click Create Access Key, and create the access key and get the information required above.


Create a shell script  
vim aws_resource_tracker.sh
Press
Esc + I to change to insert mode



Start the script by specifying the executable used (Here it is bash) 
#!/bin/bash


Add metadata details like the Author, Date, and version.
You can add a one-line description of the project  
i.e. This script will report the AWS Resource Usage.
The metadata section will look like this.
#########################################
#Author: Prathyush K V
#Date: 01-07-24
#Version: v1
#This script will report the AWS resource usage
#########################################

Adding all this will be useful when you refer to the script after some days.


This is the main part of the script. Here you specify what resources are going to be tracked.
In this project, we will be tracking 
AWS S3
AWS EC2
Lambda Functions
IAM Users


For listing the S3 buckets, the command is
echo "Print all the s3 buckets"
aws s3 ls


For listing the EC2 instances, the command is
echo "Print all the ec2 instances"
aws ec2 describe-instances
This command will list all the instances. It will contain all the information about each instance you have.

aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
This command will only display the InstanceId of the instances you have. Filtering is done because the command without filtering will provide all the information, which will contain information that is not necessary. So you can read through json using jq and filter the output with the only required information.


The '|' command will send the output of the command to jq. jq is a parser that is used to get information from json.
yq is another parser that is used for yaml parser

Here InstanceId comes under Reservation list(which is represented as []) under that Instances[].InstanceId





For listing the Lambda functions, the command is
#List the lambda functions
echo "Print all the lambda functions"
aws lambda list-functions --function-version ALL | jq '.Functions[].FunctionName'

This command will list the lambda functions, filter them out, and display the names of the lambda functions.



For listing the IAM Users, the command is,
#List all the IAM Users
echo "Print all the IAM Users"
aws iam list-users | jq '.Users[].UserName'

This command will list the IAM Users, filter them out, and display the names of the users.

If you are stuck or don't know the commands, just refer to AWS CLI commands through AWS reference documentation

Adding comments and print statements will help everyone understand what command is being executed.


Now the script is completed, to save and exit 
Click Esc + :wq


Provide the permission to the script using the chmod command 
chmod 777 aws_resource_tracker.sh
777 permission is given as this is a demo project. It is important to provide the permissions carefully.

After providing the permissions, It is time to execute the project. The command for executing the project is
./aws_resource_tracker.sh


For redirecting this output to a separate file you just add the > and >> operator
1. > (Greater Than): Output Redirection

The > operator redirects the standard output (stdout) of a command to a file.
If the file doesn't exist, it's created.
If the file already exists, its contents are overwritten with the command's output.

Example:
ls > directory_listing.txt

This command lists the contents of the current directory and redirects the output to a file named directory_listing.txt. If the file doesn't exist, it will be created. If it already exists, its previous contents will be replaced with the new listing.

2. >> (Double Greater Than): Append to Output

The >> operator also redirects stdout, but it appends the command's output to an existing file.
If the file doesn't exist, it's created, and the command's output becomes the initial content.

Example:
echo "This is a new line" >> log_file.txt

This command adds the text "This is a new line" to the end of a file named log_file.txt. If the file doesn't exist, it will be created with the added text. If the file already exists, the new line will be appended to the existing content.

To automate the execution of this script. You can use crotab.
To create and execute a cron job using the vim editor:

1. Accessing Crontab with vim:

The process starts similarly by opening the crontab file for editing, but this time using crontab -e with the vim editor.
crontab -e -v
The -v flag with vim ensures that the file is opened in a read-only mode by default, preventing accidental modifications to your crontab entries.


2. Cron Job Syntax:

The crontab file uses a specific syntax to define cron jobs. Each line in the file represents a single cron job.

The syntax consists of five fields separated by spaces:

  Minute (0-59): Specifies the minute (0 for the 0th minute)

  Hour (0-23): Specifies the hour of the day (0 for midnight)

  Day of Month (1-31): Specifies the day of the month

  Month (1-12): Specifies the month (1 for January)

  Day of Week (0-6 or SUN-SAT): Specifies the day of the week (0 or SUN for Sunday)

  Wildcards (*) can be used to represent any value within the range.

  Commas (,) can be used to specify multiple values within a field.

  Slashes (/) can be used to specify an interval between values.


3. Creating a Cron Job:

Here if we want to run the script called aws_resource_tracker.sh every hour (on the 0th minute of every hour). You would add the following line to your crontab:

0 * * * * /path/to/my_script.sh
Breakdown:
  0: Run the script on the 0th minute of every hour.
  *: Run the script on every hour (because it's in the hour field).
  * * *: Run the script every day of the month, every month, and every day of the week (using wildcards).
  /path/to/aws_resource_tracker.sh: The path to the script you want to run.


4. Saving and Exiting:

Once you've added your cron job(s), save and exit vim using the command mentioned earlier: 
Type :wq to save and exit

5. Verifying Cron Jobs (Optional):

To view all your cron jobs, use the crontab -l command.



This is how you can automate the execution of the script.
