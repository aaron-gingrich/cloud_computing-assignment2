# cloud_computing-assignment2
<div id="top"></div>
<!-- PROJECT LOGO -->
<br />
<div align="center">

<h3 align="center">CS4843: Asignment 2, CloudFormation</h3>

  <p align="center">
    This is a project for my Cloud Computing class showing my ability to use AWS service to deploy a technology stack. This tech stack will be deployed using CloudFormation serrvice from AWS. 
</div>


<!-- ABOUT THE PROJECT -->
## About The Project

<img src="img\cs4843 - a2.png">

<p align="right">(<a href="#top">back to top</a>)</p>



### Built With

* [AWS](https://aws.amazon.com/)
* [YAML](https://yaml.org/)
* [JSON](https://www.json.org/json-en.html)
* [Cloud Formation](https://aws.amazon.com/cloudformation/)


<p align="right">(<a href="#top">back to top</a>)</p>

## Prerequisites

* AWS CLI
* AWS Configured on local machine


## Network

The net work was designed using YAML and JSON file provided by my profesor, Paul Rad
The network contains a VPC that covers the whole project. This would be similar to using a VPN if all hardware was on site. 

Within the VPC we have two availabiity zones

Each AZ has a public subnet and a private subnet

The Public subnet has and EIP that get's the users into the NAT Gatewate which then send you to the web server application. 

The private subnet contains the web application and the MySQL Database

The files are `ci-network.yml` and `ci-network-param.json`

Creat Network Stack
```sh
aws cloudformation create-stack --stack-name <stack-name> --template-body file://ci-network.yml --parameters file://ci-network-param.json
  ```
### Parameters: `ci-network-param.json`
```
[
	{
		"ParameterKey": "EnvironmentName",
		"ParameterValue": "ScalableWebSRVProject"
	},
	{
		"ParameterKey": "VpcCIDR",
		"ParameterValue": "10.0.0.0/16"
	},
    {
		"ParameterKey": "PublicSubnet1CIDR",
		"ParameterValue": "10.0.0.0/24"
	},
	{
		"ParameterKey": "PublicSubnet2CIDR",
		"ParameterValue": "10.0.1.0/24"
	},
	{
		"ParameterKey": "PrivateSubnet1CIDR",
		"ParameterValue": "10.0.2.0/24"
	},
	{
		"ParameterKey": "PrivateSubnet2CIDR",
		"ParameterValue": "10.0.3.0/24"
	}
]
```

## Web Server
I spun up a basic EC2 instance, installed nginx, and configured a simple landing page for the server, I then created a an AMI of that server to use in my cloud formation. This AMI is in region US-west-2 (Oregon). The servers get deyployed inside the network that was created in previouse step.

The files are `servers.yml` and `servers.param.json`

WEB SERVER AMI
  ```
  ami-0c73b44c0a71ff6fa
  ```

<img src="img\web-server-sc.png">

Create Webserver Stack
  ```
  aws cloudformation create-stack --stack-name <stack-name> --template-body file://servers.yml --parameters file://servers-param.json
  ```

### Paramaters: `servers-param.json` 
  ```
  [
	{
		"ParameterKey": "EnvironmentName",
		"ParameterValue": "ScalableWebSRVProject"
	},
    {
        "ParameterKey": "KeyName",
        "ParameterValue": "cs4843key"
    },
    {
        "ParameterKey": "AMItoUse",
        "ParameterValue": "ami-0c73b44c0a71ff6fa"
    }
]
  ```

## Database (RDS)
The database will be deployed last, it will be deployed in the private subnets. Both apps will point to the primary database then the primary database will point to the back up.

You will have to manually set up a Security group for the database as well subnet groups. This will hopfully be implemented in future revisions of this infrastructure as a code. 

Check out these links to do so. 
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithSecurityGroups.html
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html

Create Database stack
```
aws cloudformation create-stack --stack-name <stack-name> --template-body file://database.yml --parameters file://database-param.json  
```

### Paramaters: `database-param.json` 
  ```
  [
	{
		"ParameterKey": "EnvironmentName",
		"ParameterValue": "ScalableWebSRVProject"
	},
    {
		"ParameterKey": "MasterUsername",
		"ParameterValue": "root"
	},
    {
		"ParameterKey": "MasterUserPassword",
		"ParameterValue": "password"
	},
    {
		"ParameterKey": "DBSubnetGroupName",
		"ParameterValue": "meme"
	}
]
```


## Getting Started

1. Create AWS account
2. Add User and asign all admin privs
3. Download AWS CLI
4. Run aws configure and use the info to the account you created. (Make sure to use us-west-2)
5. Edit `ci-network-param.json` and run
```sh
aws cloudformation create-stack --stack-name <stack-name> --template-body file://ci-network.yml --parameters file://ci-network-param.json
  ```
5. Edit `servers-param.json` and run
 ```
aws cloudformation create-stack --stack-name <stack-name> --template-body file://servers.yml --parameters file://servers-param.json
  ```
6. Create DB Security group in console
7. Create db subnet
8. Edit  `database-param.json` and run
 ```
aws cloudformation create-stack --stack-name <stack-name> --template-body file://database.yml --parameters file://database-param.json
  ```

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/github_username/repo_name.svg?style=for-the-badge
[contributors-url]: https://github.com/github_username/repo_name/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/github_username/repo_name.svg?style=for-the-badge
[forks-url]: https://github.com/github_username/repo_name/network/members
[stars-shield]: https://img.shields.io/github/stars/github_username/repo_name.svg?style=for-the-badge
[stars-url]: https://github.com/github_username/repo_name/stargazers
[issues-shield]: https://img.shields.io/github/issues/github_username/repo_name.svg?style=for-the-badge
[issues-url]: https://github.com/github_username/repo_name/issues
[license-shield]: https://img.shields.io/github/license/github_username/repo_name.svg?style=for-the-badge
[license-url]: https://github.com/github_username/repo_name/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/linkedin_username
[product-screenshot]: images/screenshot.png