### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Ruby Development


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

It describes how to bind various service packs registered on an open platform to an application written in Ruby language, link applications using VCAP_SERVICES bound to the application, and create a Ruby application to distribute to an open platform in a Windows-based environment.

### <div id='8'></div> 2.2.	Development Environment Configuration

The following environment was constructed for Ruby Application Development.

-	OS : Windows 7 64bit
-	Ruby : 1.9.3-p551
-	Framwork : Ruby On Rails 4.1.8
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
- Click “I accept the License” and “Next” button after   

![ruby04]  
- Select “Add Ruby executables to your PATH” and click “Install” button   
	
![ruby05]  
- Click “Finish” button and complete Ruby installation.   


3)	DEVELOPMENT KIT Installation
- Double-click DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe and execute installation.   
![ruby06]  
![ruby07]  
- Specify the folder to install and click the "Extract" button..
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
-	Run the command "gem update doc" to update the rdoc gem. (If not executed, errors may occur during rails installation.)
![ruby11] 
-	Execute “gem install rails –v 4.1.8” command and install rails.
![ruby12] 
-	Check the rails version by using “rails –v” command.
![ruby13] 


### <div id='10'></div> 2.3.	Development

It describes the creation and environment setting of applications to develop Ruby sample applications, the acquisition of VCAP_SERVICES information, and the interworking method of each service.


- Download Sample Application

 The completed sample application can be downloade in the /OpenPaaSSample/ruby-sample-app link below.

> https://nextcloud.paas-ta.org/index.php/s/x8Tg37WDFiL5ZDi/download

##### <div id='11'></div> 2.3.1.	Create Application

1)	Create Rails Application (bundle install excluded)
>rails new [application name] –B –skip-bundle
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
    <td> This file is a short description for the application. A guide for installation and usage. </td>
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
    <td> This is the only folder that can be viewed externally.Keep images, JavaScript, style sheets, and other static files here. </td>
</tr>
<tr>
    <td> script/ </td>
    <td> Contains rails scripts. Put the relevant scripts here when you run or deploy the application. </td>
</tr>
<tr>
    <td> test/ </td>
    <td> Unit test, fixture, and other test tools. Rail application test is in charge of this part. </td>
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
When modifying(Setting) ./Gemfile, it is recommend to install the appropriate Gem for the version of Ruby installed.

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

※	Windows 환경에서 Cubrid 드라이버는 Cubrid의 라이브러리를 사용하므로 해당 Ruby 버전에 맞는 Cubrid를 설치하여 라이브러리를 참조 할 수 있도록 하여야한다.   
※	Since the sample is Ruby 1.9.3 (not 64 bit), CUBRID-Windows-x86 (32 bit) version is installed.   

