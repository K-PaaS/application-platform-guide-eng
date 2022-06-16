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
> The cash directory can be used to temporarily as store dependencies that are downloaded during the buildpack compilation process.
> The compilation process is output to the user during the execution of the script.

1)  Write Example

> 컴파일 스크립트는 빌드팩과 어플리케이션이 구동 시 필요로 하는 환경에 따라 다양하게 작성될 수 있다.
> 아래는 컴파일 스크립트의 간단한 작성 예를 보여준다.
> 해당 스크립트는 Ruby 어플리케이션을 구동시키기 위한 환경을 구성하므로, BUILD\_PATH에 Ruby 인터프리터를 우선 설치하고, 환경설정이나 필요한 바이너리들을 설치하는 등의 내용을 작성한다.

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

        \#...중략

        setup\_language\_pack\_environment

        install\_binaries

        \#...중략

    end

  end

  def install\_ruby

    puts "Installing Ruby"

    \# !!! build tasks go here !!!

    \# download ruby to cache\_path

    \# install ruby

  end
  
  ````

### <a name="313"/>3.1.3. 릴리즈 (Release) 

릴리즈는 어플리케이션 실행방법에 대한 정보를 플랫폼에 응답해주는 기능을
한다. 릴리즈 스크립트에는 플랫폼에 응답할 정보를 생성하는 내용을
작성한다.

1)  동작 설명

> \$ bin/release &lt;BUILD\_DIR&gt;
>
> 릴리즈 스크립트를 호출할 때 전달되는 인자 값은, 빌드
> 디렉터리(BUILD\_DIR)이다. 실행방법 정보는 반드시 아래와 같은 포맷의
> YAML로 제공해야 한다.

  ````
  config\_vars:

    name: value

  default\_process\_types:

    web: commandLine
  ````

> config\_vars에는 옵션으로 제공 할 환경변수들을 작성하며, 해당
> 환경변수들은 어플리케이션이 실행되는 환경에서 정의될 변수들을
> 의미한다. default\_process\_types는 실행될 어플리케이션의 유형과
> 실행시킬 때 사용할 명령행을 작성한다. 현재는 웹 어플리케이션 유형만을
> 지원한다.

1)  작성 예

> 아래는 릴리즈 스크립트에 작성된 응답정보의 예를 보여준다. 해당
> 응답정보는 Rack[^6] 어플리케이션에서 필요한 환경변수와 실행하는 방법을
> 포함하고 있으며, 개방형 클라우드 플랫폼에 전달된다.

  ````
  config\_vars:

    RACK\_ENV: production

  default\_process\_types:

    web: bundle exec rackup config.ru -p \$PORT
  ````

### <a name="32"/>3.2. 부가 기능 

빌드팩은 컴파일 시, 어플리케이션 구동을 위해 필요한 종속성들(바이너리나
라이브러리 등)을 설치한다. 기본적으로 빌드팩은 이러한 종속성을
네트워크를 통해 다운로드하기 때문에, 네트워크가 단절된 환경에서의 사용에
제약이 있다. 따라서 빌드팩 개발 시, 개방형 클라우드 플랫폼이 설치되는
환경에 대해서도 고려할 필요가 있다. 본 절에서는 빌드팩의 종속성 관리와
관련이 있는 패키지와 저장소 기능에 대해 설명한다.

### <a name="321"/>3.2.1. 패키지 (Package) 

패키지는 빌드팩을 하나의 압축파일로 만드는 기능 혹은 압축파일 자체를
의미한다. 빌드팩 패키지의 목적은 다음 2가지이다. 첫째는 시스템
빌드팩으로 등록 가능한 형태로 만드는 것이고, 둘째는 네트워크가 단절된
환경에서 빌드팩 컴파일을 지원하는 것이다. 개발자는 이를 위해, 빌드팩에
다음과 같은 패키지 기능들을 추가로 구현 할 수 있다.

-   **패키징(Packaging) 기능:** 온라인 또는 오프라인 타입으로
    패키징하는 기능이다. 두 패키지 타입의 차이점은 빌드팩 컴파일 시,
    네트워크 접속 여부에 있다. 온라인 패키징은 기본요소들만 압축파일로
    만들기 때문에, 컴파일 시 네트워크에 접속하여 종속성들을
    다운로드 한다. 반면에, 오프라인 패키지는 네트워크 접속 없이
    실행되도록 설계된 빌드팩 패키지 버전으로써, 패키지 시 지원하는
    종속성들을 모두 패키지 안에 포함 시킨다.

-   **버저닝(Versioning) 기능:** 빌드팩 패키징 시, 생성 할 패키지 이름에
    버전 정보를 추가하는 기능이다.

패키지를 지원하기 위해서는 패키징과 버저닝 기능을 직접 구현하거나, Cloud
Foundry에서 제공하는 Buildpack packager어플리케이션을 사용하는 방법
2가지가 있다.

1)  직접 구현하는 예

