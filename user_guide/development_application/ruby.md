### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Ruby Development


## Table of Contents
1.	[Outline](#1)
     * [1.1.	Document Outline](#2)
          * [1.1.1.	Purpose](#3)
          * [1.1.2.	Range](#4)
          * [1.1.3.	References](#5)
2.	[Ruby Application Development Guide](#6)
     * [2.1.	Outline](#7)
     * [2.2.	Development Environment Configuration](#8)
          * [2.2.1.	Ruby & Ruby On Rails Installation](#9)
     * [2.3.	Development](#10)
          * [2.3.1.	Create Application](#11)
          * [2.3.2.	Application Environment Setting](#12)
          * [2.3.3.	VCAP_SERVICES Environment Setting Information](#13)
          * [2.3.4.	Connect Mysql](#14)
          * [2.3.5.	Connect Cubrid](#15)
          * [2.3.6.	Connect MongoDB](#16)
          * [2.3.7.	Connect Redis](#17)
          * [2.3.8.	Connect RabbitMQ](#18)
          * [2.3.9.	Connect GlusterFS ](#19)
     * [2.4.	Deployment](#21)
          * [2.4.1.	Open Platform Application Deployment](#22)
     * [2.5.	Test](#23)

# <div id='1'></div> 1.	Outline

### <div id='2'></div> 1.1.	Document Outline

##### <div id='3'></div> 1.1.1.	Purpose

This document (Ruby Application Development Guide)presents how to use service packs (Mysql, Cubrid, MongoDB, RabbitMQ, Radis, and ClusterFS) in conjunction with Ruby applications and deploy Ruby applications.

##### <div id='4'></div> 1.1.2.	Range

The range of this document is limited to Ruby application development, service pack linkage, and application distribution of Open PaaS projects.

##### <div id='5'></div>  1.1.3.	References
**<http://rubyinstaller.org/>**  
**<https://docs.pivotal.io/pivotalcf/buildpacks/ruby/index.html/>**  
**<http://rubykr.github.io/rails_guides/getting_started.html/>**  
**<https://github.com/brianmario/mysql2/>**  
**<http://www.cubrid.org/manual/93/ko/api/ruby.html/>**  
**<https://docs.mongodb.org/ecosystem/drivers/ruby/>**  
**<http://rubybunny.info/articles/getting_started.html/>**  
**<https://github.com/redis/redis-rb/>**  
**<https://github.com/fog/fog/>**  


# <div id='6'></div> 2.	Ruby Application Development Guide


### <div id='7'></div> 2.1.	Outline

It describes how to bind various service packs registered on an open platform to an application written in Ruby language, link applications using VCAP_SERVICES bound to the application, and create a Ruby application to deploy to an open platform in a Windows-based environment.

### <div id='8'></div> 2.2.	Development Environment Configuration

The following environment was constructed for Ruby Application Development.

-	OS : Windows 7 64bit
-	Ruby : 1.9.3-p551
-	Framework : Ruby On Rails 4.1.8
-	IDE : RubyMine 7.1.1   

※	The latest Ruby driver version of CubidDB supported up to Ruby 1.9.3 and was selected. It is recommended that you use the Ruby version that fits the supported driver (or Gem) for each service.   
※	Ruby IDE is used individually. 


##### <div id='9'></div> 2.2.1.	Ruby & Ruby On Rails Installation

1)	Ruby & DevKit Download   
**<http://rubyinstaller.org/downloads/>**   
![ruby01]
- Download  
RubyInstallers : Ruby 1.9.3-p551  
DEVELOPMENT KIT : DevKit-tdm-32-4.5.2-20111229-1559-sfx  

2)	Ruby Installation
- Double-click Ruby 1.9.3-p551.exe and execute installation.   
![ruby02]    
- Click “OK” button  

![ruby03]  
- Click the “I accept the License” and “Next” button after   

![ruby04]  
- Select “Add Ruby executables to your PATH” and click the “Install” button   
	
![ruby05]  
- Click the “Finish” button and complete the Ruby installation.   


3)	DEVELOPMENT KIT Installation
- Double-click DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe and execute installation.   
![ruby06]  
![ruby07]  
- Specify the folder to install and click the "Extract" button.
- Run the CMD window in Windows to the DevKit installation folder.  

>ruby dk.rb init
-	Execute “ruby dk.rb init” command and create “config.yml” file.
![ruby08] 

>ruby dk.rb install
-	Execute “ruby dk.rb install” command and install DevKit.
![ruby09] 
-	Check the ruby version by using “ruby –v” command.
![ruby10] 

4)	Ruby On Rails Installation
-	Run the command "gem update doc" to update the rdoc gem. (If not executed, errors may occur during Rails installation.)
![ruby11] 
-	Execute “gem install rails –v 4.1.8” command and install Rails.
![ruby12] 
-	Check the Rails version by using the “rails –v” command.
![ruby13] 


### <div id='10'></div> 2.3.	Development

It describes the creation and environment setting of applications to develop Ruby sample applications, the acquisition of VCAP_SERVICES information, and the interworking method of each service.


- Download Sample Application

 The completed sample application can be downloaded in the /OpenPaaSSample/ruby-sample-app link below.

> https://nextcloud.k-paas.org/index.php/s/x8Tg37WDFiL5ZDi/download

##### <div id='11'></div> 2.3.1.	Create Application

1)	Create Rails Application (bundle install excluded)
>Rails new [application name] –B –skip-bundle
<br>

![ruby14] 
![ruby15] 

2)	Define autogenerated folders and files

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> Gemfile </td>
    <td> This file is used to describe the Jem dependency information that your rail application needs. </td>
</tr>
<tr>
    <td> README </td>
    <td> This file is a short description of the application. A guide for installation and usage. </td>
</tr>
<tr>
    <td> Rakefile </td>
    <td> This file contains batch jobs that can be executed on the terminal. </td>
</tr>
<tr>
    <td> app/ </td>
    <td> Includes controllers, models, and views for applications. This guide will focus on this folder. </td>
</tr>
<tr>
    <td> config/ </td>
    <td> Save settings such as rules, routing, database, etc. of the application's execution time. </td>
</tr>
<tr>
    <td> config.ru </td>
    <td> This setting is required when rack-based servers start up. </td>
</tr>
<tr>
    <td> db/ </td>
    <td> The schema of the current database is shown(known for database migration) You'll learn a little bit about migration. </td>
</tr>
<tr>
    <td> doc/ </td>
    <td> Detailed description of the application. </td>
</tr>
<tr>
    <td> lib/ </td>
    <td> Extension module for applications (not covered in this document). </td>
</tr>
<tr>
    <td> public/ </td>
    <td> This is the only folder that can be viewed externally. Keep images, JavaScript, style sheets, and other static files here. </td>
</tr>
<tr>
    <td> script/ </td>
    <td> Contains Rails scripts. Put the relevant scripts here when you run or deploy the application. </td>
</tr>
<tr>
    <td> test/ </td>
    <td> Unit test, fixture, and other test tools. The rail application test is in charge of this part. </td>
</tr>
<tr>
    <td> tmp/ </td>
    <td> Temporary File </td>
</tr>
<tr>
    <td> vendor/ </td>
    <td> Space for third-party codes. General rail applications have Ruby Gem and Rail Source-Installation-On-Project and pre-packaged additional plug-ins. </td>
</tr>
</table>

##### <div id='12'></div> 2.3.2.	Application Environment Setting

The example is based on Ruby 1.9.3 and installed with an explicit selection of each driver's version.  
When modifying(Setting) ./Gemfile, it is recommended to install the appropriate Gem for the version of Ruby installed.

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./Gemfile </td>
    <td> This file describes the dependency information that your rail application needs </td>
</tr>
<tr>
    <td> ./config/application.rb </td>
    <td> Environment Setting of the application </td>
</tr>
<tr>
    <td> ./config/routes.rb </td>
    <td> Set up the mapping of the Request URL to the controller </td>
</tr>
<tr>
    <td> ./config/environments/development.rb  </td>
    <td> Development Environment Setting (Default Environment Setting when running Localhost server) </td>
</tr>
<tr>
    <td> ./config/environments/production.rb  </td>
    <td> Common use Environment Setting(default environment setting used when deploying open platforms) </td>
</tr>
</table>

1)	./Gemfile Modification
-	Define the drivers and the required gems to be used in each service.

```
# Modify https => http
source 'http://rubygems.org' 

# Specify Ruby Version
ruby '1.9.3' 

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '4.1.8'
...(Skip)...
# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin]

# Cloud Foundry Utils
gem 'cf-app-utils'

# MySQL Driver
gem 'mysql2', '~> 0.3.20'

# Cubrid Driver
gem 'cubrid'

# MongoDB Driver
gem 'mongo'#, '~> 2.1'

# RabbitMP Driver
gem 'amq-protocol', '1.9.2'
gem 'bunny', '1.7.0'

# Openstack for swift(glusterfs)' Driver
gem 'net-ssh', '2.9.2'
gem 'fog-google', '0.1.0'
gem 'fog', '1.34.0'

# Redis Driver
gem 'redis'

group :development, :test do
  gem 'rspec-rails', '~> 3.0.0'
end
```

※	The Cubid driver uses the library of Cubid in a windows environment. Refer to the library to download the appropriate Cubid for the version of Ruby.   
※	Since the sample is Ruby 1.9.3 (not 64 bit), CUBRID-Windows-x86 (32 bit) version is installed.   

2)	Gem Installation
-	Install the Gem defined in the Gemfile.  
```
bundle install
```

3)	Modify ./config/application.rb 
-	Environment Setting of the Application

```
require File.expand_path('../boot', __FILE__)

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module RubySampleApp
  class Application < Rails::Application
    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.

    # Set Time.zone default to the specified zone and make Active Record auto-convert to this zone.
    # Run "rake -D time" for a list of tasks for finding time zone names. The default is UTC.
    # config.time_zone = 'Central Time (US & Canada)'

    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
    # config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]
    # config.i18n.default_locale = :de

    # Adding a library loading path when running the Rails application
    config.autoload_paths += %W(#{config.root}/lib)

    # Exception handling (set to refer to routes.rb)
    config.exceptions_app = self.routes

  end
end
```

4)	Modify ./config/routes.rb
-	Set Mapping for Requesting URL and Controller
```
Rails.application.routes.draw do

  # Index(root) page settings
root 'static#login'

# Static page settings(.html)
  get '/login' => 'static#login'
  get '/main/:org_id' => 'static#main'
  get '/manage' => 'static#manage'
...(Skip)...
  # API Path Settings by Function
#[HTTPMethod] ‘[Uri]’   =>   ‘[Controller#Method]’
  get	'org-chart/:org_id/mysql' 	  	=> 'org_chart_mysql#index'
  get	'org-chart/:org_id/cubrid' 	  	=> 'org_chart_cubrid#index'
get	'org-chart/:org_id/mongo' 	=> 'org_chart_mongo#index'
...(Skip)
```

5)	./config/environments/development.rb 
-	Development Environment Setting( Default environment setting when running Localhost server)

```
Rails.application.configure do
  # Settings specified here will take precedence over those in config/application.rb.
...(Skip)...

# Show full error reports and disable caching.
# Set exception handling function (to receive return as type Json)
  config.consider_all_requests_local       = false
  config.action_controller.perform_caching = false

...(Skip)...

# public static html page view
# Set whether to allow access to ./public folders (js, css, image static resources)
  config.serve_static_assets = true

  # Disable request forgery protection in test environment
# authenticity_token ignore
# Set authentication procedure when calling Post, Put method
  config.action_controller.allow_forgery_protection    = false
end
```
6)	./config/environments/production.rb 
-	Use environment settings  (default environment setting used when deploying open platforms)

```
Rails.application.configure do
  # Settings specified here will take precedence over those in config/application.rb.

...(Skip)...

  # Disable request forgery protection in test environment
  # authenticity_token ignore
# Set authentication procedure when calling Post, Put method
config.action_controller.allow_forgery_protection    = false
```

##### <div id='13'></div> 2.3.3.	VCAP_SERVICES Environment Setting Information

To obtain access information for each service to which an application deployed on an open platform is bound, information may be obtained by reading VCAP_SERVICES configuration information registered for each application.

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/vcap.rb </td>
    <td>  A class that reads connection information per service from VCAP_SERVICES environment information of the application from open platform  </td>
</tr>
</table>

1)	Applications environment information of the Open Platform
-	When the service is bound, environment setting information is registered for each application in the form of JSON.

```
{
 "VCAP_SERVICES": {
 "p-mysql": [
   {
    "credentials": {
     "hostname": "10.30.40.63",
     "jdbcUrl": "jdbc:mysql://10.30.40.63:3306/cf_ea68784e_3de6_439d_afc1_d51b4e95627b?user=ZwCFnQRiT3KANqHZ\u0026password=qs7oqi4nSvWq6UQa",
     "name": "cf_ea68784e_3de6_439d_afc1_d51b4e95627b",
     "password": "qs7oqi4nSvWq6UQa",
     "port": 3306,
     "uri": "mysql://ZwCFnQRiT3KANqHZ:qs7oqi4nSvWq6UQa@10.30.40.63:3306/cf_ea68784e_3de6_439d_afc1_d51b4e95627b?reconnect=true",
     "username": "ZwCFnQRiT3KANqHZ"
    },
    "label": "p-mysql",
    "name": "sample-mysql-instance",
    "plan": "100mb",
    "tags": [
     "mysql"
    ]
   }
  ],
...(Skip)...
```

2)	Create ./lib/vcap.rb file
-	A class that reads connection information per service from VCAP_SERVICES environment information of the application from an open platform
```
# Use cf-app-utils library
require 'cf-app-utils' 

module VcapService
  class Vcap

# Class Reset Method
    def initialize 
    end

    def serviceInfo(service) #VCAP_SERVICES information retrieving method
      #Return information by retrieving the label information of the service registered in VCAP_SERVICES
      CF::App::Credentials.find_by_service_label(service) 
    end
  end
end
```

-	When not using the cf-app-utils 
```
# Information can be read in the form of Json by accessing environmental variables directly without using cf-app-utils.

vcap_services = JSON.parse(ENV['VCAP_SERVICES'])
```


##### <div id='14'></div>  2.3.4.	Connect Mysql

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/mysql_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_mysql_controller.rb </td>
    <td> Controller class used by call Service Connection class </td>
</tr>
</table>

1)	./lib/mysql_service.rb
-	A class that inherits a Vcap class to create a Mysql Connection

```
require 'vcap'
module Connector
  class MysqlService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('p-mysql') # “p-mysql” service credentials check
      Mysql2::Client.new(:host =>  credentials['hostname'],
                         :username => credentials['username'],
                         :password => credentials['password'],
                         :database => credentials['name'])
    end
end
end
```

2)	./app/controllers/orgs_chart_mysql_controller.rb service connection class call

```
# encoding: UTF-8      # Encoding set(Korean Language Supported)
require 'mysql_service'   # Add mysql_service class (Additional part of each service class)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # Call processing method(DB Access)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 
  after_action :db_close  # Call postprocessing method (DB Closed)

  # Org group list lookup method
  def index
    if @org == nil
      render json: {error: 'request value wrong'}, status: 400
    else
      render json: {org: @org, groups: @client.query(@query.group_index(@param[:org_id]))}
    end
  end

# Connect to the service before the method is called
  def db_connection
    @client = Connector::MysqlService.new.connector #Access information was obtained by calling the service interworking class, and this was declared as a class variable.
    @query = Connector::MysqlQuery.new
  end

# Service closed after method call is done
  def db_close
    @client.close
  end

  # Param Processing Method
def set_param
    @param = {:org_id => params[:org_id]}
  end

  # Org information lookup method
  def set_org
    begin
      @org = @client.query(@query.org_show(@param[:org_id])).first
    rescue
      @org = nil
    end
  end
end
```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.

##### <div id='15'></div> 2.3.5.	Connect Cubrid

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/cubrid_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_cubrid_controller.rb </td>
    <td> Controller class used by Call Service Connection class </td>
</tr>
</table>

1)	./lib/cubrid_service.rb
-	Class that inherits a Vcap class to create a Cubid Connection

```
require 'vcap'
module Connector
  class CubridService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('CubridDB') # “CubridDB” service credentials check
      Cubrid.connect(credentials['name'],
                            credentials['hostname'],
                            33000,
                            credentials['username'],
                            credentials['password'])
    end
end
end
```

2)	./app/controllers/orgs_chart_cubrid_controller.rb service connection class call

```
# encoding: UTF-8      # Encoding set(Korean language supported)
require 'cubrid_service'    # Add cubrid_service class (Additional part of each service class)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # Call processing method(DB Access)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 
  after_action :db_close  # Call postprocessing method (DB Closed)

  # Org group list lookup method
  def index
    if @org == nil
      render json: {error: 'request value wrong'}, status: 400
    else
      result = @client.query(@query.group_index(@param[:org_id]))
      groups = []
      while rs = result.fetch_hash
        rs['label'] = rs['label'].to_s.force_encoding('UTF-8')
        rs['desc'] = rs['desc'].to_s.force_encoding('UTF-8')
        rs['thumb_img_name'] = rs['url'].to_s.force_encoding('UTF-8')
        rs['thumb_img_path'] = rs['url'].to_s.force_encoding('UTF-8')
        rs['url'] = rs['url'].to_s.force_encoding('UTF-8')
        groups.push(rs)
      end
      render json: {org: @org, groups: groups}
    end  end

# Connect to the service before the method is called
  def db_connection
    @client = Connector::CubridService.new.connector #Access information was obtained by calling the service interworking class, and this was declared as a class variable.
    @query = Connector::MysqlQuery.new
  end

# Service closed after method call is done
  def db_close
    @client.close
  end

  # Param process method
def set_param
    @param = {:org_id => params[:org_id]}
  end

  # Org information lookup method
  def set_org
    begin
      @org = @client.query(@query.org_show(@param[:org_id])).first
    rescue
      @org = nil
    end
  end
end

```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.

##### <div id='16'></div> 2.3.6.	Connect MongoDB

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/mongo_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_mongo_controller.rb </td>
    <td> Controller class used by Call Service Connection class </td>
</tr>
</table>

1)	./lib/mongo_service.rb
-	Class to create MongoDB Connection by inheriting Vcap class

```
require 'vcap'
module Connector
  class MongoService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('Mongo-DB') # “Mongo-DB” service credentials check
      Mongo::Client.new(credentials['hosts'],
                        :database => credentials['name'],
                        :user =>  credentials['username'],
                        :password => credentials['password'],
                        :connect => :sharded)

    end
  end
end
```

2)	./app/controllers/orgs_chart_mongo_controller.rb service connection class call
```
# encoding: UTF-8      # Encoding set(Korean Language Supported)
require ‘mongo_service’    # Add mongo_service class (Additional part of each service class)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # Call processing method(DB Access)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 

  # Org group list lookup method
  def index
    if @org == nil
      render json: {error: 'request value wrong'}, status: 400
    else
      result = @client[:Groups].find(:orgId => BSON::ObjectId(@param[:orgId])).sort({ _id: -1 } )
      groups=[]
      result.each do |rs|
        rs['created'] = Date.strptime(rs['created'].as_json['t'].to_s,'%s').strftime('%F')
        rs['modified'] = Date.strptime(rs['modified'].as_json['t'].to_s,'%s').strftime('%F')
        rs_new = {'id' => rs.delete('_id').to_s,
                  'org_id' => rs.delete('orgId').to_s,
                  'parent_id' => rs.delete('parentId').to_s,
                  'label' => rs.delete('label').to_s,
                  'desc' => rs.delete('desc').to_s,
                  'thumb_img_name' => rs.delete('thumbImgName').to_s,
                  'thumb_img_path' => rs.delete('thumbImgPath').to_s,
                  'url' => rs.delete('url').to_s,
                  'created' => rs.delete('created').to_s,
                  'modified' => rs.delete('modified').to_s
        }.merge(rs)
        groups.push(rs_new)
      end
      render json: {org: @org, groups: groups}
    end
  end

# Connect to the service before the method is called
  def db_connection
    @client = Connector:: MongoService.new.connector #Access information was obtained by paging the service interworking class, and this was declared as a class variable.
  end

# Param processing method
def set_param
    @param = {:orgId => params[:org_id]}
  end

  # Org information lookup method
  def set_org
    begin
      #@org = @client[:Orgs].find(:_id => BSON::ObjectId(@param[:orgId])).first
      org_tmp = @client[:Orgs].find(:_id => BSON::ObjectId(@param[:orgId])).first
      org_tmp['created'] = Date.strptime(org_tmp['created'].as_json['t'].to_s,'%s').strftime('%F')
      org_tmp['modified'] = Date.strptime(org_tmp['modified'].as_json['t'].to_s,'%s').strftime('%F')
      @org = {'id' => org_tmp.delete('_id').to_s}.merge(org_tmp)
    rescue
      @org = nil
    End
  end
end
```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.


##### <div id='17'></div> 2.3.7.	Connect Redis

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/redis_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/login_controller.rb </td>
    <td> Controller class used by Call Service Connection class </td>
</tr>
</table>

1)	./lib/rddis_service.rb
-	Class to create Redis Connection by inheriting Vcap class Vcap

```
require 'vcap'
module Connector
  class RedisService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('redis-sb') # “redis-sb” service credentials check
      Redis.new(:host => credentials['host'],
                :port => credentials['port'],
                :password => credentials['password'])
    end
  end
end
```

2)	./app/controllers/login_controller.rb service Connection class call
```
# encoding: UTF-8      # Encoding set(Korean Language Supported)
require ‘redis_service’    # Add redis_service class (Additional part of each service class)
class LoginController < ApplicationController
  #preprocessing method
  before_action :redis_connection # Redis server access

  def login
    id = params[:id]
    pwd = params[:password]

    if id == nil || id =='' || pwd == nil || pwd == ''
      render json: {error: 'request value wrong'}, status: 400
    elsif (id.eql? 'admin') && (pwd.eql? 'admin')
      key = SecureRandom.uuid.to_s
      @redis.set(key, "admin")
      cookies['login_cookie'] = key
      render json: {}
    else
      render json: {error: 'not authz'}, status: 401
    end
  end

  def logout
    key = cookies['login_cookie'].to_s
    p key
    @redis.del(key)
    render json: {}
  end

  def redis_connection
    @redis = Connector::RedisService.new.connector  #Access information was obtained by paging the service interworking class, and this was declared as a class variable.
  end
end
```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.

