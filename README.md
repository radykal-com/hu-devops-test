# Simple ansible playbook
## Intro
Running this playbook will provision and configure a single ec2 instance to serve an html file

## Requirements
In order to run this playbook you first need is a working Python environment (tested with python 3.6), preferably with pyenv.

Then step into the root directory of this project and run the following command to install the dependencies
```
pip3 install -r requirements.txt
```

## Usage

### Configuration
Make a copy of the environment.yml.dist (in vars directory) and name it to whatever you like with .yml extension
Then fill the properties accordly:
- aws_region (optional, default: eu-west-1): the AWS region to deploy the resources. Read further to view list of allowed regions
- management_cidr: IP CIDR from which ssh access will be allowed (required by ansible itself, so public IP of computer running this playbook must be in the CIDR). Format example: 8.8.8.0/24

### Launch
From the root directory run the following command:
```
AWS_PROFILE={aws_profile} ansible-playbook deploy.yml --extra-vars "deploy_environment={deploy_environment}"
```

Replacing the `{}` placeholders with correct values:
- aws_profile: a valid AWS profile name already in your `~/.aws/credentials` file ( Check the following link in order to understand named profiles of AWS CLI https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html )
- deploy_environment: the name of the previously created configuration file without extension

### Limitations
It is required to have a default VPC in order to deploy this playbook.
Only working in the following AWS regions (to allow more regions the corresponding AMI ids should be added to `roles/helloworldapp.provisioning/defaults/main.yml`):
  - eu-west-1 (default)
  - eu-west-2
  - eu-west-3
  - eu-central-1
  - us-east-1

### AWS Resources created
- Security Group: HTTPs (443) open to the world
- Security Group: SSH (22) open to the CIDR defined in the configuration file
- Key Pair: with the name configured in the configuration or with a default value of "hu-test". The private key will be stored in the root directory of this project. If you delete this file, neither Ansible or you will be able to connect to any instance launched using this key, so be carefully
- EC2 instance: running Amazon Linux 2018.03 AMI
- @TODO Clean resources automatically with a flag?

### Access to the Hello World application
The last task in the playbook will show a friendly message with the URL to the application.
If accessing from a Browser you will need to allow the use of self-signed certificates
The site is protected with http basic auth, the default credentials are:
- username: admin
- password: admin

You can check it the following curl command 
```
curl -k -u admin:admin {url}
```
\* replace {url} with the real URL obtained from the playbook

### Improvements
Network resources (VPC, Subnets and RouteTables) could be provisioned prior to create dependant resources to prevent
the creation in the default VPC
