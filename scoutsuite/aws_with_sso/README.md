# Running NCC Scoutsuite on an environment that is using AWS SSO and has multiple accounts
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
sudo apt install pandas

# Run aws configure 
# Put in dummy values for key and secret
# edit ~/.aws/credentials - paste in credentials (taken from SSO screen) from sso environment into "default" profile to test.

# confirm you can see something, your connection to AWS is sound
aws s3 ls 
```


# Install scoutsuite 

```

mkdir ~/scoutsuite
cd ~/scoutsuite
sudo apt install python3-virtualenv
virtualenv -p python3 venv
source venv/bin/activate
pip install scoutsuite
scout --help

scout aws 

The above will use the default credentials.
See https://github.com/nccgroup/ScoutSuite/wiki/Setup for scoutsuite readme etc..

```

# Download helper script to consolidate scoutsuite findings

Download the scout2csv script 
https://github.com/7Elements/scout2csv/blob/main/scout2csv.py
Copy this to the ~/scoutsuite directory

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

# Automating scoutsuite with aws-sso-util


Configure the script aws_sso_scout.sh to suit your environment

```
CONFIG_FILE=~/.aws/config
CREDS=~/CREDS
date=$(date '+%Y-%m-%d')
REPORTS=~/REPORTS/$date/
scout=~/scoutsuite
```

scipt should create a folder of reports by account/profile and a tools_output.csv that is a summary of all findings.