2)	Gem Installation
-	Install the Gem defined in the Gemfile..  
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
    # Run "rake -D time" for a list of tasks for finding time zone names. Default is UTC.
    # config.time_zone = 'Central Time (US & Canada)'

    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
    # config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]
    # config.i18n.default_locale = :de

    # Rails 애플리케이션 구동시 라이브러리 Loading path 추가
    config.autoload_paths += %W(#{config.root}/lib)

    # 예외처리(routes.rb참조 하도록 설정)
    config.exceptions_app = self.routes

  end
end
```

4)	./config/routes.rb 수정
-	Request URL과 컨트롤러의 매핑 설정
```
Rails.application.routes.draw do

  # 인덱스(root) 페이지 설정
root 'static#login'

# 정적페이지설정(.html)
  get '/login' => 'static#login'
  get '/main/:org_id' => 'static#main'
  get '/manage' => 'static#manage'
...(중략)...
  # 기능별 API Path 설정
#[HTTP메서드] ‘[Uri]’   =>   ‘[Controller#메서드]’
  get	'org-chart/:org_id/mysql' 	  	=> 'org_chart_mysql#index'
  get	'org-chart/:org_id/cubrid' 	  	=> 'org_chart_cubrid#index'
get	'org-chart/:org_id/mongo' 	=> 'org_chart_mongo#index'
...(생략)
```

5)	./config/environments/development.rb 
-	개발환경 설정( Localhost 서버 실행시 기본 환경설정)

```
Rails.application.configure do
  # Settings specified here will take precedence over those in config/application.rb.
...(중략)...

# Show full error reports and disable caching.
# 예외 발생처리 기능 설정(Json형으로 반환 받기위함)
  config.consider_all_requests_local       = false
  config.action_controller.perform_caching = false

...(중략)...

# public static html page view
# ./public 폴더 접근 허용 여부 설정(js, css, image 정적 리소스)
  config.serve_static_assets = true

  # Disable request forgery protection in test environment
# authenticity_token ignore
# Post, Put 메서드 호출시 인증 절차 설정
  config.action_controller.allow_forgery_protection    = false
end
```
6)	./config/environments/production.rb 
-	상용환경 설정(개방형 플랫폼 배포시 사용되는 기본 환경설정)

```
Rails.application.configure do
  # Settings specified here will take precedence over those in config/application.rb.

...(중략)...

  # Disable request forgery protection in test environment
  # authenticity_token ignore
# Post, Put 메서드 호출시 인증 절차 설정
config.action_controller.allow_forgery_protection    = false
```

##### <div id='13'></div> 2.3.3.	VCAP_SERVICES 환경설정 정보

개방형 플랫폼에 배포되는 애플리케이션이 바인딩된 서비스별 접속 정보를 얻기 위해서는 애플리케이션별로 등록되어있는 VCAP_SERVICES 환경설정 정보를 읽어들여 정보를 획득 할 수 있다.

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/vcap.rb </td>
    <td> 개방형 플랫폼의 애플리케이션별 VCAP_SERVICES 환경정보에서 서비스별 접속정보를 읽어오는 클래스 </td>
</tr>
</table>

1)	개방형 플랫폼의 애플리케이션 환경정보
-	서비스를 바인딩하면 JSON 형태로 환경설정 정보가 애플리케이션 별로 등록된다.

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
...(이하 생략)...
```

2)	./lib/vcap.rb 파일 생성
-	개방형 플랫폼의 애플리케이션별 VCAP_SERVICES 환경정보에서 서비스별 접속정보를 읽어오는 클래스
```
# cf-app-utils 라이브러리 사용
require 'cf-app-utils' 

module VcapService
  class Vcap

# 클래스 초기화 메서드
    def initialize 
    end

    def serviceInfo(service) #VCAP_SERVICES 정보 조회 메서드
      #VCAP_SERVICES에 등록된 서비스의 label 정보를 기준으로 조회하여 정보를 반환
      CF::App::Credentials.find_by_service_label(service) 
    end
  end
end
```

-	cf-app-utils 를 사용하지 않을경우 
```
# cf-app-utils을 사용하지 않고 직접 환경 변수에 접근하여 Json형태로 정보를 읽어올수 있다.

vcap_services = JSON.parse(ENV['VCAP_SERVICES'])
```


##### <div id='14'></div>  2.3.4.	Mysql 연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/mysql_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_mysql_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/mysql_service.rb
-	Vcap 클래스를 상속하여 Mysql Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class MysqlService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('p-mysql') # “p-mysql” 서비스 credentials 조회
      Mysql2::Client.new(:host =>  credentials['hostname'],
                         :username => credentials['username'],
                         :password => credentials['password'],
                         :database => credentials['name'])
    end
end
end
```

2)	./app/controllers/orgs_chart_mysql_controller.rb 서비스 Connection 클래서 호출

```
# encoding: UTF-8      # Encoding 지정(한글지원)
require 'mysql_service'   # mysql_service 클래스 추가 (각 서비스별 클래스 추가부분)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # 전처리 메소드 호출(DB 접속)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 
  after_action :db_close  # 후처리 메소드 호출 (DB 닫음)

  # Org 그룹 목록 조회 메서드
  def index
    if @org == nil
      render json: {error: 'request value wrong'}, status: 400
    else
      render json: {org: @org, groups: @client.query(@query.group_index(@param[:org_id]))}
    end
  end

# 메소드가 호출되기전 서비스 접속
  def db_connection
    @client = Connector::MysqlService.new.connector #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.
    @query = Connector::MysqlQuery.new
  end

# 메소드 호출이 끝난후 서비스 닫음
  def db_close
    @client.close
  end

  # Param 처리 메소드
def set_param
    @param = {:org_id => params[:org_id]}
  end

  # Org 정보 조회 메서드
  def set_org
    begin
      @org = @client.query(@query.org_show(@param[:org_id])).first
    rescue
      @org = nil
    end
  end
