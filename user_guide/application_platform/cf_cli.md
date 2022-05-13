### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > cf CLI

## Table of Contents
1. [Outline](#개요)
     * [Document Purpose](#문서-목적)
     * [Range](#범위)
     * [References](#참고-자료)

1. [Basic OpenPaaS CLI Usage](#ID-OpenPaaS-CLI-USAGE)

1. [GETTING STARTED](#ID-GETTING-STARTED)
     * [login](#login)
     * [logout](#logout)
     * [passwd](#passwd)
     * [target](#target)
     * [api](#api)
     * [auth](#auth)

1. [APPS](#ID-APPS)
     * [apps](#apps)
     * [app](#app)
     * [push, p](#push-p)
     * [scale](#scale)
     * [delete](#delete)
     * [rename](#rename)
     * [start, st](#start-st)
     * [stop, sp](#stop-sp)
     * [restart, rs](#restart-rs)
     * [restage, rg](#restage-rg)
     * [restart-app-instance](#restart-app-instance)
     * [events](#events)
     * [files](#files)
     * [logs](#logs)
     * [env, e](#env-e)
     * [set-env, se](#set-env-se)
     * [unset-env](#unset-env)
     * [stacks](#stacks)
     * [stack](#stack)
     * [copy-source](#copy-source)
     * [create-app-manifest](#create-app-manifest)

1. [SERVICES](#ID-SERVICES)
    * [marketplace, m](#marketplace-m)
    * [services, s](#services-s)
    * [service](#service)
    * [create-service](#create-service)
    * [update-service](#update-service)
    * [delete-service](#delete-service)
    * [rename-service](#rename-service)
    * [create-service-key, csk](#create-service-key-csk)
    * [service-keys, sk](#service-keys-sk)
    * [service-key](#service-key)
    * [delete-service-key, dsk](#delete-service-key-dsk)
    * [bind-service, bs](#bind-service-bs)
    * [unbind-service,us](#unbind-service-us)
    * [create-user-provided-service, cups](#create-user-provided-service-cups)
    * [update-user-provided-service, cups](#update-user-provided-service-uups)

1. [ORGS](#ID-ORGS)
    * [orgs, o](#orgs-o)
    * [org](#org)
    * [create-org](#create-org-co)
    * [delete-org](#delete-org)
    * [rename-org](#rename-org)

1. [SPACES](#ID-SPACES)
    * [spaces](#spaces)
    * [space](#space)
    * [create-space](#create-space)
    * [delete-space](#delete-space)
    * [rename-space](#rename-space)

1. [DOMAINS](#ID-DOMAINS)
    * [domains](#domains)
    * [create-domain](#create-domain)
    * [delete-domain](#delete-domain)
    * [create-shared-dommain](#create-shared-domain)
    * [delete-shared-dommain](#delete-shared-domain)

1. [ROUTES](#ID-ROUTES)
    * [routes, r](#routes-r)
    * [create-route](#create-route)
    * [update-route](#update-route)
    * [check-route](#check-route)
    * [map-route](#map-route)

1. [BUILDPACKS](#ID-BUILDPACKS)
    * [buildpacks](#buildpacks)
    * [create-buildpack](#create-buildpack)
    * [update-buildpack](#update-buildpack)
    * [rename-buildpack](#rename-buildpack)
    * [delete-buildpack](#delete-buildpack)

1. [USER ADMIN](#ID-USER-ADMIN)
    * [create-user](#create-user)
    * [delete-user](#delete-user)
    * [org-users](#org-users)
    * [set-org-role](#set-org-role)
    * [unset-org-role](#unset-org-role)
    * [space-user](#space-user)
    * [set-space-role](#set-space-role)
    * [unset-space-role](#unset-space-role)

1. [ORG ADMIN](#ID-ORG-ADMIN)
    * [quotas](#quotas)
    * [quota](#quota)
    * [set-quota](#set-quota)
    * [create-quota](#create-quota)
    * [delete-quota](#delete-quota)
    * [update-quota](#update-quota)
    * [shared-private-domain](#shared-private-domain)
    * [unshared-private-domain](#unshared-private-domain)

1. [SPACE ADMIN](#ID-SPACE-ADMIN)
    * [space-quotas](#space-quotas)
    * [space-quota](#space-quota)
    * [create-space-quota](#create-space-quota)
    * [update-space-quota](#update-space-quota)
    * [delete-space-quota](#delete-space-quota)
    * [set-space-quota](#set-space-quota)
    * [unset-space-quota](#unset-space-quota)

1. [SERVICE ADMIN](#ID-SERVICE-ADMIN)
    * [service-auth-tokens](#service-auth-tokens)
    * [create-service-auth-token](#create-service-auth-token)
    * [update-service-auth-token](#update-service-auth-token)
    * [delete-service-auth-token](#delete-service-auth-token)
    * [service-brokers](#service-brokers)
    * [create-service-broker](#create-service-broker)
    * [create-service-broker](#create-service-broker)
    * [update-service-broker](#update-service-broker)
    * [delete-service-broker](#delete-service-broker)
    * [rename-service-broker](#rename-service-broker)
    * [migrate-service-broker](#migrate-service-broker)
    * [purge-service-offering](#purge-service-offering)
    * [service-access](#service-access)
    * [enable-service-access](#enable-service-access)
    * [disable-service-access](#disable-service-access)

1. [SECURITY GROUP](#ID-SECURITY-GROUP)
    * [security-group](#security-group)
    * [security-groups](#security-groups)
    * [create-security-group](#create-security-group)
    * [update-security-group](#update-security-group)
    * [delete-security-group](#delete-security-group)
    * [bind-security-group](#bind-security-group)
    * [unbind-security-group](#unbind-security-group)
    * [bind-staging-security-group](#bind-staging-security-group)
    * [staging-security-groups](#staging-security-groups)
    * [unbind-staging-security-group](#unbind-staging-security-group)
    * [running-security-group](#running-security-group)

1. [ENVIRONMENT VARIABLE GROUPS](#ID-ENVIRONMENT-VARIABLE-GROUPS)
    * [running-environment-variable-group, revg](#running-environment-variable-group-revg)
    * [staging-environment-variable-group, sevg](#staging-environment-variable-group-sevg)
    * [set-staging-environment-variable-group, ssevg](#set-staging-environment-variable-group-ssevg)
    * [set-running-environment-variable-group, ssevg](#set-running-environment-variable-group-ssevg)

1. [FEATURE FLAGS](#ID-FEATURE-FLAGS)
    * [feature-flags](#feature-flags)
    * [feature-flag](#feature-flag)
    * [enable-feature-flag](#enable-feature-flag)
    * [disable-feature-flag](#disable-feature-flag)

1. [ADVANCE](#ID-ADVANCE)
    * [curl](#curl)
    * [config](#config)
    * [oauth-token](#oauth-token)

1. [ADD/REMOVE PLUGIN REPOSITORY](#ID-ADD-REMOVE-PLUGIN-REPOSITORY)
    * [add-plugin-repo](#add-plugin-repo)
    * [remove-plugin-repo](#remove-plugin-repo)
    * [list-plugin-repos](#list-plugin-repos)
    * [repo-plugins](#repo-plugins)

1. [ADD/REMOVE PLUGIN](#ID-ADD-REMOVE-PLUGIN)
    * [plugins](#plugins)
    * [install-plugin](#install-plugin)

## Outline
---

#### Document Purpose

The purpose of this document is to understand OpenPaaS through basic usage and use examples for OpenPaaS CLI, a tool for installing and managing operations for OpenPaaS.

#### Range

This document is written about OpenPaaS CLI classification and basic usage.

#### References

 This document is based on the CF document of Cloud Foundry.

 [***https://docs.cloudfoundry.org/devguide/installcf/***](https://docs.cloudfoundry.org/devguide/installcf/)

## <div id='ID-OpenPaaS-CLI-USAGE'/> Basic OpenPaaS CLI Usage

OpenPaaS CLI : CLI tool to manage OpenPaaS.

CLI is a command-line utility that helps you manage OpenPaaS deployments and releases, and is used as follows.



 - **Basic Syntax**


 ```
cf [global options] command <arguments...> [command options]
 ```

Provides abbreviations according to the OpenPaaS command. As an example, the App start CLI command is start, but st can be used too..

- **Abbreviation used example**

```
$ cf start

$ cf st
```

[command options], a square-braced factor in OpenPaaS commands, is optionally used according to the command, and the command '<arguments>' factor is a required factor. OpenPaaS CLI, a tool for operating and managing OpenPaaS, provides the following commands.


## <div id='ID-GETTING-STARTED'/> GETTING STARTED


#### login


- **Basic Syntax**


```
$ cf login [-a API_URL] [-u USERNAME] [-p PASSWORD] [-o ORG] [-s SPACE]
```


- **Description**


```
Command used to log in to OpenPaaS
```


- **Parameter**


| Parameter Name   |           Description                 | Necessity(O/X) |
|-------------|-----------------------------|-----------|
|-a API_URL    |OpenPaaS  URL CLI uses to access<br>Ex) https://api.10.244.0.34.xip.io    |X        |
|-u USERNAMEL  |ID of the user accessing OpenPaaS               |X        |
|-p PASSWORD   |Password of the user accessing OpenPaaS          |X        |
|-o ORG        |Organization name of the user accessing OpenPaaS      |X        |
|-s SPACE      |Space name of the user accessing OpenPaaS      |X        |


- **Used Example**


```
# When parameter is specified
$ cf login --skip-ssl-validation -a https://api.10.244.0.34.xip.io -u admin -p admin -o crossent -s development

# When parameter is not specified
$ cf login
API endpoint: https://api.10.244.0.34.xip.io

Email> admin

Password>
Authenticating...
OK

Targeted org crossent

Select a space (or press enter to skip):
1. development
2. staged
3. oper

Space> 1
Targeted space development

API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.29.0)   
User:           admin   
Org:            crossent   
Space:          development

```

#### logout
- **Basic Syntax**


```
$ cf logout
```


- **Description**


```
logout cf.
```


- **Parameter**

  -None


- **Used Example**

```
$ cf logout

```

#### passwd
- **Basic Syntax**


```
$ cf passwd
```



- **Description**


```
Change users password in OpenPaaS.
```


- **Parameter**

  -None


- **Used Example**


```
$ cf passwd
Current Password>

New Password>

Verify Password>
Changing password...
OK
Please log in again  
```

#### target
- **Basic Syntax**


```
$ cf target [-o ORG] [-s SPACE]
```

- **Description**


```
Set the target organization and space to be used by the logged-in user.
```


- **Parameter**

| Parameter Name   |           Description                 | Necessity(O/X) |
|-------------|-----------------------------|-----------|
|-o ORG      |Target Organization                    |X        |
|-s SPACE    |Target Space                |X        |



- **Used Example**

```
# When parameter is specified
$ cf target -o cf -s development
API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.29.0)   
User:           admin   
Org:            cf   
Space:          development
# When parameter is not specified(Outputs currently targeted information)
$ cf target
API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.29.0)   
User:           admin   
Org:            cf
Space:          development  
```


#### api
- **Basic Syntax**

```
$ cf api <URL>
```


- **Description**


```
View Target api or Sets target api URL.
```



- **Parameter**

| Parameter Name   |           Description                 | Necessity(O/X) |
|-------------|-----------------------------|-----------|
|URL         |Api Target URL                   |O        |


- **Used Example**

```
$ cf api --skip-ssl-validation api.10.244.0.34.xip.io
```

#### auth
- **Basic Syntax**


```
$ cf auth <USERNAME> <PASSWORD>
```


- **Description**


```
OpenPaaS login only logs in, no spaces and targets are specified.
```


- **Parameter**

| Parameter Name   |           Description                 | Necessity(O/X) |
|-------------|--------------------------------|-----------|
|USERNAME     |Login user ID                 |O        |
|PASSWORD    |Lodin user PASSWORD            |O        |



- **Used Example**

```
$ cf api --skip-ssl-validation api.10.244.0.34.xip.io
```
## <div id='ID-APPS'/> APPS


#### apps
- **Basic Syntax**


```
$cf apps
```


- **Description**


```
View list of Apps in the target space.
```


- **Parameter**

  -None

  - **Used Example**

  ```
  $ cf apps
  ```

#### app

  - **Basic Syntax**


  ```
  $cf app <APP_NAME>
  ```


  - **Description**


  ```
  Checks the status of App.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP name                          |O        |


    - **Used Example**

    ```
    $ cf app spring-music
    ```
    
#### <div id='push-p'/> push,p

  - **Basic Syntax**


  ```
  $ cf push <APP_NAME> [-b BUILDPACK_NAME] [-c COMMAND] [-d DOMAIN] [-f MANIFEST_PATH] [-i NUM_INSTANCES] [-k DISK] [-m MEMORY] [-n HOST] [-p PATH] [-s STACK] [-t TIMEOUT] [--no-hostname] [--no-manifest] [--no-route] [--no-start]
  ```


  - **Description**


  ```
  Deploy the app to OpenPaaS and start the app.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |Name of th app to be pushed(directory name if not specified)                         |O        |
  |-b BUILDPACK |custom buildpack URL  |X        |
  |-c COMMAND   |App start command              |X        |
  |-d DOMAIN    |App Domain                      |X        |
  |-f MANIFEST_PATH    |Manifest file path       |X        |
  |-i NUM_INSTANCES     |Number of app instances        |X        |
  |-m MEMORY     |Instance memory capacity             |X        |
  |-k DISK     |Disk Usage Capacity                  |X        |
  |-n HOST     |Host name <br> ex) my-subdomain)  |X        |
  |-p PATH     |The app's directory path or App file (zip, war, etc.) path   |X        |
  |-s STACK    |The operating system file system on which the app runs(default: cflinuxfs2)       |X        |
  |-t TIMEOUT  |Timeout time the CLI waits while the app is running                          |X        |
  |--no-hostname     |Map root domain to App                          |X        |
  |--no-manifest     |Ignore Manifest File.                         |X        |
  |--no-route     |Delete route information to a pushed app and do not map route information to an app   |X        |
  |--no-start     |Push App but do not Start                       |X        |
  |--random-route    |Create random route infromation to App                |X        |

  - **Used Example**

  ```
  $ cf push spring-music
  ```

#### scale

  - **Basic Syntax**


  ```
  $ cf scale <APP_NAME> [-i INSTANCES] [-k DISK] [-m MEMORY] [-f]
  ```


  - **Description**


  ```
  Adjust the app's memory, disk size, and number of instances.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |-i INSTANCES |Number of Instances                    |X        |
  |-k DISK      |Disk Memory                      |X        |
  |-m MEMORY    |Memoey Capacity                      |X        |
  |-f           |Force App restart                |X        |


  - **Used Example**

  ```
  $ cf scale spring-music -i 2 -m 512m
  ```


#### delete

  - **Basic Syntax**


  ```
  $ cf delete <APP_NAME> [--f] [--r]
  ```


  - **Description**


  ```
  Delete App.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |--f          |Delete App without confirmation               |X        |
  |--r          |Delete the route information mapped to the App     |X        |


  - **Used Example**

  ```
  $  cf delete spring-music
  ```


#### rename

  - **Basic Syntax**


  ```
  $ cf rename <APP_NAME> <NEW_APP_NAME>
  ```


  - **Description**


  ```
  Change App Name.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |NEW_APP_NAME |Name of the App to change                 |O        |


  - **Used Example**

  ```
  $  cf rename spring-music new-spring-music
  ```

#### <div id='start-st'/> start,st

  - **Basic Syntax**


  ```
  $ cf start <APP_NAME>
  ```


  - **Description**


  ```
  Start the App.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |


  - **Used Example**

  ```
  $  cf start spring-music
  ```
#### <div id='stop-sp'/> stop,sp

  - **Basic Syntax**


  ```
  $ cf stop <APP_NAME>
  ```


  - **Description**


  ```
  Stop the App.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |


  - **Used Example**

  ```
  $  cf stop spring-music
  ```

#### <div id='restart-rs'/> restart, rs

  - **Basic Syntax**


  ```
  $ cf restart <APP_NAME>
  ```


  - **Description**


  ```
  Restart the App.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |


  - **Used Example**

  ```
  $cf restart spring-music
  ```

####  <div id='restage-rg'/> restage, rg

  - **Basic Syntax**


  ```
  $ cf restage <APP_NAME>
  ```


  - **Description**


  ```
  Restage the app (used when setting environment variables or binding services)
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |


  - **Used Example**

  ```
  $cf restage spring-music
  ```

#### restart-app-instance

  - **Basic Syntax**


  ```
  $ cf restart-app-instance <APP_NAME> <INDEX>
  ```


  - **Description**


  ```
  Restart a specific instance of the app.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |INDEX        |Instance Index                   |O        |

  - **Used Example**

  ```
  $cf restart-app-instance spring-music 1
  ```

#### events

  - **Basic Syntax**


  ```
      $ cf events <APP_NAME>
  ```


  - **Description**


  ```
    View recent event information that occurred in the app. (History of start/stop/scale, etc.)
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |

  - **Used Example**

  ```
  $ cf events spring-music
  ```


#### files

  - **Basic Syntax**


  ```
  $ cf files <APP_NAME> [PATH] [-i INSTANCE]
  ```


  - **Description**


  ```
  View the file and directory list of the app.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |PATH         |Directory of the APP                   |X        |
  |-i INSTANCE  |App Instance Index               |X        |

  - **Used Example**

  ```
  $  cf files spring-music
  ```

#### logs

  - **Basic Syntax**


  ```
  $ cf logs <APP_NAME> [--recent]
  ```


  - **Description**


  ```
  View the log that occurred in the app.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Description(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |PATH         |Directory of the APP                   |X        |
  |-i INSTANCE  |App Instance Index               |X        |

  - **Used Example**

  ```
  $  cf logs spring-music
  ```
#### <div id='env-e'/> env,e

  - **Basic Syntax**


  ```
  $ cf env  <APP_NAME>
  ```


  - **Description**


  ```
 Inquire environment variables of the app.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |

  - **Used Example**

  ```
  $ cf env spring-music
  ```

#### <div id='set-env-se'/> set-env,se

  - **Basic Syntax**


  ```
  $ cf set-env <APP_NAME> <ENV_VAR_NAME> <ENV_VAR_VALUE>
  ```


  - **Description**


  ```
  Set environment variables for the app. (Requires restage when applied)
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |ENV_VAR_NAME |App's environment variable Key               |O        |
  |ENV_VAR_VALUE|App's environment variable Value               |O        |


  - **Used Example**

  ```
  $ cf se spring-music author Jim
  ```



#### unset-env

  - **Basic Syntax**


  ```
  $ cf unset-env <APP_NAME> <ENV_VAR_NAME>
  ```


  - **Description**


  ```
  Delete the environment variable set in the app (restage required when applied)
  ```


  - **Parameter**


  | Parameter Name   |           Descriprion                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |ENV_VAR_NAME |App's variable environment Key               |O        |


  - **Used Example**

  ```
  $ cf unset-env spring-music author
  ```

#### stacks

  - **Basic Syntax**


  ```
  $ cf stacks
  ```


  - **Description**


  ```
  Lsit the stack list (operating system file system) list of OpenPaaS.
  ```


  - **Parameter**


    - None


  - **Used Example**

  ```
  $  cf stacks
  ```

#### stack

  - **Basic Syntax**


  ```
  $ cf stack <STACK_NAME> [--guid]
  ```


  - **Description**


  ```
  View the stack list (operating system file system) list of OpenPaaS.
  ```


  - **Parameter**


  | Parameter   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP Name                           |O        |
  |--guid       |Inquire Stack guid            |X        |


  - **Used Example**

  ```
  $  cf stack cflinuxfs2
  ```

#### copy-source

  - **Basic Syntax**


  ```
  $ cf copy-source <SOURCE-APP> <TARGET-APP> [-o TARGET-ORG] [-s TARGET-SPACE] [--no-restart]
  ```


  - **Description**


  ```
  ACopy the source of the app to another app. If the file is not overwritten, it will be automatically restarted
  ```


  - **Parameter**


  | Parameter   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |SOURCE-APP   |Original APP Name                        |O        |
  |TARGET-APP   |Name of the app to which the source will be copied            |X        |
  |-o TARGET-ORG |Target Organization                         |O        |
  |-s TARGET-SPACE|Target Space                    |X        |
  |--no-restart   |Copy source and do not restart  |X        |

  - **Used Example**

  ```
  $ cf copy-source spring-music another-music
  ```

#### create-app-manifest

  - **Basic Syntax**


  ```
  $ cf create-app-manifest <APP_NAME> [-p /path/<app-name>-manifest.yml]
  ```


  - **Description**


  ```
  Create App's manifest file.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |SOURCE-APP   |Original APP Name                        |O        |
  |-p /path/<app-name>.yml   |Location and filename where the file will be created (automatically generated if -p is not used)            |X        |

  - **Used Example**

  ```
  $  cf create-app-manifest spring-music -p ./spring-music-manifest.yml
  ```

## <div id='ID-SERVICES'/> SERVICES

#### <div id='marketplace-m'/> marketplace,m

  - **Basic Syntax**


  ```
  $ cf marketplace [-s SERVICE_NAME]
  ```


  - **Description**


  ```
  Inquires the list of services provided by the cf marketplace.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |-s SERVICE_NAME   |Inquries plan of the service.    |X        |


  - **Used Example**

  ```
  $  cf create-app-manifest spring-music -p ./spring-music-manifest.yml
  ```

#### <div id='services-s'/> services,s

  - **Basic Syntax**


  ```
  $ cf services
  ```


  - **Description**


  ```
  Inquires the list of service instances in the target space.
  ```


  - **Parameter**


  | Parameter Name   |           Description                 | Necessity(O/X) |
  |-------------|--------------------------------|-----------|
  |-s SERVICE_NAME   |Inquires plan of the service.    |X        |


  - **Used Example**

  ```
  $  cf create-app-manifest spring-music -p ./spring-music-manifest.yml
  ```

#### service

  - **Basic Syntax**


  ```
  $ cf service <SERVICE_INSTANCE> [--guid]
  ```


  - **Description**


  ```
  Inquires information of service instances.
  ```


  - **Parameter**


  | Parameter Name   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE   |서비스 인스턴스명           |O        |
  |--guid             |서비스 인스턴스의 Guid를 조회합니다.   |X        |

  - **사용예시**

  ```
  $ cf service spring-music-db
  ```


#### create-service

  - **기본 Syntax**


  ```
  $ cf create-service <SERVICE> <PLAN> <SERVICE_INSTANCE> [-c PARAMETERS_AS_JSON] [-t TAGS]
  ```


  - **설명**


  ```
  마켓플레이스에서 제공하는 서비스로 서비스 인스턴스를 만든다.
  ```

  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE      |마켓플레이스에 있는 서비스명                                              |O        |
  |PLAN         |서비스 플랜명                                                           |O        |
  |SERVICE_INSTANCE   |만들 서비스 인스턴스명                                             |O        |
  |-c PARAMETERS_AS_JSON |서비스 설정정보를 json 형태로 입력 <br> Ex) -c '{"ram_gb":4}'    |X        |
  |-t TAGS      |서비스 인스턴스 테그                                                     |X        |

  - **사용예시**

  ```
  $ cf create-service spring-music-db silver p-mysql
  ```


#### update-service

  - **기본 Syntax**


  ```
  $ cf update-service <SERVICE_INSTANCE> [-p NEW_PLAN] [-c PARAMETERS_AS_JSON] [-t TAGS]
  ```


  - **설명**


  ```
  서비스 인스턴스를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE        |서비스 인스턴스명                                               |O        |
  |-p NEW_PLAN             |서비스 플랜명                                                  |O        |
  |-c PARAMETERS_AS_JSON   |서비스 설정정보를 json 형태로 입력 <br> Ex) -c '{"ram_gb":4}'    |O        |
  |-t TAGS                 |서비스 인스턴스 테그                                            |X        |

  - **사용예시**

  ```
  $ cf update-service spring-music-db -p gold_plan
  ```


#### delete-service

  - **기본 Syntax**


  ```
  $ cf delete-service SERVICE_INSTANCE [-f]
  ```


  - **설명**


  ```
  서비스 인스턴스를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE  |서비스 인스턴스명                                        |O        |
  |-f                |삭제 확인 메시지 없이 서비스 인스턴스 삭제합니다.             |X        |

  - **사용예시**

  ```
  $ cf delete-service spring-music-db
  ```

#### rename-service

  - **기본 Syntax**


  ```
  $ cf rename-service <SERVICE_INSTANCE> <NEW_SERVICE_INSTANCE>
  ```


  - **설명**


  ```
  서비스 인스턴스명을 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE       |서비스 인스턴스명                       |O        |
  |NEW_SERVICE_INSTANCE   |변경하려는 서비스 인스턴스명             |O        |

  - **사용예시**

  ```
  $ cf rename-service spring-music-db new_spring-music-db
  ```

#### <div id='create-service-key-csk'/> create-service-key,csk

  - **기본 Syntax**


  ```
  $ cf create-service-key <SERVICE_INSTANCE> <SERVICE_KEY> [-c PARAMETERS_AS_JSON]
  ```


  - **설명**


  ```
  서비스 인스턴스의 key를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE       |서비스 인스턴스명                       |O        |
  |SERVICE_KEY            |서비스 인스턴스 key명                   |O        |
  |-c PARAMETERS_AS_JSON  |서비스 인스턴스 설정(JSON Parameter)    |X        |


  - **사용예시**

  ```
  $ cf create-service-key spring-music-db mykey -c '{"permissions":"read-only"}'
  ```

#### <div id='service-keys-sk'/> service-keys,sk

  - **기본 Syntax**


  ```
  $ cf service-keys <SERVICE_INSTANCE>
  ```


  - **설명**


  ```
  서비스 인스턴스의 key 목록을 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE       |서비스 인스턴스명                       |O        |


  - **사용예시**

  ```
  $ cf service-keys spring-music-db
  ```

#### service-key

  - **기본 Syntax**


  ```
  $ cf service-key <SERVICE_INSTANCE> <SERVICE_KEY> [--guid]
  ```


  - **설명**


  ```
  서비스 인스턴스의 key의 상세정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE  |서비스 인스턴스명                       |O        |
  |SERVICE_KEY       |서비스 인스턴스 key명                   |O        |
  |--guid            |서비스 인스턴스 guid를 조회합니다.         |X        |



  - **사용예시**

  ```
  $ cf service-key spring-music-db mykey
  ```

#### <div id='delete-service-key-dsk'/> delete-service-key,dsk

  - **기본 Syntax**


  ```
  $ cf delete-service-key <SERVICE_INSTANCE> <SERVICE_KEY> [-f]
  ```


  - **설명**


  ```
  서비스 key를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE  |서비스 인스턴스명                       |O        |
  |SERVICE_KEY       |서비스 인스턴스 key명                   |O        |
  |--guid            |서비스 인스턴스 guid를 조회합니다.         |X        |



  - **사용예시**

  ```
  $ cf delete-service-key spring-music-db mykey
  ```

#### <div id='bind-service-bs'/> bind-service,bs

  - **기본 Syntax**


  ```
  $ cf bind-service <APP_NAME> <SERVICE_INSTANCE> [-c PARAMETERS_AS_JSON]
  ```


  - **설명**


  ```
  App과 서비스 인스턴스를 바인딩합니다.<br> - 서비스 인스턴스와 APP을 바인딩해야 App에서 서비스 사용가능
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |APP명                                            |O        |
  |SERVICE_INSTANCE  |서비스 인스턴스 명                           |O        |
  |-c PARAMETERS_AS_JSON   |바인딩 설정 파라미터 (json형태)         |X        |



  - **사용예시**

  ```
  $ cf bind-service spring-music spring-music-db -c '{"permissions":"read-only"}'

  $ cf bind-service spring-music spring-music-db -c ~/workspace/tmp/instance_config.json
  ```

#### <div id='unbind-service-us'/> unbind-service,us

  - **기본 Syntax**


  ```
  $ cf unbind-service <APP_NAME> <SERVICE_INSTANCE>
  ```


  - **설명**


  ```
  App과 서비스 인스턴스를 언바인딩합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME          |서비스 인스턴스명                            |O        |
  |SERVICE_INSTANCE  |서비스 인스턴스 명                           |O        |



  - **사용예시**

  ```
  $ cf unbind-service spring-music spring-music-db
  ```

#### <div id='create-user-provided-service-cups'/> create-user-provided-service,cups

  - **기본 Syntax**


  ```
  $ cf create-user-provided-service <SERVICE_INSTANCE> [-p CREDENTIALS] [-l SYSLOG-DRAIN-URL]
  ```


  - **설명**


  ```
  Market place에서 제공하는 서비스를 사용하지 않고 사용자가 별도의 서비스를 구성하여 APP과 바인딩합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE          |서비스 인스턴스명                            |O        |
  |-p CREDENTIALS            |서비스 인스턴스 명                           |X        |
  |-l SYSLOG-DRAIN-URL       |서비스 인스턴스 명                           |X        |


  - **사용예시**

  ```
  $ cf create-user-provided-service spring-music-db -p '{"username":"admin","password":"pa55woRD"}'
  ```

#### <div id='update-user-provided-service-uups'/> update-user-provided-service,uups

  - **기본 Syntax**


  ```
  $ cf update-user-provided-service <SERVICE_INSTANCE> [-p CREDENTIALS] [-l SYSLOG-DRAIN-URL]
  ```


  - **설명**


  ```
  user-provided service instance 정보를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_INSTANCE          |서비스 인스턴스명                            |O        |
  |-p CREDENTIALS            |서비스 인스턴스 명                           |X        |
  |-l SYSLOG-DRAIN-URL       |서비스 인스턴스 명                           |X        |


  - **사용예시**

  ```
  $  cf update-user-provided-service spring-music-db -p '{"username":"admin","password":"pa55woRD"}'
  ```

## <div id='ID-ORGS'/> ORGS

#### <div id='orgs-o'/> orgs,o

  - **기본 Syntax**


  ```
  $ cf orgs
  ```


  - **설명**


  ```
  조직정보 목록을 조회합니다...
  ```


  - **파라미터**

   - 없음


  - **사용예시**

  ```
  $ cf orgs
  ```



#### org

  - **기본 Syntax**


  ```
  $ cf org <ORG_NAME>
  ```


  - **설명**


  ```
  조직 상세 정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME      |조직명                         |O        |
  |--guid       |조직의 guid를 조회합니다.           |X        |


  - **사용예시**

  ```
  $ cf org cf
  ```


#### <div id='create-org-co'/> create-org,co

  - **기본 Syntax**


  ```
  $ cf create-org <ORG_NAME> [-q QUOTA_NAME]
  ```


  - **설명**


  ```
  조직정보를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME      |조직명                         |O        |
  |-q QUOTA_NAME |조직에게 할당할 quota           |X        |


  - **사용예시**

  ```
  $cf create-org test -q default
  ```



#### delete-org

  - **기본 Syntax**


  ```
  $ cf delete-org <ORG_NAME> [-f]
  ```


  - **설명**


  ```
  조직정보 목록을 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME      |조직명                          |O        |
  |-f           |확인메시지 없이 조직정보 삭제합니다.  |X        |


  - **사용예시**

  ```
  $ cf delete-org cf -f
  ```


#### rename-org

  - **기본 Syntax**


  ```
    $ cf rename-org <ORG_NAME> <NEW_ORG_NAME>
  ```


  - **설명**


  ```
  조직명을 변경합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME       |조직명                          |O        |
  |NEW_ORG_NAME   |변경할 조직명                    |O        |


  - **사용예시**

  ```
  $ cf rename cf new-cf
  ```

## <div id='ID-SPACES'/> SPACES

#### spaces

  - **기본 Syntax**


  ```
  $ cf spaces
  ```


  - **설명**


  ```
  스페이스 목록을 가져온다.
  ```


  - **파라미터**

   - 없음


  - **사용예시**

  ```
  $ cf spaces
  ```


#### space

  - **기본 Syntax**


  ```
  $ cf space <SPACE_NAME>
  ```


  - **설명**


  ```
  스페이스 상세정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME   |스페이스명                           |O          |


  - **사용예시**

  ```
  $ cf space development
  ```

#### create-space

  - **기본 Syntax**


  ```
  $ cf create-space <SPACE_NAME> [-o ORG_NAME] [-q SPACE-QUOTA-NAME]
  ```


  - **설명**


  ```
  스페이스 정보를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME   |스페이스명                           |O         |
  |-o ORG_NAME  |스페이스에 매핑될 조직명               |X         |
  |-q SPACE-QUOTA-NAME    |스페이스에 할당될 QUOTA명    |X         |

  - **사용예시**

  ```
  $ cf create-space -o cf -q cf-space-quota
  ```

#### delete-space

  - **기본 Syntax**


  ```
  $ cf delete-space <SPACE_NAME> [-f]
  ```


  - **설명**


  ```
  스페이스정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME   |스페이스명                           |O         |
  |-f           |삭제 확인메시지 없이 스페이스 삭제합니다. |X         |

  - **사용예시**

  ```
  $ cf delete-space development
  ```

#### rename-space

  - **기본 Syntax**


  ```
  $ cf rename-space <SPACE_NAME> <NEW_SPACE_NAME>
  ```


  - **설명**


  ```
  스페이스 명을 변경합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME     |스페이스명                           |O         |
  |NEW_SPACE_NAME |삭제 확인메시지 없이 스페이스 삭제합니다. |O         |

  - **사용예시**

  ```
  $ cf rename-space development new_development
  ```

## <div id='ID-DOMAINS'/> DOMAINS

#### domains

  - **기본 Syntax**


  ```
  $ cf domains
  ```


  - **설명**


  ```
  도메인 정보 목록을 조회합니다.
  ```


  - **파라미터**


    - 없음


  - **사용예시**

  ```
  $ cf domains
  ```


#### create-domain

  - **기본 Syntax**


  ```
  $ cf create-domain <ORG_NAME> <DOMAIN>
  ```


  - **설명**


  ```
  도메인 정보 목록을 생성합니다. 생성된 도메인은 설정된 조직에서 사용가능하다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME     |조직명                           |O         |
  |DOMAIN       |도메인명                          |O         |


  - **사용예시**

  ```
  $ cf create-domain cf-org cf.or.kr
  ```


#### delete-domain

  - **기본 Syntax**


  ```
  $ cf delete-domain <DOMAIN> [-f]
  ```


  - **설명**


  ```
  도메인 정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |DOMAIN       |도메인명                           |O         |
  |-f           |삭제 확인메시지 없이 도메인을 삭제합니다. |X         |


  - **사용예시**

  ```
  $ cf delete-domain cf.or.kr
  ```


#### create-shared-domain

  - **기본 Syntax**


  ```
  $ cf create-shared-domain <DOMAIN>
  ```


  - **설명**


  ```
  공유 도메인정보를 생성한다
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |DOMAIN       |조직명                           |O         |


  - **사용예시**

  ```
  $ cf create-shared-domain cf.or.kr
  ```

#### delete-shared-domain

  - **기본 Syntax**


  ```
  $ cf delete-shared-domain <DOMAIN> [-f]
  ```


  - **설명**


  ```
  공유 도메인정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |DOMAIN       |조직명                           |O         |
  |-f           |삭제 확인메시지 없이 도메인을 삭제합니다.    |X         |

  - **사용예시**

  ```
  $ cf delete-shared-domain cf.or.kr
  ```

## <div id='ID-ROUTES'/> ROUTES

#### <div id='routes-r'/> routes, r

  - **기본 Syntax**


  ```
  $ cf routes
  ```


  - **설명**


  ```
  현재 조직/스페이스에 존재하는 라우트 정보목롤을 조회합니다.
  ```


  - **파라미터**

    - 없음

  - **사용예시**

  ```
  $ cf routes
  ```


#### create-route

  - **기본 Syntax**


  ```
  $ cf create-route <SPACE_NAME> <DOMAIN> [-n HOSTNAME]
  ```


  - **설명**


  ```
  공유 도메인정보를 삭제합니다...
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME   |스페이스명                           |O         |
  |DOMAIN       |삭제 확인메시지 없이 공유 도메인을 삭제합니다. <br>   - 도메인 정보가 입력되어있어야 합니다.   |O         |
  |-n HOSTNAME  |호스트 명                          |X         |


  - **사용예시**

  ```
  $ cf create-route development cf.or.kr
  ```


#### update-route

  - **기본 Syntax**


  ```
  $ cf update-route <SPACE_NAME> <DOMAIN> [-n HOSTNAME]
  ```


  - **설명**


  ```
  공유 도메인정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_NAME   |스페이스명                           |O         |
  |DOMAIN       |삭제 확인메시지 없이 공유 도메인을 삭제합니다. <br>   - 도메인 정보가 입력되어있어야 합니다.   |O         |
  |-n HOSTNAME  |호스트 명                          |X         |


  - **사용예시**

  ```
  $ cf update-route development cf.or.kr
  ```



#### check-route

  - **기본 Syntax**


  ```
  $ cf check-route <HOST> <DOMAIN>
  ```


  - **설명**


  ```
  라우트 정보가 존재하는지 체크합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |HOST         |호스트 명                                    |O         |
  |DOMAIN       |삭제 확인메시지 없이 공유 도메인을 삭제합니다.    |O         |


  - **사용예시**

  ```
  $ cf check-route spring-music cf.or.kr
  ```


#### map-route

  - **기본 Syntax**


  ```
  $ cf map-route <APP_NAME> <DOMAIN> [-n HOSTNAME]
  ```


  - **설명**


  ```
  App에게 URL route정보를 할당합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |App명                           |O         |
  |DOMAIN       |App에게 할당할 도메인             |O         |
  |-n HOSTNAME  |App에게 할당할 Host              |X         |


  - **사용예시**

  ```
  $ cf map-route spring-music cf.or.kr -n test
  ```


#### unmap-route

  - **기본 Syntax**


  ```
  $ cf unmap-route <APP_NAME> <DOMAIN> [-n HOSTNAME]
  ```


  - **설명**


  ```
  App에게 URL route정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |APP_NAME     |App명                           |O         |
  |DOMAIN       |App에게 할당할 도메인             |O         |
  |-n HOSTNAME  |App에게 할당할 Host              |X         |


  - **사용예시**

  ```
  $ cf unmap-route spring-music cf.or.kr -n spring-music
  ```

#### delete-route

  - **기본 Syntax**


  ```
  $ cf delete-route <DOMAIN> [-n HOSTNAME] [-f]
  ```


  - **설명**


  ```
  App에게 URL route정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |DOMAIN       |App에게 할당할 도메인             |O         |
  |-n HOSTNAME  |App에게 할당할 Host              |X         |
  |-f           |삭제 확인메시지 없이 라우트 정보를 삭제합니다.              |X         |


  - **사용예시**

  ```
  $ cf delete-route spring-music cf.or.kr -n spring-music
  ```

#### delete-orphaned-routes

  - **기본 Syntax**


  ```
  $ cf delete-orphaned-routes [-f]
  ```


  - **설명**


  ```
  App에 매핑되지 않은 라우트 정보를 모두 삭제한다
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |-f           |삭제 확인메시지 없이 라우트 정보를 삭제합니다.           |X         |


  - **사용예시**

  ```
  $ cf delete-orphaned-routes
  ```

## <div id='ID-BUILDPACKS'/> BUILDPACKS


#### buildpacks

  - **기본 Syntax**


  ```
  $ cf buildpacks
  ```


  - **설명**


  ```
  빌드팩 목록을 조회합니다.
  ```


  - **파라미터**


    - 없음


  - **사용예시**

  ```
  $ cf buildpacks
  ```


#### create-buildpack

  - **기본 Syntax**


  ```
  $ cf create-buildpack <BUILDPACK> <-p PATH> <-i POSITION> [--enable|--disable]
  ```


  - **설명**


  ```
  빌드팩을 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |BUILDPACK     |빌드팩명                           |O         |
  |-p PATH       |빌드팩 경로                      |O         |
  |-i POSITIONE  |빌드팩 auto-detection동안 빌드팩 체크 순서  <br> ex)1.2.3              |O         |
  |--enable      |스테이징시 사용                  |X         |
  |--disable     |스테이징시 미사용                |X         |



  - **사용예시**

  ```
  $ cf create-buildpack egov-buildpack ~/workspace/buildpack/egov -i 1
  ```


#### update-buildpack

  - **기본 Syntax**


  ```
  $ cf update-buildpack <BUILDPACK> [-p PATH] [-i POSITION] [--enable|--disable] [--lock|--unlock]
  ```


  - **설명**


  ```
  빌드팩 정보를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |BUILDPACK     |빌드팩명                           |O         |
  |-p PATH       |빌드팩 경로                      |O         |
  |-i POSITIONE  |빌드팩 auto-detection동안 빌드팩 체크 순서  <br> ex)1.2.3              |O         |
  |--enable      |스테이징시 사용                  |X         |
  |--disable     |스테이징시 미사용                |X         |



  - **사용예시**

  ```
  $ cf create-buildpack egov-buildpack ~/workspace/buildpack/egov -i 1
  ```

#### delete-buildpack

  - **기본 Syntax**


  ```
  $ cf delete-buildpack <BUILDPACK_NAME> [-f]
  ```


  - **설명**


  ```
  빌드팩을 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |BUILDPACK     |빌드팩명                           |O         |
  |-f           |삭제 확인메시지 없이 빌드팩 정보를 삭제       |X         |


  - **사용예시**

  ```
  $ cf delete-buildpack egov-buildpack
  ```

## <div id='ID-USER-ADMIN'/> USER ADMIN


#### create-user

  - **기본 Syntax**


  ```
  $ cf create-user <USERNAME> <PASSWORD>
  ```


  - **설명**


  ```
  새로운 사용자 계정을 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자 ID                        |O         |
  |PASSWORD     |패스워드                         |O         |


  - **사용예시**

  ```
  $ cf create-user cfuser userpassword
  ```


#### delete-user

  - **기본 Syntax**


  ```
  $ cf delete-user <USERNAME> [-f]
  ```


  - **설명**


  ```
    새로운 사용자 계정을 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자 ID                       |O         |
  |-f           |삭제 확인메시지 없이 사용자 정보를 삭제                         |X         |


  - **사용예시**

  ```
  $ cf delete-user cfuser
  ```


#### org-users

  - **기본 Syntax**


  ```
  $ cf org-users <ORG_NAME>
  ```


  - **설명**


  ```
  조직에 소속된 사용자를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG_NAME     |조직명                          |O         |


  - **사용예시**

  ```
  $ cf org-users cforg
  ```

#### set-org-role

  - **기본 Syntax**


  ```
  $ cf set-org-role <USERNAME> <ORG> <ROLE>
  ```


  - **설명**


  ```
  사용자에게 특정조직의 role을 설정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자명                          |O         |
  |ORG          |조직명                          |O         |
  |ROLE        |역할명 <br>  - OrgManager : 사용자 관리 및 plan설정/변경 권한 <br> - BillingManager : 빌링계정 및 과금정보 생성 및 관리 <br>  - OrgAuditor : 조직 quota사용률 및 사용자 role을 조회             |O         |



  - **사용예시**

  ```
  $ cf set-org-role cfuser cforg OrgManager
  ```


#### unset-org-role

  - **기본 Syntax**


  ```
  $ cf unset-org-role <USERNAME> <ORG> <ROLE>
  ```


  - **설명**


  ```
  사용자에게 특정조직의 role을 설정을 해제합니다..
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자명                          |O         |
  |ORG          |조직명                          |O         |
  |ROLE        |역할명 <br>  - OrgManager : 사용자 관리 및 plan설정/변경 권한 <br> - BillingManager : 빌링계정 및 과금정보 생성 및 관리 <br>  - OrgAuditor : 조직 quota사용률 및 사용자 role을 조회             |O         |



  - **사용예시**

  ```
  $ cf unset-org-role cfuser cforg OrgManager
  ```


#### space-users

  - **기본 Syntax**


  ```
  $ cf space-users <ORG> <SPACE>
  ```


  - **설명**


  ```
  조직의 스페이스에 할당된 사용자 목록정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG          |조직명                          |O         |
  |SPACE        |스페이스명                       |O         |


  - **사용예시**

  ```
  $ cf space-users development
  ```


#### set-space-role

  - **기본 Syntax**


  ```
  $ cf set-space-role <USERNAME> <ORG> <SPACE> <ROLE>
  ```


  - **설명**


  ```
  사용자에게 조직의 스페이스에 role을 할당합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자명                         |O         |
  |ORG          |조직명                           |O         |
  |SPACE        |스페이스명                       |O         |
  |ROLE         |역할명  <br>  - SpaceManager: 스페이스의 관리자로 스페이스 내의 사용자 계정 관리 및 인스턴스 수, 서비스 바인딩 상태 및 스페이스 내의 리소스 상태를 조회 및 변경 <br> - SpaceDeveloper: 서비스 관리로 App 배포 <br> - SpaceAuditor: 서비스 관리로 App을 배포   |O         |

  - **사용예시**

  ```
  $ cf set-space-role cfuser cforg development OrgManager
  ```


#### unset-space-role

  - **기본 Syntax**


  ```
  $ cf unset-space-role <USERNAME> <ORG> <SPACE> <ROLE>
  ```


  - **설명**


  ```
  사용자에게 조직의 스페이스에 role을 회수합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |사용자명                         |O         |
  |ORG          |조직명                           |O         |
  |SPACE        |스페이스명                       |O         |
  |ROLE         |역할명  <br>  - SpaceManager: 스페이스의 관리자로 스페이스 내의 사용자 계정 관리 및 인스턴스 수, 서비스 바인딩 상태 및 스페이스 내의 리소스 상태를 조회. <br> - SpaceDeveloper: 서비스 관리로 App 배포 <br> - SpaceAuditor: 스페이스 내의 서비스 바인딩, 인스턴스 수, app사용률등을 조회   |O         |

  - **사용예시**

  ```
  $ cf unset-space-role cfuser cforg development OrgManager
  ```

## <div id='ID-ORG-ADMIN'/> ORG ADMIN


#### quotas

  - **기본 Syntax**


  ```
  $ cf quotas
  ```


  - **설명**


  ```
  Quota 목록을 조회합니다.
  ```


  - **파라미터**

    - 없음

  - **사용예시**

  ```
  $ cf quotas
  ```


#### quota

  - **기본 Syntax**


  ```
  $ cf quota <QUOTA>
  ```


  - **설명**


  ```
  Quota의 상세정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |USERNAME     |QUOTA명                         |O         |



  - **사용예시**

  ```
  $ cf quota cf-quota
  ```

#### set-quota

  - **기본 Syntax**


  ```
  $ cf set-quota <ORG> <QUOTA>
  ```


  - **설명**


  ```
  조직에게 QUOTA를 할당합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG          |직명                            |O         |
  |QUOTA        |QUOTA명                         |O         |


  - **사용예시**

  ```
  $ cf set-quota cf-quota
  ```


#### create-quota

  - **기본 Syntax**


  ```
  $ cf create-quota <QUOTA> [-m TOTAL_MEMORY] [-i INSTANCE_MEMORY] [-r ROUTES] [-s SERVICE_INSTANCES] [--allow-paid-service-plans]
  ```


  - **설명**


  ```
  Quota정보를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |QUOTA                       |QUOTA명                                                       |O         |
  |-m TOTAL_MEMORY             |메모리 할당량  <br> Ex) 1024M, 1G, 10G                         |X         |
  |-i INSTANCE_MEMORY          |App instance가 가질수 있는 최대할당량 (-1은 무한대) <br>  Ex) 1024M, 1G, 10G                        |X         |
  |-r ROUTES                   |최대 라우트 수                                                 |X         |
  |-s SERVICE_INSTANCES        |최대 서비스 인스턴스 수                                         |X         |
  |--allow-paid-service-plans  |과금 서비스 plan 사용가능                                       |X        |


  - **사용예시**

  ```
  $ cf create-quota cf-quota -m 500m -i 256m -r 2000 -s 500
  ```


#### delete-quota

  - **기본 Syntax**


  ```
  $ cf delete-quota <QUOTA> [-f]
  ```


  - **설명**


  ```
  Quota정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |QUOTA        |QUOTA명                                                  |O         |
  |-f           |삭제 확인메시지 없이 QUOTA 정보를 삭제                      |X         |


  - **사용예시**

  ```
  $ cf delete-quota cf-quota
  ```


#### update-quota

  - **기본 Syntax**


  ```
  $ cf update-quota <QUOTA> [-m TOTAL_MEMORY] [-i INSTANCE_MEMORY][-n NEW_NAME] [-r ROUTES] [-s SERVICE_INSTANCES] [--allow-paid-service-plans | --disallow-paid-service-plans]
  ```


  - **설명**


  ```
  Quota정보를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |QUOTA                       |QUOTA명                                                       |O         |
  |-m TOTAL_MEMORY             |메모리 할당량  <br> Ex) 1024M, 1G, 10G                         |X         |
  |-i INSTANCE_MEMORY          |App instance가 가질수 있는 최대할당량 (-1은 무한대) <br>  Ex) 1024M, 1G, 10G                        |X         |
  |-n NEW_NAME                 |QUOTA명 변경시 변경할 이름                                      |X         |
  |-r ROUTES                   |최대 라우트 수                                                 |X         |
  |-s SERVICE_INSTANCES        |최대 서비스 인스턴스 수                                         |X         |
  |--allow-paid-service-plans  |과금 서비스 plan 사용가능                                       |X        |
  |--disallow-paid-service-plans  |과금 서비스 plan 사용 불가                                       |X        |

  - **사용예시**

  ```
  $ cf update-quota cf-quota -m 500m -i 256m -r 2000 -s 500
  ```


#### shared-private-domain

  - **기본 Syntax**


  ```
  $ cf shared-private-domain <ORG> <DOMAIN>
  ```


  - **설명**


  ```
  private도메인을 다른 조직과 공유합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |QUOTA                 |QUOTA명                        |O         |
  |DOMAIN                |도메인명                        |O         |


  - **사용예시**

  ```
  $ cf shared-private-domain cf-org sharedomain.or.kr
  ```


#### unshared-private-domain

  - **기본 Syntax**


  ```
  $ cf unshared-private-domain <ORG> <DOMAIN>
  ```


  - **설명**


  ```
  다른 조직과 share한 도메인 정보를 unshare합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ORG                   |도메인명                        |O         |
  |DOMAIN                |도메인명                        |O         |


  - **사용예시**

  ```
  $ cf unshared-private-domain cf-org sharedomain.or.kr
  ```

## <div id='ID-SPACE-ADMIN'/> SPACE ADMIN


#### space-quotas

  - **기본 Syntax**


  ```
  $ cf space-quotas
  ```


  - **설명**


  ```
  Space-quota정보 목록을 조회합니다.
  ```


  - **파라미터**


    - 없음


  - **사용예시**

  ```
  $ cf space-quotas
  ```



#### space-quota

  - **기본 Syntax**


  ```
  $ cf space-quota <SPACE_QUOTA_NAME>
  ```


  - **설명**


  ```
  Space quota 상세정보를 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE_QUOTA_NAME       |스페이스 QUOTA명       |O         |


  - **사용예시**

  ```
  $ cf space-quota cf-space-quota
  ```


#### create-space-quota

  - **기본 Syntax**


  ```
  $ cf create-space-quota <QUOTA> [-i INSTANCE_MEMORY] [-m MEMORY] [-r ROUTES] [-s SERVICE_INSTANCES] [--allow-paid-service-plans]
  ```


  - **설명**


  ```
  스페이스 Quota정보를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |QUOTA                       |QUOTA명                                                       |O         |
  |-m TOTAL_MEMORY             |메모리 할당량  <br> Ex) 1024M, 1G, 10G                         |X         |
  |-i INSTANCE_MEMORY          |App instance가 가질수 있는 최대할당량 (-1은 무한대) <br>  Ex) 1024M, 1G, 10G                        |X         |
  |-r ROUTES                   |최대 라우트 수                                                 |X         |
  |-s SERVICE_INSTANCES        |최대 서비스 인스턴스 수                                         |X         |
  |--allow-paid-service-plans  |과금 서비스 plan 사용가능                                       |X        |


  - **사용예시**

  ```
  $ cf create-space-quota cf-space-quota -i 2G -m 10G -r 3000 -s 200
  ```


#### update-space-quota

  - **기본 Syntax**


  ```
      $ cf update-space-quota <SPACE-QUOTA-NAME> [-i MAX-INSTANCE-MEMORY] [-m MEMORY] [-n NEW_NAME] [-r ROUTES] [-s SERVICES] [--allow-paid-service-plans | --disallow-paid-service-plans]
  ```


  - **설명**


  ```
  스페이스 Quota정보를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE-QUOTA-NAME            |스페이스 QUOTA명                                               |O         |
  |-i MAX-INSTANCE-MEMORY      |App instance가 가질수 있는 최대할당량 (-1은 무한대) <br>  Ex) 1024M, 1G, 10G                        |X         |
  |-m MEMORY                   |스페이스가 가질수 있는 최대 메모리                               |X         |
  |-n NEW_NAME                 |변경하려는 SPACE-QUOTA명                                       |X         |
  |-r ROUTES                   |스페이스가 가지는 최대 route 갯수                               |X         |
  |-s SERVICES                 |스페이스가 가지는 최대 서비스 인스턴스 갯수                       |X         |
  |--allow-paid-service-plans  |과금 서비스 plan 사용가능                                       |X        |
  |--disallow-paid-service-plans  |과금 서비스 plan 사용 불가                                   |X        |


  - **사용예시**

  ```
  $ cf update-space-quota cf-space-quota -i 2G -m 10G -r 3000 -s 200
  ```


#### delete-space-quota

  - **기본 Syntax**


  ```
  $ cf delete-space-quota <SPACE-QUOTA-NAME>
  ```


  - **설명**


  ```
  스페이스 Quota정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE-QUOTA-NAME     |스페이스 QUOTA명                                     |O         |
  |-f           |삭제 확인메시지 없이 SPACE-QUOTA 정보를 삭제               |X         |



  - **사용예시**

  ```
  $ cf delete-space-quota cf-space-quota
  ```


#### set-space-quota

  - **기본 Syntax**


  ```
  $ cf set-space-quota <SPACE-NAME> <SPACE-QUOTA-NAME>
  ```


  - **설명**


  ```
  스페이스에 quota를 할당합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE-NAME            |스페이스명                    |O         |
  |SPACE-QUOTA-NAME      |스페이스 Quota명              |O         |



  - **사용예시**

  ```
  $ cf set-space-quota development cf-space-quota
  ```


#### unset-space-quota

  - **기본 Syntax**


  ```
  $ cf unset-space-quota SPACE QUOTA
  ```


  - **설명**


  ```
  스페이스에 할당된 quota를 회수합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SPACE        |스페이스명                   |O         |
  |QUOTA        |스페이스 Quota명             |O         |



  - **사용예시**

  ```
  $ cf unset-space-quota development cf-space-quota
  ```

## <div id='ID-SERVICE-ADMIN'/> SERVICE ADMIN


#### service-auth-tokens

  - **기본 Syntax**


  ```
  $ cf service-auth-tokens
  ```


  - **설명**


  ```
  서비스 인증 토큰 목록을 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $ cf service-auth-token
  ```


#### create-service-auth-token

  - **기본 Syntax**


  ```
  $ cf create-service-auth-token <LABEL> <PROVIDER> <TOKEN>
  ```


  - **설명**


  ```
  스페이스에 할당된 quota를 회수합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |LABEL        |서비스 토큰 라벨                 |O         |
  |PROVIDER     |서비스 제공자                    |O         |
  |TOKEN        |토큰명                          |O         |


  - **사용예시**

  ```
  $ cf create-service-auth-token token-label mysql token
  ```


#### update-service-auth-token

  - **기본 Syntax**


  ```
  $ cf update-service-auth-token <LABEL> <PROVIDER> <TOKEN>
  ```


  - **설명**


  ```
  Service auth token 정보를 수정합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |LABEL        |서비스 토큰 라벨                 |O         |
  |PROVIDER     |서비스 제공자                    |O         |
  |TOKEN        |토큰명                          |O         |


  - **사용예시**

  ```
  $ cf update-service-auth-token token-label mysql token
  ```


#### delete-service-auth-token

  - **기본 Syntax**


  ```
  $ cf delete-service-auth-token <LABEL> <PROVIDER> [-f]
  ```


  - **설명**


  ```
  Service auth token 정보를 삭제합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |LABEL        |서비스 토큰 라벨                 |O         |
  |PROVIDER     |서비스 제공자                    |O         |
  |-f           |삭제 확인메시지 없이 SERVICE TOKEN 정보를 삭제      |X         |


  - **사용예시**

  ```
  $ cf delete-service-auth-token token-label mysql
  ```


#### service-brokers

  - **기본 Syntax**


  ```
  $ cf delete-service-auth-token <LABEL> <PROVIDER> [-f]
  ```


  - **설명**


  ```
  Service Broker정보 목록을 조회합니다.
  ```



  - **파라미터**

    - 없음


  - **사용예시**

  ```
  $ cf service-brokers
  ```


#### create-service-broker

  - **기본 Syntax**


  ```
  $ cf create-service-broker <SERVICE_BROKER> <USERNAME> <PASSWORD> <URL>
  ```


  - **설명**


  ```
  Service Broker정보를 등록합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_BROKERABEL        |서비스 브로커명             |O         |
  |USERNAME                   |사용자명                   |O         |
  |PASSWORD                   |패스워드                   |O         |
  |URL                        |서비스 브로커 URL           |O         |



  - **사용예시**

  ```
  $ cf create-service-broker mysql-service-broker admin password http://p-mysql.10.244.0.34.xip.io
  ```


#### update-service-broker

  - **기본 Syntax**


  ```
  $ cf update-service-broker <SERVICE_BROKER> <USERNAME> <PASSWORD> <URL>
  ```


  - **설명**


  ```
  Service Broker정보를 등록합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_BROKERABEL        |서비스 브로커명             |O         |
  |USERNAME                   |사용자명                   |O         |
  |PASSWORD                   |패스워드                   |O         |
  |URL                        |서비스 브로커 URL           |O         |



  - **사용예시**

  ```
  $ cf update-service-broker mysql-service-broker admin password http://p-mysql.10.244.0.34.xip.io
  ```


#### delete-service-broker

  - **기본 Syntax**


  ```
  $ cf delete-service-broker <SERVICE_BROKER> [-f]
  ```


  - **설명**


  ```
  Service Broker정보를 삭제합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_BROKER    |서비스 브로커명                                          |O         |
  |-f                |삭제 확인메시지 없이 SERVICE BROKER 정보를 삭제       |X         |



  - **사용예시**

  ```
  $ cf delete-service-broker mysql-service-broker
  ```


#### rename-service-broker

  - **기본 Syntax**


  ```
  $ cf rename-service-broker <SERVICE_BROKER> <NEW_SERVICE_BROKER>
  ```


  - **설명**


  ```
  Service Broker명을 수정합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE_BROKER     |서비스 브로커명             |O         |
  |NEW_SERVICE_BROKER |변경할 서비스 브로커명       |O         |



  - **사용예시**

  ```
  $ cf rename-service-broker mysql-service-broker new_mysql-service-broker
  ```


#### migrate-service-broker

  - **기본 Syntax**


  ```
  $ cf migrate-service-instances <v1_SERVICE> <v1_PROVIDER> <v1_PLAN> <v2_SERVICE> <v2_PLAN>
  ```


  - **설명**


  ```
  서비스 인스턴스에서 사용하는 서비스 및 플랜을 다른 플랜으로 변경합니다. <br> - App이 사용하는 서비스를 다른 서비스로 변경하려 할때 사용합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |v1_SERVICE     |기존 서비스 명                         |O         |
  |v1_PROVIDER    |기존 서비스를 제공하는 제공자            |O         |
  |v1_PLAN        |기존 서비스 인스턴스에서 사용하는 플랜    |O         |
  |v2_SERVICE     |신규 서비스 명                         |O         |
  |v2_PLAN        |신규 서비스에서 사용하는 플랜            |O         |  


  - **사용예시**

  ```
  $ cf migrate-service-instances p-mysql mysql-provider silver  postgres silver
  ```


#### purge-service-offering

  - **기본 Syntax**


  ```
  $ cf purge-service-offering <SERVICE> [-p PROVIDER]
  ```


  - **설명**


  ```
  cf와 서비스 브로커간의 정보 불일치를 해결할때 사용합니다. <br>   (migrate-service-instances 명령 이후 사용)
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE      |서비스 명                                  |O         |
  |-p PROVIDER  |서비스 제공자                               |O         |
  |-f           |삭제 확인메시지 없이 서비스 정보를 삭제한다    |O         |



  - **사용예시**

  ```
  $ cf purge-service-offering mysql
  ```


#### service-access

  - **기본 Syntax**


  ```
  $ cf service-access
  ```


  - **설명**


  ```
  서비스 access 될 서비스 목록 조회합니다..
  ```



  - **파라미터**


     - 없음


  - **사용예시**

  ```
  $ cf service-access
  ```


#### enable-service-access

  - **기본 Syntax**


  ```
  $ cf enable-service-access <SERVICE> [-p PLAN] [-o ORG]
  ```


  - **설명**


  ```
  조직 또는 서비스 plan을 서비스에 접근 가능하도록 설정합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE      |서비스 명                        |O          |
  |-p PLAN      |PLAN명                          |O          |
  |-o ORG       |조직명                           |O          |



  - **사용예시**

  ```
  $ cf enable-service-access mysql -p silver -o cf-org
  ```


#### disable-service-access

  - **기본 Syntax**


  ```
  $ cf disable-service-access <SERVICE> [-p PLAN] [-o ORG]
  ```


  - **설명**


  ```
  조직 또는 서비스 plan을 서비스에 접근 불가 하도록 설정합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SERVICE      |서비스 명                        |O          |
  |-p PLAN      |PLAN명                          |O          |
  |-o ORG       |조직명                           |O          |



  - **사용예시**

  ```
  $ cf disable-service-access mysql -p silver -o cf-org
  ```

## <div id='ID-SECURITY-GROUP'/> SECURITY GROUP


#### security-group

  - **기본 Syntax**


  ```
  $ cf security-group <SECURITY_GROUP>
  ```


  - **설명**


  ```
  시큐리티 그룹 상세정보를 조회합니다.
  ```



  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP      |서큐리티 그룹명                        |O          |



  - **사용예시**

  ```
  $ cf security-group cf-security-group
  ```


#### security-groups

  - **기본 Syntax**


  ```
  $ cf security-groups
  ```


  - **설명**


  ```
  시큐리티 그룹 목록을 조회합니다.
  ```


  - **파라미터**


    - 없음



  - **사용예시**

  ```
  $ cf security-groups
  ```


#### create-security-group

  - **기본 Syntax**


  ```
  $ cf create-security-group <SECURITY_GROUP> <PATH_TO_JSON_RULES_FILE>
  ```


  - **설명**


  ```
  시큐리티 그룹정보를 생성합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP      |서큐리티 그룹명                                           |O          |
  |PATH_TO_JSON_RULES_FILE      |시큐리티 룰을 명세한 JSON 파일의 경로 및 파일명<br> ex) rule 파일 작성 예제 <br> [ <br> &nbsp;&nbsp;{   <br> &nbsp;&nbsp;&nbsp;&nbsp;"protocol": "tcp",     <br> &nbsp;&nbsp;&nbsp;&nbsp;"destination": "10.244.1.18", <br>     &nbsp;&nbsp;&nbsp;&nbsp;"ports": "3306" <br>&nbsp;&nbsp;} <br> ]     |O          |


  - **사용예시**

  ```
  $ cf create-security-group cf-security-group ./rule.json
  ```


#### update-security-group

  - **기본 Syntax**


  ```
  $ cf update-security-group <SECURITY_GROUP> <PATH_TO_JSON_RULES_FILE>
  ```


  - **설명**


  ```
  시큐리티 그룹정보를 수정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP      |서큐리티 그룹명                                           |O          |
  |PATH_TO_JSON_RULES_FILE      |시큐리티 룰을 명세한 JSON 파일의 경로 및 파일명<br> ex) rule 파일 작성 예제 <br> [ <br> &nbsp;&nbsp;{   <br> &nbsp;&nbsp;&nbsp;&nbsp;"protocol": "tcp",     <br> &nbsp;&nbsp;&nbsp;&nbsp;"destination": "10.244.1.18", <br>     &nbsp;&nbsp;&nbsp;&nbsp;"ports": "3306" <br>&nbsp;&nbsp;} <br> ]     |O          |

  - **사용예시**

  ```
  $ cf update-security-group cf-security-group ./rule.json
  ```


#### delete-security-group

  - **기본 Syntax**


  ```
  $ cf delete-security-group <SECURITY_GROUP> [-f]
  ```


  - **설명**


  ```
  시큐리티 그룹정보를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP  |서큐리티 그룹명                                    |O          |
  |-f              |삭제 확인메시지 없이 시큐리지 그룹 정보를 삭제합니다.    |X          |


  - **사용예시**

  ```
  $ cf update-security-group cf-security-group ./rule.json
  ```


#### bind-security-group

  - **기본 Syntax**


  ```
  $ cf bind-security-group <SECURITY_GROUP> <ORG> <SPACE>
  ```


  - **설명**


  ```
  시큐리티 그룹 정보와 스페이스를 바인드 합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP  |서큐리티 그룹명                |O          |
  |ORG             |조직명                        |O          |
  |SPACE           |스페이스명                    |O         |


  - **사용예시**

  ```
  $ cf update-security-group cf-security-group ./rule.json
  ```


#### unbind-security-group

  - **기본 Syntax**


  ```
  $ cf unbind-security-group <SECURITY_GROUP> <ORG> <SPACE>
  ```


  - **설명**


  ```
  시큐리티 그룹 정보와 스페이스를 언바인드 합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP  |서큐리티 그룹명                |O          |
  |ORG             |조직명                        |O          |
  |SPACE           |스페이스명                     |O          |


  - **사용예시**

  ```
  $ cf unbind-security-group cf-security-group cf-group development
  ```


#### bind-staging-security-group

  - **기본 Syntax**


  ```
  $ cf bind-staging-security-group <SECURITY_GROUP>
  ```


  - **설명**


  ```
  App staging처리를 하기 위해 시큐리티 그룹을 설정합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP  |서큐리티 그룹명                |O          |


  - **사용예시**

  ```
  $ cf bind-staging-security-group cf-security-group
  ```


#### staging-security-groups

  - **기본 Syntax**


  ```
  $ cf staging-security-groups
  ```


  - **설명**


  ```
  Staging security group 정보 목록을 조회합니다.
  ```


  - **파라미터**

    - 없음


  - **사용예시**

  ```
  $ cf staging-security-groups
  ```


#### unbind-staging-security-group

  - **기본 Syntax**


  ```
  $ cf unbind-staging-security-group <SECURITY_GROUP>
  ```


  - **설명**


  ```
  App staging처리를 하기 위한 시큐리티 그룹을 설정을 해제 합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |SECURITY_GROUP  |서큐리티 그룹명                |O          |


  - **사용예시**

  ```
  $ cf unbind-staging-security-group cf-security-group
  ```


#### running-security-groups

  - **기본 Syntax**


  ```
  $ cf unbind-staging-security-group <SECURITY_GROUP>
  ```


  - **설명**


  ```
  실행중인 시큐리트 그룹 목록을 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $ cf unbind-staging-security-group cf-security-group
  ```


## <div id='ID-ENVIRONMENT-VARIABLE-GROUPS'/> ENVIRONMENT VARIABLE GROUPS

#### <div id='running-environment-variable-group-revg'/> running-environment-variable-group, revg

  - **기본 Syntax**


  ```
  $ cf running-environment-variable-group
  ```


  - **설명**


  ```
  실환경변수 내용을 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $ cf running-environment-variable-group
  ```

#### <div id='staging-environment-variable-group-sevg'/> staging-environment-variable-group, sevg

  - **기본 Syntax**


  ```
  $ cf staging-environment-variable-group
  ```


  - **설명**


  ```
  스테이징시 사용되는 환경변수 내용을 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $ cf staging-environment-variable-group
  ```



#### <div id='set-staging-environment-variable-group-ssevg'/> set-staging-environment-variable-group, ssevg

  - **기본 Syntax**


  ```
  $ cf set-staging-environment-variable-group <ENV_VARIABLE>
  ```


  - **설명**


  ```
  스테이징시 사용되는 환경변수 내용을 설정한다
  ```


  - **파라미터**



  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ENV_VARIABLE  |환경변수 내용으로 KEY/VALUE로 구성              |O          |



  - **사용예시**

  ```
  $ cf set-staging-environment-variable-group '{"name":"value","name":"value"}'
  ```

#### <div id='set-running-environment-variable-group-ssevg'/> set-running-environment-variable-group, ssevg

  - **기본 Syntax**


  ```
  $ cf set-running-environment-variable-group <ENV_VARIABLE>
  ```


  - **설명**


  ```
  환경변수 내용을 설정 합니다.
  ```


  - **파라미터**



  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |ENV_VARIABLE  |환경변수 내용으로 KEY/VALUE로 구성된다.                |O          |



  - **사용예시**

  ```
  $ cf set-running-environment-variable-group '{"name":"value","name":"value"}'
  ```

## <div id='ID-FEATURE-FLAGS'/> FEATURE FLAGS

#### feature-flags

  - **기본 Syntax**


  ```
  $ cf feature-flags
  ```


  - **설명**


  ```
  feature flags 목록을 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $ cf feature-flags
  ```


#### feature-flag

  - **기본 Syntax**


  ```
  $ cf feature-flag <FEATURE_NAME>
  ```


  - **설명**


  ```
  특정 Feature flag의 상태를 조회합니다.
  ```


  - **파라미터**



  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |FEATURE_NAME  |Feature flag 명. <br> - feature flag에는 6가지가 있다. <br> 1)user_org_creation <br> 2) private_domain_creation <br> 3) app_bits_upload <br> 4) app_scaling <br>  5) route_creation <br> 6) service_instance_creation               |O          |



  - **사용예시**

  ```
  $ cf feature-flag app_bits_upload
  ```


#### enable-feature-flag

  - **기본 Syntax**


  ```
  $ cf enable-feature-flag <FEATURE_NAME>
  ```


  - **설명**


  ```
  특정 Feature flag의 상태를 enable로 변경합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |FEATURE_NAME  |Feature flag 명. <br> - feature flag에는 6가지가 있다. <br> 1)user_org_creation <br> 2) private_domain_creation <br> 3) app_bits_upload <br> 4) app_scaling <br>  5) route_creation <br> 6) service_instance_creation               |O          |



  - **사용예시**

  ```
  $ cf enable-feature-flag app_bits_upload
  ```


#### disable-feature-flag

  - **기본 Syntax**


  ```
  $ cf disable-feature-flag <FEATURE_NAME>
  ```


  - **설명**


  ```
  특정 Feature flag의 상태를 disable로 변경합니다.
  ```


  - **파라미터**



  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |FEATURE_NAME  |Feature flag 명. <br> - feature flag에는 6가지가 있다. <br> 1)user_org_creation <br> 2) private_domain_creation <br> 3) app_bits_upload <br> 4) app_scaling <br>  5) route_creation <br> 6) service_instance_creation               |O          |



  - **사용예시**

  ```
  $ cf disable-feature-flag app_bits_upload
  ```

## <div id='ID-ADVANCE'/> ADVANCE


#### curl

  - **기본 Syntax**


  ```
  $ cf curl <PATH> [-i] [-v] [-X METHOD] [-H HEADER] [-d DATA] [--output FILE]
  ```


  - **설명**


  ```
  OpenPaaS CLI명령어가 아닌 OpenPaaS API를 호출합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |PATH         |Cf api path <br>  Ex) /v2/spaces/2d94e7ee-9805-408d-a1eb-ceac319e603b/summary             |O          |
  |-i           |Response header포함한 결과                                                                  |X          |
  |-v           |Request/response에 CF_TRACE enable된 내용 포함                                              |X          |
  |-X METHOD    |HTTP method((GET,POST,PUT,DELETE,etc)                                                      |X          |
  |-H HEADER    |Request에 Custom Header를 포함합니다.                                                         |X          |
  |-d DATA      |Request에 Http data를 포함합니다.                                                             |X          |
  |--output FILE |Response결과를 stdout대신 FILE로 결과 저장                                                  |X          |


  - **사용예시**

  ```
  $ cf curl /v2/spaces/2d94e7ee-9805-408d-a1eb-ceac319e603b/summar
  ```


#### config

  - **기본 Syntax**


  ```
  $ cf config [--async-timeout TIMEOUT_IN_MINUTES] [--trace true | false | path/to/file] [--color true | false] [--locale (LOCALE | CLEAR)]
  ```


  - **설명**


  ```
  CF CLI에 대한 설정.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |--async-timeout TIMEOUT_IN_MINUTES        |CLI 명령 전송시 async timeout 설정                     |X          |
  |--trace (true / false / path/to/file   )    |CLI 명령 수행시 실행되는 cf api의 내용 출력 설정         |X          |
  |--color true / false                      |CLI 명령 수행시 실행되는 cf api의 내용 color 설정        |X          |
  |--locale (LOCALE / CLEAR)                 |CLI 명령 수행시 실행되는 cf api의 내용 locale 설정       |X          |


  - **사용예시**

  ```
  $ cf curl /v2/spaces/2d94e7ee-9805-408d-a1eb-ceac319e603b/summar
  ```

#### oauth-token

  - **기본 Syntax**


  ```
  $ cf oauth-token
  ```


  - **설명**


  ```
  사용자가 cf login후 CF에서 받은 token 값 조회합니다.
  ```


  - **파라미터**

    - 없음



  - **사용예시**

  ```
  $cf oauth-token
  ```

## <div id='ID-ADD-REMOVE-PLUGIN-REPOSITORY'/>  ADD/REMOVE PLUGIN REPOSITORY


#### add-plugin-repo

  - **기본 Syntax**


  ```
  $ cf add-plugin-repo <REPO_NAME> <URL>
  ```


  - **설명**


  ```
  OpenPaaS CLI plugin repository(저장소)를 추가합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |REPO_NAME    |Repository 명                   |X          |
  |URL          |Repository URL                 |X          |



  - **사용예시**

  ```
  cf add-plugin-repo Diego-SSH http://plugins.cloudfoundry.org
  ```


#### remove-plugin-repo

  - **기본 Syntax**


  ```
  $ cf remove-plugin-repo <REPO_NAME> <URL>
  ```


  - **설명**


  ```
  CLI plugin repository(저장소)를 삭제합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |REPO_NAME    |Repository 명                   |O          |
  |URL          |Repository URL                 |O         |



  - **사용예시**

  ```
  cf remove-plugin-repo Diego-SSH http://plugins.cloudfoundry.org
  ```


#### list-plugin-repos

  - **기본 Syntax**


  ```
  $ cf list-plugin-repos
  ```


  - **설명**


  ```
  CLI에 추가된 plugin repository(저장소)목록을 조회합니다.
  ```


  - **파라미터**

    - 없음


  - **사용예시**

  ```
  $cf list-plugin-repos
  ```


#### repo-plugins

  - **기본 Syntax**


  ```
  $ cf repo-plugins [-r REPO_NAME]
  ```


  - **설명**


  ```
  Repository에 있는 플러그인 목록을 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |REPO_NAME    |Repository 명                   |X          |



  - **사용예시**

  ```
  $ cf repo-plugins
  ```

## <div id='ID-ADD-REMOVE-PLUGIN'/> ADD/REMOVE PLUGIN


#### plugins

  - **기본 Syntax**


  ```
  $ cf plugins
  ```


  - **설명**


  ```
  추가된 plugin의 사용가능한 명령어 목록을 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |REPO_NAME    |Repository 명                   |X          |



  - **사용예시**

  ```
  $ cf repo-plugins
  ```



#### install-plugin

  - **기본 Syntax**


  ```
  $ cf install-plugin < URL or LOCAL-PATH/TO/PLUGIN> [-r REPO_NAME]
  ```


  - **설명**


  ```
  추가된 plugin의 사용가능한 명령어 목록을 조회합니다.
  ```


  - **파라미터**


  | 파라미터명   |           설명                 | 필수(O/X) |
  |-------------|--------------------------------|-----------|
  |URL or LOCAL-PATH/TO/PLUGIN   |Plugin URL 또는 로컬경로 또는 repository에 있는 플러그인명                |X          |
  |-r REPO_NAME                  |Plugin repository명                                                   |X          |


  - **사용예시**

  ```
  $cf install-plugin 'Usage Report' -r CF-Community
  ```


----------
  
  
### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > cf CLI