> Cloud Foundry의 JAVA-BUILDPACK은 소스에 포함된 Package모듈을 통해
> 패키지 기능을 지원한다. 패키지는 bundle exec rake[^7] 명령어를 통해
> 수행된다. 인자 값으로 OFFLINE=true를 사용하면, 오프라인 패키지를
> 생성할 수 있고, *VERSION=&lt;VERSION&gt;* 인자 값을 사용하면 패키지
> 이름에 버전 정보를 추가할 수 있다.
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
> bundle exec rake 명령어는 Rakefile[^8]에 정의된 Package모듈을
> 순서대로 실행하며, 실행 순서는 다음과 같다. 오프라인 패키지를 만드는
> 경우, 우선 설정파일들에 정의된 의존성들을 모두 다운로드 한다. 이후
> cache.yml에 있는 remote\_download값을 disables로 만들고, 패키지.zip
> 파일을 생성한다. 아래는 Rakefile과 Package 모듈의 일부를 보여준다.

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

1)  Buildpack packager 사용 예

> Buildpack packager는 Cloud Foundry에서 제공하는 빌드팩 패키지
> 도구이다. buildpack-packager는 어플리케이션의 종속성이 아니라 빌드팩의
> 종속성을 캐시하는 것이다. 아래는buildpack-packager를 사용하여, RUBY
> 빌드팩을 패키지 하는 방법이다.
>
> \# ruby-buildpack git에서 submodules(compile-extentions)를 fetch 한다.
````
\$ git submodule update –init
````
> \# 최종 빌드팩 종속성들을 다운로드한다.
````
\$ BUNDLE\_GEMFILE=cf.Gemfile bundle
````
> \# buildpack packager를 실행한다.
````
\$ BUNDLE\_GEMFILE=cf.Gemfile bundle exec buildpack-packager \[uncached | cached \]
````
> Buildpack packager는 실행 시 manifest.yml파일을 확인하므로, 해당
> 파일을 우선 작성해야 한다. 아래는 manifest.yml 파일의 작성 예를
> 보여준다.

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

-   **language:** 패키지 파일에 사용될 이름을 작성한다.
    

-   **url\_to\_dependency\_map:** 해당 종속성의 이름과 버전을 추출하고
    맵핑 시키기 위한 정규 표현식의 목록을 작성한다.

-   **dependencies:** 컴파일 시 다운로드 할 리소스들에 대해 name,
    version, uri, md5, cf\_stacks[^9] 정보를 명시한다. packager는
    명시된 리소스들을 dependencies 폴더에 다운로드하고 설치한다.
    dependencies 폴더는 패키지 파일에 포함된다.

> packager는 manifest.yml의exclude\_files에 명시된 파일들을 제외한
> 빌드팩 디렉터리의 모든 것을 패키지 파일에 추가한다. cached 옵션으로
> 실행한 경우, manifest.yml에 명시된 종속성들을 다운로드하고 패키지
> 파일에 포함한다. 즉 cached 옵션은 오프라인 패키지와 동일한 역할을
> 한다.

### <a name="322"/>3.2.2. 저장소 (Repository) 

> 저장소는 빌드팩 컴파일 시 다운로드 하는 다양한 종속성들이 존재하는
> 공간이다. 저장소는 개방형 클라우드 플랫폼이 설치된 환경에 따라, 외부
> 또는 내부 네트워크 위치에 구성할 수 있다. 빌드팩은 저장소의 위치를
> 설정 및 변경할 수 있는 방법을 제공하며, 이러한 저장소 설정관련 부분은
> 빌드팩 소스마다 다르다. 해당 설정방법에 대해서는 4.빌드팩 확장
> 가이드에서 상세히 다룬다. 단, Build-packager를 사용하는 빌드팩(e.g.
> ruby)의 경우, 앞서 패키지에서 설명한 manifest.yml 파일의
> dependencies:uri 항목에 다운로드 할 저장소 위치 및 라이브러리 정보를
> 작성한다.

# <a name="4"/>4. 빌드팩 확장 가이드 

빌드팩 또는 어플리케이션 개발자는 기존의 빌드팩을 수정하여 자신에게
필요한 빌드팩을 만들 수 있다. GitHub에는 다양한 어플리케이션 실행환경을
지원하기 위한 다수의 빌드팩 소스가 존재한다. 본 장에서는 GitHub에
존재하는 빌드팩 중에 몇 가지 예를 들어, 각 빌드팩의 설계, 소스 구조를
설명하고 이를 확장하는 방법을 가이드한다.

### <a name="41"/>4.1. JAVA 빌드팩 확장 

JAVA 빌드팩은 JVM기반의 어플리케이션 실행환경을 구성하는 데 목적이 있다.
JAVA 빌드팩은 Containers, Frameworks, JREs 3가지 타입의 표준
컴포넌트들로 설계되어 있다. \[표 4-1\]은 JAVA 빌드팩의 표준 컴포넌트
유형과 설명을 나타낸다.

        표 4‑1. JAVA 빌드팩 표준 컴포넌트

