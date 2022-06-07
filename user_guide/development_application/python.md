### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Python Development

## Table of Contents

1. [Outline](#1)	
	-	[1.1. Document Outline](#1-1)  
		-	[1.1.1.	Purpose](#1-1-1)  
		-	[1.1.2.	Range](#1-1-2)  
		-	[1.1.3.	References](#1-1-3)  

2. [python Application Development Guide](#2)  

	-	[2.1. Outline](#2-1)  
	-	[2.2. Development Evnvironment Configuration](#2-2)  
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
This document (python Application Development Guide)presents the service packs of Open PaaS projects (Mysql, Cubrid, MongoDB, RabbitMQ, Redis, and GlusterFS) in connect with python applications to use services and deploy applications.

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
 run python at command prompt
 At the command prompt, type 'python' to confirm the execution of python.
`Python`

![python-3]  

* End Python Run 
 Since it is confirmed that Python has been executed normally, enter 'ctrl'+'c' to terminate Python. 

![python-4]  

* pip installation check  
 Enter pip at command prompt
 If the pip is installed normally, you can check the description of the pip command by entering the command. 

`pip`

![python-5]  

※ pip is a tool that supports the installation of python-related packages. In general, installing python will also install pip. But in some cases, pip installation may not be possible. In this case, install pip using easy_install, another installation tool that is provided as a basic installation for python installation.

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

* Go to the directory where you want to create the virtual environment and create virtual environment 

`cd c:\`

`virtualenv my_virtual_env`


※ If different versions of python are installed, you can select the python to be used for configuring the virtual environment by specifying the path of the python with the '-p' option when creating the virtual environment. Below are the examples.

`virtualenv -p C:\Python34\python.exe my_virtual_env_34`

`virtualenv -p C:\Python27\python.exe my_virtual_env_27`

* Run Virtual Environment

 Use the command below at the command prompt to run virtual environment.
 
`my_virtual_env\Scripts\activate`

 If the virtual environment runs normally, the command line is named after the command prompt as shown on below.

![python-6]
  
 ※ To terminate the execution of the virtual environment, a deactivate command is used. When the virtual environment ends, the virtual environment name in the command input line is removed

`deactivate`

![python-7]

##### <div id='2-2-3'></div> 2.2.3. Django Installation
 Since the sample application was developed by applying the Django framework, Django is installed for application creation. The version of django to install is 1.8.6 . use pip to install django.

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
※ Users who configure a virtual environment and install Django in the virtual environment enter all commands while executing the virtual environment. Refer to [2.2.2.python Virtual Environment Configuration] (#2-2-2) in this document for configuration and execution of the virtual environment.

`django-admin startproject my_sampleproject`

※ When creating the django project, '_'(undercore) was used because the project name cannot include '-'(hyphen).

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
		<td>Defines the script that runs when the package is imported.. If the __init__.py file is deleted, the submodule in the directory cannot be found and the import fails.</td>
	</tr>
	<tr>
</table>

* Create django Application 

 If you created the django project, go to the project directory you created and create the application. The application generation command is as follows.

Go to Project Directory

`cd my_sampleproject`

Create Application

`python manage.py startapp my_sampleapp`

※ When creating django application, '_' (undercore) was used because the application name cannot contain '-' (hyphen).

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
		<td>Defines the script that runs when the package is imported.. If the __init__.py file is deleted, the submodule in the directory cannot be found and the import fails.	</td>
	</tr>
</table>

##### <div id='2-3-2'></div> 2.3.2. 	Application Environment Setting

 In the django application, the configuration is to be defined in the settings module. The settings module refers to the settings.py file in the my_sampleproject directory automatically generated by project creation in [2.3.1.1. Create django Project] (#2-3-3-1). To use the package used by the sample application in the django application, you must add or modify settings in this settings module. It is described and explained below of the parts to add or modify the settings module.

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
#	기존의 TEMPLATES를 연두색 음영으로 표시된 부분으로 수정한다.
#	..\my_sampleproject\static\template 디렉토리에 위치한 템플릿은 django의 기본 템플릿 엔진으로 읽고, ..\my_sampleproject\static\jinja2 디렉토리에 위치한 템플릿은 jinja2 엔진으로 읽기 위해서 위와 같이 수정한다. jinja2 엔진은 템플릿 main.html과 manage.html에 쓰인 일부 신택스를 django의 기본 템플릿 엔진이 정상적으로 읽어낼 수 없기 때문에 사용한다.

- TIME_ZONE = 'UTC'  #다음과 같이 수정
+ TIME_ZONE = ' Asia/Seoul'
#	애플리케이션의 시간 설정을 'Asia/Seoul'로 변경한다

+STATIC_BASE_DIR = os.path.dirname(os.path.abspath(__file__))
+STATICFILES_DIRS = (
+    os.path.join(STATIC_BASE_DIR, '../static/resources'),
+)
+STATIC_ROOT = 'staticfiles' 
#	STATIC_BASE_DIR는 애플리케이션의 경로를 의미한다. 본 문서의 안내대로 진행했다면 애플리케이션의 경로는 '..\my_sampleproject\my_sampleapp'가 된다. 개방형  클라우드 플랫폼에 애플리케이션을 배포할 때, STATIC_BASE_DIR의 상위 디렉토리의 static/resources 경로에 위치한 파일들이 STATIC_ROOT로 정의된 'staticfiles' 디렉토리에 모이게 된다.

-STATIC_URL = '/static/'  #다음과 같이 수정
+STATIC_URL = '/resources/'
#	샘플의 템플릿에서 사용하는 리소스에 접근할 수 있는 URL

※ settings 모듈에 DATABASES 설정과 cache 설정 등, 각각의 서비스와 연동할 수 있는 VCAP_SERVICES 정보를 획득하는 코드가 추가되어야 하지만 하단의 [2.3.4.] ~ [2.3.9]에서 자세히 소개하므로 이 장에서는 다루지 않는다.
```

WhiteNoise를 사용할 수 있도록 wsgi 모듈을 수정한다. 

`..\my_sampleproject\my_sampleproject\wsgi.py`

```yml
-application = get_wsgi_application()
+from whitenoise.django import DjangoWhiteNoise
+application = DjangoWhiteNoise(get_wsgi_application())
```

##### <div id='2-3-3'></div> 2.3.3. VCAP_SERVICES 환경설정 정보 

개방형 플랫폼에 배포되는 애플리케이션이 바인딩 된 각각의 서비스의 접속 정보를 얻기 위해서는 각각의 애플리케이션에 등록되어있는 VCAP_SERVICES 환경설정 정보를 읽어 들여 정보를 획득 해야 한다.

* 개방형 플랫폼의 애플리케이션 환경정보
  서비스를 바인딩하면 JSON 형태로 환경설정 정보가 애플리케이션 별로 등록된다.

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
(이하 생략)
```

##### <div id='2-3-4'></div> 2.3.4. Mysql 연동
 
 각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성]에서 프로젝트 생성시 자동으로 생성된다. MySQL의 경우는 MySQL-python 드라이버가 django 연동을 지원하기 때문에 settings 모듈의 DATABASES 정보가 정의된 부분을 찾아 다음과 같이 수정함으로써 연동이 가능하다.

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
 ※ DATABASES에 'default' 데이터베이스로 MySQL을 정의하였다. django에서는 데이터베이스 명칭에서 'default'는 반드시 존재해야 하기 때문에 'mysql'이 아닌 'default'를 사용하였다. 다른 데이터베이스를 추가하고자 한다면 추가되는 데이터베이스에 대해서는 임의의 명칭을 사용할 수 있다.
 
 <br>
 
 ※ 'Mysql-DB' 라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.

mysql_views.py 모듈에서 connections를 임포트하여 다음과 같은 형태로 cursor를 생성한다.

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

##### <div id='2-3-5'></div> 2.3.5. Cubrid 연동

 각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성](#2-3-1-1) 에서 프로젝트 생성시 자동으로 생성된다. CUBRID-Python 드라이버는 django 연동을 지원하지 않기 때문에 settings 모듈에서 VCAP_SERVICES 환경설정 정보의 credentials 정보를 cubrid_views 모듈에서 읽어와 connection을 생성한다.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'CubridDB' in vcap_services:
        cubrid_srv = vcap_services['CubridDB'][0]
        CUBRID_CRED = cubrid_srv['credentials']
``` 

 ※django의 settings.py 모듈에 정의된 값을 다른 모듈에서 불러오기 위해서는 settings.py 모듈에서 영문 대문자로 정의되어 있어야만 한다.
 <br>
 ※ 'CubridDB'라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.

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

##### <div id='2-3-6'></div> 2.3.6. MongoDB 연동

 각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성]에서 프로젝트 생성시 자동으로 생성된다. pymongo 드라이버는 django 연동을 지원하지 않기 때문에 settings 모듈에서 획득한 VCAP_SERVICES 환경설정 정보의 credentials 정보를 mongo_views 모듈에서 읽어와 db를 생성한다.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])
    if 'Mongo-DB' in vcap_services:
        mongo_srv = vcap_services['Mongo-DB'][0]
        MONGO_CRED = mongo_srv['credentials']
```

 ※ django의 settings 모듈에 정의된 값을 다른 모듈에서 불러오기 위해서는 settings 모듈에서 영문 대문자로 정의되어 있어야만 한다.
 <br>
 ※ 'Mongo-DB'라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.

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

##### <div id='2-3-7'></div> 2.3.7. Redis 연동

 각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성]에서 프로젝트 생성시 자동으로 생성된다. redis의 경우는 django-redis-cache 드라이버가 django 연동을 지원하기 때문에 settings 모듈의 CACHES 정보가 정의된 부분을 찾아 다음과 같이 수정함으로써 연동이 가능하다.

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
 ※ 'redis-sb'라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.

`..\my_sampleproject\my_sampleapp\redis_views.py`

```python
from django.core.cache import cache
```

 ※ cache를 임포트하면 다음과 같은 형태로 바로 사용이 가능하다.
 
 ```python
cache.get('key값')
cache.set('key값','value값')
cache.delete('key값')
```

##### <div id='2-3-8'></div> 2.3.8. RabbitMQ연동

 각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성](#2-3-1-1)에서 프로젝트 생성시 자동으로 생성된다. settings 모듈에서 획득한 VCAP_SERVICES 환경설정 정보의 credentials 정보를 rabbitmq_views 모듈에서 읽어와 connection을 생성한다.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'p-rabbitmq' in vcap_services:
        rabbitmq_srv = vcap_services['p-rabbitmq'][0]
        RABBITMQ_CRED = rabbitmq_srv['credentials']
```        
        
 ※ django의 settings 모듈에 정의된 값을 다른 모듈에서 불러오기 위해서는 settings 모듈에서 영문 대문자로 정의되어 있어야만 한다.
 <br>
 ※ 'p-rabbitmq'라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.


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

##### <div id='2-3-9'></div> 2.3.9. GlusterFS 연동 

각 서비스에 대한 VCAP_SERVICES 환경설정 정보는 settings 모듈에서 획득할 수 있다. settings 모듈은 [2.3.1.1. django 프로젝트 생성]에서 프로젝트 생성시 자동으로 생성된다. settings 모듈에서 획득한 VCAP_SERVICES 환경설정 정보의 credentials 정보를 gluster_views 모듈에서 읽어와 connection을 생성한다.

`..\my_sampleproject\my_sampleproject\settings.py`

```python
import json
if 'VCAP_SERVICES' in os.environ:
    vcap_services = json.loads(os.environ['VCAP_SERVICES'])

    if 'glusterfs' in vcap_services:
        gluster_srv = vcap_services['glusterfs'][0]
        GLUSTERFS_CRED = gluster_srv['credentials']
```

 ※ django의 settings 모듈에 정의된 값을 다른 모듈에서 불러오기 위해서는 해당 값이 settings 모듈에서 영문 대문자로 정의되어 있어야만 한다.
 <br>
 ※ 'glusterfs'라고 기입된 부분은 개방형 클라우드 플랫폼에서 서비스되는 서비스의 이름이다. 실제 서비스 되는 이름과 정확히 일치해야 한다. 개방형 클라우드 플랫폼에서 'cf marketplace' 명령어를 입력하여 서비스명을 확인할 수 있다.

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

## <div id='2-4'></div> 2.4. 배포

 개발 완료된 애플리케이션을 개방형 클라우드 플랫폼에 배포하는 방법을 기술한다. 배포과정은 개방형 클라우드 플랫폼 로그인, 서비스 생성, 애플리케이션 배포, 서비스 바인드, 애플리케이션 실행의 과정으로 이루어져 있다.

##### <div id='2-4-1'></div> 2.4.1. 완성된 샘플 애플리케이션 다운로드
[2.3. 개발]에서 설명한 애플리케이션 개발 과정을 거쳐 완성된 샘플 애플리케이션을 다음의 위치에서 다운로드 할 수 있다. 하단의 링크에서 python-sample-app 디렉토리 전체를 다운로드하면 이미 완성된 샘플 애플리케이션을 이용하여 [2.4. 배포] 과정을 진행 할 수 있다.  
Sample-App: [https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download](https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download)

##### <div id='2-4-2'></div> 2.4.2. 개방형 클라우드 플랫폼 로그인

 애플리케이션 배포과정을 진행하기 위해 개방형 클라우드 플랫폼의 사용자 계정으로 로그인한다.
로그인을 하기 이전에 먼저 target을 지정한다. target 지정 명령어는 아래와 같다. 

 `# cf api [target URL]`
 
 `$ cf api --skip-ssl-validation https://api.cf.open-paas.com`
 
타겟 지정이 완료되었다면, 로그인 명령어를 통해 로그인한다.

`# cf login –u [user name] –o [org name] –s [space name]`

`$ cf login -u testUser -o sample_test -s sample_space`

##### <div id='2-4-3'></div> 2.4.3. 서비스 생성

 서비스 생성은 개방형 클라우드 플랫폼에서 제공하는 서비스에 대해서 사용자가 서비스 인스턴스를 생성하는 과정이다. 개방형 클라우드 플랫폼에서 제공하지 않는 서비스에 대해서는 서비스 생성이 불가능하며, 서비스 제공 여부는 플랫폼 관리자(운영자)가 결정한다.

 먼저 아래의 명령어를 통해, 사용 가능한 서비스의 목록을 확인한다.

`$ cf marketplace`

![pyhthon-11]

 상단의 명령어를 통해 확인한 서비스 목록에서 사용하고자 하는 서비스를 생성한다. 샘플 애플리케이션에서는 MySQL, Cubrid, MongoDB, Resdis, RabbitMQ, GlusterFS 서비스를 사용하므로 6개의 서비스를 생성한다. 서비스 생성 명령어는 다음과 같다. 상단의 그림에서 파란색 박스로 표시된 부분이 서비스 명이다.

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

 ※cf create-service 명령어는 서비스명, 플랜, 서비스 인스턴스명을 순서대로 입력하게 되어 있다. 서비스명과 플랜은 cf marketplace 명령어를 통해 확인하고, 서비스 인스턴스명은 임의의 명칭을 사용한다.

##### <div id='2-4-4'></div> 2.4.4. 애플리케이션 배포

* requirements.txt 생성

 requirements.txt 파일에 python 샘플 애플리케이션 구동에 필요한 패키지들이 정의된다. 개방형 클라우드 플랫폼에서는 애플리케이션이 배포될 때, requirements.txt 파일에 정의된 패키지들을 설치한다. 따라서 requirements.txt 파일이 존재하지 않거나 내용이 잘못 되어 있을 경우, 애플리케이션 실행에 문제가 발생한다. 

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

각각의 패키지에 대한 설명은 하단 표를 참고한다.
<table>
<tr>
<th>패키지명</th><th>버전</th><th>설명</th>
</tr>
<tr><td>Django</td><td>1.8.6</td><td>python 웹 프레임워크</td>
</tr>
<tr><td>djangorestframework</td><td>3.3.1</td><td>django 기반의 웹 api 구현 프레임워크</td>
</tr>
<tr><td>Gunicorn</td><td>19.1.1</td><td>유닉스 환경에서 애플리케이션 실행을 위한 WSGI server. 개방형 클라우드 플랫폼에 배포하기 위해서 필요.</td>
</tr>
<tr><td>jinja2</td><td>2.8</td><td>django template 엔진. 샘플 애플리케이션 template의 일부 syntax를 django의 기본 template 엔진이 정상적으로 읽을 수 없기 때문에 사용.</td>
</tr>
<tr><td>WhiteNoise</td><td>2.0.4</td><td>Python 웹 애플리케이션에서 정적파일(static files)을 제공하기 위해 사용</td>
</tr>
<tr><td>MySQL-python</td><td>1.2.3</td><td>Django 애플리케이션과 mysql을 연동하는 드라이버</td>
</tr>
<tr><td>CUBRID-Python</td><td>>9.3.0.0001</td><td>python과 Cubrid를 연동하는 드라이버</td>
</tr>
<tr><td>pymongo</td><td>2.8</td><td>python과 Mongodb를 연동하는 드라이버</td>
</tr>
<tr><td>pika</td><td>0.10.0</td><td>python과 RabbitMQ를 연동하는 드라이버</td>
</tr>
<tr><td>django-redis-cache</td><td>1.6.4</td><td>django 애플리케이션과 Redis를 연동하는 드라이버 </td>
</tr>
<tr>
<td>python-keystoneclient</td><td>2.0.0.</td><td>Openstack의 인증 API를 사용하기 위한 클라이언트.<br>GlusterFS 연동에 필요</td>
<tr>
<td>python-swiftclient</td><td>2.6.0</td><td>Openstack의 swift 클라이언트 
GlusterFS 연동에 필요</td>
</tr>
</table> 

* manifest.yml 생성 

 애플리케이션 배포 시, manifest.yml 파일을 참조하여 배포가 이루어진다.

`..\my_sampleproject(python-sample-app)\manifest.yml`

```yml
---
applications:
- name: python-sample-app      # 애플리케이션 이름
  buildpack: python_buildpack    # 빌드팩 이름
  memory: 512M                # 애플리케이션 메모리 사이즈
  instances: 1                   # 애플리케이션 인스턴스 개수
```

 ※	manifest.yml 파일은 더 많은 설정을 포함할 수 있지만, 필요한 부분만 기술하였다.
 
 * 애플리케이션 배포

 애플리케이션 배포 명령어는 다음과 같다. 별도의 옵션을 넣지 않으면 manifest.yml에 정의된 설정을 사용한다. 서비스가 애플리케이션에 바인드되지 않은 상태로 애플리케이션이 배포되기 때문에 --no-start 옵션을 넣어 애플리케이션이 실행되지 않도록 한다.  

`cf push --no-start`

##### <div id='2-4-5'></div> 2.4.5. 서비스 바인드

[2.4.3. 서비스 생성](#2-4-3)에서 생성한 서비스와 [2.4.4. 애플리케이션 배포](#2-4-4)에서 배포한 애플리케이션을 연결하는 것을 서비스 바인드(bind)라고 한다. 서비스 바인드를 통해서 애플리케이션은 서비스에 접근할 수 있는 VCAP_SERVICES 환경설정 정보를 얻을 수 있게 되고 이는 [2.3.3. VCAP_SERVICES 환경설정 정보](#2-3-3)에서 확인할 수 있다. 

```
# cf bind-service APP_NAME SERVICE_INSTANCE [-c PARAMETERS_AS_JSON]
$ cf bind-service python-sample-app python-mysql
$ cf bind-service python-sample-app python-cubrid
$ cf bind-service python-sample-app python-mongodb
$ cf bind-service python-sample-app python-redis
$ cf bind-service python-sample-app python-glusterfs
$ cf bind-service python-sample-app python-rabbitmq
```

 ※ cf bind-service 명령어는 바인드할 애플리케이션명과 서비스 인스턴스명을 순서대로 입력하여 사용한다. 이때 '-c' 옵션을 이용해 애플리케이션의 VCAP_SERVICES 환경설정 정보에 추가적인 정보를 담을 수 있다. 

##### <div id='2-4-6'></div> 2.4.6. 애플리케이션 실행

서비스 바인드가 완료되었다면 애플리케이션을 정상적으로 실행시킬 수 있다. 애플리케이션 실행 명령어는 다음과 같다.

`$ cf start python-sample-app`

 ※MySQL과 Cubrid 서비스는 사용자가 직접 DB에 접속하여 테이블을 먼저 생성해야 사용이 가능하다.

### <div id='2-5'></div> 2.5.	테스트 

 python 샘플 애플리케이션은 REST 서비스로 구현되어 있으며, REST 테스트를 위해 python에 기본적으로 내장된 모듈인 unittest 모듈을 사용한다. django의 테스트 기능이 있지만, 샘플 애플리케이션에서 사용하기에는 적절하지 않아 tests 모듈을 생성하여 REST 테스트를 진행하였다. 추가적으로 테스트 결과를 html파일로 저장하기 위해 nose-html-report를 설치하였다.

* 테스트 실행

 테스트 모듈이 위치한 디렉토리에서 다음 명령어를 사용한다.

`python tests.py`

테스트를 실행하면 다음과 같은 화면을 확인할 수 있다.

![python-12]

 * 테스트 결과를 html 파일로 저장할 경우

 테스트 결과를 html 파일로 저장하기 위해서 nose-html-report를 설치한다. 설치는 pip를 이용한다.
 
`pip install nose-html-reporting`

테스트 실행 명령어는 다음과 같다.

`nosetests tests.py --with-html`

테스트가 완료되면 nosetests.html 파일이 생성되는 것을 확인할 수 있다.

![python-13]

생성된 nosetests.html 파일은 다음과 같은 화면을 출력한다. Detail 버튼을 눌러 세부 사항을 확인할 수 있다.
 
![python-14]
![python-15]

# <div id='3'></div> 3. 부록

## <div id='3-1'></div> 3.1.	이클립스 개발환경 설정

 본 장은 본 문서의 [2.3. 개발] 과정에서 이클립스(eclipse)를 개발 도구로 사용할 수 있도록 안내한다. 본 장은 사용자가 본 문서의 [2.2.3. Django 설치]까지 완료하였음을 전제로 기술된다.

이클립스 개발환경 설정은 아래와 같은 환경에서 이루어졌다.

* OS : Windows 8 64bit 
* python : 2.7.10 
* Framwork : Django 1.8.6 
* Eclipse: version Mars.1 Release (4.5.1)

이클립스 다운로드는 하기 링크를 이용한다. 사용자 각각의 환경에 맞는 이클립스를 다운로드한다. 

[**http://www.eclipse.org/downloads/**](http://www.eclipse.org/downloads/)

다운로드가 완료되면 압축을 풀고 이클립스를 실행한다.

## <div id='3-2'></div> 3.2.	이클립스 python 설정

##### <div id='3-2-1'></div> 3.2.1.PyDev 설치

 PyDev는 이클립스에서 python 개발을 가능하게 하는 이클립스 플러그인이다. 
 
![python-16]
 
이클립스가 실행되면 상단 메뉴바의 help - Eclipse Marketplace를 클릭한다.

![python-17]

마켓 플레이스에서 'pydev'로 검색한다.

![python-18]

검색 결과에서 'PyDev'를 찾아 'install' 버튼을 클릭한다.

![python-19]
![python-20]

설치가 시작되면 몇 가지 사항에 대해 사용자에게 확인 및 동의를 요구한다.
'Confirm' 버튼을 클릭하여 다음단계로 진행한다.

![python-21]
 
①번과 같이 저작권 조항에 동의를 표하고 'finish' 버튼을 클릭한다.
 
![python-22]
 
① 체크박스에 체크하고 'ok' 버튼을 클릭한다. 이 과정에서 체크박스에 체크하지 않으면 PyDev설치가 취소된다.


![python-22]
 

설치가 완료되면 이클립스를 재시작해야 한다. 'Yes' 버튼을 선택한다.


![python-23]
 

##### <div id='3-2-2'></div> 3.2.2. python 설정


![python-23]

 재실행된 이클립스에서 상단 Windows - Preferences를 클릭하여 설정화면을 연다. 
 
![python-24]
 
 그림처럼 ①,②,③의 순서로 클릭하여 Python Interpreters 설정 화면을 연다. ④번의 Quick Auto-Config를 클릭하면 중앙의 파란색 박스처럼 설치된 python 버전을 확인할 수 있다. python 설치 시, 환경변수 설정을 하지 않은 경우는 python 정보가 제대로 나타나지 않을 수 있다. 

 ※가상환경을 구성하여 패키지를 설치한 사용자는 다음의 안내를 따른다.

![python-25]

 'New' 버튼을 클릭하여 가상환경을 추가한다. 

![python-26]

가상환경 명칭을 입력하고 Browse 버튼을 클릭한다.

![python-27]

가상환경의 python.exe 파일의 경로를 지정한다.

![python-28]

'ok' 버튼을 클릭해 인터프리터 추가를 완료한다.

![python-29]

인터프리터를 추가했다면 'ok' 버튼을 눌러 설정을 완료한다. 

##### <div id='3-2-3'></div> 3.2.3. django 프로젝트 생성
 
![python-30]
 
화면 상단의 File - New - Other를 클릭하여 프로젝트 생성 창을 실행한다.

![python-31]

①번과 같이 PyDev를 클릭해 하단 메뉴를 열고 메뉴 중 ② PyDev Django Project를 선택하고 'Next '버튼을 눌러 다음단계로 진행한다.

![python-32] 

 ①번 위치에 프로젝트 이름을 입력한다. 주의할 점은 명령어를 사용할 때와 마찬가지로 django는 프로젝트 이름에 '-'(hyphen)을 포함할 수 없기 때문에 '_'(underscore)로 대체한다. ②번 박스처럼 인터프리터를 선택한다. 가상환경을 구성한 사용자는 가상환경을 선택한다. 그리고 'Next' 버튼을 눌러 다음 단계로 진행한다.

![python-33] 

 샘플 애플리케이션에서는 django의 ORM(객체 관계 매핑) 기능을 사용하지 않기 때문에 Database 설정을 하지 않는다. 그림처럼 기존의 값을 삭제하고 'Finish'버튼을 눌러 프로젝트 생성을 완료한다.

##### <div id=3-2-4></div> 3.2.4.	django 애플리케이션 생성

![python-34]

 이클립스 화면 좌측의 PyDev Package Explorer에서 생성한 프로젝트를 찾을 수 있다. 생성한 프로젝트에 마우스를 올리고 마우스 오른쪽 버튼을 클릭하여 메뉴를 연다. 메뉴에서 Django 메뉴를 찾아 'Create appliation (manage.py startapp)'을 선택한다. 
 
![python-35] 

애플리케이션 이름을 입력하는 창이 생성된다. 애플리케이션 이름 역시 프로젝트와 마찬가지로 '-'(hyphen)을 포함할 수 없기 때문에 '_'(underscore)로 대체한다. 'ok' 버튼을 입력하면 django 애플리케이션 생성이 완료된다.

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


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Python 개발
