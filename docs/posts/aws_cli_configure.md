# Amazon Web Services Command Line Interface (AWS CLI)
The AWS CLI is powerful tool that allow us to bypass some of the limitations of the Amazon Web Services User Interface. 
```
Unfortunately there is no way to download the contents of an entire directory from S3 using the graphic
user interface, arrgh. So to get the tile files you will have to install AWS-CLI. This is accomplished by
using 'pip.' 

// this is an example, it is highly recommend that you do an internet search
// for the most recent install information
sudo pip install awscli
```
Installing AWS-CLI is the easy part, now you have to configure the credentials. First, you have to get the
credentials. For obvious reasons, I can not give too much detail here, and you will have to contact a team 
member for more specific advice. However, once you have the credentials you will run . . .
```
aws configure
```
This will start a program that will ask you to enter four items:
1) AWS Access Key ID 
2) AWS Secret Access Key 
3) Default region name
4) Default output format

The answers you enter will be saved at ~/.aws/credentials on Linux, macOS, or Unix, or at C:\Users\USERNAME\.aws\credentials on Windows.
I am not sure if it best practice, but I have edited this file manually without problems. 

If you open the '.aws' directory you will find two text files: 'credentials,' and 'config.' The 'config' contains the answers to the
last two questions and the name of the profile.
```
// example of 'config'

[default] // this is the profile
region = '' // this is the location of the AWS server like 'us-west-2'
output = json // this seems fine
``` 
When opened in a text editor, the credentials file looks something like this:
```
[default] // again, this is the profile
aws_access_key_id = the key id you added
aws_secret_access_key = the key you added
```
Once that is all set up, you are good to go. The AWS-CLI will find the credentials, and you will not be prompted
to enter them when you run commands such as . . .
```
// General example to copy directory from S3 to current working directory
aws s3 cp s3://mybucket . --recursive

// Specific Example for grabbing '.mbtiles' and placing them in the current working directory
// We have been using the 'tiles' directory at the project root
aws s3 cp s3://prod-owi-resources/resources/Application/wbeep/tiles_current . --recursive 
```