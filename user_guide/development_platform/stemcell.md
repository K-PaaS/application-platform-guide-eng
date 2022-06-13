### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Stemcell Development

## Table of Contents
1. [Document Outline](#1--문서-개요)
     * [Purpose](#11--목적)
     * [Range](#12--범위)
     * [References](#13--참고자료)
2. [Environmental Preparation](#2--환경-준비)
     * [Preparations Before Installation](#21--설치전-준비사항)
     * [Configure AWS Environment](#22--aws-환경-구성)
     * [RUBY Installation](#23--ruby-설치)
     * [BOSH Installation](#24--bosh-설치)
     * [Vagrant Installation](#25--vagrant-설치)
     * [VM Installation for Stemcell Creation](#26--스템셀-생성을-위한-vm-설치)
     * [When creating a stem cell by modifying a BOSH Source, etc](#27--bosh-source-등을-수정하여-스템셀을-생성할-경우)
3. [Create default OS Image](#3--기본-os-이미지-생성)
     * [Create Ubuntu OS Image](#31--ubuntu-os-이미지-생성)
     * [Create RHEL OS Image](#32--rhel-os-이미지-생성)
     * [Archive Location for the Default OS Created Image](#33--생성한-기본-os-이미지의-보관장소)
4. [Create BOSH Stemcell](#4--BOSH-스템셀-생성)
     * [Create Stemcell Using the OS Image in Remote Server](#41--원격지의-os-이미지를-사용한-스템셀-생성)
     * [Create Stemcell Using the OS Image in Local Server](#42--로컬의-os-이미지를-사용한-스템셀-생성)
     * [Storage of the Created Stemcell](#43--생성한-스템셀의-보관장소)
5. [Create BOSH Light Stemcell](#5--bosh-light-스템셀-생성)
     * [Create Bosh Light Stemcell](#51--bosh-light-스템셀-생성)
6. [Customizing Stemcell](#6--스템셀-커스터마이징)
     * [Modify Stemcell Creating Source](#61--스템셀-생성-소스-수정)


# 1.  Document Outline

## 1.1.  Purpose 

The purpose is to create user-defined stemcell.

## 1.2.  Range

The guide range describes the establishment of an environment for generating a BOSH stemcell and the contents for generating a BOSH stemcell and a BOSH light stemcell.

## 1.3.  References

This document was writen by referring to Cloud Foundry's BOSH Document.

Create Bosh Stemcell:
[https://github.com/cloudfoundry/bosh-linux-stemcell-builder/blob/master/README.md](https://github.com/cloudfoundry/bosh-linux-stemcell-builder/blob/master/README.md)


# 2.  Environmental Preparation

## 2.1.  Preparations Before Installation

This installation guide is described based on running in a Linux (Ubuntu 14.04 64bit) environment. Ruby and Bosh are installed to generate stemcells. It also configures additional packages and execution environments for installing Ruby and Bosh.

-   AWS Environment
-   rvm
-   Ruby (2.1.6 or 1.9.3-p545)
-   Bundler
-   Bosh
-   Vagrant
-   There should be a internet connection to create a stemcell.

## 2.2.  Configure AWS Environment

BOSH creates and manages VMs that create stemcells on AWS. In order to create a stemcell, an account must be created on AWS and an environment must be configured.

-   AWS Account

	AWS Website: [https://aws.amazon.com/ko/](https://aws.amazon.com/ko/)


-   Access Key Setting

	1.  Login to AWS: [https://console.aws.amazon.com/console/home](https://console.aws.amazon.com/console/home)


		![account-dashboard](./images/stemcell/account-dashboard.png "account-dashboard")


	2.  Select the account from the top right of the screen and select Security Credentials

		![security-credentials-menu](./images/stemcell/security-credentials-menu.png "security-credentials-menu")


	3.  When 'AWS IAM' confirm pop up appears, select 'Continue to Security Credentials' button and procceed to Security Credentials screen


	4.  Select Access Keys and click Create New Access Key to create the Access Key.
    
		![security-credentials-dashboard](./images/stemcell/security-credentials-dashboard.png "security-credentials-dashboard")


	5.  Check the information of the created key.

		![access-keys-modal](./images/stemcell/access-keys-modal.png "access-keys-modal")

		Set the Access Key ID on the screen at the ***BOSH\_AWS\_ACCESS\_KEY\_ID***.

		Set the  Secret Access Key on the screen at the ***BOSH\_AWS\_SECRET\_ACCESS\_KEY***.


	6.  Close the dialog screen.



-   Configure Virtual Private Cloud (VPC)


	1.  Select the regional menu in the upper right corner of the screen. (The light stemcell is currently available only in the N. Virginia region.)

		![account-dashboard-region-menu.png](./images/stemcell/account-dashboard-region-menu.png "account-dashboard-region-menu")

	2.  Select the VPC menu from the AWS console screen.

		![account-dashboard-vpc](./images/stemcell/account-dashboard-vpc.png "account-dashboard-vpc")

	3.  Select VPC Wizard.

		![vpc-dashboard-start](./images/stemcell/vpc-dashboard-start.png "vpc-dashboard-start")

	4.  Select “VPC with a Single Public Subnet”

		![vpc-dashboard-wizard](./images/stemcell/vpc-dashboard-wizard.png "vpc-dashboard-wizard")

	5.  Enter the network information and click Create VPC button to create VPC.
		
		![create-vpc](./images/stemcell/create-vpc.png "create-vpc")

	6.  The list of created VPC as shown below outputs.

		![list-subnets](./images/stemcell/list-subnets.png "list-subnets")

		Set Subnet ID at ***BOSH\_AWS\_SUBNET\_ID***.


-   Create Key Pair

	1.  On the AWS console screen, select the 'EC2' menu to go to the EC2 dashboard screen.

	2.  Select ‘Key Pairs’ and ‘Create Key Pair’ button in order.

		![list-key-pairs](./images/stemcell/list-key-pairs.png "list-key-pairs")

	3.  Enter Key Pair name from the Create Key Pair Dialog Screen. Create and download Key Pair.

		![create-key-pair](./images/stemcell/create-key-pair.png "create-key-pair")

	4.  Relocate the downloaded Key(Ex: bosh.pem) to the key storing directory and change authority.

  
			# When Key pair name downloaded ‘bosh’ key at ‘~/Downloads’ directory
			$ mv ~/Downloads/bosh.pem ~/.ssh/
			$ chmod 400 ~/.ssh/bosh.pem
  

		Set the Key path and name at the ***BOSH\_VAGRANT\_KEY\_PATH***.

-   Create Security group

	1.  EC2 Click  ‘Security Groups’ and ‘Create Security Group’ button in order from the EC2 dashboard screen.

		![list-security-groups](./images/stemcell/list-security-groups.png "list-security-groups")

	2.  From the Create Security Group pop up screen, enter the value as shown below and create security group.

		![create-security-group](./images/stemcell/create-security-group.png "create-security-group")

			
		|Provision                  | Set Value                             |Description|
		|---------------------|----------------------------------|----------------------------
		|Security group name   |Any (Ex: bosh_stemcell)           |Security group name|
		|Description           |Any (Ex: BOSH builds Stemcells)   |Description about the security group|
		|VPC                   |VPC created from the VPC             |VPC to apply the security group|


	3.  Select 'Edit' on the 'Inbound' tab to set the security policy for the created security group.

		![open-edit-security-group-modal](./images/stemcell/open-edit-security-group-modal.png "open-edit-security-group-modal")

	4.  Add a security policy as shown in the table below.

		|Type   |Protocol   |Port Range   |Source|
		|------|----------|------------|--------|
		|SSH    |TCP        |22           |My IP|


		Set the created Security Group ID at ***BOSH\_AWS\_SECURITY\_GROUP***.


## 2.3.  RUBY Installation

The procedure of installing Ruby is as follows.


1.  Installing dependency packages

  -   For Ubuntu

			$ sudo apt-get update
			$ sudo apt-get install -y build-essential zlibc zlib1g-dev ruby ruby-dev openssl libxslt-dev libxml2-dev libssl-dev libreadline6 libreadline6-dev libyaml-dev libsqlite3-dev sqlite3 libxslt1-dev libpq-dev libmysqlclient-dev
    

  -   For CentOS
   
    		$ sudo yum install gcc ruby ruby-devel mysql-devel postgresql-devel postgresql-libs sqlite-devel libxslt-devel libxml2-devel yajl-ruby
    

  -   For OSX
    
    		$ xcode-select --install
		    xcode-select: note: install requested for command line developer tools
  

2.  Ruby Installation Admin and Ruby Installation

		$ curl -L https://get.rvm.io | bash -s stable
		$ source ~/.rvm/scripts/rvm

		#Install Ruby 2.1.6
		$ rvm install 2.1.6

		#Set Default Version of Ruby
		$ rvm use 2.1.6 --default


3.  Vefify Installation

		$ ruby -v

## 2.4.  BOSH Installation

The procedure for BOSH installation is as follows.


1.  Create environment to install

		#Install git
		$ sudo apt-get install git

		#Create execution directory
		$ mkdir -p ~/workspace
		$ cd ~/workspace


2.  Bosh Installation and Setting
  
		#Install Bosh
		$ git clone https://github.com/cloudfoundry/bosh.git
  		
		#Install Bosh sub-module
		$ cd bosh
		$ git submodule update --init --recursive

		#Install Bosh depending package
		$ bundle install

		#Install bosh_cli
		$ gem install bosh_cli
  

3.  Verify Bosh Installation

		$ bundle exec bosh or bosh


## 2.5.  Vagrant Installation

Vagrant is an open source that builds a virtual environment. Use vagrant to manage VMs for creating stemcells.


1.  Install Vagrant

		$ sudo apt-get install vagrant

2.  Install Vagrant Plugin

		$ vagrant plugin install vagrant-berkshelf
		$ vagrant plugin install vagrant-omnibus
		$ vagrant plugin install vagrant-aws --plugin-version 0.5.0


## 2.6.  VM Installation for Stemcell Creation

Configure the virtual environment for creating stemcell.


1.  Set AWS Environment Variables
  
		#Set the environment variables as below.
		$ export BOSH_AWS_ACCESS_KEY_ID=<YOUR-AWS-ACCESS-KEY>
		$ export BOSH_AWS_SECRET_ACCESS_KEY=<YOUR-AWS-SECRET-KEY>
		$ export BOSH_AWS_SECURITY_GROUP=<YOUR-AWS-SECURITY-GROUP-ID>
		$ export BOSH_AWS_SUBNET_ID=<YOUR-AWS-SUBNET-ID>
  

2.  Create VM

	***Default VM size is m3.xlarge type and is subject to AWS billing.***

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant up remote --provider=aws


3.  Verifying Remote Access

		#Enable access key information settings
		$ export BOSH_KEY_PATH=<Path and file name of the access key>
		$ export BOSH_VAGRANT_KEY_PATH=<Path and file name of the access key>

		#Execute Access
		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh remote


4.  Send and receive remote files

		#Enable access key information settings
		$ export BOSH_KEY_PATH=<Path and filename of the access key>
		$ export BOSH_VAGRANT_KEY_PATH=<Path and filename of the access key>

		#Execute file transmission and reception
		$ cd ~/workspace/bosh/bosh-stemcell

		#When sending a file
		$ vagrant scp <local file> remote:<Remote file storage path>

		#When receiving a file
		$ vagrant scp remote:<Remote file storage path> <local file>
 

## 2.7.  When creating a stem cell by modifying a BOSH Source, etc

1.  When Reflecting the modified or updated gem file of the Source Code to the stemcell-generated VM

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant provision remote


# 3.  Create default OS Image 

If a stemcell configured with a user-defined OS suitable for the user environment is required, create a basic OS image first. The basic OS image consists of the minimum OS function required by the stemcell and the Bosh agent and Bosh monitor.


## 3.1.  Create Ubuntu OS Image

Describe the procedure for creating an Ubuntu OS image.


1.  Execute Build\_os\_image

		#Create default OS image
		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh -c '
		cd /bosh
		bundle exec rake stemcell:build_os_image[ubuntu,trusty,/tmp/ubuntu_base_image.tgz]
		' remote


2.  Input option information

	|Option Name                      |Necessity   |Description                                          |Example|
	|--------------------------|------|--------------------------------------------|------------------------------|
	|Operating system name      |O      |OS Type                                      |ubuntu|
	|Operating system version   |O      |OS Version                                      |trusty|
	|OS image path              |O      |Directory and name in which the default OS image is created       |/tmp/ubuntu_base_image.tgz|


	※ Enter ‘’for non-necessary areas.


## 3.2.  Create RHEL OS image 

Describes the procedure for creating a RHEL OS image.


1.  Download RHEL 7.0 iso and upload it to the stemcell creation VM.

  	[https://access.redhat.com/downloads/content/69/ver=/rhel---7/7.0/x86\_64/product-downloads](https://access.redhat.com/downloads/content/69/ver=/rhel---7/7.0/x86_64/product-downloads)
  
	※ To dowload, one should have RedHat account.


2.  Configuring the Execution Environment

		#Access to stemcell creation VM
		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh remote

		#RHEL image mount
		$ sudo mkdir -p /mnt/rhel
		$ sudo mount rhel-server-7.0-x86_64-dvd.iso /mnt/rhel

		#redhat account information setting
		$ export RHN_USERNAME=<RHEL Account>
		$ export RHN_PASSWORD=<RHEL Password>

3.  Execute Build\_os\_image

		#Execute build_os_image from the stemcell creation VM
		$ cd /bosh
		$ bundle exec rake stemcell:build_os_image[rhel,7,/tmp/rhel_7_base_image.tgz]

	※ The default RHELOS image is not provided by BOSH.

	***※ If an error occurs when creating a basic RHEL OS image, RHEL provides a basic RHEL OS image and generates a stemcell.***


## 3.3.  Archive Location for the Default OS Created Image 

1.  Check created default OS image

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh remote
		$ ll /tmp


2.  Download created default OS
 
		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant scp remote:/tmp/<Name of the created default OS image> <Loacl path to download>


# 4.  Create BOSH Stemcell 

## 4.1.  Create Stemcell Using the OS Image in Remote Server 

Describe the procedure for generating stemcells using remote server's OS images.

1.  Execute Build

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh -c '
		cd /bosh
		CANDIDATE_BUILD_NUMBER=<current_build> bundle exec rake stemcell:build[vsphere,esxi,centos,7,go,bosh-os-images,bosh-centos-7-os-image.tgz]
		' remote


2.  Input Options Information

	|Option Name                      |Necessity    |Description                       |Example|
	|--------------------------|------|-----------------------------------------------|------|
	|CANDIDATE_BUILD_NUMBER     |O      |Current stemcell version            |3147|
	|Infrastructure             |O      |Infrastructure type                 |Vsphere|
	|Hypervisor                 |O      |Hypervisor type          |Esxi|
	|Operating system name      |O      |OS type                    |Centos|
	|Operating system version   |O      |OS version                    |7|
	|Agent type                 |X      |Agent type               |Go|
	|OS image s3 bucket name    |O      |OS Image Bucket Name for Bosch     |Bosh-os-image|
	|OS image key               |O      |OS image name                |Bosh-centos-7-os-image.tgz|


	※ For other OS images, refer to below. 
[http://s3.amazonaws.com/bosh-os-images/](http://s3.amazonaws.com/bosh-os-images/)

	※ Agent type is not a necessary item, but since it does not support agents other than go type as of the moment, so enter go.


3.  Configuring Setable Options

	|Infrastructure             |Hypervisor                |OS|
	|--------------------------|-------------------------|----------------------------|
	|aws                        |Xen                       |ubuntu|
    |aws                        |Xen                       |centos|
	|openstack                  |Kvm						|ubuntu|                       
	|openstack                  |Kvm						|centos|		
	|vcloud					   |Esxi						 |ubuntu|		
	|vsphere					|Esxi						 |ubuntu|
	|vsphere					|Esxi						 |centos|

	※ If you want to specify an option different from the above, modify or develop the necessary parts in Bosh source.


## 4.2.  Create Stemcell Using the OS Image in Local Server 

Describes the procedure for generating a stemcell using a local OS image.


1.  Create or download the default OS image.

	|OS Name            |URL|
	|----------------|----------------------------------------------------------------------------|
	|ubuntu           |[https://s3.amazonaws.com/bosh-os-images/bosh-ubuntu-trusty-os-image.tgz](https://s3.amazonaws.com/bosh-os-images/bosh-ubuntu-trusty-os-image.tgz)|
	|centos           |[https://s3.amazonaws.com/bosh-os-images/bosh-centos-7-os-image.tgz](https://s3.amazonaws.com/bosh-os-images/bosh-centos-7-os-image.tgz)|
	|Custom OS     |[3. Create default OS Image](#3--기본-os-이미지-생성)|



2.  If you downloaded default OS image, upload it to stemcell-generated VM.


3.  Execute build\_with\_local\_os\_image
  
		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh -c '
		cd /bosh
		bundle exec rake STEMCELL_BUILD_NUMBER=<stemcell version> stemcell:build_with_local_os_image[aws,xen,ubuntu,trusty,go,/tmp/ubuntu_base_image.tgz]
		' remote

	※ When skipping STEMCELL\_BUILD\_NUMBER, the version of the stemcell produced is fixed to 0000.


4.  Input Options Information

	|Option Name                      |Necessity    |Description                                   |Example|
	|--------------------------|------|--------------------------------------|------------------------------|
	|Infrastructure             |O      |Infrastructure type                             |Aws|
	|Hypervisor                 |O      |Hypervisor type                       |Xen|
	|Operating system name      |O      |OS type                                |Ubuntu|
	|Operating system version   |O      |OS version                                |Trusty|
	|Agent type                 |X      |Agent type                           |Go|
	|Local os image path        |O      |OS image path on the stemcell generating VM      |/tmp/ubuntu_base_image.tgz|


	※ Agent type is not a necessary item, but since it does not support agents other than go type as of the moment, so enter go.


5.  Configuring Setable Options

	|Infrastructure             |Hypervisor                |OS|
	|--------------------------|-------------------------|----------------------------|
	|aws                        |Xen                       |ubuntu|
    |aws                        |Xen                       |centos|
	|openstack                  |Kvm						|ubuntu|                       
	|openstack                  |Kvm						|centos|		
	|vcloud					   |Esxi						 |ubuntu|		
	|vsphere					|Esxi						 |ubuntu|
	|vsphere					|Esxi						 |centos|


## 4.3.  Storage of the Created Stemcell 

1.  Check created stemcell

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh remote
		$ ll /bosh/tmp


2.  Download created stemcell

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant scp remote:/bosh/tmp/<Name of the created stemcell> <Local path to download>


# 5.  Create BOSH Light Stemcell 

## 5.1.  Create Bosh Light Stemcell

The Bosh light stemcell is a lightweight stemcell that can only be used in AWS (N. Virginia region only). A stemcell is registered as AMI in AWS, and a file recording the registered image ID and stemcell information is generated and compressed into tgz.

1.  Upload downloaded or created stemcells to the stemcell creation VM.

		$ cd ~/workspace/bosh/bosh-stemcell	
		$ scp <Stemcell to upload> remote:/tmp/bosh-stemcell.tgz


2.  Execute build\_light

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh -c '
		cd /bosh
		export BOSH_AWS_ACCESS_KEY_ID=<YOUR-AWS-ACCESS-KEY>
		export BOSH_AWS_SECRET_ACCESS_KEY=<YOUR-AWS-SECRET-KEY>
		bundle exec rake stemcell:build_light[/tmp/bosh-stemcell.tgz,hvm]
		' remote


3.  Input option information

	|Option Name                |Necessity   |Description                   |Example|
	|---------------------|------|----------------------|------------------------|
	|Local stemcell path   |O      |Stemcell path of the Local   |/tmp/bosh-stemcell.tgz|
	|Virtualization type   |X      |Virtualization type            |Hvm|

	※ Enter ‘’for non-necessary areas.


# 6.  Customizing Stemcell 

## 6.1.  Modify Stemcell Creating Source 

In order to generate a stemcell that meets the user's requirements, it may be necessary to modify the stemcell generation source. Most of the files that make up the stemcell generation are in the directory below.

	bosh/stemcell_builder/stages/<stemcell creating stage>/<folder or file>

1.  Apply the modified contents to the stemcell creation VM.

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant provision remote


2.  Create Stemcell. (Create default OS image first if necessary.)


3.  If an error occurs while generating a stem cell, the error can be taken and then proceed from the stage where the error occurred. In this case, add resume\_from=<create stemcell stage\> to the generation command.
 ※ But, in resume option
    When generating a stemcell, an error may occur in a stage that performed normally before. In this case, do not use the resume\_from option.

		$ cd ~/workspace/bosh/bosh-stemcell
		$ vagrant ssh -c '
		cd /bosh
		bundle exec rake stemcell:build_with_local_os_image[aws,xen,ubuntu,trusty,go,/tmp/ubuntu_base_image.tgz] resume_from=stemcell_openstack
		' remote


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Stemcell Development