##### <div id='18'></div> 2.3.8.	Connect RabbitMQ

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/rabbitmq_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/status_controller.rb </td>
    <td> Controller class used by Call Service Connection class </td>
</tr>
</table>

1)	./lib/rabbitmq_service.rb
-	Class to create RabbitMQ Connection by inheriting Vcap class

```
require 'vcap'
module Connector
  class RabbitmqService < VcapService::Vcap

    def initialize
      super()
    end
    def connector

      credentials = serviceInfo('p-rabbitmq') # “p-rabbitmql” service credentials check
      protocols = credentials['protocols']
      amqp_credentials = protocols['amqp+ssl'] || protocols['amqp']
      Bunny.new(
          :host      => amqp_credentials['hosts'].sample,
          :port      => amqp_credentials['port'],
          :vhost     => amqp_credentials['vhost'],
          :user      => amqp_credentials['username'],
          :pass      => amqp_credentials['password'],
          tls_ca_certificates: %w(./tls/ca_certificate.pem),
          verify_peer: false)
    end
  end
end
```
※In Bunny 2.2.x and later driver versions, if you do not specify a path for TLS/SSL CA, the default path is used. Ex) Ubuntu/Debian : /etc/ssl/certs/ca-certificates.crt

2)	./app/controllers/status_controller.rb service connection class call
```
# encoding: UTF-8      # Encoding set(Korean Language Supported)
require ‘rabbitmq_service’    # Add rabbitmq_service class (Additional part of each service class)
class StatusController < ApplicationController
  before_action :rabbit_ connection # RabbitMQ server access
  def status

    if ENV['RAILS_ENV'].to_s != "development" && !ENV['RAILS_ENV'].to_s != "test"
      @conn.start

      orgId = params[:org_id]
      dbType = params[:db_type]
      queue_name = dbType +'_'+ orgId.to_s

      @channel ||= @conn.create_channel
      @queue ||= @channel.queue(queue_name, :auto_delete => true)

      if @conn.queue_exists?(queue_name)
        delivery_info, metadata, payload = @queue.pop
      else
        delivery_info, metadata, payload = nil
      end

      @conn.stop
    end

    if payload == nil || payload.length == 0
      payload = 'NO_CHANGES'
    end
    render json: {:status => payload}

  end

  def rabbit_connection
    @conn = Connector::RabbitmqService.new.connector   #Access information was obtained by paging the service interworking class, and this was declared as a class variable.

  end
end
```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.