end
```
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.

##### <div id='15'></div> 2.3.5.	Cubrid 연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/cubrid_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_cubrid_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/cubrid_service.rb
-	Vcap 클래스를 상속하여 Cubrid Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class CubridService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('CubridDB') # “CubridDB” 서비스 credentials 조회
      Cubrid.connect(credentials['name'],
                            credentials['hostname'],
                            33000,
                            credentials['username'],
                            credentials['password'])
    end
end
end
```

2)	./app/controllers/orgs_chart_cubrid_controller.rb 서비스 Connection 클래서 호출

```
# encoding: UTF-8      # Encoding 지정(한글지원)
require 'cubrid_service'    # cubrid_service 클래스 추가 (각 서비스별 클래스 추가부분)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # 전처리 메소드 호출(DB 접속)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 
  after_action :db_close  # 후처리 메소드 호출 (DB 닫음)

  # Org 그룹 목록 조회 메서드
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

# 메소드가 호출되기전 서비스 접속
  def db_connection
    @client = Connector::CubridService.new.connector #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.
    @query = Connector::MysqlQuery.new
  end

# 메소드 호출이 끝난후 서비스 닫음
  def db_close
    @client.close
  end

  # Param 처리 메소드
def set_param
    @param = {:org_id => params[:org_id]}
  end

  # Org 정보 조회 메서드
  def set_org
    begin
      @org = @client.query(@query.org_show(@param[:org_id])).first
    rescue
      @org = nil
    end
  end
end

```
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.

##### <div id='16'></div> 2.3.6.	MongoDB 연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/mongo_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/orgs_chart_mongo_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/mongo_service.rb
-	Vcap 클래스를 상속하여 MongoDB Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class MongoService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('Mongo-DB') # “Mongo-DB” 서비스 credentials 조회
      Mongo::Client.new(credentials['hosts'],
                        :database => credentials['name'],
                        :user =>  credentials['username'],
                        :password => credentials['password'],
                        :connect => :sharded)

    end
  end
end
```

2)	./app/controllers/orgs_chart_mongo_controller.rb 서비스 Connection 클래서 호출
```
# encoding: UTF-8      # Encoding 지정(한글지원)
require ‘mongo_service’    # mongo_service 클래스 추가 (각 서비스별 클래스 추가부분)
class OrgChartMysqlController < ApplicationController
  before_action :db_connection    # 전처리 메소드 호출(DB 접속)
  before_action :set_param, only: [:index] 
  before_action :set_org, only: [:index] 

  # Org 그룹 목록 조회 메서드
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

# 메소드가 호출되기전 서비스 접속
  def db_connection
    @client = Connector:: MongoService.new.connector #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.
  end

# Param 처리 메소드
def set_param
    @param = {:orgId => params[:org_id]}
  end

  # Org 정보 조회 메서드
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
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.


##### <div id='17'></div> 2.3.7.	Redis 연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/redis_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/login_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/rddis_service.rb
-	Vcap 클래스를 상속하여 Redis Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class RedisService < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('redis-sb') # “redis-sb” 서비스 credentials 조회
      Redis.new(:host => credentials['host'],
                :port => credentials['port'],
                :password => credentials['password'])
    end
  end
end
```

2)	./app/controllers/login_controller.rb 서비스 Connection 클래서 호출
```
# encoding: UTF-8      # Encoding 지정(한글지원)
require ‘redis_service’    # redis_service 클래스 추가 (각 서비스별 클래스 추가부분)
class LoginController < ApplicationController
  #선처리 메소드
  before_action :redis_connection # Redis 서버 접속

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
    @redis = Connector::RedisService.new.connector  #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.
  end
end
```
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.

##### <div id='18'></div> 2.3.8.	RabbitMQ연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/rabbitmq_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/status_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/rabbitmq_service.rb
-	Vcap 클래스를 상속하여 RabbitMQ Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class RabbitmqService < VcapService::Vcap

    def initialize
      super()
    end
    def connector

      credentials = serviceInfo('p-rabbitmq') # “p-rabbitmql” 서비스 credentials 조회
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
※Bunny 2.2.x 이후 드라이버 버전에서는 TLS/SSL CA의 경로를 지정하지 않으면 기본으로 설정된 경로를 사용한다. Ex) Ubuntu/Debian : /etc/ssl/certs/ca-certificates.crt

2)	./app/controllers/status_controller.rb 서비스 Connection 클래서 호출
```
# encoding: UTF-8      # Encoding 지정(한글지원)
require ‘rabbitmq_service’    # rabbitmq_service 클래스 추가 (각 서비스별 클래스 추가부분)
class StatusController < ApplicationController
  before_action :rabbit_ connection # RabbitMQ 서버 접속
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
    @conn = Connector::RabbitmqService.new.connector   #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.

  end
