### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > BOSH CLI

## Table Contents
1. [Outline](#1)
	* [Document Purpose](#2)
	* [Document Range](#3)
	* [References](#4)

1. [BOSH CLI Basic Usage](#5)


1. [BOSH CLI - Environments](#6)
	* [bosh environments](#7)
	* [bosh create-env](#8)
	* [bosh alias-env](#9)
	* [bosh environment](#10)
	* [bosh delete-env](#11)


1. [BOSH CLI - Session](#12)
	* [bosh log-in](#13)
	* [bosh log-out](#14)


1. [BOSH CLI - Stemcell](#15)
	* [bosh Stemcells](#16)
	* [bosh upload-stemcell](#17)
	* [bosh delete-stemcell](#18)
	* [bosh repack-stemcell](#19)

1. [BOSH CLI - Release creation](#20)
	* [bosh init-release](#20)
	* [bosh generate-job](#21)
	* [bosh generate-package](#22)
	* [bosh vendor-package](#23)
	* [bosh create-release](#24)
	* [bosh finalize-release](#25)
	* [bosh reset-release](#26)

1. [BOSH CLI - Release blobs](#27)
	* [bosh blob](#28)
	* [bosh add-blob](#29)
	* [bosh reomove-blob](#30)
	* [bosh sync-blob](#31)

1. [BOSH CLI - Releases](#32)
	* [bosh releases](#33)
	* [bosh upload-release](#34)
	* [bosh delete-release](#35)
	* [bosh export-release](#36)
	* [bosh inspect-release](#37)

1. [BOSH CLI - Configs](#38)
	* [bosh configs](#39)
	* [bosh update-config](#40)
	* [bosh delete-config](#41)

1. [BOSH CLI - Cloud config](#42)
	* [bosh cloud-configs](#43)
	* [bosh update-cloud-config](#44)

1. [BOSH CLI - Runtime config](#45)
	* [bosh runtime-configs](#46)
	* [bosh update-runtime-config](#47)

1. [BOSH CLI – CPI config](#48)
	* [bosh cpi-configs](#49)
	* [bosh update-cpi-config](#50)

1. [BOSH CLI - Deployments](#51)
	* [bosh deployments](#52)
	* [bosh deployment](#53)
	* [bosh deploy](#54)
	* [bosh delete-deployment](#55)
	* [bosh delete-instance](#56)
	* [bosh manifest](#57)
	* [bosh recreate](#58)
	* [bosh restart](#59)
	* [bosh start](#60)
	* [bosh stop](#61)
	* [bosh ignore](#62)
	* [bosh unignore](#63)
	* [bosh logs](#64)

1. [BOSH CLI - VMs](#65)
	* [bosh vms](#66)
	* [bosh delete-vms](#67)

1. [BOSH CLI - Disks](#68)
	* [bosh disks](#69)
	* [bosh attach-disk](#70)
	* [bosh delete-disk](#71)

1. [BOSH CLI - SSH](#72)
	* [bosh ssh](#73)
	* [bosh scp-disk](#74)

1. [BOSH CLI - Errands](#75)
	* [bosh errands](#76)
	* [bosh run-errand](#77)

1. [BOSH CLI - Tasks](#78)
	* [bosh tasks](#79)
	* [bosh task](#80)
	* [bosh cancle-task](#81)

1. [BOSH CLI - Snapshot](#82)
	* [bosh snapshots](#83)
	* [bosh take-snapshot](#84)
	* [bosh delete-snapshot](#85)
	* [bosh delete-snapshots](#86)

1. [BOSH CLI - Deployment recovery](#87)
	* [bosh update-resurrection](#88)
	* [bosh cloud-check](#89)
	* [bosh locks](#90)

1. [BOSH CLI - Misc](#91)
	* [bosh clean-up](#92)
	* [bosh help](#93)
	* [bosh interpolate](#94)



## <div id='1'/>Document Outline

### <div id='2'/>Document Purpose 
The purpose of this document is to understand BOSH through basic usage and use examples for BOSH CLI v2, a tool for installation and operation management of BOSH. 

### <div id='3'/>Document Range 

This document describes how to use BOSH CLI V2.

### <div id='4'/>References 

Rhis document referred to BOSH Document([http://bosh.io](http://bosh.io)) of Cloud Foundry.

## <div id='5'/>BOSH CLI Basic Usage

To help BOSH deployment and Release management, CLI is divided in the Command Line Commands as below.

- bosh-cli: A CLI to manage BOSH



- **Basic Syntax**

		$ bosh [<options>] <command> [<args>]
		
	<options> and <args>, which are the factors enclosed in square brackets in the bosh command, are used selectively according to the command, and the <command> factor is an essential factor.

- **Options**

	|**Numbers**   |**Options**                  |**Description**|
	|----------|-------------------------|--------------------------------|
	|1          |-c, --config              |Designate BOSH configuration file|
	|2          |--ca-cert                 |Specify the CA certificate used for the Director and UAA connection|
	|3          |--client                  |Define Username or UAA Client|
	|4          |-n                        |Don't ask for user input|
	|5          |--json                    |Change output format to JSON|
	|6          |--tty                     |Include all commonly displayed text in the output when the command is not redirected|
	|7          |--no-color                |Disable colors|
	|8          |--deployment, -d          |Specify a placement for deploy commands|
	|9          |-h, --help                |See help message|
	|10         |--column=                 |Show filtered columns only|
	|11         |-e, --enviroment          |Director environment name or URL|
	|12         |--sha2     |Use SHA256 checksum|
	|13         |--parallel=                |Maximum number of parallel operations|
	|14         |--client-secret=                |Password or UAA client password reset|

##  <div id='6'/>BOSH CLI - Environments

### <div id='7'/>***bosh environments***

- **Basic Syntax**

		$ bosh environments (Alias: envs)

- **Description**

		Lists the environment in which the director registered with the BOSH CLI is nicknamed

- **Parameter**

- **Used Example**
 
		$ bosh envs
		URL              Alias
		104.154.171.255  gcp
		192.168.56.6     vbox
		2 environments
		Succeeded


### <div id='8'/>***bosh create-env***

- **Basic Syntax**

		$ bosh create-env [deploymentFile] [--state path] [-v ...] [-o ...] [--vars-store path]

- **Description**

	Create a single VM based on Manifest File using the BOSH CLI. Commonly used to create a Director environment

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|deploymentFile|Install Manfiest file|O|
	|--state path|Deployment state file path|X|
	|-v|Manifest Replace variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml...|X|
	|--vars -store path|creds.yml file, Authentication key and Job Password yml file path|X|

- **Used Example**

		$ bosh create-env ~/workspace/bosh-deployment/bosh.yml \
  		--state state.json \
		--vars-store ./creds.yml \
  		-o ~/workspace/bosh-deployment/virtualbox/cpi.yml \
  		-o ~/workspace/bosh-deployment/virtualbox/outbound-network.yml \
  		-o ~/workspace/bosh-deployment/bosh-lite.yml \
  		-o ~/workspace/bosh-deployment/jumpbox-user.yml \
  		-v director_name=vbox \
  		-v internal_ip=192.168.56.6 \
  		-v internal_gw=192.168.56.1 \
  		-v internal_cidr=192.168.56.0/24 \
  		-v network_name=vboxnet0 \
  		-v outbound_network_name=NatNetwork


### <div id='9'/>***bosh alias-env*** 

- **Basic Syntax**

		$ bosh alias-env [name] -e [location] [--ca-cert=path]

- **Description**

	Nickname the director to access through the BOSH CLI.

- **Parameter**


	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|name|Set environment name|O|
	|location|Set director location|O|
	|--ca-cert=path|Set CA authentication certificate|X|

### <div id='10'/>***bosh environment*** 

- **Basic Syntax**

		$ bosh -e [my-env] environment (Alias: env)

- **Description**

	Output the corresponding Director information

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**
 
		$ bosh -e vbox env
		Using environment '192.168.56.6' as '?'

		Name      vbox
		UUID      eeb27cc6-467e-4c1d-a8f9-f1a8de759f52
		Version   260.5.0 (00000000)
		CPI       warden_cpi
		Features  compiled_package_cache: disabled
          dns: disabled
          snapshots: disabled
		User      admin

		Succeeded


### <div id='11'/>***bosh delete-env***

- **Basic Syntax**

		$ bosh delete-env [deploymentFile] [--state path] [-v ...] [-o ...] [--vars-store path]

- **Description**

	Deletes previously created VM based on the manifest. Same flags provided to create-env command should be given to the delete-env command.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|deploymentFile|Installed Manfiest File|O|
	|--state path|Deployment state file path|O|
	|-v|Manifest Replace variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml…|X|
	|--vars -store path|creds.yml file, authentication key and Job Password yml file path|X|

- **Used Example**

		$ bosh delete-env ~/workspace/bosh-deployment/bosh.yml \
  		--state state.json \
  		--vars-store ./creds.yml \
  		-o ~/workspace/bosh-deployment/virtualbox/cpi.yml \
  		-o ~/workspace/bosh-deployment/virtualbox/outbound-network.yml \
  		-o ~/workspace/bosh-deployment/bosh-lite.yml \
  		-o ~/workspace/bosh-deployment/jumpbox-user.yml \
  		-v director_name=vbox \
 		-v internal_ip=192.168.56.6 \
  		-v internal_gw=192.168.56.1 \
  		-v internal_cidr=192.168.56.0/24 \
  		-v network_name=vboxnet0 \
  		-v outbound_network_name=NatNetwork


## <div id='12'/>BOSH CLI - Session

### <div id='13'/>***bosh log-in***

- **Basic Syntax**

		$ bosh -e [my-env] l

- **Description**

	Logs in given user into the Director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|BOSH Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env l
		User (): admin
		Password ():


### <div id='14'/>***bosh log-out***

- **Basic Syntax**

		$ bosh -e [my-env] log-out 

- **Description**

	Logs out currently logged in user

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|BOSH Specified Director Environment Name|O|

- **Used Example**

		$ bosh log-out 
		Logged out from '192.168.10.241'

		Succeeded


## <div id='15'/>BOSH CLI - Stemcells

### <div id='16'/>***bosh Stemcells***

- **Basic Syntax**

		$ bosh -e [my-env] stemcells (Alias: ss)

- **Description**

	View uploaded releases

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env ss
		Using environment '192.168.56.6' as '?'

		Name                                         Version  OS             CPI  CID
		bosh-warden-boshlite-ubuntu-trusty-go_agent  3363*    ubuntu-trusty  -    6cbb176a-6a43-42...
		~                                            3312     ubuntu-trusty  -    43r3496a-4rt3-52...
		bosh-warden-boshlite-centos-7-go_agent       3363*    centos-7       -    38yr83gg-349r-94...

		(*) Currently deployed

		3 stemcells

		Succeeded


### <div id='17'/>***bosh upload-stemcell***

- **Basic Syntax**

		$ bosh -e [my-env] upload-stemcell [location] [--sha1=digest] [--fix]

- **Description**

	Stemcell upload

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|location|Set stemcell file location and URL|X|
	|--sha1|Check stem cell file sh1um value|X|
	|--fix|Replacing previously uploaded stem cells with the same name and version|X|

- **Used Example**

		$ bosh -e my-env us ~/Downloads/bosh-stemcell-3468.17-warden-boshlite-ubuntu-trusty-go_agent.tgz

		$ bosh -e my-env us https://bosh.io/d/stemcells/bosh-stemcell-warden-boshlite-ubuntu-trusty-go_agent?v=3468.17


### <div id='18'/>***bosh delete-stemcell***

- **Basic Syntax**

		$ bosh -e [my-env] delete-stemcell [name]/[version]

- **Description**

	Delete uploaded stemcell

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|name|Name of the stemcell to delete|O|
	|version|version of the stemcell to be deleted|O|

- **Used Example**

		$ bosh -e my-env delete-stemcell bosh-warden-boshlite-ubuntu-trusty-go_agent/3468.17

### <div id='19'/>***bosh repack-stemcell***

- **Basic Syntax**

		$ bosh repack-stemcell src.tgz dst.tgz [--name=name] [--version=ver] [--cloud-properties=json-string]

- **Description**

	Creates a new stem cell tarball with updated properties such as the name, version, and cloud properties of an existing stem cell
	Reference URL: https://bosh.io/docs/repack-stemcell.html 


- **Parameter**

	|**Parameter Name**|**Description**|**necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|name|Update registration stemcell name|O|
	|version|Update registration stemcell version|X|
	|cloud-properties|Update registration stemcell cloud-properties, Json format|X|

- **Used Example**

		$ bosh repack-stemcell --name=acme-ubuntu-encrypted --cloud-properties='{"encrypted": true, "kms_key_arn": "arn:aws:kms:us-east-1:088444384256:key/4ffbe966-d138-4f4d-a077-4c234d05b3b1"}' bosh-stemcell-3363.9-aws-xen-hvm-ubuntu-trusty-go_agent.tgz acme-encrypted-stemcell.tgz


## <div id='20'/>BOSH CLI - Release creation 

### <div id='21'/>***bosh init-release***

- **Basic Syntax**

		$ bosh init-release [--git] [--dir=dir]

- **Description**

	Creates a configuration file for the release in dir Current directory if dir is not used

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|--git|Create appropriate .gitignore file for BOSH release Git repository|X|
	|--dir|Create an empty release configuration file for directory|X|

- **Parameter**

		$ bosh init-release --git --dir release-dir
		$ cd release-dir


### <div id='22'/>***bosh generate-job***

- **Basic Syntax**

		$ bosh generate-job [name] [--dir=dir]

- **Description**

	Creates an empty job file for a release in dir

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|name|Release Job Name|O|
	|--dir|Creates an empty release component file related to Job about the directory|X|

- **Used Example**

		$ bosh generate-job jenkins

### <div id='23'/>***bosh generate-package***

- **Basic Syntax**

		$ bosh generate-pakage [name] [--dir=dir]

- **Description**

	Create empty file for package for release in dir

- **Parameter**

	|**parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|name|Release pakage name|O|
	|--dir|Create a blank release configuration file related to package Job in the directory|X|

- **Used Example**

		$ bosh generate-package jenkins

### <div id='24'/>***bosh vendor-package***

- **Basic Syntax**

		$ bosh vendor-package [name] src-dir [--dir=dir]

- **Description**

	Provide packages from other releases as releases of dir, including spec.lock in the directory so that the CLI references a specific package when creating a release
	References https://bosh.io/docs/package-vendoring.html 


- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|name|Release package name|O|
	|--dir|Creating a package-related empty release configuration file for a directory|X|

- **Used Example**

		$ bosh vendor-package golang-1.8-linux ~/workspace/golang-release

### <div id='25'/>***bosh create-release***

- **Basic Syntax**

		$ bosh create-release [--force] [--version=ver] [--timestamp-version] [--final] [--tarball=path] [--dir=dir] (Alias: cr)

- **Description**

	Creates a new version of release saved at the dir

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|--force|Specify to ignore uncommitted changes in the release directory|X|
	|--version|Provides a custom release version|X|
	|--timestamp-version|Create a time stamp-based dev release version|X|
	|--tarball|Specify the target for the release tarball|X|
	|--sha2|Specify SHA256 Checksum Usage|X|

- **Used Example**

		$ bosh create-release --force

### <div id='26'/>***bosh finalize-release***

- **Basic Syntax**

		$ bosh finalize-release [path] [--force] [--version=ver] [--dir=dir]

- **Description**

	Create final release from dev release tarball

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|path|Set release tarball|O|
	|--force|Specify to ignore uncommitted changes in the release directory|X|
	|--version|Provides a custom release version|X|
	|--dir|Set directory location|X|

- **Used Example**

		$ cd release-dir
		$ bosh finalize-release /tmp/my-release.tgz
		$ bosh finalize-release /tmp/my-release.tgz --version 20
		$ git commit -am 'Final release 20'
		$ git push origin master

### <div id='27'/>***bosh reset-release***

- **Basic Syntax**

		$ bosh reset-release [--dir=dir]

- **Description**

	Removes temporary artifacts such as dev releases, blobs, etc. kept in the release directory
	
- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|--dir|Set directory location|O|


- **Used Example**

		$ bosh reset-release ~/Download/jenkins


## <div id='28'/>BOSH CLI - Release blobs

### <div id='29'/>***bosh blob*** 

- **Basic Syntax**

		$ bosh blobs

- **Description**

	Outputs registered Blob at Release Blobstore

- **Used Example**

		$ cd release-dir
		$ bosh blobs
		Path                               Size     Blobstore ID         Digest
		golang/go1.6.2.linux-amd64.tar.gz  81 MiB   f1833f76-ad8b-4b...  b8318b0...
		stress/stress-1.0.4.tar.gz         187 KiB  (local)              e1533bc...

		2 blobs

		Succeeded


### <div id='30'/>***bosh add-blob***

- **Basic Syntax**

		$ bosh add-blob [src-path] [dst-path]

- **Description**

	add local blob at release Blobstore

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|src-path|Local Blob Directory|O|
	|dst-path|Blob directory within the release|X|

- **Used Example**

		$ cd release-dir
		$ bosh add-blob ~/Downloads/stress-1.0.4.tar.gz stress/stress-1.0.4.tar.gz


### <div id='31'/>***bosh reomove-blob***

- **Basic Syntax**

		$ bosh remove-blob [blob-path]

- **Description**

	Delete a Blob that exists in the Release Blobstore

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|blob-path|blob directory within the release|O|

- **Used Example**

		$ cd release-dir
		$ bosh remove-blob stress/stress-1.0.4.tar.gz


### <div id='32'/>***bosh sync-blob***

- **Basic Syntax**

		$ bosh sync-blobs

- **Description**

	synchronize blob of blobstore within release

- **Used Example**

		$ cd release-dir
		$ bosh sync-blobs

	
## <div id='33'/>BOSH CLI - Releases

### <div id='34'/>***bosh releases***

- **Basic Syntax**

		$ bosh -e [my-env] releases (Alias: rs)

- **Description**

	View uploaded releases

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env rs
		Using environment '192.168.56.6' as client 'admin'

		Name               Version   Commit Hash
		capi               1.21.0*   716aa812
		cf-mysql           34*       e0508b5
		cf-smoke-tests     11*       a6dad6e
		cflinuxfs2-rootfs  1.52.0*   4827ef51+
		consul             155*      22515a98+
		diego              1.8.1*    0cca668e
		dns                3*        57e27da
		etcd               94*       57c81e16
		garden-runc        1.2.0*    2b3dedc5
		loggregator        78*       773a3ba
		nats               15*       d4dfc4c1+
		routing            0.145.0*  dfb44c41+
		statsd-injector    1.0.20*   552926d
		syslog             9         ac2172f
		uaa                25*       86ec7568

		(*) Currently deployed
		(+) Uncommitted changes

		15 releases

		Succeeded


### <div id='35'/>***bosh upload-release***

- **Basice Syntax**

		$ bosh -e [my-env] upload-release [location] [--version=ver] [--sha1=digest] [--fix] (Alias: ur)

- **Description**

	Release Upload

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|location|Specified release file location and URL|O|
	|--sha1|Check release file sh1um value|X|
	|--fix|Replace previously uploaded releases with the same name and version|X|

- **Used Example**

		$ bosh -e my-env ur
		$ bosh -e my-env ur https://bosh.io/d/github.com/concourse/concourse?v=2.7.3
		$ bosh -e my-env ur git+https://github.com/concourse/concourse --version 2.7.3


### <div id='36'/>***bosh delete-release*** 

- **Basic Syntax**

		$ bosh -e [my-env] delete-release [name]/[version]

- **Basic**

	Delete uploaded release

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|name|Name of the release to delete|O|
	|version|Version of the release to delete|O|

- **Used Example**

		$ bosh -e my-env delete-release cf-smoke-tests/94

### <div id='37'/>***bosh export-release***

- **Basic Syntax**

		$ bosh -e [my-env] -d my-dep export-release [name]/[version] [os]/[version] [--dir=dir]

- **Description**

	Compile and export releases for the specified stemcells

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|name|Release Name|O|
	|version|Release Version|O|
	|os|Stemcell os Name|O|
	|version|Stemcell os Version|O|
	|dir|Export Directory|X|

- **Used Example**

		$ bosh -e my-env -d my-dep export-release cf-smoke-tests/94 ubuntu-trusty/3369

### <div id='38'/>***bosh inspect-release***

- **Basic Syntax**

		$ bosh -e [my-env] inspect-release [name]/[version]

- **Description**

	All Jobs, metadata packages for the Job, and packages associated with the release version

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|name|Release Name|O|
	|version|Release Version|O|


- **Used Example**

		$ bosh -e gcp-test inspect-release consul/155
		Using environment '192.168.56.6' as client 'admin'

		Job                                                                    Blobstore ID       Digest       Links Consumed    Links Provided
		acceptance-tests/943c6083581e623dc66c7d9126d8e5989c4c2b31              0f3cd013-1d3d-...  17e5e4fc...  -                 -
		consul-test-consumer-windows/6748c0675da2292c680da03e89b738a9d5818370  7461c74c-745d-...  9809861c...  -                 -
		consul-test-consumer/7263db87ba85eaf0dd41bd198359c8597e961839          8bde4572-8e8b-...  7b08b059...  -                 -
		consul_agent/b4872109282347700eaa884dcfe93f3a03dc22dd                  e41f705e-2cb7-...  a8db2c76...  - name: consul    - name: consul
                                                                                                         		type: consul      type: consul
                                                                                                         		optional: true
		consul_agent_windows/a0b91cb0aa1029734d77fcf064dafdb67f14ada6          3a8755d0-7a39-...  17f07ec0...  - name: consul    - name: consul
                                                                                                         		type: consul      type: consul
                                                                                                         		optional: true
		fake-dns-server/a1ea5f64de0860512470ace7ce2376aa9470f9b1               5bb53f17-eba9-...  0565f9af...  -                 -

		6 jobs

		Package                                                            Compiled for          Blobstore ID            Digest
		acceptance-tests-windows/e36cef763e5cfd4e28738ad314807e6d1e13b960  (source)              03589024-2596-49fc-...  96eaaf4ba...
		acceptance-tests/9d56ac03d7410dcdfd96a8c96bbc79eb4b53c864          (source)              79fb9ba7-cd23-4b93-...  e08ee88f5...
		confab-windows/52b117effcd95138eca94c789530bcd6499cff9d            (source)              53d4b206-b064-462d-...  43f92c8d0...
		confab/b2ff0bbd68b7d600ecb1ffaf41f59af073e894fd                    (source)              b93214eb-a816-4029-...  4b627d264...
		~                                                                  ubuntu-		trusty/3363.9  f66fe541-8c21-4fe3-...  8e662c2e2...
		consul-windows/2a8e0b7ce1424d1d5efe5c7184791481a0c26424            (source)              9516870b-801e-42ea-...  19db18127...
		consul/6049d3016cd34ac64ccbf7837b06b6db81942102                    (source)              04aa38af-e883-4842-...  c42cacfc7...
		~                                                                  ubuntu-trusty/3363.9  ab4afda6-881e-46b1-...  27c1390fa...
		golang1.7-windows/1a80382e081cd429cf518f0c783f4e4172cac79e         (source)              d7670210-7038-4749-...  b91caa06a...
		golang1.7/181f7537c2ec17ac2406d9f2eb3322fd80fa2a1c                 (source)              ac8aa36a-8965-46e9-...  ca440d716...
		~                                                                  ubuntu-trusty/3363.9  9d40794f-0c50-4d0c-...  9d6e29221...
		
		11 packages

		Succeeded


## <div id='39'/>BOSH CLI - Configs

### <div id='40'/>***bosh configs***

- **Basic Syntax**

		$ bosh -e [my-env] configs [--type=my-type] [--name=my-name]

- **Description**

	Outputs all configurations of the Director

- **Parameter**
	
	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|--name|Default config name|X|
	|--type|config type ㅜname|X|

- **Used Example**

		$ bosh -e my-env configs
		Using environment '192.168.50.6' as client 'admin'

		Type     Name
		cloud    default
		~        custom-vm-types
		cpi      default
		runtime  default

		3 configs

		Succeeded


### <div id='41'/>***bosh update-config***

- **Basic Syntax**

		$ bosh -e [my-env] update-config [my-type] [config.yml] [--name=my-name]

- **Description**

	Add or update configuration in Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-typ|config type Name|O|
	|config.yml|config type of property Manifest file|O|
	|--name|Default config name|X|

- **Used Example**

		$ bosh -e my-env config my-type config.yml

### <div id='42'/>***bosh delete-config***

- **Basic Syntax**

		$ bosh -e [my-env] delete-config [my-type] [--name=my-name]

- **Description**

	Deleter  my-type component from Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-type|config type Name|O|
	|--name|Default config name|X|

- **Used Example**

		$ bosh -e my-env config my-type config.yml

## <div id='43'/>BOSH CLI - Cloud config

### <div id='44'/>***bosh  cloud-configs***

- **Basic Syntax**

		$ bosh -e [my-env] cloud-config (Alias: cc)

- **Description**

	Deployment Property Setting

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env cloud-config

###  <div id='45'/>***bosh update-cloud-config***

- **Basic Syntax**

		$ bosh -e [my-env] update-cloud-config [config.yml] [-v ...] [-o ...] (Alias: ucc)

- **Description**

	Add or modify the cloud-conifg component of the Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|config.yml|property Manifest File|O|
	|-v|Manifest Replace variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml…|X|

- **Used Example**

		$ bosh -e my-env ucc cc.yml


## <div id='46'/>BOSH CLI - Runtime config

### <div id='47'/>***bosh runtime-configs***

- **Basic Syntax**

		$ bosh -e my-env runtime-config (Alias: rc)

- **Description**

	Outputs the runtime-conifg component of the Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env runtime-config

### <div id='48'/>***bosh update-runtime-config***

- **Basic Syntax**

		$ bosh -e my-env update-runtime-config [config.yml] [-v ...] [-o ...] (Alias: urc)

- **Description**

	Add or modify the cloud-conifg component of the Director

- **Parameter**

	|**Parameter**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|config.yml|property Manifest File|O|
	|-v|Manifest Replace Variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml…|X|


- **Used Example**

		$ bosh -e my-env urc runtime.yml

## <div id='49'/>BOSH CLI - CPI config

### <div id='50'/>***bosh cpi-configs***

- **Basic Syntax**

		$ bosh -e my-env cpi-config

- **Description**

	Output the cpi-conifg component of the Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|

- **Used Example**

		$ bosh -e my-env cpi-config

### <div id='51'/>***bosh update-cpi-config***

- **Basic Syntax**

		$ bosh -e my-env update-cpi-config config.yml [-v ...] [-o ...]

- **Description**

	Add or modify the cpi-conifg component of the Director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|config.yml|property Manifest File|O|
	|-v|Manifest Replace Variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml…|X|


- **Used Example**

		$ bosh -e my-env update-cpi-config runtime.yml

## <div id='52'/>BOSH CLI – Deployments

### <div id='53'/>***bosh deployments***

- **Basic Syntax**

		$ bosh -e [my-env] deployments (Alias: ds)

- **Description**

	Outputs a complete list of deployments installed by the director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|


- **Parameter**

		$ bosh -e my-env ds
		Using environment '192.168.56.6' as client 'admin'

		Name                                Release(s)                Stemcell(s)                                         Team(s)  Cloud Config
		cf                                  binary-buildpack/1.0.9    bosh-warden-boshlite-ubuntu-trusty-go_agent/3363.9  -        latest
                                    capi/1.21.0
                                    cf-mysql/34
                                    cf-smoke-tests/11
                                    cflinuxfs2-rootfs/1.52.0
                                    consul/155
                                    diego/1.8.1
                                    etcd/94
                                    garden-runc/1.2.0
                                    loggregator/78
                                    nats/15
                                    routing/0.145.0
                                    statsd-injector/1.0.20
                                    uaa/25
		service-instance_0d4140a0-42b7-...  mysql/0.6.0               bosh-warden-boshlite-ubuntu-trusty-go_agent/3363.9  -        latest

		2 deployments

		Succeeded



### <div id='54'/>***bosh deployment***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] deployment

- **Description**

	View deployment list of names specified by the director

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specified deployment name|O|

- **Used Example**

		$ bosh -e vbox -d cf dep
		Using environment '192.168.56.6' as client 'admin'

		Name  Release(s)              Stemcell(s)                                         Team(s)  Cloud Config
		cf    binary-buildpack/1.0.9  bosh-warden-boshlite-ubuntu-trusty-go_agent/3363.9  -        latest
      	capi/1.21.0
      	cf-mysql/34
      	cf-smoke-tests/11
      	uaa/25
      	dns/3
      	...

		1 deployments

		Succeeded

### <div id='55'/>***bosh deploy***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] deploy [manifest.yml] [-v ...] [-o ...]

- **Description**

	Installing VMs through Manifest Files with Director-Specified Deployment Names

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Deployment Name|O|
	|-v|Manifest Replace Variable ex) internal_ip, deployment_name|X|
	|-o|option Manifest File ex) jumpbox-user.yml, uaa.yml…|X|
	|manifest.yml|Deployment Manifest File|O|


- **Used Example**

		$ bosh -e vbox -d cf deploy cf.yml -v system_domain=sys.example.com -o large-footprint.yml



### <div id='56'/>***bosh delete-deployment***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] delete-deployment [--force] (Alias: deld)

- **Description**

	Delete VM with deployment name specified by director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Deployment Name|O|
	|--force|Ignore various errors (IaaS, blobstore, database)|X|



- **Used Example**

		$ bosh -e vbox -d cf deld

### <div id='57'/>***bosh manifest***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] manifest (Alias: man)

- **Description**

	Outputs the Manifest file with the distribution name specified by the director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Deployment Name|O|


- **Used Example**

		$ bosh -e vbox -d cf man > /tmp/manifest.yml

### <div id='58'/>***bosh recreate***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] recreate [group[/instance-id]] [--skip-drain] [--fix] [--canaries=] [--max-in-flight=] [--dry-run]

- **Description**

	Recreate the VM for the director-specified instance of the deployment..

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specified Deployment Name|O|
	|group[/instance-id]|Group or group and instance ID|X|
	|--fix|Replacing VMs that are not responding |X|
	|--skip-drain|Skip drain scripts.|X|
	|--canaries=|Specify deployment name|X|
	|--max-in-flight=|Overwrite the max-in-flight value of Manifest.|X|
	|--dry-run|Execute the task without changing the deployment.|X|


- **Used Example**

		$ bosh -e vbox -d cf recreate
		$ bosh -e vbox -d cf recreate --fix
		$ bosh -e vbox -d cf recreate diego-cell
		$ bosh -e vbox -d cf recreate diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0
		$ bosh -e vbox -d cf recreate diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0 --skip-drain
		$ bosh -e vbox -d cf recreate diego-cell --canaries=0 --max-in-flight=100%



### <div id='59'/>***bosh restart***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] restart [group[/instance-id]] [--skip-drain] [--canaries=] [--max-in-flight=]

- **Description**

	Restart the instance related VM specified by director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID|X|
	|--skip-drain|Skip drain scripts.|X|
	|--canaries=|specify deployment name|X|
	|--max-in-flight=|Overwrite the max-in-flight value of Manifest.|X|


- **Used Example*

		$ bosh -e my-env -d my-dep restart

### <div id='60'/>***bosh start***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] start [group[/instance-id]] [--canaries=] [--max-in-flight=]

- **Description**

	Starts the VM for the instance of the deployment specified by the director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID|X|
	|--canaries=|specify deployment name|X|
	|--max-in-flight=|Overwrite the max-in-flight value of Manifest.|X|



- **Used Example**

		$ bosh -e my-env -d my-dep start


### <div id='61'/>***bosh stop***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] stop [group[/instance-id]] [--skip-drain] [--canaries=] [--max-in-flight=]

- **Description**

	Starts the VM for the instance of the deployment specified by the director.

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID|X|
	|--canaries=|specify deployment name|X|
	|--max-in-flight=|Overwrite the max-in-flight value of Manifest.|X|
	|--skip-drain|Skip drain scripts.|X|
	|hard|Force delete VM, keep permanent disk|X|



- **Used Example**

		$ bosh -e my-env -d my-dep stop


### <div id='62'/>***bosh ignore***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] ignore group/instance-id

- **Description**

	Ignore instances to avoid being affected by other commands, such as bosh deploy

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID
|X|


- **Used Example**

		$ bosh -e my-env -d my-dep ignore cell

### <div id='63'/>***bosh unignore***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] unignore group/instance-id

- **Description**

	DO NOT ignore instances so that they are not affected by other commands such as bosh deploy

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID|X|


- **Used Example**

		$ bosh -e my-env -d my-dep unignore cell

### <div id='64'/>***bosh logs***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] logs [group[/instance-id]] [--follow] [--num] [--gw-*] [--quiet]

- **Description**

	Download logs from one or more instances

- **Parameter**

	|**Paremeter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specify deployment name|O|
	|group[/instance-id]|Group or group and instance ID|X|
	|--dir|Specify Log Directory|X|
	|--job|Log settings for a specific Job|X|
	|--only|Log Filtering|X|
	|--agent|include bosh agent log only|X|
	|--follow|Execute Additional flags for following logs via SSH Log|X|
	|--num|Output Additional flags for following logs via SSH last row number|X|
	|--gw|Constructs Additional flags for following logs via SSH ssh gateway|X|
	|--quiet|Suppress Additional flags for following logs via SSH header outputs|X|



- **Used Example**

		$ bosh -e vbox -d cf logs diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0
		$ bosh -e vbox -d cf logs diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0 --job=rep --job=silkd
		$ bosh -e vbox -d cf logs -f
		$ bosh -e vbox -d cf logs -f --num=1000



## <div id='65'/>BOSH CLI - VMs

### <div id='66'/>***bosh vms***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] vms [--vitals]

- **Description**

	View all vm's managed by the director or deployments

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specify deployment name|O|
	|--vitals|View VM's default informations such as RAM CPU disk|X|

- **Used Example**

		$ bosh -e vbox vms
		$ bosh -e vbox -d cf vms
		$ bosh -e vbox -d cf vms --vitals

### <div id='67'/>***bosh delete-vms*** 

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] delete-vm [cid]


- **Description**

	Delete VM without goinf through Lifecycle of Instance

- **Parameter**

	|**Parameter Name**|**Description**|**Necessity****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|Specified Director Environment Name|O|
	|my-dep|Specify deployment name|O|
	|cid|Specify Instance ID|X|

- **Used Example**

		$ bosh -e vbox -d cf delete-vm i-fs384238fjwjf8


## <div id='68'/>BOSH CLI - Disks

### <div id='69'/>***bosh disks***

- **Basic Syntax**

		$ bosh -e [my-env] -d [my-dep] disks [--orphaned]

- **Description**

	View all disks of deployment or Director managed disks

- **Parameter**

	|**Parameter Name**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|--orphaned|사용하지 않는 DISK를 나열|X|

- **사용 예시**

		$ bosh -e vnps -d cf disks  

### <div id='70'/>***bosh attach-disk***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] attach-disk [group/instance-id] disk-cid

- **설명**

	인스턴스에 disk attach. 만약 attach된 disk 가 있다면 가장 최근 attach 된 disk를 replace한다.

- **파라미터**

	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|--orphaned|사용하지 않는 DISK를 나열|X|
	|group/instance-id|그룹 또는 그룹과 인스턴스 아이디|X|

- **사용 예시**

		$ bosh -e vbox -d cf attach-disk postgres/209c42e5-3c1a-432a-8445-ab8d7c9f69b0 vol-shw8f293f2f2

### <div id='71'/>***bosh delete-disk***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] delete-disk [cid]

- **설명**

	사용하지 않는 disk 삭제

- **파라미터**

	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|--cid|삭제 할 DISK 아이디 지정|X|

- **사용 예시**

		$ bosh -e vbox -d cf delete-disk vol-shw8f293f2f2


## <div id='72'/>BOSH CLI - SSH

### <div id='73'/>***bosh ssh***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] ssh [destination] [-r] [-c=cmd] [--opts=opts] [--gw-* ...]

- **설명**

	인스턴스 한개 또는 여러 개에 SSH설정

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|-c|커맨드 라인 설정|X|
	|destination|SSH 목적지 지정 그룹 또는 그룹과 인스턴스 아이디|X|
	|--opts|ssh에 옵션을 전달 ex) 포트 포워딩|X|
	|--gw-*|SSH 게이트웨이를 구성|X|
	|-r, --recursive|directory의 반복 복사 허용|X|

- **사용 예시**

		# execute command on all instances in a deployment
		$ bosh -e vbox -d cf ssh -c 'uptime'

		# execute command on one instance group
		$ bosh -e vbox -d cf ssh diego-cell -c 'uptime'

		# execute command on a single instance
		$ bosh -e vbox -d cf ssh diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0 -c 'uptime'

		# execute command with passwordless sudo
		$ bosh -e vbox -d cf ssh diego-cell -c 'sudo lsof -i|grep LISTEN'

		# present output in a table by instance
		$ bosh -e vbox -d cf ssh -c 'uptime' -r

		# port forward UAA port locally
		$ bosh -e vbox -d cf ssh uaa/0 --opts ' -L 8080:localhost:8080'


### <div id='74'/>***bosh scp***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] scp src/dst:[file] src/dst:[file] [-r] [--gw-* ...]

- **설명**

	인스턴스로 또는 인스턴스로 부터 SCP  (to/from)설정

- **파라미터**

	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|src/dst:<file>|복사 받을 script.sh file 경로|O|
	|src/dst:<file>|복사 될 script.sh file 경로|O|
	|-r, --recursive|directory의 반복 복사 허용|X|
	|--gw-*|SCP gateway설정|X|

- **사용 예시**

		# copy file from this machine to machines a deployment
		$ bosh -e vbox -d cf scp ~/Downloads/script.sh :/tmp/script.sh
		$ bosh -e vbox -d cf scp ~/Downloads/script.sh diego-cell:/tmp/script.sh
		$ bosh -e vbox -d cf scp ~/Downloads/script.sh diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0:/tmp/script.sh
		$ bosh -e vbox -d cf scp ~/Downloads/script.ps1 windows_diego_cell:c:/temp/script/script.ps1

		# copy file from remote machines in a deployment to this machine
		$ bosh -e vbox -d cf scp :/tmp/script.sh ~/Downloads/script.sh
		$ bosh -e vbox -d cf scp diego-cell:/tmp/script.sh ~/Downloads/script.sh
		$ bosh -e vbox -d cf scp diego-cell/209c42e5-3c1a-432a-8445-ab8d7c9f69b0:/tmp/script.sh ~/Downloads/script.sh
		$ bosh -e vbox -d cf scp windows_diego_cell:c:/temp/script/script.ps1:~/Downloads/script.ps1

		# copy files from each instance into instance specific local directory
		$ bosh -e vbox -d cf scp diego-cell:/tmp/logs/ /tmp/logs/((instance_id))

## <div id='75'/>BOSH CLI - Errands

### <div id='76'/>***bosh errands***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] errands (Alias:es)

- **설명**

	deployment로 정의 된 모든 errand 목록 조회

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|


- **사용 예시**

		$ bosh -e vbox -d cf es
		Using environment '192.168.56.6' as '?'

		Using deployment 'cf'

		Name
		smoke-tests

		1 errands

		Succeeded


### <div id='77'/>***bosh run-errand***

- **기본 Syntax**

		$ bosh -e <my-env> -d <my-dep> run-errand <name> [--keep-alive] [--when-changed] [--download-logs] [--logs-dir=<dir>] [--instance=<instance-group/instance-id>]

- **설명**

	errand job 을 name 단위로 실행

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|name|실행 할 errand 이름|O|
	|--keep-alive|errand가 실행 되는 곳에서 VM 유지|X|
	|--when-changed|errand가 skip 설정: 이전에 이미 실행하였고 (성공적으로 마침) errand job 설정값이 바뀌지 않았을 경우|X|
	|--download-logs|errand log를 통채로 --logs-dir에 명시된 경로에 저장|X|
	|--logs-dir=<dir>|errand log를 저장 할 파일 경로|X|
	|instance=<instance-group/instance-id> (v2.0.31+)|errand를 실행하기위해 어떤 인스턴스를 사용할지 결정|X|


- **사용 예시**

		$ bosh -e vbox -d cf run-errand smoke-tests
		$ bosh -e vbox -d cf run-errand smoke-tests --keep-alive
		$ bosh -e vbox -d cf run-errand smoke-tests --when-changed

		# execute errand on all instances that have colocated status errand
		$ bosh -e vbox -d zookeeper run-errand status

		# execute errand on one instance
		$ bosh -e vbox -d zookeeper run-errand status --instance zookeeper/3e977542-d53e-4630-bc40-72011f853cb5

		# execute errand on one instance within an instance group
		# (note that select instance may not necessarily be first based on its index)
		$ bosh -e vbox -d zookeeper run-errand status --instance zookeeper/first

		# execute errand on all instance in an instance group
		$ bosh -e vbox -d zookeeper run-errand status --instance zookeeper

		# execute errand on two instances
		$ bosh -e vbox -d zookeeper run-errand status \
  			--instance zookeeper/671d5b1d-0310-4735-8f58-182fdad0e8bc \
  			--instance zookeeper/3e977542-d53e-4630-bc40-72011f853cb5




## <div id='78'/>BOSH CLI - Tasks

### <div id='79'/>***bosh tasks***

- **기본 Syntax**

		$ bosh -e [my-env] tasks [--recent[=num]] [--all] (Alias: ts)

- **설명**

	활성 및 이전에 실행 한 작업에 대한 task를 출력

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|--recent|최근 순 4개 task 조회|X|
	|num|최근 순 조회하고 싶은 task 숫자|X|
	|--all|active tasks 모두 조회|X|
	|-d, -deployment  |deployment 단위로 필터링 해서 조회|X|


- **사용 예시**

		# currently active tasks
		$ bosh -e vbox ts

		# currently active tasks for my-dep deployment
		$ bosh -e vbox -d my-dep ts
		Using environment '192.168.56.6' as '?'

		#   State  Started At                    Last Activity At              User   Deployment   Description                   Result

		27  done   Thu Feb 16 19:16:15 UTC 2017  Thu Feb 16 19:20:33 UTC 2017  admin  cockroachdb  create deployment             /deployments/cockroachdb
		26  done   Thu Feb 16 18:54:32 UTC 2017  Thu Feb 16 18:55:27 UTC 2017  admin  cockroachdb  delete deployment cockroachd  /deployments/cockroachdb
		...

		110 tasks

		Succeeded

		# show last 30 tasks
		$ bosh -e vbox ts -r --all

		# show last 1000 tasks
		$ bosh -e vbox ts -r=1000


### <div id='80'/>***bosh task***

- **기본 Syntax**

		$ bosh -e [my-env] task [id] [--debug] [--result] [--event] [--cpi] (Alias: t)

- **설명**

	task 아이디를 기준으로 상세 조회. 

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|id|task 아이디|O|
	|--debug|Debug 로그 출력|X|
	|--result|Result 로그 출력|X|
	|--event|Event 로그 출력|X|
	|--cpi|CPI 로그 출력|X|


- **사용 예시**

		$ bosh -e vbox t 281
		$ bosh -e vbox t 281 --debug

### <div id='81'/>***bosh cancle-task***

- **기본 Syntax**

		$ bosh -e [my-env] cancel-task [id] (Alias: ct)

- **설명**

	task 취소. 다음 checkpoint 에서 task를 취소 한다. task가 취소될 때까지 대기하지 않는다

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|id|task 아이디|O|


## <div id='82'/>BOSH CLI - Snapshot

### <div id='83'/>***bosh snapshots***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] snapshots

- **설명**

	deployment의 스냅샷 목록을 출력 한다.

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|



- **사용 예시**

		$ bosh -e my-env -d my-dep snapshots


### <div id='84'/>***bosh take-snapshot***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] take-snapshot [group/instance-id]

- **설명**

	특정 인스턴스 또는 deployment에 대한 스냅샷을 생성 한다.

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|group/instance-id|그룹 또는 그룹과 인스턴스 아이디|X|


- **사용 예시**

		$ bosh -e my-env -d my-dep take-snapshot cell


### <div id='85'/>***bosh delete-snapshot***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] delete-snapshot [cid]

- **설명**

	특정 스냅샷을 삭제 한다.

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|cid|스냅샷 아이디 지정|O|


- **사용 예시**

		$ bosh -e my-env -d my-dep delete-snapshot 1acsda-ccas


### <div id='86'/>***bosh delete-snapshots***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] delete-snapshots

- **설명**

	스냅샷을 전체 삭제 한다.

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|



- **사용 예시**

		$ bosh -e my-env -d my-dep delete-snapshots


## <div id='87'/>BOSH CLI - Deployment recovery

### <div id='88'/>***bosh update-resurrection***

- **기본 Syntax**

		$ bosh -e [my-env] update-resurrection [on/off]

- **설명**

	디렉터가 지정한 환경에 대해 recovery를 활성/비활성화

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|on/off|활성/비활성화|O|



- **사용 예시**

		$ bosh -e my-env update-resurrection on


### <div id='89'/>***bosh cloud-check***

- **기본 Syntax**

		$ bosh -e [my-env] -d [my-dep] cloud-check [--report] [--auto] (Alias: cck)

- **설명**

	리소스에 대해 일관적인 검사를 하고 대화 형 복구를 허용 한다.

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|my-dep|배포 명 지정|O|
	|--report|Report 생성|X|
	|--auto|자동으로 Problem해결|X|



- **사용 예시**

		$ bosh -e vbox -d cf cloud-check --report --auto


### <div id='90'/>***bosh locks***

- **기본 Syntax**

		$ bosh -e [my-env] locks

- **설명**

	최근 lock 목록 조회

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|




- **사용 예시**

		$ bosh -e my-env locks


## <div id='91'/>BOSH CLI - Misc

### <div id='92'/>***bosh clean-up***

- **기본 Syntax**

		$ bosh -e [my-env] clean-up [--all]

- **설명**

	releases, stemcells, orphaned disks 그리고 사용되지 않는 다른 리소스를 clean up 한다

- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|my-env|지정 한 Director 환경 이름 명칭|O|
	|--all|orphaned disks에 강제 clean up적용|X|



- **사용 예시**

		$ bosh -e my-env clean-up --all


### <div id='93'/>***bosh help***

- **기본 Syntax**

		$ bosh help

- **설명**

	사용 가능한 모든 명령어와 global option 목록 조회 각각의 command에 대해서는 -h 사용


- **파라미터**
	
		없음



- **사용 예시**

		$ bosh help

		$ bosh upload-release -h


### <div id='94'/>***bosh interpolate***

- **기본 Syntax**

		$ bosh interpolate manifest.yml [-v ...] [-o ...] [--vars-store path] [--path op-path] (Alias: int)

- **설명**

	결과 값 이 sudout로 넘겨지는 Manifest.yml에 추가적으로 merge할 yml 파일이나 설정 값을 입력


- **파라미터**
	
	|**파라미터 명**|**설명**|**필수****(O/X)**|
	|----------|-------------------------|--------------------------------|
	|-v|수정/입력 하는 variable list|X|
	|-o|수정/입력 하는 operation file list|X|
	|--vars-store path|디렉터 접근 아이디 Key 및 각 JOB 패스워드 등이 존재하는 설정 파일 생성 위치|X|
	|--path op-path|Manifest의 해당 값 출력|X|


- **사용 예시**

		$ bosh int bosh-deployment/bosh.yml \
  			--vars-store ./creds.yml \
  			-o bosh-deployment/virtualbox/cpi.yml \
  			-o bosh-deployment/virtualbox/outbound-network.yml \
  			-o bosh-deployment/bosh-lite.yml \
  			-o bosh-deployment/jumpbox-user.yml \
  			-v director_name=vbox \
  			-v internal_ip=192.168.56.6 \
  			-v internal_gw=192.168.56.1 \
  			-v internal_cidr=192.168.56.0/24 \
  			-v network_name=vboxnet0 \
  			-v outbound_network_name=NatNetwork

		$ bosh int creds.yml --path /admin_password
		skh32i7rdfji4387hg

		$ bosh int creds.yml --path /director_ssl/ca
		-----BEGIN CERTIFICATE-----

  
  ### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > BOSH CLI
