# Running Aqua Cloudsploit on an environment that is using AWS SSO and has multiple accounts
Instructions based on Debian OS, you need to adapt for other distros.

Install aws cli and test access to aws

```
# AWS CLI
sudo apt install unzip zip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Useful tools you may need
sudo apt install nodejs npm
sudo apt install jq
sudo apt install python3 pip-python
sudo apt install git curl

# Run aws configure 
# Put in dummy values for key and secret
# edit ~/.aws/credentials - paste in credentials (taken from SSO screen) from sso environment into "default" profile to test.

# confirm you can see something, your connection to AWS is sound
aws s3 ls 
```


# Install cloudspolit 

```
cd ~
git clone https://github.com/aquasecurity/cloudsploit.git
cd cloudsploit

(required Nodejs and npm installed)

npm install

# NOTE: no special config.js is needed for AWS if using default credentials profile

# Execute cloudsploit, with default credentials
	
./index.js --csv myfilename.csv

The above will use the default credentials.
See https://github.com/aquasecurity/cloudsploit for cloudsploit readme etc..

```

# FOR SSO ENVIRONMENTS INSTALL aws-sso-util
aws-sso-util makes sso auth much easier to manage at the CLI

https://pypi.org/project/aws-sso-util/

```
python3 -m pip install --user pipx
python3 -m pipx ensurepath

# Logout and in again

pipx install aws-sso-util

# If "aws-sso-util" doesnt work, logout and in again

# Use the aws cli to authenticate onces and setup your ~/.aws files
# IF PREVIOUS SSO USED REMOVE ~/.aws/config and ~/.aws/credentails files before running this

aws configure sso 

# Populate config file, use a suitable region
aws-sso-util configure populate --region eu-central-1

#This populates ~/.aws/config with details of all accounts you have access to with that SSO login
#To extract secret and access key for a specific account 

aws-sso-util credential-process --profile [PROFILE NAME FROM AWS CONFIG FILE]

```

# Automating cloudsploit with aws-sso-util

Configure cloudsploit to use environment variables, an example config file is included in this repo config_aws.js and is referenced in teh bash script below.

```
credentials: {
        aws: {
            // OPTION 1: If using a credential JSON file, enter the path below
            // credential_file: '',
            // OPTION 2: If using hard-coded credentials, enter them below
            access_key: process.env.AWS_ACCESS_KEY_ID || '',
            secret_access_key: process.env.AWS_SECRET_ACCESS_KEY || '',
            session_token: process.env.AWS_SESSION_TOKEN || '',
            // plugins_remediate: ['bucketEncryptionInTransit']
        },

```

Configure the script aws_sso_cloudsploit.sh to suit your environment

```
CONFIG_FILE=~/.aws/config
CREDS=~/CREDS
date=$(date '+%Y-%m-%d')
REPORTS=~/REPORTS/$date/
cloudsploit=~/cloudsploit
```

This should create a reports and credentails folder, execute the cloudsploit tool per profile and output a csv for each account/profile combination.