##### <div id='19'></div> 2.3.9.	Connect GlusterFS

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./lib/glusterfs_service.rb </td>
    <td> A class that inherits a Vcap class to create a Connection </td>
</tr>
<tr>
    <td> ./app/controllers/upload_controller.rb </td>
    <td> Controller class used by Call Service Connection class </td>
</tr>
</table>

1)	./lib/glusterfs_service.rb
-	Class to create GlusterFS Connection by inheriting Vcap class

```
require 'vcap'
module Connector
  class Glusterfs < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('glusterfs') # “glusterfs” service credentials check
      Fog::Storage.new({
                           :provider            => 'OpenStack',
                           :openstack_username  => credentials['username'],
                           :openstack_api_key   => credentials['password'],
                           :openstack_auth_url  => credentials['auth_url']+'/tokens'
                       })
    end
  end
end
```
2)	./app/controllers/upload_controller.rb service connection class call
```
# encoding: UTF-8      # Encoding set(Korean Language Supported)
require ‘glusterfs_service’    # Add glusterfs_service class (Additional part of each service class)
class UploadController < ApplicationController
  before_filter :authenticate   # Check authentication before calling the method.
  before_action :gs_connection  # Acquire Swift credentials for file upload.

  # Uploads file.
  def upload
    @img = params[:file]

    if @img == nil || @img == ''
      render json: {error: 'request value wrong'}, status: 400
    else
      cont = @service.directories.get "ruby-thumb"

      if cont == nil
        cont = @service.directories.create :key => 'ruby-thumb', :public => true
        cont.save
      end

      file = cont.files.create :key => DateTime.now.strftime('%Q')+"_" + @img.original_filename, :body => File.open(@img.tempfile.path)

      render json: {thumb_img_path: file.public_url}
    end
  end

  def gs_connection
    @service = Connector::Glusterfs.new.connector   #Access information was obtained by paging the service interworking class, and this was declared as a class variable.

  end
end
```
※The class is a sample example, and the method of obtaining and utilizing access information for the service can be used according to the structure and characteristics of the application.


