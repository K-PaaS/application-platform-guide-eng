### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Python Development

## Table of Contents

1. [Outline](#1)	
	-	[1.1. Document Outline](#1-1)  
		-	[1.1.1.	Purpose](#1-1-1)  
		-	[1.1.2.	Range](#1-1-2)  
		-	[1.1.3.	References](#1-1-3)  

2. [python Application Development Guide](#2)  

	-	[2.1. Outline](#2-1)  
	-	[2.2. Development Environment Configuration](#2-2)  
		-	[2.2.1.	python Installation](#2-2-1)  
		-	[2.2.2.	python Virtual Environment Configuration](#2-2-2)  
		-	[2.2.3.	Django Installation](#2-2-3)  

	-	[2.3. Development2-3)  
		-	[2.3.1.	Create django Application](#2-3-1)  
		-	[2.3.2.	Application Environment Setting](#2-3-2)  
		-	[2.3.3.	VCAP_SERVICES Environment Setting Information](#2-3-3)  
		-	[2.3.4.	Connect Mysql](#2-3-4)  
		-	[2.3.5.	Connect Cubrid](#2-3-5)  
		-	[2.3.6.	Connect MongoDB](#2-3-6)  
		-	[2.3.7.	Connect Redis](#2-7-7)  
		-	[2.3.8.	Connect RabbitMQ](#2-3-8)  
		-	[2.3.9.	Connect GlusterFS ](#2-3-9)  

	-	[2.4. Deployment](#2-4) 
	-	-	[2.4.1. Download the Completed Sample Application](#2-4-1)
		-	[2.4.2.	Log in to Open Cloud Platform](#2-4-2) 
		-	[2.4.3.	Create Service](#2-4-3) 
		-	[2.4.4.	Application Deployment](#2-4-4) 
		-	[2.4.5.	Bind Service](#2-4-5) 
		-	[2.4.6.	Execute Application](#2-4-6) 

	-	[2.5. Test](#2-5)  
	
3.	[Supplement](#3)  
	-	[3.1. Eclipse Development Environment Setting](#3-1)  
	-	[3.2. Eclipse python Setting](#3-2)  
		-	[3.2.1.	PyDev Installation](#3-2-1)  
		-	[3.2.2.	python Setting](#3-2-2)  
		-	[3.2.3.	Create django Project](#3-2-3)  
		-	[3.2.4.	Create django Application](#3-2-4)  


# <div id='1'></div> 1. Outline
## <div id='1-1'></div> 1.1. Document Outline  

##### <div id='1-1-1'></div> 1.1.1 Purpose  
This document (python Application Development Guide)presents the service packs of Open PaaS projects (Mysql, Cubrid, MongoDB, RabbitMQ, Redis, and GlusterFS) in connection with python applications to use services and deploy applications.

##### <div id='1-1-2'></div> 1.1.2 Range  
The range of this document is limited to the python application development and service pack linkage of Open PaaS projects.

##### <div id='1-1-3'></div>  1.1.3 References  
[**http://docs.run.pivotal.io/buildpacks/python/index.html**](http://docs.run.pivotal.io/buildpacks/python/index.html) <br>
[**http://www.cubrid.com/manual/93/api/python.html**](http://www.cubrid.com/manual/93/api/python.html) <br>
[**https://docs.djangoproject.com/en/1.9/intro/tutorial01**](https://docs.djangoproject.com/en/1.9/intro/tutorial01) <br>
[**http://pythontips.com/**](http://pythontips.com/) <br>

# <div id='2'></div> 2.python Application Development Guide  

## <div id='2-1'></div> 2.1.	Outline
Various service packs registered in Open PaaS are bound to applications written in Python language, and access information for each service is obtained from environmental information (VCAP_SERVICES) bound to the application to be applied to the application so that python applications can be created in a Windows environment.  

## <div id='2-2'></div>  2.2.	Development Environment Configuration  
The environment in which python sample application development was made is as follows.

* OS : Windows 8 64bit
* python : 2.7.10
* Framwork : Django 1.8.6   

##### <div id='2-2-1'></div>  2.2.1 python Installation

* python 2.7.10 Download 

[**https://www.python.org/downloads/release/python-2710/**](https://www.python.org/downloads/release/python-2710/)
	
![python-2]

* Download

Windows x86-64 MSI installer
 ※ The installation file may vary depending on the environment of each user.

* python Installation 

 Double-click downloaded python-2.7.10.msi to run the installation.
 ※ When installing python, you can select an option to automatically add environment variables.

* Environment Variable Setting
 If the option to add environment variables is not selected when installing Python, it is added directly to the system variable Path as follows. 
;C:\Python27;C:\Python27\Scripts

* python installation check
 run python at the command prompt
 At the command prompt, type 'python' to confirm the execution of python.
`Python`

![python-3]  

* End Python Run 
 Since it is confirmed that Python has been executed normally, enter 'ctrl'+'c' to terminate Python. 

![python-4]  

* pip installation check  
 Enter pip at the command prompt
 If the pip is installed normally, you can check the description of the pip command by entering the command. 

`pip`

![python-5]  

※ pip is a tool that supports the installation of Python-related packages. In general, installing python will also install pip. But in some cases, pip installation may not be possible. In this case, install pip using easy_install, another installation tool that is provided as a basic installation for python installation.

※ pip installation using easy_install
 At the command prompt, navigate to the Scripts directory in the python installation path. 
 
` cd C:\Python27\Scripts`

 Enter pip installation command.

` easy_install pip` 

 ##### <div id='2-2-2'><div> 2-2-2. python Virtual Environment Configuration

 To construct an independent Python development environment, install and use virtualenv which is a Python virtual environment creation tool. Perform package installation in a virtual environment. The virtual environment configuration may be omitted according to the user's needs.

* Installation of Virtual Environment Tool
 virtualenv installation

` pip install virtualenv`

* Go to the directory where you want to create the virtual environment and create the virtual environment 

`cd c:\`

`virtualenv my_virtual_env`


※ If different versions of python are installed, you can select the python to be used for configuring the virtual environment by specifying the path of the python with the '-p' option when creating the virtual environment. Below are the examples.

`virtualenv -p C:\Python34\python.exe my_virtual_env_34`

`virtualenv -p C:\Python27\python.exe my_virtual_env_27`

* Run Virtual Environment

 Use the command below at the command prompt to run the virtual environment.
 
`my_virtual_env\Scripts\activate`

 If the virtual environment runs normally, the command line is named after the command prompt as shown below.

![python-6]
  
 ※ To terminate the execution of the virtual environment, a deactivate command is used. When the virtual environment ends, the virtual environment name in the command input line is removed

`deactivate`

![python-7]

##### <div id='2-2-3'></div> 2.2.3. Django Installation
 Since the sample application was developed by applying the Django framework, Django is installed for application creation. The version of django to install is 1.8.6. use pip to install django.

`pip install Django==1.8.6`

![python-8]

 ※ A user who wants to install a warehouse in a virtual environment inputs a command while executing the virtual environment.

## <div id='2-2'></div> 2.3. Development
Because data management for sample applications uses either MySQL, CubridDB, or MongoDB, it is determined by the DBType value of the body of the request upon API request.

※Sample Application Download
The completed sample application can be downloaded from the /OpenPaas-Sample/python-sample-app link below.
<br>
Sample-App: [https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download](https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download)


##### <div id='2-3-1'></div> 2.3.1. Create django Application

* Create django Project 

 Go to the directory where you want to create the django project and enter the command below to create the sample application project.
※ Users who configure a virtual environment and install Django in the virtual environment enter all commands while executing the virtual environment. Refer to [2.2.2. python Virtual Environment Configuration] (#2-2-2) in this document for configuration and execution of the virtual environment.

`django-admin startproject my_sampleproject`

※ When creating the django project, '_'(underscore) was used because the project name cannot include '-'(hyphen).

The file is created in the following structure. 
 
![python-9] 

<table>
	<tr>
		<td>manage.py</td>
		<td>Files that support site management. It supports functions such as server startup and app creation. </td>
	</tr>
	<tr>
		<td>setting.py</td>
		<td>Manage settings for the site. The Python sample app in this document includes interworking settings for all services.</td>
	</tr>
	<tr>
		<td>urls.py</td>
		<td>Modules defining URL pattern lists.</td>
	</tr>
	<tr>
		<td>wsgi.py</td> <td>Modules defining WSGI applications.</td>
	</tr>
	<tr>
		<td>__init__.py</td>
		<td>Defines the script that runs when the package is imported. If the __init__.py file is deleted, the submodule in the directory cannot be found and the import fails.</td>
	</tr>
	<tr>
</table>

* Create django Application 

 If you created the django project, go to the project directory you created and create the application. The application generation command is as follows.

Go to Project Directory

`cd my_sampleproject`

Create Application

`python manage.py startapp my_sampleapp`

※ When creating the django application, '_' (underscore) was used because the application name cannot contain '-' (hyphen).

By looking at the structure of the project's file, you can see that a red boxed portion has been added

![python-10] 

<table>
	<tr>
		<td>admin.py</td>
		<td>A necessary module to use the database manager dashboard provided by django. The Sample app does not use the database management function so it may be deleted.</td>
	</tr>
	<tr>
		<td>model.py</td>
		<td>A module that defines data for object relationship mapping (ORM) in django. The sample app does not use this so it may be deleted.</td>
	</tr>
	<tr>
		<td>tests.py</td>
		<td>Test Module of django. The sample app does not use this so it may be deleted.</td>
	</tr>
	<tr>
		<td>views.py</td>
		<td>Module that acts as controller of MVC pattern. In django, view functions as controller.</td>
	</tr>
	<tr>
		<td>__init__.py</td>
		<td>Defines the script that runs when the package is imported. If the __init__.py file is deleted, the submodule in the directory cannot be found and the import fails.	</td>
	</tr>
</table>

##### <div id='2-3-2'></div> 2.3.2. 	Application Environment Setting

 In the django application, the configuration is to be defined in the settings module. The settings module refers to the settings.py file in the my_sampleproject directory automatically generated by project creation in [2.3.1.1. Create django Project] (#2-3-3-1). To use the package used by the sample application in the django application, you must add or modify settings in this settings module. It is described and explained below the parts to add or modify the settings module.

※  The code to which the part marked + is to be added, and the part marked - is to be deleted.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    +'rest_framework',
    +'my_sampleapp',
)

 ※	Add django rest framework package at INSTALLED APPS to use.
 ※	Add the application created from [2.3.1.2. Create django Application](#2-3-1-2) at the INSTALLED APPS.

-TEMPLATES = [ Skip ... ]  #modify as follows
+TEMPLATES = [
+    {
+        'BACKEND': 'django.template.backends.django.DjangoTemplates',
+        'DIRS': [os.path.join(BASE_DIR, 'static/templates'), ],
+    }, 
+    {
+        'BACKEND': 'django.template.backends.jinja2.Jinja2',
+        'DIRS': [os.path.join(BASE_DIR, 'static/jinja2'), ],
+    },
+]
#	Modify the existing TEMPLATES to the part marked with light green shading.
#	Modify as shown above to read the template located at the ..\my_sampleproject\static\template directory with django's default template engine and template located at ..\my_sampleproject\static\jinja2 directory with jinja2 engine. The jinja2 engine uses some syntaxes written in the template main.html and manage.html because django's default template engine cannot read them correctly.

- TIME_ZONE = 'UTC'  #Modify as shown
+ TIME_ZONE = ' Asia/Seoul'
#	Change the application's time set to 'Asia/Seoul'

+STATIC_BASE_DIR = os.path.dirname(os.path.abspath(__file__))
+STATICFILES_DIRS = (
+    os.path.join(STATIC_BASE_DIR, '../static/resources'),
+)
+STATIC_ROOT = 'staticfiles' 
#	STATIC_BASE_DIR refers to the path of the application. If you followed the instructions in this document, the path of the application becomes '..\my_sampleproject\my_sampleapp'. When deploying applications on an open cloud platform, files located in the static/resources path, the parent directory of STATIC_BASE_DIR, are collected in the 'static files' directory defined as STATIC_ROOT.

-STATIC_URL = '/static/'  #Modify as shown
+STATIC_URL = '/resources/'
#	URL to access the resources used by the template of the sample

※ The settings module should include code to obtain VCAP_SERVICES information that can be linked to each service, such as DATABASES settings and cache settings, but is not covered in this chapter, as details are in [2.3.4.] ~ [2.3.9] below.
```

Modify the WSGI module to enable WhiteNoise. 

`..\my_sampleproject\my_sampleproject\wsgi.py`

```yml
-application = get_wsgi_application()
+from whitenoise.django import DjangoWhiteNoise
+application = DjangoWhiteNoise(get_wsgi_application())
```

##### <div id='2-3-3'></div> 2.3.3. VCAP_SERVICES Environment Setting Information 

To obtain access information for each service to which an application distributed on an open platform is bound, the VCAP_SERVICES configuration information registered in each application must be read to obtain information.

* Open Platform's Application Environment Information
  When the service is bound, configuration information is registered by the application in the form of JSON.

```json
{
 "VCAP_SERVICES": {
  "CubridDB": [
   {
    "credentials": {
     "host": "10.30.60.23::",
     "hostname": "10.30.60.23",
     "jdbcurl": "jdbc:cubrid:10.30.60.23::fccf1d7869ff72ce:d90e3b43e151bc27:a7a7                                           cdfec36147bc:",
     "name": "fccf1d7869ff72ce",
     "password": "a7a7cdfec36147bc",
     "port": "",
     "uri": "cubrid:10.30.60.23::fccf1d7869ff72ce:d90e3b43e151bc27:a7a7cdfec3614                                           7bc:",
     "username": "d90e3b43e151bc27"
    },
    "label": "CubridDB",
    "name": "sample-cubrid-instance",
    "plan": "utf8",
    "tags": [
     "cubrid",
     "document"
    ]
   }
  ],
  "Mongo-DB": [
   {
    "credentials": {
     "hosts": [
      "10.30.60.53:27017"
     ],
     "name": "e37e541c-75de-4f01-8196-63e2d902e768",
     "password": "c5649c42-ca5e-42be-926a-c3231aa81dc1",
     "uri": "mongodb://b5d67268-897
(Skip)
```

##### <div id='2-3-4'></div> 2.3.4. Connect Mysql
 
 VCAP_SERVICES environment setting information for each service may be obtained from the settings module. The settings module is automatically created when the project is created in [2.3.1.1. Create django Project].In the case of MySQL, the MySQL-python driver supports django integration, so it can be linked by finding the part where the DATABASES information in the settings module is defined and modifying it as follows.

`..\my_sampleproject\my_sampleproject\settings.py` 

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'Mysql-DB' in vcap_services:
        mysql_srv = vcap_services['Mysql-DB'][0]
        mysql_cred = mysql_srv['credentials']

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',  
                'NAME': mysql_cred['name'],  
                'USER': mysql_cred['username'],  
                'PASSWORD': mysql_cred['password'],  
                'HOST': mysql_cred['hostname'],  
                'PORT': mysql_cred['port'],  
            }, 
        }
```
 ※ DataBASES defined MySQL as the 'default' database. In django, 'default' was used instead of 'mysql' because 'default' must exist in the database name. When adding another database, you may use any name for the next database.
 
 <br>
 
 ※ The part written as 'Mysql-DB' is the name of the service served on an open cloud platform and it must match the actual service name exactly. The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.

Import connections from the mysql_views.py module to create cursors in the following form.

`..\my_sampleproject\my_sampleapp\mysql_views.py`

```python
from django.db import connections
```

```python
def make_connection():
    db_type = 'default'
    cursor = connections[db_type].cursor()
    return cursor
```

##### <div id='2-3-5'></div> 2.3.5. Connect Cubrid

 VCAP_SERVICES configuration information for each service may be obtained from the settings module. settings Modules are automatically created when a project is created in [2.3.1.1. Create django Project] (#2-3-1-1). Because the CUBRID-Python driver does not support django integration, the settings module reads the credentials information of the VCAP_SERVICES configuration information from the credits_views module to create a connection.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'CubridDB' in vcap_services:
        cubrid_srv = vcap_services['CubridDB'][0]
        CUBRID_CRED = cubrid_srv['credentials']
``` 

 ※ To retrieve the values defined in django's settings.py module from other modules, they must be defined in English capital letters in the settings.py module.
 <br>
 ※ The part written with 'CubridDB' is the name of the service served on an open cloud platform and it must match the actual service name exactly.  The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.

`..\my_sampleproject\my_sampleapp\cubrid_views.py`

```python
from django.conf import settings
import CUBRIDdb

def make_connection():
    if settings.CUBRID_CRED:
        credentials = settings.CUBRID_CRED
        connection = CUBRIDdb.connect(
            'CUBRID:'+credentials['hostname']+':33000:'+credentials['name']+':::',
            credentials['username'],
            credentials['password']
            )
        return connection

```

##### <div id='2-3-6'></div> 2.3.6. Connect MongoDB

 VCAP_SERVICES configuration information for each service may be obtained from the settings module. The settings module is automatically created when the project is created in [2.3.1.1. Create django Project] (#2-3-1-1). Because the pymongo driver does not support django interworking, the credentials information of VCAP_SERVICES configuration information obtained from the settings module is read from the mongo_views module to generate db.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])
    if 'Mongo-DB' in vcap_services:
        mongo_srv = vcap_services['Mongo-DB'][0]
        MONGO_CRED = mongo_srv['credentials']
```

 ※ To retrieve the value defined in the settings module of django from another module, it must be defined in English capital letters in the settings module.
 <br>
 ※ The part written with 'Mongo-DB' is the name of the service served on an opencloud platform and it must match the actual service name exactly.  The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.

`..\my_sampleproject\my_sampleapp\mongo_views.py`

```python
from django.conf import settings
from pymongo import MongoClient
```

```python
def make_connection():
    if settings.MONGO_CRED:
        credentials = settings.MONGO_CRED
        mongo_hosts = credentials['hosts'][0].split(':')
        connection = MongoClient(mongo_hosts[0], int(mongo_hosts[1]))
        db = connection[credentials['name']]
        db.authenticate(credentials['username'], credentials['password'])
        return db
```

##### <div id='2-3-7'></div> 2.3.7. Connect Redis

 VCAP_SERVICES configuration information for each service may be obtained from the settings module. The settings module is automatically created when the project is created in [2.3.1.1. Create django Project] (#2-3-1-1). In the case of redis, the django-redis-cache driver supports the django linkage, so it can be linked by finding the part where the CACHES information in the settings module is defined and modifying it as follows.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:  
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'redis-sb' in vcap_services:
        redis_srv = vcap_services['redis-sb'][0]
        redis_cred = redis_srv['credentials']
        
        CACHES = {
            "default": {
                "BACKEND": "redis_cache.RedisCache",
                "LOCATION": str(redis_cred['host'])+":"+str(redis_cred['port']),
                "OPTIONS": {
                    "CLIENT_CLASS": "django_redis.client.DefaultClient",
                    'PASSWORD': redis_cred['password'],
                },
            },
        }

```

 <br>
 ※ The part written with 'redis-sb' is the name of the service served on an opencloud platform and it must match the actual service name exactly.  The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.

`..\my_sampleproject\my_sampleapp\redis_views.py`

```python
from django.core.cache import cache
```

 ※ When the cache is imported, you can use it immediately in the following form.
 
 ```python
cache.get('key value')
cache.set('key value','value value')
cache.delete('key value')
```

##### <div id='2-3-8'></div> 2.3.8. Connect RabbitMQ

 VCAP_SERVICES configuration information for each service may be obtained from the settings module. The settings module is automatically created when the project is created in [2.3.1.1. Create django Project] (#2-3-1-1). The credentials information of the VCAP_SERVICES configuration information obtained from the settings module is read from the rabbitmq_views module to create a connection.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'p-rabbitmq' in vcap_services:
        rabbitmq_srv = vcap_services['p-rabbitmq'][0]
        RABBITMQ_CRED = rabbitmq_srv['credentials']
```        
        
 ※ To retrieve the value defined in the settings module of django from another module, it must be defined in English capital letters in the settings module.
 <br>
 ※ The part written with 'p-rabbitmq' is the name of the service served on an opencloud platform and it must match the actual service name exactly.  The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.


`..\my_sampleproject\my_sampleapp\rabbitmq_views.py`

```python
from django.conf import settings
import pika
```

```python
def make_connection():
    if settings.RABBITMQ_CRED:
        credentials = settings.RABBITMQ_CRED
        rabbitmq_protocols = credentials['protocols']
        rabbitmq_uri = rabbitmq_protocols['amqp+ssl']['uri']
        parameters = pika.URLParameters(rabbitmq_uri)
        connection = pika.BlockingConnection(parameters)
        return connection
```

##### <div id='2-3-9'></div> 2.3.9. Connect GlusterFS 

VCAP_SERVICES configuration information for each service may be obtained from the settings module. The settings module is automatically created when the project is created in [2.3.1.1. Create django Project] (#2-3-1-1). The credentials information of VCAP_SERVICES configuration information obtained from the settings module is read from the cluster_views module to create a connection.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'glusterfs' in vcap_services:
        gluster_srv = vcap_services['glusterfs'][0]
        GLUSTERFS_CRED = gluster_srv['credentials']
```

 ※ To retrieve the value defined in the settings module of django from another module, the value must be defined in English capital letters in the settings module.
 <br>
 ※ The part written with 'glusterfs' is the name of the service served on an opencloud platform and it must match the actual service name exactly.  The service name can be checked by entering the 'cf marketplace' command on an open cloud platform.

`..\my_sampleproject\my_sampleapp\gluster_views.py`

```python
from django.conf import settings
import swiftclient
```

```python
def make_connection():
    if settings.GLUSTERFS_CRED:
        credentials = settings.GLUSTERFS_CRED
        auth_url = credentials['auth_url']
        username = credentials['username']
        password = credentials['password']
        tenantname = credentials['tenantname']
        connection = swiftclient.client.Connection(
            auth_version='2',
            authurl=auth_url,
            user=username,
            key=password,
            tenant_name=tenantname
            )
        return connection
```

## <div id='2-4'></div> 2.4. Deployment

 This chapter describes how to deploy developed applications to opencloud platforms. The deployment process consists of opencloud platform login, service creation, application deployment, service binding, and application execution.

##### <div id='2-4-1'></div> 2.4.1. Download the Completed Sample Application
After the application development process described in [2.3. Development], the completed sample application can be downloaded from the link below. If you download the entire Python-sample-app directory from the link below, you can use the completed sample application to proceed with the [2.4. Deployment] process.  
Sample-App: [https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download](https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download)

##### <div id='2-4-2'></div> 2.4.2. Log in to Open Cloud Platform

 Log in with a user account on an opencloud platform to proceed with the application deployment process.
Specify the target before logging in. The target designation command is as follows. 

 `# cf api [target URL]`
 
 `$ cf api --skip-ssl-validation https://api.cf.open-paas.com`
 
Once the target has been designated, log in via the login command.

`# cf login –u [user name] –o [org name] –s [space name]`

`$ cf login -u testUser -o sample_test -s sample_space`

##### <div id='2-4-3'></div> 2.4.3. Create Service

 Service creation is the process by which a user creates a service instance for a service provided by an open cloud platform. It is impossible to create a service for services that are not provided by an open cloud platform, and the platform administrator (operator) decides whether to provide the service.

 First, check the list of available services through the command below.

`$ cf marketplace`

![pyhthon-11]

 The service to be used is generated from the list of services identified through the command above. The sample application uses MySQL, Cubrid, MongoDB, Redis, RabbitMQ, and ClusterFS services, creating six services. The service creation command is as follows. The part marked in the blue box in the picture above is the service name.

```
 # cf create-service SERVICE PLAN SERVICE_INSTANCE [-c PARAMETERS_AS_JSON] [-t TAGS]

예시)
 $ cf create-service Mysql-DB Mysql-Plan1-10con python-mysql
 $ cf create-service CubridDB utf8 python-cubrid
 $ cf create-service Mongo-DB default-plan python-mongodb
 $ cf create-service redis-sb shared-vm python-redis
 $ cf create-service glusterfs glusterfs-5Mb python-glusterfs
 $ cf create-service p-rabbitmq standard python-rabbitmq 
```

 ※The cf create-service command is to enter the service name, plan, and service instance name in order. The service name and plan are checked through the cf marketplace command, and the service instance names are random.

##### <div id='2-4-4'></div> 2.4.4. Application Deployment

* Create requirements.txt

 The requirements.txt file defines the packages required to run the python sample application. When an application is deployed on an opencloud platform, packages defined in the requirements.txt file are installed. Therefore, if the requirements.txt file does not exist or if the content is incorrect, there will be a problem in executing the application. 

`..\my_sampleproject(python-sample-app)\requirements.txt`

```yml
Django==1.8.6
djangorestframework==3.3.1
gunicorn==19.1.1
jinja2==2.8
WhiteNoise==2.0.4
MySQL-python==1.2.3
CUBRID-Python==9.3.0.0001
pymongo==2.8
pika==0.10.0
django-redis-cache==1.6.4
python-keystoneclient==2.0.0
python-swiftclient==2.6.0
```

See the table below for a description of each package.
<table>
<tr>
<th>Package Name</th><th>Version</th><th>Description</th>
</tr>
<tr><td>Django</td><td>1.8.6</td><td>python Web Framework</td>
</tr>
<tr><td>djangorestframework</td><td>3.3.1</td><td>django-based web api implementation framework</td>
</tr>
<tr><td>Gunicorn</td><td>19.1.1</td><td>WSGI server for running applications in UNIX environments; required for deployment on open cloud platforms.</td>
</tr>
<tr><td>jinja2</td><td>2.8</td><td>django template engine. Use some syntaxes from the sample application template because django's default template engine cannot read properly.</td>
</tr>
<tr><td>WhiteNoise</td><td>2.0.4</td><td>Used by Python web applications to provide static files</td>
</tr>
<tr><td>MySQL-python</td><td>1.2.3</td><td>Driver to link mysql with Django application</td>
</tr>
<tr><td>CUBRID-Python</td><td>>9.3.0.0001</td><td>python과 Cubrid를 연동하는 드라이버</td>
</tr>
<tr><td>pymongo</td><td>2.8</td><td>Driver to link python with Mongodb</td>
</tr>
<tr><td>pika</td><td>0.10.0</td><td>Driver to link python with RabbitMQ</td>
</tr>
<tr><td>django-redis-cache</td><td>1.6.4</td><td>A driver to link django application with Redis </td>
</tr>
<tr>
<td>python-keystoneclient</td><td>2.0.0.</td><td>Clients for using Openstack's authentication API.<br>Required for GlusterFS linking</td>
<tr>
<td>python-swiftclient</td><td>2.6.0</td><td>Openstack's swift client 
Required for GlusterFS linking</td>
</tr>
</table> 

* Create manifest.yml 

 When deploying an application, it is deployed by referring to the manifest.yml file.

`..\my_sampleproject(python-sample-app)\manifest.yml`

```yml
---
applications:
- name: python-sample-app      # Application Name
  buildpack: python_buildpack    # Buildpack Name
  memory: 512M                # Application Memory Size
  instances: 1                   # Number of Application Instances
```

 ※	The manifest.yml file may contain more settings, but only the necessary parts are described.
 
 * Application Deployment

 The command for application deployment is as shown below. It uses the settings defined in manifest.yml unless there is an option given. Because the application is deployed with the service not bound to the application, the --no-start option is inserted to prevent the application from running.  

`cf push --no-start`

##### <div id='2-4-5'></div> 2.4.5. Service Bind

The connection between a service created by [2.4.3. Create Service] (#2-4-3) and an application distributed by [2.4.4.4. Application Deployment] (#2-4-4) is called a service bind. Through the service bind, the application may obtain VCAP_SERVICES configuration information for accessing the service, which may be confirmed in [2.3.3. VCAP_SERVICES Environment Setting Information] (#2-3-3). 

```
# cf bind-service APP_NAME SERVICE_INSTANCE [-c PARAMETERS_AS_JSON]
$ cf bind-service python-sample-app python-mysql
$ cf bind-service python-sample-app python-cubrid
$ cf bind-service python-sample-app python-mongodb
$ cf bind-service python-sample-app python-redis
$ cf bind-service python-sample-app python-glusterfs
$ cf bind-service python-sample-app python-rabbitmq
```

 ※ The cf bind-service command is used by entering the application name to be bound and the service instance name in order. At this time, additional information may be included in the VCAP_SERVICES environment setting information of the application using the '-c' option. 

##### <div id='2-4-6'></div> 2.4.6. Execute Application

If the service binding is completed, the application can be executed normally. The application execution command is as follows.

`$ cf start python-sample-app`

 ※MySQL and Cubrid services are available only when users directly access DB and create tables first.

### <div id='2-5'></div> 2.5.	Test 

 The python sample application is implemented as a REST service and uses the unit test module, a module built into python by default, for REST testing. Although there is a test function of django, it is not suitable for use in a sample application, so a test module was created to conduct a REST test. In addition, a nose-html-report was installed to store the test results as an HTML file.

* Execute Test

 Use the following command in the directory where the test module is located.

`python tests.py`

When the test is executed, a screen below will appear.

![python-12]

 * When saving the test results as an html file

 Install nose-html-report to save test results as an HTML file. Use pip for installation.
 
`pip install nose-html-reporting`

The command to execute the test is as follows.

`nosetests tests.py --with-html`

When the test is complete, you can see that the nosetests.html file is created.

![python-13]

The generated nosetests.html file outputs the following screen. You can check the details by pressing the Detail button.
 
![python-14]
![python-15]

# <div id='3'></div> 3. Supplement

## <div id='3-1'></div> 3.1.	Eclipse Development Environment Setting

 This chapter guides you through the use of eclipse as a development tool in the [2.3. Development] process of this document. This chapter is described on the premise that the user has completed [2.2.3. Django Installation] of this document.

The Eclipse development environment was made in the following environment.

* OS : Windows 8 64bit 
* python : 2.7.10 
* Framework : Django 1.8.6 
* Eclipse: version Mars.1 Release (4.5.1)

Eclipse download uses the following link. Download Eclipse with the appropriate environment. 

[**http://www.eclipse.org/downloads/**](http://www.eclipse.org/downloads/)

When the download is complete, unzip and run Eclipse.

## <div id='3-2'></div> 3.2.	Eclipse python Setting

##### <div id='3-2-1'></div> 3.2.1.PyDev Installation

 PyDev is an Eclipse plug-in that enables python development in Eclipse. 
 
![python-16]
 
When this clip is enabled, click Help - Eclipse Marketplace on the top menu bar.

![python-17]

Search for 'pydev' at the marketplace.

![python-18]

Find 'PyDev' in the search results and click the 'install' button.

![python-19]
![python-20]

When the installation begins, the user is asked to confirm and agree to a few things.
Click the 'Confirm' button and proceed to the next step.

![python-21]
 
As shown in number①, accept the copyright clause and click the 'finish' button.
 
![python-22]
 
Check the ①checkbox and click the 'ok' button. If the checkbox is not checked in this procedure, the PyDev Installation gets canceled.


![python-22]
 

When the installation is completed, Eclipse must be restarted. Click the 'Yes' button.


![python-23]
 

##### <div id='3-2-2'></div> 3.2.2. python Setting


![python-23]

 Click Windows - Preferences at the top of the rerun eclipse to open the settings screen. 
 
![python-24]
 
 As shown in the picture, click ①,②, and ③ in order and open the Python Interpreters settings screen. Click on ④ Quick Auto-Config to see the Python version installed like the blue box in the center. When python is installed, python information may not be displayed properly if environmental variables are not set. 

 ※For users who configure a virtual environment and install a package follow the following guidelines.

![python-25]

 Click the 'New' button to add Virtual Environment. 

![python-26]

Enter the Virtual Environment's name and click the Browse button.

![python-27]

Set path for the python.exe file of the Virtual Environment.

![python-28]

Complete adding Interpreter by clicking 'ok'.

![python-29]

If the Interpreter was successfully added, click 'ok' and complete the setting. 

##### <div id='3-2-3'></div> 3.2.3. Create django Project
 
![python-30]
 
Click File - New - Other at the top of the screen to run the Create Project window.

![python-31]

Click ①PyDev to open the bottom menu, select ② PyDev Django Project from the menu, and press the 'Next' button to proceed to the next step.

![python-32] 

 Enter the project Name where number ① is located. It should be noted that django replaces the project name with '_' (underscore) because it cannot contain '-' (hyphen) in the name of the project, just like when using the command. Select Interpreter as shown in number ② box. The user configured with the virtual environment selects the virtual environment. Then press the 'Next' button to proceed to the next step.

![python-33] 

 The sample application does not use django's Object Relational Mapping (ORM) function, so it does not set up the database. Delete the existing value as shown in the figure and press the 'Finish' button to complete the project creation.

##### <div id=3-2-4></div> 3.2.4.	Create django Application

![python-34]

 You can find projects created by PyDev Package Explorer on the left side of the Eclipse screen. Right-click the created project to open the menu. Find the Django menu from the menu and select 'Create an application (manage.py start app)'. 
 
![python-35] 

A window for entering the application name is created. The application name is also replaced with '_' (underscore) because it cannot contain '-' (hyphen) like the project. If you enter the 'ok' button, the django application creation is completed.

![python-36] 




[python-2]:./images/python/image2.png
[python-3]:./images/python/image3.png
[python-4]:./images/python/image4.png
[python-5]:./images/python/image5.png
[python-6]:./images/python/image6.png
[python-7]:./images/python/image7.png
[python-8]:./images/python/image8.png
[python-9]:./images/python/image9.png
[python-10]:./images/python/image10.png
[python-11]:./images/python/image11.png
[python-12]:./images/python/image12.png
[python-13]:./images/python/image13.png
[python-14]:./images/python/image14.png
[python-15]:./images/python/image15.png
[python-16]:./images/python/image16.png
[python-17]:./images/python/image17.png
[python-18]:./images/python/image18.png
[python-19]:./images/python/image19.png
[python-20]:./images/python/image20.png
[python-21]:./images/python/image21.png
[python-22]:./images/python/image22.png
[python-23]:./images/python/image23.png
[python-24]:./images/python/image24.png
[python-25]:./images/python/image25.png
[python-26]:./images/python/image26.png
[python-27]:./images/python/image27.png
[python-28]:./images/python/image28.png
[python-29]:./images/python/image29.png
[python-30]:./images/python/image30.png
[python-31]:./images/python/image31.png
[python-32]:./images/python/image32.png
[python-33]:./images/python/image33.png
[python-34]:./images/python/image34.png
[python-35]:./images/python/image35.png
[python-36]:./images/python/image36.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Python Development
