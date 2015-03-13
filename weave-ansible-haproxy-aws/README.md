# Deploying a containerized app with Weave on AWS using Ansible #

## What you will build ##

Weave allows you to focus on developing your application, rather than your infrastructure.

In this example we will demonstrate how Weave allows you to quickly and easily deploy HAProxy as
a load balancer for a simple php application running in containers on multiple nodes in [Amazon 
Web Services](http://aws.amazon.com), with no modifications to the application and minimal docker 
knowledge.

## What you will use ##

* [Weave](http://weave.works)
* [Docker](http://docker.com)
* [Ansible](http://ansible.com)
* [HAProxy](http://haproxy.org)
* [Apache](http://httpd.apache.org)
* [Ubuntu](http://ubuntu.com)
* [Amazon Web Services](http://aws.amazon.com)

## What you will need to complete this guide ##

This getting started guide is self contained. You will use Weave, Docker, Ansible, HAProxy, Apache, 
Ubuntu and Amazon Web Services.  

You will need to have a valid [Amazon Web Services](http://aws.amazon.com) (AWS) account and to know your AWS acess key id and secret. Please refer to [AWS Identity and Access Management documentation] (http://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html#IAM-credentials-summary) for details on how to retrive your credentials. If you have already installed the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) these details will be accesible to Ansible.

You will need to have [Ansible installed](http://docs.ansible.com/intro_installation.html). Interacting with AWS via Ansible also requires [boto](http://docs.pythonboto.org/en/latest/), which provides a Python interface to AWS.  

* 15 minutes
* [Git](http://git-scm.com/downloads)
* [Ansible >= 1.8.4](http://docs.ansible.com/intro_installation.html)

It may be helpful to have the [AWS CLI]((http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed, but this is optional.
 
* [AWS CLI > 1.7.12 ](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)

## What you will do ##

You will use Weave and Ansible to

* Launch two ec2 instances with Weave and Docker installed on Ubuntu
* Start a number of containers on each ec2 instance and place a HAProxy container in front of them 

You will then connect to your public facing HAProxy container.

## Configuring and setting up your Instances ## 

All of the code for this example is available on [github](http://github.com/fintanr/weave-gs), and you first clone the 
getting started repository.

```bash
git clone http://github.com/fintanr/weave-gs
cd weave-gs/weave-ansible-haproxy-aws
```

### AWS Regions ### 

The ansible playbook associated with this guide use the `eu-west-1` region by default, and an [AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) from the same region. You can change the region and AMI to your preferred option by calling `./check-region.sh -r <your region>`.

Alternatively if you have the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed you can call `./check-region.sh` which will select an AMI in your region.

In both cases this will create a file `ansible_aws_variables.yml` which our Ansible playbook will use. You can also edit this file directly and put in your choosen region and AMI. 

```bash
cat ansible_aws_variables.yml 
---
aws_region: eu-west-1
template: ami-799e100e
```

## Using Ansible to setup your EC2 instances ##

You will use one of the two Ansible playbooks provided to 

* Create a private key
* Create a security group
* Launch two EC2 instances and install Docker and Weave onto each of these instances

You execute this by calling

```bash
ansible-playbook setup-weave-ubunu-aws.yml
```

This ansible playbook will take four to five minutes to complete. When you execute this playbook you will get output similar to what you can see at this [gist](https://gist.github.com/fintanr/4d6bb5bbc92f4b1197a5). 


## What has happened ##

You have created a private key for use with AWS and a security group, weavedemo, to run your instances in.
You have then started two ec2 instances with Ubuntu, updated your image and installed Docker and Weave. 

## Using Ansible and Weave to launch our Containerized App ##

You will use the second Ansible playbook provided to

* Launch Weave and WeaveDNS on each EC2 instance
* Launch a set of containers containing a simple webapp
* Launch HAProxy as a load balancer in front of your webapp 

```bash
ansible-playbook launch-weave-haproxy-aws-demo.yml
```

This ansible playbook will take a few minutes to complete. When you execute this playbook you will get output similar to what you can see at this [gist](). 

## What has happened ##

You firstly started Weave and WeaveDNS on each ec2 instance, which creates an overlay network between the hosts.
You then used weave to launch three docker containers on each ec2 instance running our simple php application, 
followed by launching HAProxy.

## Connecting to your application ##

You can connect to the application directly using the curl commands below, or alternatively you can use the 
included script `access_aws_hosts.sh`. This script will make six calls to the HAProxy instance to cycle through
all of the web application containers you have started.

```bash
./access_aws_hosts.sh 
``` 

Which will give you output similar too

```bash

```

### Connecting directly with curl ###

```bash
AWS_PUBLIC_IP=$(grep public_ip inventory | cut -d"=" -f2)
curl $AWS_PUBLIC_IP
```

## Summary ##

You have used Weave, Docker and Ansible to deploy a load balanced application with HAProxy on AWS.

## Credits ##

The Ansible playbook used here was based on the work of [Sebastien Goasguen](http://sebgoa.blogspot.com/) in his [ansible-rancher playbook](https://github.com/runseb/ansible-rancher). 