### <div id='21'></div> 2.4.	Deployment

Explains how to deploy developed applications on open platforms.

##### <div id='22'></div> 2.4.1.	Open Platform Application Deployment

<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> ./manifest.yml </td>
    <td> Open platform deployment environment setting file </td>
</tr>
</table>

1)	Create ./manifest.yml
-	In the cf push command, the deployment proceeds by referring to the manifest.yml in the current directory.

```
---
applications:
- name: ruby-sample-app # Application Name
  memory: 512M        # Application Memory Size
  instances: 1           # Applications Number of Instances
  path: .                # Applications location
  command: bundle exec Rails server -p $PORT # Deployment command after deployment
```
※The port that was allocated during application staging is registered as an environment variable. This $PORT is also used to check the status of the application. It is recommended to specify the port.

2)	Open Platform Login
```
$ cf api https://api.cf.open-paas.com   # Set Open Platform TARGET
 #cf api [API Address of the Open Platform] : Sets the API address of the Open Platform.

$ cf login -u testUser -o sample_test -s sample_space   # Request Login
 #cf login –u [User Name] –o [Organization Name] –s [Space Name] : If there is no Organization and Space, creation is required

API endpoint: https://api.cf.open-paas.com

Password> 
Authenticating...
OK

Targeted org sample_test

Targeted space sample_space
                   
API endpoint:   https://api.cf.open-paas.com (API version: 2.29.0)   
User:           testUser   
Org:            sample_test   
Space:          sample_space   

$
```