end
```
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.

##### <div id='19'></div> 2.3.9.	GlusterFS 연동

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./lib/glusterfs_service.rb </td>
    <td> Vcap 클래스를 상속하여 Connection을 생성하는 클래스 </td>
</tr>
<tr>
    <td> ./app/controllers/upload_controller.rb </td>
    <td> 서비스 Connection 클래스 호출하여 사용하는 컨트롤러 클래스 </td>
</tr>
</table>

1)	./lib/glusterfs_service.rb
-	Vcap 클래스를 상속하여 GlusterFS Connection을 생성하는 클래스

```
require 'vcap'
module Connector
  class Glusterfs < VcapService::Vcap

    def initialize
      super()
    end
    def connector
      credentials = serviceInfo('glusterfs') # “glusterfs” 서비스 credentials 조회
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
2)	./app/controllers/upload_controller.rb 서비스 Connection 클래서 호출
```
# encoding: UTF-8      # Encoding 지정(한글지원)
require ‘glusterfs_service’    # glusterfs_service 클래스 추가 (각 서비스별 클래스 추가부분)
class UploadController < ApplicationController
  before_filter :authenticate   # 메서드를 호출하기전 인증여부를 확인합니다.
  before_action :gs_connection  # 파일 업로드를 위한 Swift 인증정보를 획득합니다.

  # 파일을 업로드 합니다.
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
    @service = Connector::Glusterfs.new.connector   #서비스 연동 클래스를 호출하여 접속정보를 획득하고 이를 클래스 변수로 선언하였다.

  end
end
```
※해당 클래스는 샘플 예제이며 서비스의 접속정보의 획득 및 활용 방법은 애플리케이션의 구조및 특성에 맞게 사용 할 수 있다.


### <div id='21'></div> 2.4.	배포

개발 완료된 애플리케이션을 개방형 플랫폼에 배포하는 방법을 설명한다.

##### <div id='22'></div> 2.4.1.	개방형 플랫폼 애플리케이션 배포

<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> ./manifest.yml </td>
    <td> 개방형 플랫폼 배포 환경 설정 파일 </td>
</tr>
</table>

1)	./manifest.yml 생성
-	cf push 명령시 현재 디렉토리의 manifest.yml을 참조하여 배포가 진행된다.

```
---
applications:
- name: ruby-sample-app # 애플리케이션 이름
  memory: 512M        # 애플리케이션 메모리 사이즈
  instances: 1           # 애플리케이션 인스턴스 개수
  path: .                # 애플리케이션 위치
  command: bundle exec rails server -p $PORT # 애플리케이션 배포 후 실행 명령어
```
※애플리케이션 스테이징시 할달 받은 포트가 환경변수로 등록되어있다. 이 $PORT는 애플리케이션의 상태 체크에도 사용되므로 위와 같이 포트를 지정할 것을 권장한다.

2)	개방형 플랫폼 로그인
```
$ cf api https://api.cf.open-paas.com   # 개방형 플랫폼 TARGET 지정
 #cf api [개방형 플랫폼 API 주소] : 개방형 플랫폼 API 주소를 지정한다.

$ cf login -u testUser -o sample_test -s sample_space   # 로그인 요청
 #cf login –u [사용자 이름] –o [조직명] –s [스페이스명] : 조직, 스페이스가 없을경우 생성필요

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

3)	개방형 플랫폼 서비스 생성
```
$ cf marketplace     # 마켓플레이스 목록 요청

service         plans                    description
p-mysql	       100mb, 1gb		MySQL databases on demand   
p-rabbitmq     standard		        RabbitMQ is a robust …..   
redis-sb	       shared-vm, dedicated-vm	Redis service to provide a ……

$ cf create-service p-mysql 100mb sample-mysql-instance    # 서비스 생성
 #cf create-service [서비스명] [플랜명] [생성할 서비스명]

$ cf services    # 서비스 목록 조회