| 컴포넌트 유형 | 설명 |
|-------------|-----------------------------------------------------------------------------------------|
|Container | -   컨테이너는 어플리케이션이 어떻게 실행될지에 대한 방법을 나타내는 컴포넌트이다. 이 유형의 컴포넌트는 어떤 컨테이너를 다운로드 및 사용할지를          결정하고, 플랫폼에서 런타임 시 실행시킬 커맨드를 만드는 데 책임이 있다.<br>-   하나의 컨테이너 컴포넌트만 어플리케이션을 실행할 수 있다. 하나 이상의 컨테이너가 사용된다면 스테이징단계에서 오류가 발생한다.<br> -   컨테이너 유형은 가장 단순하게는Java main() 함수로부터 어플리케이션 서버, 서블릿 컨테이너 등을 포함한다.|
|Framework | -   프레임워크는 추가적인 동작 또는 어플리케이션이 실행될 때 사용되는 변경사항들을 나타내는 컴포넌트이다. 이 유형의 컴포넌트는 어떤 프레임워크가 필요한지 결정하고, 어플리케이션을 변환하고, 그리고 런타임 시 사용되어야 하는 추가적인 다른 옵션을 제공하는 데 책임이 있다.<br> -   어플리케이션 실행 시 여러 개의 프레임워크 컴포넌트가 사용될 수 있다.<br> -   프레임워크 유형은 서비스 바인드와 자동으로 DataSource를 재설정하기 위해, JDBC jars를 다운로드하는 기능을 포함한다.|
|Jre | -   Jre는 어플리케이션이 실행될 때 사용되는 JAVA환경을 나타낸다. 이 유형의 컴포넌트는 어떤 jre를 사용하고, 다운로드하고 언팩킹할지 그리고 런타임 시 사용해야 하는 상세화된 jre 옵션들을 해결할 책임이 있다.<br> -  어플리케이션을 실행시키기 위해 하나의 Jre만 사용될 수 있다. 하나 이상의 Jre가 사용된다면 스테이징단계에서 오류가 발생한다.| 

JAVA 빌드팩 개발자는 해당 표준 컴포넌트 유형에 따라 기존에 구현된
컴포넌트들을 참조하여, 새로운 컴포넌트를 추가할 수 있다. 각각의 유형에
속하는 컴포넌트 들은 하나의 루비파일 클래스로 구현되어 있다.

### <a name="411"/>4.1.1. 소스구조 

JAVA 빌드팩의 모든 기능들은 RUBY로 구현되어 있으며, 새로운 컴포넌트를
추가하거나 설정사항들을 수정하기 위해서는 JAVA 빌드팩의 소스구조를
참고해야 한다. \[표 4-2\]는 JAVA 빌드팩의 소스구조와 설명을 나타낸다.

        표 4‑2. JAVA 빌드팩 소스구조

| 상위 디렉터리 | 하위 디렉터리/ 주요파일 | 설명 |
|-------------|-----------------------------|-----------|
|/bin    |detect.rb<br>compile.rb<br>release.rb| 필수기능 스크립트(진입점)를 위한 디렉터리이다.|
|/congig |components.yml<br>repository.yml<br>tomcat.yml<br>...| 다운로드 할 종속성들의 URL(저장소 위치 및 버전)설정파일들을 위한 디렉터리이다.|
|/docs   |design.md<br>extending.md<br>...| 소스에 대한 가이드 문서를 위한 디렉터리이다.|
|/lib    |java\_buildpack.rb<br>/java\_buildpack<br>/component<br>/container<br>framework<br>jre<br>...| 각 컴포넌트에 대한 detect, compile, release 동작의 실제 구현 소스들을 위한 디렉터리이다.|
|/rakelib |package.rb<br>... | Rake로 실행시킬, 패키지 모듈을 위한 디렉터리이다. 패키지 기능을 위해 사용된다. |
|resources |/tomcat/conf<br>context.xml<br>logging.properties<br>server.xml<br>... | 변경 또는 추가할 필요가 있는 리소스들을 위한 디렉터리이다.|
|/spec |... | Rspec[^11]을 사용하여, 테스트 할 소스들을 위한 디렉터리이다.

JAVA빌드팩 개발자는 해당 소스구조의 /config 디렉터리의 설정파일들을
변경하거나, /lib 디렉터리의 하위디렉터리에 새로운 컴포넌트를 추가하는
방법으로 기존 빌드팩을 확장할 수 있다.

### <a name="412"/>4.1.2. 저장소(Repository) 설정 변경 

JAVA 빌드팩은 기본으로 설정된 저장소(http://download.run.pivotal.io )와
config파일에 지정된 버전을 통해 필요한 라이브러리를 다운로드 한다.
하지만 배포하려는 어플리케이션이 특정 라이브러리 버전을 사용해야
한다던가, 클라우드 플랫폼의 네트워크 환경이 제한적인 경우 고정되어있는
설정을 변경할 필요가 있다. 따라서 JAVA 빌드팩은 제공하는 라이브러리들의
버전과 이를 다운로드 할 저장소 위치를 변경할 수 있는 방법을 제공한다.
이러한 저장소와 관련된 설정 요소들을 변경하기 위해서는, 소스구조의
config 디렉터리 안에있는 *&lt;컴포넌트이름&gt;.yml* 파일의 내용을
수정하면 된다. 아래는 설정요소 파일의 예를 보여준다.

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