3)	Create Open Platform Service
```
$ cf marketplace     # Request Marketplace List

service         plans                    description
p-mysql	       100mb, 1gb		MySQL databases on demand   
p-rabbitmq     standard		        RabbitMQ is a robust …..   
redis-sb	       shared-vm, dedicated-vm	Redis service to provide a ……

$ cf create-service p-mysql 100mb sample-mysql-instance    # Create Service
 #cf create-service [Service Name] [Plan Name] [Service Name to Create]

$ cf services    # Check Service List

name                       service       plan              bound apps		last…
sample-mysql-instance       p-mysql      100mb            node-sample, p....	…
sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
sample-redis-instance        redis-sb      shared-vm         python-sample-....	…
```

4)	Binding services to open platform applications and start applications

```
$ cf push -b https://github.com/cloudfoundry/ruby-buildpack.git#v1.3.1 --no-start 
# Execute application upload and don't start.
# Current Ruby buildpacks (1.3.1 and later) do not support Ruby 1.9.3 by default. Designate and deploy buildpacks that support Ruby 1.9.3. Each application should specify and use a buildpack that fits the Ruby version. You can exclude the –b option if you use a basic build pack provided by an open platform.
# cf push –b [User Buildpack URL] –no-start

$ cf services   # Check Service List

name                       service       plan              bound apps		last…
sample-mysql-instance       p-mysql      100mb            node-sample, p....	…
sample-cubrid-instance      CubridDB      utf8              node-sample, p....	…
sample-mongo-instance      Mongo-DB   default-plan        node-sample, p....	…
sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
sample-redis-instance        redis-sb      shared-vm         python-sample-....	…
sample-glusterfs-instance    glusterfs      glusterfs-1000Mb   glusterfs-samp....	…

$ cf bind-service ruby-sample-app sample-mysql-instance   # Application Service Binding
# cf bind-service [Application Name] [Service Name]

$ cf start ruby-sample-app    # Start Application
# cf start [Application Name]
```
※Because the latest build packs do not support Ruby 1.9.3, deploy using Ruby-buildpack 1.3.1 version. Exclude the –b option if you are using a base build pack supported by an open platform 