name                       service       plan              bound apps		last…
sample-mysql-instance       p-mysql      100mb            node-sample, p....	…
sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
sample-redis-instance        redis-sb      shared-vm         python-sample-....	…
```

4)	개방형 플랫폼 애플리케이션에 서비스 바인딩 및 애플리케이션 시작

```
$ cf push -b https://github.com/cloudfoundry/ruby-buildpack.git#v1.3.1 --no-start 
# 애플리케이션 업로드만 실행하고 시작하지는 않는다.
# 최근 Ruby 빌드팩(1.3.1이후)은 Ruby 1.9.3을 기본적으로 지원하지 않는다. Ruby 1.9.3을 지원하는 빌드팩을 지정하여 배포한다. 각 애플리케이션은 Ruby 버전에 맞는 빌드팩을 지정하여 사용하여야 하거나 개방형 플랫폼에서 제공하는 기본 빌드팩을 사용할경우 –b 옵션을 제외 하여도 무방하다.
# cf push –b [사용자 빌드팩 URL] –no-start

$ cf services   # 서비스 목록 조회

name                       service       plan              bound apps		last…
sample-mysql-instance       p-mysql      100mb            node-sample, p....	…
sample-cubrid-instance      CubridDB      utf8              node-sample, p....	…
sample-mongo-instance      Mongo-DB   default-plan        node-sample, p....	…
sample-rabbitmq-instance    p-rabbitmq   standard           python-sample-....	…
sample-redis-instance        redis-sb      shared-vm         python-sample-....	…
sample-glusterfs-instance    glusterfs      glusterfs-1000Mb   glusterfs-samp....	…

$ cf bind-service ruby-sample-app sample-mysql-instance   # 애플리케이션 서비스 바인딩
# cf bind-service [애플리케이션 명] [서비스 명]

$ cf start ruby-sample-app    # 애플리케이션 시작
# cf start [애플리케이션 명]
```
※최신 빌드팩은 Ruby 1.9.3을 지원하지 않기 때문에 ruby-buildpack 1.3.1 버전을 사용하여 배포를 진행합니다. 개방형 플랫폼에서 지원하는 기본 빌드팩을 사용할경우 –b 옵션을 제외   

※애플리케이션 배포절차를 윈도우 머신에서 수행하는 경우(cf cli를 윈도우 머신에 설치하여 사용하는 경우), 애플리케이션 시작('cf start')이 제대로 되지 않을 수 있습니다. 이 때는 bin 폴더내의 3개의 파일 bundle, rake, rails를 유닉스용으로 변환하여 'cf push' 부터 다시 진행합니다. 파일 변환 절차는 다음을 따릅니다.


1.	윈도우 커맨드 창을 열어 애플리케이션 폴더로 이동합니다.
2.	(방법1.) 다음의 url에서 dos2unix를 다운로드 하고 압축을 해제하여 dos2unix.exe파일을 샘플 어플리케이션의 bin 폴더로 이동합니다.    
http://sourceforge.net/projects/dos2unix/files/latest/download

	(방법2.) 샘플 어플리케이션을 'git clone'한 사용자는 다음의 명령어를 이용하여 애플리케이션 폴더 내의 dos2unix 파일의 파일명을 변경합니다. 'rename' 명령어를 사용할 수 없다면, 'ren' 명령어를 대신 사용하거나 직접 파일명을 'dos2unix.exe'로 변경하여도 무방합니다.
>rename dos2unix dos2unix.exe   

3.	다음 명령어를 이용하여 bin 폴더 내의 3개의 파일을 유닉스 파일로 변환합니다.
>dos2unix bin/bundle bin/rake bin/rails   
>※	윈도우즈 Power Shell에서는 해당 명령어가 실행되지 않습니다. 명령 프롬프트를 이용하면 명령어를 실행시킬 수 있습니다.
변환이 정상적으로 완료되면 다음과 같은 화면을 확인할 수 있습니다.
>![ruby16] 

4.	[4) 개방형 플랫폼 애플리케이션에 서비스 바인딩 및 애플리케이션 시작] 절차를 다시 수행합니다.





### <div id='23'></div> 2.5.	테스트

Rspec을 이용한 Ruby 애플리케이션 테스트

1)	폴더 및 파일 정의
<table>
<tr align=center>
    <td> 파일/폴더 </td>
    <td> 목적 </td>
</tr>
<tr>
    <td> Spec </td>
    <td> rspec 테스트 케이스 루비 파일이 존재 폴더 </td>
</tr>
<tr>
    <td> *_spec </td>
    <td> rspec 테스트 케이스 루비 파일 </td>
</tr>
</table>

2)	테스트 실행
>bundle exec rspec   
      ※정상적인 테스트 진행을 위해서는 해당 서비스와 접속이 가능하여야 한다.(프록시, 터널링 등..)
      


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


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Ruby 개발
