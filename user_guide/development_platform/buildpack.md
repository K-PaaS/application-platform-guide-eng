### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Buildpack Development

## Table of Contents
1. [Document Outline](#1)
    * [1.1. Purpose](#11)
    * [1.2. Range](#12)
    * [1.3. References](#13)
2. [Buildpack Outline](#2)
    * [2.1. Application Deployment Process](#21)
    * [2.2. Glossary](#22)
    * [2.3. Buildpack Architecture](#23)
    * [2.4. Buildpack Type](#24)
3. [Buildpack Development Guide](#3)
    * [3.1. Necessary Functions](#31)
    * [3.1.1. Detect](#311)
    * [3.1.2. Compile](#312)
    * [3.1.3. Release](#313)
    * [3.2. Additional function](#32)
    * [3.2.1. Package](#321)
    * [3.2.2. Repository](#322)
4. [Buildpack Expansion Guide](#4)    
    * [4.1. JAVA Buildpack Exansion](#41)
    * [4.1.1. Source Sturcture](#411)
    * [4.1.2. Repository Setting Modification](#412)
    * [4.1.3. Component Extension](#413)
    * [4.1.4. Example:Add Component Class](#414)
5. [Buildpack Test Guide](#5) 
    * [5.1. Add System Buildpack](#51)
    * [5.2. Provide GitHub URL](#52) 


# <a name="1"/>1. Document Outline

### <a name="11"/>1.1. Purpose

This document (Development Guide\_Buildpack) guides the build pack development standards for open cloud platform projects and includes the contents from build pack architecture to testing.

This guide provides a better understanding of the build pack and provides a better understanding of the build pack development.
It aims to improve efficiency and maintenance. In addition, the build packs developed according to the presented standards ensure functionality and integrity in open cloud platforms.

### <a name="12"/>1.2. Range
The range of this document is limited to the development of buildpacks related to open cloud platform projects, and exceptions are made for other open source introduction.

### <a name="13"/>1.3. References
-   [***https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending.md***](https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending.md)

-   [***http://blog.cloudfoundry.org/2013/09/06/introducing-the-cloud-foundry-java-buildpack/***](http://blog.cloudfoundry.org/2013/09/06/introducing-the-cloud-foundry-java-buildpack/)

-   [***https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks***](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks)

-   [***http://java.dzone.com/articles/understanding-cloud-foundry***](http://java.dzone.com/articles/understanding-cloud-foundry)

-   ***https://github.com/cloudfoundry/ruby-buildpack***

-   [***https://github.com/cloudfoundry-incubator/buildpack-packager***](https://github.com/cloudfoundry-incubator/buildpack-packager)

# <a name="2"/>2. Buildpack Outline
Applications are developed using various languages and frameworks. 
Buildpacks play a role in supporting applications developed in these various environments to run on an open cloud platform.   
The buildpack may be automatically detected or directly designated when deploying an application.  
This chapter defines the terms used and describes the build pack architecture.

### <a name="21"/>2.1. Application Deployment Process

![그림 2-1 개방형 클라우드 플랫폼에서의 어플리케이션 배포 프로세스][buildpack_develope_guide_01]

**Picture 2-1 Application Deployment Process in Open Cloud Platform**

Applications deployed on open cloud platforms are executed through three stages: Upload, Stage, and Start.  
\[picture 2-1\] shows the deployment process of the application.

The deployment process starts when the user requests for application deployment to the Open Cloud Platform.  
Each step performs the following:  

-   **Upload:** Uploads application source file or packaging files at the platform.

-   **Stage:** Executes buildpack and configures an application runtime environment at the platform.

-   **Start:** The stage runs the completed application.

URL gets assigned when the application deploys and runs normally.
The user can check the application operation by entering URL in the corresponding web browser.

### <a name="22"/>2.2. Glossary

Prior to the architectural description, several terms used in this document are summarized as follows.

-   **Application**: In an open cloud platform, an application is a unit of deployment. 
    Which is, source code or packaged form (e.g.War) refers to a combination of a file and a file defining additional information (meta) to be used during deployment.

-   **Manifest**[^1]: In an open cloud platform, manifest is an element of an application as a file defining deployment's additional information (meta).
    It is written in YAML[^2] form, as the default file name is manifest.yml. 
    The deployment's additional information includes an application name, memory, and the number of instances..
    Below shows an example of manifest.yml for test-app to deploy.

 ````
  applications:

  ㅡ name: test-app

     memory: 512M

     instances: 2

     path: ./test-app/build/libs/test-app.war
````

-   **Droplet:** In an open cloud platform, as an archive file, droplets refer to the result of the stage step. 
    Droplet consists of a file system defined by an open cloud platform.
    Droplets contain elements of the runtime environment (library, configuration file, environment variable, execution information, etc.) that are configured 
    throughout the stage, as well as deployed application code.
    The following shows an example of a droplet file system in which a Java runtime environment is configured.

  ````
  .

  |-- app

    | |-- .java-buildpack

    | | |-- open_jdk_jre \        #Installed Runtime

    | | |-- tomcat \                #Installed Web Container

    | |-- .java-buildpack.log

    | |-- META-INF

    | |-- WEB-INF

  |-- logs

    | |-- staging_task.log

  |-- staging_info.yml

  |-- tmp
  ````

-   **Container**[^3]: An open cloud platform provides each independent execution space for the applications being deployed, which is called a container.
    The droplets are distributed to the container in the start step.

### <a name="23"/>2.3. Buildpack Architecture

![그림2-2 빌드팩 런타임 아키텍처][buildpack_develope_guide_02]

**Picture 2-2 Buildpack Runtime Architecture**

The buildpack is executed during the staging of the application deployment process. 
\[picture 2-2\] shows the buildpack architecture.

The detailed description of buildpack's runtime architecture is as follows.

1)  The application gets deployed at the Open Cloud Platform.

2)  The build pack is requested three actions in turn from the open cloud platform to build the droplets. 
    Each operation of the requested detection, compilation, and release performs the following:  

    -   **Detect:** Check the application source and check the applicability of the build pack.

    -   **Compile:** Download and install the environments necessary to run the application. 
        It installs and configures the environment required for a specific directory location, and assembles application code and configured environment to create droplets. 
        The necessary environments vary depending on the development language of the application, but the following factors are exemplified. (Some representative names) 

        -   Runtime: JVM, Interpreter(Ruby, PHP etc.)

        -   Web Container: tomcat, jboss etc.

        -   Library: Additional libraries needed(Java jar, ruby gems[^4],NPM[^5] packages)

    -   Release: Generates a command for executing the configured runtime environment and delivers the generated information to the open cloud platform.

3)  The generated droplet gets deployed at the container.

Stage is eventually the process by which a build pack "builds" a "droplet" to run an application.

### <a name="24"/>2.4. Buildpack Type

Build packs can be largely divided into two types: system and custom build packs. The definition of each build pack is as follows.

-   **System Buildpack:** It is a buildpack installed on an open cloud platform which can be retrieved and used on a cloud platform. 
    However, in order to register/modify system buildpacks, authentication to the cloud platform is required.

-   **Customize Buildpack:** Refers to the buildpack that exists externally on an open cloud platform.
    It is usually provided as GitURL and loaded when the application is deployed.
    Custom buildpack can be created in two ways: develop a new buildpack or expand the existing buildpack.
    Expanding the existing buildpack refers to modifying the open source of the build pack on GitHub.

This document guides the standards and methods for developing custom buildpack.
In order to develop a new build pack, reference and compliance with the build pack development standards provided in Chapter 3.
Chapter 4 guides on how to expand existing buildpack.

# <a name="3"/>3. Buildpack Development Guide 
A build pack is a collection of scripts that assemble the environment (runtime, container, framework, etc.) necessary to run the application and make up the droplets. 
This chapter first describes the essential functions that must be included in the development of new build packs. 
Hereinafter, additional functions that may be implemented or used according to needs in a specific situation will be described.

### <a name="31"/>3.1. Necessary Functions 

As described in the architecture of Chapter 2, the build pack operates at the request of an open cloud platform.
Therefore, buildpack developers must implement three scripts: Detect, Compile, and Release to work with open cloud platforms. 
These interfaces (API) are written in shell scripts, and there are no restrictions on the development language of the production part. 
This section describes the operations to be implemented in each API through the simplest example of writing.

### <a name="311"/>3.1.1. Detect  

Detection functions to check whether the build pack knows how to configure the runtime environment of the deployed application. 
In the detection script, write the contents to check the applicability of the build pack (if it can be staged). 
The applicability of the build pack is generally determined by the existence of a specific file.

1)  Process Description

> \$ bin/detect &lt;BUILD\_DIR&gt;
>
> The factor value passed when calling the detection script is the build directory BUILD\_DIR. 
> The build directory is the directory where the application files are located, and the script checks the files to determine whether to apply the buildpack.
> If the application to be deployed is a type supported by the build pack, the end value of '0' is returned.
> When the script returns '0', it outputs the name of the detected environment to the user.

1)  Write Example 

> The detection script should be presented with "criteria" to determine whether it is applicable.
> The following shows an example of detecting a ruby application. The example script detects the Ruby environment based on the presence or absence of "**Gemfile**" and outputs it to the user.

  ````
  \#!/usr/bin/env ruby

  gemfile\_path **=** File.**join** ARGV\[0\], "**Gemfile**"

  if File.exist?(gemfile\_path)

    puts "Ruby"

    exit 0

  else

    puts "no"

    exit 1

  end
  ````

### <a name="312"/>3.1.2. Compile
  
Compiling is practically a key feature of the build pack that builds droplets.   
In the compilation script, the contents of downloading and installing the necessary binaries for running the application and placing them in the drop file system are written.  
Examples of binaries required to run an application include runtime (JRE, Ruby, PHP, Node, etc.), web containers (Tomcat, JBoss, Webrick, etc.).

1)  Process Description  

> \$ bin/compile &lt;BUILD\_DIR&gt; &lt;CACHE\_DIR&gt;
>
> The two factor values passed when calling a compilation script are the build directory (BUILD\_DIR) and the cache directory (CACHE\_DIR). 
> The cache directory can be used to temporarily as store dependencies that are downloaded during the buildpack compilation process.
> The compilation process is output to the user during the execution of the script.

1)  Write Example

> Compilation scripts may be written in various ways according to the environment required for running the buildpack and the application.
> Below is an example of a simple compilation script.
> Since the script constitutes an environment for running Ruby applications, it first installs Ruby interpreter in BUILD\_PATH, and writes the contents such as setting the configuration or installing the necessary binaries.

  ````
  \#!/usr/bin/env ruby

  \#sync output

  \$stdout.**sync** **=** **true**

  build\_path **=** ARGV\[0\]

  cache\_path **=** ARGV\[1\]

  def compile

    instrument 'ruby.compile' do

        Dir.chdir(build\_path)

        install\_ruby

        \#...ellipsis

        setup\_language\_pack\_environment

        install\_binaries

        \#...ellipsis

    end

  end

  def install\_ruby

    puts "Installing Ruby"

    \# !!! build tasks go here !!!

    \# download ruby to cache\_path

    \# install ruby

  end
  
  ````

### <a name="313"/>3.1.3. Release 

The release functions to respond to the platform with information on how to run the application.
create information to respond to the platform in the release script.

1)  Process Description

> \$ bin/release &lt;BUILD\_DIR&gt;
>
> When invoking a release script, the factor value passed is the build directory (BUILD\_DIR).
> Execution method information must be provided in YAML format as shown below.

  ````
  config\_vars:

    name: value

  default\_process\_types:

    web: commandLine
  ````
In config\_vars, environment variables to be provided as options are written, and those environment variables refer to variables to be defined in the environment in which the application is executed.
> The default\_process\_types creates the type of application to be executed and the command line to be used when executing it. 
> Currently, only web application types are supported.

1)  Write Example

> Below is an example of response information written in a release script.
> The response information includes the required environmental variables and how to execute them in the Rack[^6] application, and is delivered to the open cloud platform.

  ````
  config\_vars:

    RACK\_ENV: production

  default\_process\_types:

    web: bundle exec rackup config.ru -p \$PORT
  ````

### <a name="32"/>3.2. Additional function 

When compiling, the build pack installs dependencies (such as binaries or libraries) necessary to drive applications.  
In using a network-disconnected environment has limitation because build packs download these dependencies through the network.
Therefore, when developing a buildpack, it is necessary to consider an environment in which an open cloud platform is installed. 
This section describes the package and storage capabilities associated with managing dependencies in buildpacks.

### <a name="321"/>3.2.1. Package 

Package refers to the function of making a buildpack into a single compressed file or the compressed file itself.
The purpose of the build pack package is as follows: first is to make it registerable as a system build pack, and the second is to support build pack compilation in a network-disconnected environment. 
The developer may additionally implement the following package functions on the buildpack.

-   **Packaging Function:** It is a function to package online or offline.
    The difference between the two package types is whether or not the network is connected when compiling the build pack.
    Online packaging only compiles the basic elements, so when compiling, it accesses the network and downloads dependencies. 
    On the other hand, an offline package is a version of a build pack package designed to run without network connectivity, and includes all the dependencies supported by the package.

-   **Versioning Funcion:** When packaging a buildpack, it is a function of adding version information to the package name to be generated.

To support the package, there are two ways to implement packaging and versioning functions directly, or to use the Buildpack packager application provided by Cloud Foundry.

1)  Example of direct implementation

> Cloud Foundry's JAVA-BUILDPACK supports package functions through the Package module included in the source.
> The package is performed via the bundle exec rake[^7] command.
> Offline packages can be created by using OFFLINE=true as factor values, and version information can be added to package names by using *VERSION=&lt;VERSION&gt;* factor values.
>
```` 
\$ bundle install
````
````
\$ bundle exec rake package OFFLINE=true VERSION=2.1
````
> ...
````
\$ Creating build/java-buildpack-offline-2.1.zip
````
> The bundle exec rake command executes the package modules defined in the Rakefile [^8] in order, and the execution order is as follows. 
> When creating offline packages, first download all the dependencies defined in the settings files.
> Then disable the remote\_download value in cache.yml, and package.Create a zip file.
> Below is a part of the Rakefile and Package module..

  ````
  \#Rakefile

  require 'rakelib/dependency\_cache\_task'

  require 'rakelib/stage\_buildpack\_task'

  require 'rakelib/package\_task'

  Package::**DependencyCacheTask**.new

  Package::**StageBuildpackTask**.new(Dir\['bin/\*\*/\*', 'config/\*\*/\*', 'lib/\*\*/\*', 'resources/\*\*/\*'\].reject { |f| File.directory? f })

  Package::**PackageTask**.new

  ----
    
  \#Package Module

  require 'java\_buildpack/buildpack\_version'

  module Package

    def self.offline

        '-offline' if BUILDPACK\_VERSION.offline

    end

    def self.version

        BUILDPACK\_VERSION.version || 'unknown'

    end

  BUILD\_DIR = 'build'.freeze

  STAGING\_DIR = "\#{BUILD\_DIR}/staging".freeze

  BUILDPACK\_VERSION = JavaBuildpack::BuildpackVersion.new(false).freeze

  PACKAGE\_NAME = "\#{BUILD\_DIR}/java-buildpack\#{offline}-\#{version}.zip".freeze

  class DependencyCacheTask** &lt; Rake::TaskLib

    def initialize

        return unless BUILDPACK\_VERSION.offline

        @cache = cache

        uris(configurations).each { .. \[cache\_task(uri)\] }

    end

    def cache

        JavaBuildpack::Util::Cache::DownloadCache.new(..).freeze

    end

    def cache\_task(uri)

        task uri do |t|

            rake\_output\_message "Caching \#{t.name}"

                cache.get(t.name) {}

            end

            uri

        end

    end

  class StageBuildpackTask** &lt; Rake::TaskLib

    def initialize(source\_files)

        disable\_remote\_downloads\_task if BUILDPACK\_VERSION.offline

    end

  def disable\_remote\_downloads\_task

    file "\#{STAGING\_DIR}/config/cache.yml" do |t|

        content = File.open(t.source, 'r') { |f| f.read.gsub(/enabled/, 'disabled') }

            File.open(t.name, 'w') { |f| f.write content }

        end

    end

  end

  class PackageTask** &lt; Rake::TaskLib

    def initialize

        desc 'Create packaged buildpack'

        task package: \[PACKAGE\_NAME\]

        multitask PACKAGE\_NAME =&gt; \[BUILD\_DIR, STAGING\_DIR\] do |t|

            rake\_output\_message "Creating \#{t.name}"

                Zip::File.open(t.name, Zip::File::CREATE) do |zipfile|

                    Dir\[File.join(STAGING\_DIR, '\*\*', '\*\*')\].each do |file|

                        zipfile.add(file.sub("\#{STAGING\_DIR}/", ''), file)

                    end

                end

            end

        end

    end

  end
  ````

1)  Buildpack packager use example

> Buildpack packager is a buildpack package tool provided by Cloud Foundry.
> Buildpack-packager is to cache the dependencies of the build pack, not the dependencies of the application.
> Below is how to package RUBY buildpack use buildpack-packager.
>
> \# Fetch submodules(compile-extentions) from ruby-buildpack git.
````
\$ git submodule update –init
````
> \# Download the final build pack dependencies.
````
\$ BUNDLE\_GEMFILE=cf.Gemfile bundle
````
> \# Execute buildpack packager.
````
\$ BUNDLE\_GEMFILE=cf.Gemfile bundle exec buildpack-packager \[uncached | cached \]
````
> The file must be created first because the Buildpack packager checks the manifest.yml file at run time.
> Below is an example of how to create manifest.yml.

  ````
  \#manifest.yml

  ---

  language: ruby

  url\_to\_dependency\_map:

  - match: bundler-(\\d+\\.\\d+\\.\\d+)

    name: bundler

    version: \$1

  - match: ruby-(\\d+\\.\\d+\\.\\d+)

    name: ruby

    version: \$1

  dependencies:

  - name: bundler

    version: 1.7.12

    uri: https://pivotal-buildpacks.s3.amazonaws.com/ruby/binaries/lucid64/bundler-1.7.12.tgz

    md5: ab7ebd9c9e945e3ea91c8dd0c6aa3562

    cf\_stacks:

    - lucid64

  - cflinuxfs2

  - name: ruby

  version: 2.1.4

  uri: https://pivotal-buildpacks.s3.amazonaws.com/ruby/binaries/lucid64/ruby-2.1.4.tgz

  md5: 72b4d193a11766e2a4c45c1fed65754c

  cf\_stacks:

  - lucid64

  exclude\_files:

    - .gitignore

    - private.key
  ````

-   **language:** Create a name to be used for the package file.
    

-   **url\_to\_dependency\_map:** Create a list of regular expressions to extract and map the names and versions of the dependencies.

-   **dependencies:** When compiling, specify the name, version, uri, md5, cf\_stacks[^9] information for the resources to be downloaded.
    The packager downloads and installs the stated resources into the dependencies folder.
    Dependencies are included in the package folder.

> The packager adds everything in the build pack directory to the package file, except the files specified in exclude\_files in manifest.yml.
> If executed with the cached option, download the dependencies specified in manifest.yml and include them in the package file.
> The cached option plays the same role as the offline package..

### <a name="322"/>3.2.2. Repository 

> Storage is a space where various dependencies exists that are downloaded when compiling a buildpack.
> The storage may be configured at an external or internal network location depending on the environment in which the open cloud platform is installed.
> Buildpack provides a way to set and modify the location of a repository, and these storage settings are specific to each build pack source.
> The corresponding setup method will be covered in detail in the 4. Build Pack Expansion Guide..
> However, in the case of build packs (e.g.ruby) using Build-packager, write the storage location and library information to be downloaded in the dependencies:uri item of the manifest.yml file described in the package.

# <a name="4"/>4. Buildpack Expansion Guide 

A buildpack or application developer can modify an existing buildpack to create the needed buildpack.
GitHub has a number of buildpack sources to support various application execution environments.
This chapter describes the design and source structure of each buildpack by giving examples, among the buildpacks that exist on GitHub, and guides to expand.

### <a name="41"/>4.1. JAVA Buildpack Expansion 

The JAVA buildpack aims to configure a JVM-based application execution environment.
The JAVA build pack is designed with three types of standard components: Containers, Frameworks, and JREs.
\[Table 4-1\] represents the standard component type and description of the JAVA build pack.

        Table 4‑1. JAVA Buildpack Standard Components

| Component Type | Description |
|-------------|-----------------------------------------------------------------------------------------|
|Container | -   A container is a component that indicates how the application will be executed. Components of this type are responsible for determining which containers to download and use, and for creating commands to run at runtime on the platform.<br>-   Only one container component may execute the application. If more than one container is used, an error occurs in the staging stage.<br> -   Container types most simply includes application servers, servlet containers, etc., from Java main() functions.|
|Framework | -   A framework is a component that represents changes used when an additional operation or application is executed. Components of this type are responsible for determining which frameworks are needed, transforming applications, and providing additional other options that should be used at runtime.<br> -   Multiple framework components may be used when executing an application.<br> -   The framework type includes the ability to download JDBC jars, in order to automatically reset the service bind and DataSource.|
|Jre | -   Jre represents the JAVA environment used when the application is executed. Components of this type are responsible for resolving which jre to use, download and unpack, and the detailed jre options to use at runtime.<br> - Only one Jre may be used to execute the application. If more than one Jre is used, an error occurs in the staging stage.| 

JAVA build pack developers can add new components by referring to previously implemented components according to their standard component type. 
Components that belongs to ruby file class are implemented as one by type.

### <a name="411"/>4.1.1. Source Structure 

Components belonging to each type are implemented as one ruby file class. 
\[Table 4-2\]shows the source structure and description of the JAVA buildpack.

        Table 4‑2. JAVA Buildpack Source Structure

| Parent Directory | Subdirectory / Key files | Description |
|-------------|-----------------------------|-----------|
|/bin    |detect.rb<br>compile.rb<br>release.rb| Directory for required function scripts.|
|/congig |components.yml<br>repository.yml<br>tomcat.yml<br>...| Directory for URL (storage location and version) settings files of dependencies to be downloaded.|
|/docs   |design.md<br>extending.md<br>...| The directory for the guide document of the source.|
|/lib    |java\_buildpack.rb<br>/java\_buildpack<br>/component<br>/container<br>framework<br>jre<br>...| Directory for actual implementation sources of detect, compile, and release operations for each component.|
|/rakelib |package.rb<br>... | This is a directory for package modules to be run by Rake. Used for package functionality. |
|resources |/tomcat/conf<br>context.xml<br>logging.properties<br>server.xml<br>... | Directory for resources that need to be modified or added.|
|/spec |... | It is a directory to test the sources using Rspec[^11].

JAVA buildpack developers can extend existing buildpacks by changing the configuration files in the /config directory of the source structure or by adding new components to the subdirectories of the /lib directory.

### <a name="412"/>4.1.2. Repository Setting Modification 

The JAVA build pack downloads the required libraries through the default repository (http://download.run.pivotal.io )and the version specified in the config file.
If the application you are deploying needs to use a specific library version, or if the network environment of the cloud platform is limited, you need to change the fixed settings. Therefore, the JAVA build pack provides a way to change the version of the libraries provided and the storage location to download them.
To change the settings associated with these repositories, *&lt;componentname&gt;.yml* in the config directory of the source structure.You can modify the contents of the yml* file.
Below is an example of a setup element file.

  ````
  \# java-buildpack/config/repository.yml

  default\_repository\_root: https://download.run.pivotal.io
  ````

  ````
  \# config/tomcat.yml

  ---

  tomcat:

    version: 8.0.+

    repository\_root: "{default.repository.root}/tomcat"

  lifecycle\_support:

    version: 2.+

    repository\_root: "{default.repository.root}/tomcat-lifecycle-support"

  logging\_support:

    version: 2.+

    repository\_root: "{default.repository.root}/tomcat-logging-support"

  access\_logging\_support:

    version: 2.+

    repository\_root: "{default.repository.root}/tomcat-access-logging-support"

    access\_logging: disabled

  \#...중략
  ````

repository.yml에 설정된 기본 저장소에서는 라이브러리 파일들을
&lt;VERSION&gt;:&lt;URI&gt;형태로 관리하며, 저장소의 루트는 index.yml
파일을 포함하고 있다. 다음은 index.yml의 예를 나타낸다.

  ````
  \# index.yml

  *---*

  *6.0.0: https://download.run.pivotal.io/tomcat/tomcat-6.0.0.tar.gz*

  *…*

  *7.0.59: https://download.run.pivotal.io/tomcat/tomcat-7.0.55.tar.gz*

  *…*

  *8.0.1: https://download.run.pivotal.io/tomcat/tomcat-8.0.1.tar.gz*

  *…*

  *8.0.21: https://download.run.pivotal.io/tomcat/tomcat-8.0.21.tar.gz*
  ````

### <a name="413"/>4.1.3. 컴포넌트 확장(Extends) 

JAVA기반의 어플리케이션을 위한 개발 및 실행 환경은 다양하다. 따라서
이러한 다양한 환경을 지원하기 위해, JAVA 빌드팩 개발자는 기존의 컴포넌트
외에 새로운 컴포넌트를 추가할 수 있다. 사용자 정의 컴포넌트는 다음과
같은 순서를 통해 JAVA빌드팩에 추가할 수 있다.

1)  JAVA빌드팩은 컴포넌트를 구현하는 데 도움이 되는 3가지 기본
    클래스들을 제공한다. 개발자는 해당 기본 클래스 중 하나를 확장하여
    새로운 컴포넌트 클래스를 작성한다.

-   [**JavaBuildpack::Component::BaseComponent**](https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending-base_component.md)**:**
    BaseComponent는 JAVA빌드팩의 모든 컴포넌트를 위한 기본 클래스이다.
    컴포넌트 동작의 부분으로써, 파일 다운로드를 위한
    메소드들을 제공한다. 아래는 BaseComponent 클래스를 나타낸다.

  ````
  \# lib/java\_buildpack/component/base\_component.rb

  class **BaseComponent**

    def initialize(context)

        @application = context\[:application\]

        @component\_name = self.class.to\_s.space\_case

        @configuration = context\[:configuration\]

        @droplet = context\[:droplet\]

    end

  def detect

    \#구현 필요 

  end

  def compile

    \#구현 필요

  end

  def release

    \#구현 필요

  end

  def download(version, uri, name = @component\_name)

    JavaBuildpack::Util::Cache::ApplicationCache.new.get(uri) do |file, downloaded|

        puts downloaded ?

            "(\#{(Time.now - download\_start\_time).duration})" : '(found in cache)'

        yield file

    end

  end

  def download\_jar(version, uri, jar\_name, target\_directory = @droplet.sandbox,name = @component\_name)

    download(version, uri, name) do |file|

        FileUtils.mkdir\_p target\_directory

        FileUtils.cp\_r(file.path, target\_directory + jar\_name)

    end

  end

  def download\_tar(version, uri, target\_directory = @droplet.sandbox,name = @component\_name)

    download(version, uri, name) do |file|

        with\_timing "Expanding \#{name} to \#{target\_directory.relative\_path\_from(@droplet.root)}" do

            FileUtils.mkdir\_p target\_directory

                shell "tar xzf \#{file.path} -C \#{target\_directory} --strip 1 2&gt;&1"

            end

        end

    end

    \#...중략

  end
  ````

-   [***JavaBuildpack::Component::ModularComponent***](https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending-modular_component.md):
    ModularComponent는 모듈화 할 필요가 있을만큼 복잡한 컴포넌트를
    위한 기본클래스이다. 해당 클래스는 컴포넌트가 여러 개의 하위
    컴포넌트들로 구성되는 것을 가능하게 하고, 하위 컴포넌트들 모두에
    걸쳐 컴포넌트 라이프사이클을 조정한다. 아래는
    ModularComponent클래스를 나타낸다.

  ````
  \# lib/java\_buildpack/component/modular\_component.rb

  class ModularComponent &lt; BaseComponent

        def initialize(context, &version\_validator)

            super(context, &version\_validator)

            @sub\_components = supports? ? sub\_components(context) : \[\]

        end

        def detect

            supports? ? @sub\_components.map(&:detect).flatten.compact : nil

        end

        def compile

            @sub\_components.each(&:compile)

        end

        def release

            @sub\_components.map(&:release)

            command

        end

  \# 추가하려는 컴포넌트가 컨테이너 유형이라면, 실행하는데 필요한 명령어를 반환

  def command

    \#구현 필요

  end

  \# The sub\_components that make up this component

  \# @return \[Array&lt;BaseComponent&gt;\] 하위 컴포넌트가 되는 BaseComponent의 모음을 반환

  def sub\_components(\_context)

    \#구현 필요 

  end

  def sub\_configuration\_context(context, key)

    c = context.clone

    c\[:configuration\] = context\[:configuration\]\[key\]

    c

  end

   \# @return \[Boolean\] 컴포넌트가 어플리케이션을 지원하는지 아닌지를 응답

  def supports?

        \#구현 필요 

    end

  end
  ````

-   [***JavaBuildpack::Component::VersionedDependencyComponent***](https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending-versioned_dependency_component.md)**:**
    VersionedDependencyCom-ponent는 의존성 라이브러리들의 다운로드를
    위해 저장소를 사용하는 컴포넌트들을 위한 기본 클래스이다. 해당
    클래스는 설정파일에 지정된 저장소로부터 각각의 컴포넌트가 @version과
    @uri를 찾는 것을 보장한다. 아래는
    VersionedDependencyComponent클래스를 나타낸다.

  ````
  \# lib/java\_buildpack/component/versioned\_dependency\_component.rb

  class VersionedDependencyComponent &lt; BaseComponent

    def initialize(context, &version\_validator)

        super(context)

        if supports?

            @version, @uri =

                JavaBuildpack::Repository::ConfiguredItem.find\_item(@component\_name, @configuration, &version\_validator)

        else

            @version = nil

            @uri = nil

        end

    end

    def detect

        @version ? id(@version) : nil

    end

    \# @return \[Boolean\] 컴포넌트가 어플리케이션을 지원하는지 아닌지를 응답

    def supports?

        \#구현 필요

    end

    \# JAR 이름을 생성 &lt;component-id&gt;-&lt;version&gt;.jar

    \# @return \[String\] 생성된 JAR이름을 반환

    def jar\_name

        "\#{@droplet.component\_id}-\#{@version}.jar"

    end

    def id(version)

        "\#{self.class.to\_s.dash\_case}=\#{version}"

    end

    def download\_jar(jar\_name = jar\_name, target\_directory = @droplet.sandbox,name = @component\_name)

        super(@version, @uri, jar\_name, target\_directory, name)

    end

    def download\_tar(target\_directory = @droplet.sandbox, name = @component\_name)

        super(@version, @uri, target\_directory, name)

    end

  **end**
  ````

> 기존의 컴포넌트들은 위에서 설명한 기본 클래스들을 확장하여 구현되었다.
> 또한 기본 컴포넌트 클래스들은 각각 아래와 같은 초기화
> 메소드(initialize)를 가지며, Context를 파라미터로 받는다.

  ````
  def initialize(context)

    @application = context\[:application\]

    @component\_name = self.class.to\_s.space\_case

    @configuration = context\[:configuration\]

    @droplet = context\[:droplet\]

  End
  ````

> Context는 컴포넌트에 의해 사용되는 유틸리티의 모음으로써, Application,
> Configuration, Droplet 3가지 항목이 있다. 인스턴스가 생성될 때
> context인스턴스 변수의 키에 매칭 및 배정된다.

| Context 유형 | 클래스 및 설명 |
|-------------|-----------------------------|
|Applicaion    |JavaBuildpack::Component::Application<br>어플리케이션을 위한 추상화 클래스|
|Configuration |Hash<br>컴포넌트 환경설정 정보 config/&lt;component-name&gt;.yml| 
|Droplet   |JavaBuildpack::Component::Droplet<br>Droplet을 위한 추상화 클래스|

2)  새로운 클래스 파일을 표준 컴포넌트 타입에 따라 아래와 같은 적합한
    위치에 추가한다.

| 컴포넌트 유형 | 디렉터리 |
|-------------|---------|
|Container | lib/java\_buildpack/container|
|Framework | lib/java\_buildpack/framework|
|JRE | lib/java\_buildpack/jre|


3)  새로운 클래스에 필수 기능 검출(Detect), 컴파일(Compile),
    릴리즈(Release)를 구현한다.

> 새로 추가하는 컴포넌트 클래스는 빌드팩의 필수 기능인 Detect, Compile,
> Release 메소드를 구현한다. 확장한 기본 클래스에 따라 Detect 기능은
> support 메소드로 구현한다.

4)  새로운 클래스의 이름을 config/components.yml 파일에 추가한다.

### <a name="414"/>4.1.4. 예제: 컴포넌트 클래스 추가 

Tomcat Container컴포넌트를 추가하는 예를 앞서 순서에 맞춰 설명한다.

1)  JavaBuildpack::Component::ModularComponent 기본 클래스를 확장하여,
    Tomcat Container컴포넌트를 추가한다.

2)  Container 타입의 컴포넌트들이 위치한 폴더에 tomcat container 클래스
    tomcat.rb를 위치시킨다. (lib/java buildpack/container/tomcat.rb)

3)  필수 기능 검출(Detect), 컴파일(Compile), 릴리즈(Release)를 다음과
    같이 구현한다.

-   **검출(Detect) 구현:** 배포된 어플리케이션에 대해 tomcat container를
    적용할지 말지를 확인하는 내용을 구현한다. war 파일을 예로들면
    tomcat.rb의 리턴값이 true여야 tomcat을 적용한다. 아래는 tomcat에
    구현된 detect 기능의 예를 보여준다.

 ````
  \# lib/java\_buildpack/container/tomcat/tomcat.rb

  def supports?

    web\_inf? && !JavaBuildpack::Util::JavaMainUtils.main\_class(@application)

  end

  def web\_inf?

    (@application.root + 'WEB-INF').exist?

  End
  ````

> 어플리케이션이 WEB-INF 폴더를 가지고 있고, main class가 아닐때
> 어플리케이션 실행을 위해 사용할 컨테이너로 tomcat이 적용된다.

-   **컴파일(Compile) 구현:** 파일시스템을 만드는 동안 해야하는 포괄적인
    작업을 구현한다. 아래는 tomcat\_instance에 구현된 compile 기능의
    예를 보여준다.

  ````
  \# lib/java\_buildpack/container/tomcat/tomcat\_instance.rb

  def compile

    download(@version, @uri) { |file| expand file }

    link\_to(@application.root.children, root)

    > @droplet.additional\_libraries &lt;&lt; tomcat\_datasource\_jar if tomcat\_datasource\_jar.exist?

    @droplet.additional\_libraries.link\_to web\_inf\_lib

  end

  def expand(file)

        > with\_timing "Expanding Tomcat to \#{@droplet.sandbox.relative\_path\_from(@droplet.root)}" do

        FileUtils.mkdir\_p @droplet.sandbox

        shell "tar xzf \#{file.path} -C \#{@droplet.sandbox} --strip 1--exclude webapps 2&gt;&1"

        @droplet.copy\_resources

        configure\_linking

  end

  end
  ````

> 위 소스코드는 tomcat과 어플리케이션 파일들의 링크를 준비하는 내용이다.
> 따라서 어플리케이션 파일들은 tomcat classpath에서 사용할 수 있다. 위
> 소스코드가 실행될 때 작업 디렉터리(working directory)는 다음과 같이
> 구성된다.

  ````
  \# working directory

  .app =&gt; @application, contains the extracted war archive

  .buildpack/tomcat =&gt; @droplet.sandbox

  .buildpack/jdk

  .buildpack/other needed components
  ````

> 작업디렉터리와 함께 tomcat\_instance의 컴파일 메소드를 상세히 설명하면
> 다음과 같다. 우선 /config/tomcat.yml 을 참고하여 tomcat 바이너리
> 파일을 다운로드 하고, @droplet.sandbox 디렉터리에 압축을 푼다. 그리고
> 리소스 폴더(/resources/tomcat/conf)의 파일들을 @droplet.sandbox/conf
> 로 복사한다. 이후, .app/ 폴더에 @droplet.sandbox/webapps/ROOT의 심볼릭
> 링크를 만들고, WEB-INF/lib에 추가 라이브러리들의 심볼릭 링크를 만든다.
> 단, 모든 심볼릭 링크는 상대경로를 사용한다.

-   **릴리즈(Release)구현:** tomcat을 시작하는 방법에 대한
    명령을 설정한다. 아래는 tomcat에 구현된 release 기능의
    예를 보여준다.

  ````
  \# lib/java\_buildpack/container/tomcat/tomcat.rb

  def command

    @droplet.java\_opts.add\_system\_property 'http.port', '\$PORT'

    \[

    @droplet.java\_home.as\_env\_var, @droplet.java\_opts.as\_env\_var,

    > "\$PWD/\#{(@droplet.sandbox + 'bin/catalina.sh').relative\_path\_from(@droplet.root)}",'run'

    \].flatten.compact.join(' ')

  End
  ````

> 위 소스코드에서는 command 메소드를 통해, tomcat의 server.xml에서
> 참조하는http.port 를 java 시스템변수에 추가하고, tomcat을 시작하는
> 명령어를 작성한다. ("./bin/catalina.sh run")

1)  클래스의 이름을 config/components.yml 파일에 추가한다.

  ````
  \# config/components.yml

  ---

  containers:

    - "JavaBuildpack::Container::JavaMain"

    - "JavaBuildpack::Container::Jboss"

    - "JavaBuildpack::Container::SpringBoot"

    - "JavaBuildpack::Container::SpringBootCLI"

  - "JavaBuildpack::Container::Tomcat" \#추가

  jres:

    - "JavaBuildpack::Jre::OpenJdkJRE"

  frameworks:

    - "JavaBuildpack::Framework::JavaOpts"

    - "JavaBuildpack::Framework::SpringAutoReconfiguration"

    - "JavaBuildpack::Framework::SpringInsight"
  ````

# <a name="5"/>5. 빌드팩 테스트 가이드 

개발한 빌드팩의 적용 테스트는 시스템 빌드팩에 추가 또는 GitHub URL을
사용하는 2가지방법으로 시도할 수 있다. 본 장에서는 해당 적용 테스트
방법들을 가이드한다. 테스트를 위해서는 PC에 CF CLI(Command Line
Interface) 툴을 설치하고, 개발한 빌드팩을 적용할 테스트 어플리케이션을
준비해야한다.

### <a name="51"/>5.1. 시스템 빌드팩 추가 

개발한 빌드팩을 시스템 빌드팩에 추가하는 것은 CF CLI 명령어를 통해
수행하며, 개방형 클라우드 플랫폼에 대한 관리자 권한이 필수적이다. JAVA
빌드팩을 예로들면, 다음과 같은 순서와 명령어를 통해 시스템 빌드팩에
추가하고, 어플리케이션 배포 시 추가한 빌드팩을 선택한다.
````
\# 개발한 빌드팩을 패키징한다.


\$ bundle install


\$ bundle exec rake package VERSION=2.7

…

Creating build/java-buildpack-2.7.zip

\# 시스템 빌드팩을 생성하고, 조회한다.

\# cf create-buildpack &lt;생성할 이름&gt; &lt;패키지 파일&gt;&lt;우선순위&gt;

\$ cf create-buildpack java-buildpack-2.7 java-buildpack-2.7.zip 1


\$ cf buildpacks

\# 어플리케이션 배포시, 추가한 빌드팩 이름을 지정한다.

\$ cf push –b java-buildpack-2.7
````
### <a name="52"/>5.2 GitHub URL 제공 

어플리케이션 배포 명령어(push)의 옵션 값(-b)으로 개발한 빌드팩의
공용(Public) 또는 개인(Private) git 저장소의 URL을 입력하여 적용테스트를
할 수 있다. Git URL로 빌드팩을 제공하는 경우 어플리케이션이 플랫폼에
배포될 때 저장소로부터 복제되고, Detect 스크립트가 ‘0’ 리턴값을 제공하면
어플리케이션에 적용된다.
````
\$ cf push -b https://github.com/johndoe/my-buildpack.git
````
사용자이름/패스워드 인증이 필요한 개인 git 저장소를 사용하는 경우,
다음과 같이 요청하면 된다.
````
\$ cf push -b
https://username:password@github.com/johndoe/my-buildpack.git
````
기본적으로 개방형 클라우드 플랫폼은 빌드팩의 git 저장소의 마스터
브랜치를 사용한다. 다른 브랜치를 사용하기 위해서는 아래와 같이 요청하면
된다.
````
\$ cf push -b
https://username:password@github.com/johndoe/my-buildpack.git\#my-branch-name
````
※주의: 윈도우에서 작업한 빌드팩을 git 저장소에 처음 업로드 하는 경우,
“bin” 디렉터리 안에 존재하는 detect, compile, release 스크립트의
실행(executable)속성이 없어질 수 있다. 이 경우, 플랫폼에서 빌드팩을
실행시키지 못하여, 오류가 발생하게 된다. 따라서 리눅스 환경에서 각각의
스크립트에 실행속성을 부여하고, git 저장소에 이를 적용하는 추가적인
작업이 필요할 수 있다.


[^1]: Application Manifests,[***http://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html***](http://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html)

[^2]: YAML Ain’t Markup Language, [***http://www.yaml.org***](http://www.yaml.org),[***http://ko.wikipedia.org/wiki/YAML***](http://ko.wikipedia.org/wiki/YAML)

[^3]: 컨테이너는 호스트운영체제의 자원(CPU, 메모리, 블록I/O, 네트워크
    등)을 공유하여 사용한다.

[^4]: 루비의 서드파티 라이브러리들을 gem이라하며, RubyGems라는 패키지
    매니저로 관리할 수 있다.

[^5]: NPM은 javascript를 위한 패키지 매니저이다.

[^6]: Rack, ruby web server interface

[^7]: Ruby Make(Rake), [***https://github.com/ruby/rake***](https://github.com/ruby/rake)

[^8]: Rake’s version of Makefiles(Rakefiles)

[^9]: cf stack, the root file system

[^10]: Rspec, 루비를 위한 BDD(behavior-driven development) 프레임워크[***http://rspec.info/***](http://rspec.info/)

[buildpack_develope_guide_01]:./images/openpaas-buildpack-devolpe-guide/buildpack_develope_guide_01.png
[buildpack_develope_guide_02]:./images/openpaas-buildpack-devolpe-guide/buildpack_develope_guide_02.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Buildpack 개발