※If the application deployment procedure is performed on a Windows machine (if cf cli is installed and used on a Windows machine), the application may not start ('cf start') correctly. In this case, convert the three files bundle, rake, and Rails in the bin folder to Unix and proceed from 'cf push'. The file conversion procedure follows:


1.	Open a windows command window and navigate to the Applications folder.
2.	(Method 1.) Download and extract dos2unix from the following url to move the dos2unix.exe file to the bin folder of the sample application.    
http://sourceforge.net/projects/dos2unix/files/latest/download

	(Method 2.) The user who 'git clone' the sample application changes the file name of the dos2unix file in the application folder using the following command. If the 'rename' command is not available, you may use the 'ren' command instead or change the file name to 'dos2unix.exe' by yourself.
>rename dos2unix dos2unix.exe   

3.	Convert the three files in the bin folder to UNIX files using the following command.
>dos2unix bin/bundle bin/rake bin/rails   
>※	This command does not run on Windows Power Shell. Command prompts allow you to execute commands.
Once the conversion has been completed successfully, you can see the following screen:
>![ruby16] 

4.	Perform the procedure [4) Start Application and Bind Service on Open Platform Application] again.





### <div id='23'></div> 2.5.	Test

Ruby Application Test using Rspec

1)	Define folders and files
<table>
<tr align=center>
    <td> File/Folder </td>
    <td> Purpose </td>
</tr>
<tr>
    <td> Spec </td>
    <td> rspec test case ruby file existing folder </td>
</tr>
<tr>
    <td> *_spec </td>
    <td> rspec test case ruby file </td>
</tr>
</table>

2)	Execute Test
>bundle exec rspec   
      ※ To proceed with the test normally, access to the service must be possible.(proxy, tunneling, etc.)
      


[ruby01]:./images/ruby/ruby_01.png
[ruby02]:./images/ruby/ruby_02.png
[ruby03]:./images/ruby/ruby_03.png
[ruby04]:./images/ruby/ruby_04.png
[ruby05]:./images/ruby/ruby_05.png
[ruby06]:./images/ruby/ruby_06.png
[ruby07]:./images/ruby/ruby_07.png
[ruby08]:./images/ruby/ruby_08.png
[ruby09]:./images/ruby/ruby_09.png
[ruby10]:./images/ruby/ruby_10.png
[ruby11]:./images/ruby/ruby_11.png
[ruby12]:./images/ruby/ruby_12.png
[ruby13]:./images/ruby/ruby_13.png
[ruby14]:./images/ruby/ruby_14.png
[ruby15]:./images/ruby/ruby_15.png
[ruby16]:./images/ruby/ruby_16.png


### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Ruby Developmet
