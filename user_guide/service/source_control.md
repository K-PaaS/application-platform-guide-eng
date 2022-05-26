### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Source Control Service 

# [PaaS-TA Configuration Management Service User's Guide]

## Table of Contents
1. [Document Outline](#1)
     * [1.1. Create PaaS-TA Portal Account](#2)
     * [1.2. Range](#3)
2. [PaaS-TA User Portal Service](#4)
     * [2.1. Create PaaS-TA Portal Account](#5)
3. [Configuration Management User Portal Service](#6)
     * [3.1. Create Configuration Management Service User](#7)
     * [3.1.1 Login](#8)
     * [3.1.2 Repository Contributor Authority Management](#9)
     * [3.1.2.1.1.	Add Repository Contributor Authority](#10)
     * [3.1.2.1.2.	Delete Repository Contributor Authority](#11)     
     * [3.1.2.1.3.	Check Repository Contributor Authority](#12)
     * [3.1.2.1.4.	Check Instance User Authority](#13)
     * [3.1.2.1.5.	Search for User ID Repository Contributors](#14)
     * [3.1.2.1.6.	Search for user information by instance ID search criteria](#15)
     * [3.1.3. Create Repository](#16)
     * [3.1.3.1.1.	Check All Repositories](#17)
     * [3.1.3.1.2.	Check User Repository List](#18)
     * [3.1.3.1.3.	Delete Repository](#19)
     * [3.1.3.1.4.	Modify Repository](#20)
     * [3.1.3.1.5.	Check Repository Details](#21)  
     * [3.1.3.1.6.	Check Repository File List](#22)
     * [3.1.3.1.6.1. Check Repository Branch List](#23)
     * [3.1.3.1.6.2. Check Repository Tag List](#24)
     * [3.1.3.1.7.	Check Repository Browser List](#25)
     * [3.1.3.1.8.	Check Repository Modified List](#26)
     * [3.1.4.	User Management](#27)
     * [3.1.4.1.1.	Check All Users](#28)
     * [3.1.4.1.2.	Create User](#29)
     * [3.1.4.1.3.	Delete User](#30)
     * [3.1.4.1.4.	Modify User](#31)
     * [3.1.4.1.5.	Check User's Detailed Informations](#32)
     * [3.1.4.1.6.	Check Users of Service Instance Repository](#33)
     * [3.1.4.1.7.	Delete Instance User](#34)




# <div id='1'/> 1. Document Outline

### <div id='2'/> 1.1. Create PaaS-TA Portal Account
This document describes how to use configuration management user.

### <div id='3'/> 1.2. Range
This document is written on how to use the Configuration Management User Portal based on Windows environment.

# <div id='4'/> 2. PaaS-TA User Portal Service

### <div id='5'/> 2.1. Create PaaS-TA Portal Account
Before creating a configuration management service, creating of PaaS-TA user portal account is required. For creating a user account or reset password for PaaS-TA, refer to PaaS-TA User Portal Guide.


# <div id='6'/> 3. Configuration Management User Portal Service


### <div id='7'/> 3.1. Preparation Needed before Creating Configuration Management Service
One Configuration Management Service can be created per organization. Refer to PaaS-TA User Portal Guide for details of creating configuration management service.


### <div id='8'/>  3.1.1. Login
1.	Access to PaaS-TA User Portal.  
![002]

2. Click "Login" button.  
![003]

3.	Enter the email address and password to use and click “SIGN IN” button.  
![004]

4.	When logged in, it goes to dashboard page. Enter the organization name to create and click “Create Organization” button.  
![005]

5.	When an Organization is created, organization, space, domain and user appears on screen. Click “Catalog” menu on top and proceed to catalog list page.
  !![006]

6.	At the very bottom part of catalog's [Integrated Development Tool] List, click  “Configuration Management Service” image to go to the configuration management's details page.  
![007]

7.	On the detailed page of the configuration management service, input the content for creating the configuration management service.Enter the service name in English and click the "Create" button to complete creating configuration management service instance and proceeds to space dashboard.  
![008]

8.	Check the Configuration Management Service Instance that was entered at space dashboard. Go to Configuration Management Dashboard by clicking “Dashboard” button at the right side of Configuration Management Service Instance.  
![009]

9.	When using the dashboard for the fist time after creating a Configuration Management Service, enter password between 6~16 letters.  
![010]

### <div id='9'/> 3.1.2. Repository Contributor Authority Management
The purpose of managing the Repository Contributor Authority is to give authorities to the contributors. The managment are the followings: 
Add, delete, and check Repository Contributor authority, check Instance User authority, search User ID repository contributor, and search instance ID through fiiltering.

### <div id='10'/> 3.1.2.1.1. Add Repository Contributor Authority
1.	On the Configuration Management Repository List page, click the name of the repository to add authority.  
![011]

2.	Once it's clicked, it will take you to the Repository Details page. Among the three tabs in the page, click the rightmost tab, Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

3.	Click “Add Contributor” button once the page appears.  
![012]

4.	Enter the User to give authority within [Search Source Controller User]. After checking the user in the user search list, click "+" icon.  
![013]

5.	Bellow [Search Source Controller User], [Invite Contributor], Authority and descriptions are shown.
After checking the informations of User ID, Name, Email added in [Invite Contributor], Give the required authority (Modify/View) and click “Invite” when completed.  
![014]

6.	When notification “[Contributor has been Invited]” appears, the repository contributor authority has been given.  
![015]


### <div id='11'/> 3.1.2.1.2. Delete Repository Contributor Authority
1.	Click the contributor to remove authority from the users list.

2.	Click the repository name from the contribution management repository list.

3.	Once it's clicked, it will take you to the Repository Details page. Among the three tabs in the page, click the rightmost tab, Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

4.	Once the screen appears, click the repository contributor authority to delete.  
![016]

    Or, adding authority to Repository Contributors likewise, enter the name of the contributor whose authority is to be removed in [Search Source Controller User]. Check the searched user and click '-' icon beside it.  
![013]

5.	When Notification “Contributor has been Deleted.” appears, the repository contributor authority has been deleted successfully.


### <div id='12'/> 3.1.2.1.3. Check Repository Contributor Authority
1.	From the Repository Details page. Among the three tabs in the page, click the rightmost tab, Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

2.	A list of all the contributors created at the repository can be checked. Repository Contributor Authority can be checked easily by checking the Use/Stop image and Owner/Write/View image located on the right side of the page.  
![017]

3.	Detailed Contributor Authorities can be checked by clicking the contributor's ID. Detailed information includes ID, Name, Email, Authority, and description of the user.  
![018]


### <div id='13'/> 3.1.2.1.4. Check Instance User Authority
1.	Click repository name to check the contributor authority from the repository list.  
![019]

2.	Contributor's list can be checked by clicking “Contributor” located at the right side of repository details tab. Contributor can be searched with the conditions of three following combobox:  “Contributor ID search”, “All/View/Modify", or “All/Use/Stop”.  
![020]


### <div id='14'/> 3.1.2.1.5. Search for User ID Repository Contributors
1.	Repository Contributor can be searched at the search box located at the left side of Users list page. Enter ID or Name and click the magnifying glass image to look up to the list.  
![021]

2.	Using of capital letters doesn't matter at the Users list page. Contributors can be searched according to authority other than the search function (all/Administrator/user) and (all/use/stop).  
![022]

3.	Message saying “No data has been checked.” will appear when the created User ID or Name is not added to the Users list page.  
![023]


### <div id='15'/> 3.1.2.1.6. Search for user information by instance ID search criteria
1.	Repository Contributors can be seached from the search tab at the left side of Users list page. Enter or click the magnifying glass after entering the ID or Name to search.  
![024]

2.	Using of capital letters doesn't matter at the Users list page. Contributors can be searched according to authority other than the search function (all/Administrator/user) and (all/use/stop).  
![025]


### <div id='16'/> 3.1.3. Create Repository
1.	Access to PaaS-TA Developers Portal. (Skip when already logged in.)

2.	After logging in, click “Dashboard” from the top of creating organization page's menu to proceed to the organization dashboard page. Click the “dev” space name located at the center of the screen to go to the space dashboard.  
![026]

3.	Click “Dashboard” button to go to configuration management dashboard from space dashboard configuration managagement service instance.  
![027]

4.	When using the dashboard for the fist time after creating a Configuration Management Service, enter password between 6~16 letters. (When registering created configuration management service, it automatically goes to configuration management dashboard.)  
![028]

5.	Click “Create New” button from the menu on top.  
![029]

6.	Input the repository name in english at the create new repository page, select type(Git, SVN), and click “Create” button. Click “OK” when notification with the message of “Repository was newly created.” appears.
  - Repository Name : XXX (English)
  - Type : Git
  - Repository Description : XXX  
![030]


### <div id='17'/> 3.1.3.1.1. Check All Repositories
1.	Check the created repository list from the repository list page.


### <div id='18'/> 3.1.3.1.2. Check User Repository List
1.	Check the user's repository list by searching the Repository List page, combobox configuration management type, all repository and updating sequence from the repository list page.


### <div id='19'/> 3.1.3.1.3. Delete Repository
1.	Go to repository details page by clicking the repository name at the repository list page.  
![019]

2.	Click “View information/Modify” button at the right side of repository details page.  
![031]

3.	Click “Delete Repository” button at the right-buttom part of the view information/modify page.  
![032]


### <div id='20'/>  3.1.3.1.4. Modify Repository
1.	Click the repository name from the repository list and proceed to repository details page.  
![019]

2.	Modify by clicking the “Modify” button at the right side of view repository details page. Modification is completed when notification with the message of “Modification Completed.” appears.  
![033]


### <div id='21'/> 3.1.3.1.5. Check Repository Details
Can check the File,Commit, and Contributor at the repository details page.


### <div id='22'/> 3.1.3.1.6. Check Repository File List
1.	레파지토리 상세보기 페이지의 탭 첫 번째 왼쪽의 “파일(file)”을 클릭하면 브렌치, 태그, 참여자에 대한 정보 확인이 가능하다.  
![034]

2.	“파일(file)” 목록에서는 기본적으로 폴더/파일명, 최종 변경사항, 파일 크기, 마지막 업데이트를 확인할 수 있다.

3.	레파지토리 상세보기 오른쪽의 “레파지토리 클론”을 통해 URL 복사가 가능하다.  
![035]


### <div id='23'/> 3.1.3.1.6.1. 레파지토리 브렌치 목록 조회
1.	“파일(file)” 의 하단에 있는 “브렌치 : “ 를 클릭하면 사용한 브렌치의 리스트를 확인할 수 있으며, 빈칸의 검색을 통해 빠르게 조회할 수 있다.  
![036]


### <div id='24'/> 3.1.3.1.6.2. 레파지토리 태그 목록 조회
1.	“파일(file)”의 하단에 있는  “Tag : “ 를 클릭하면 사용한 Tag의 리스트를 확인할 수 있으며, 빈칸의 검색을 통해 빠르게 조회할 수 있다.  
![037]


### <div id='25'/> 3.1.3.1.7. 레파지토리 브라우저 목록 조회
1.	레파지토리 브라우저 목록에서는 폴더/파일명, 최종 변경사항, 파일 크기, 마지막 업데이트를 확인할 수 있다. 첫 화면에서 폴더 이미지와 함께 src와 target을 볼 수 있다.  
![038]

2.	[폴더] 이미지를 클릭 하면 main을 포함한 이외의 소스 내용과 정보를 확인할 수 있다.
“[폴더]…”는 그전 페이지로 돌아가는 기능을 수행한다.


### <div id='26'/>  3.1.3.1.8. 레파지토리 변경 목록 조회
1.	레파지토리 상세보기 페이지의 탭 중간의 “커밋(Commit)”을 클릭하면 변경된 목록을 조회할 수 있다.  
![039]


### <div id='27'/> 3.1.4. 사용자 관리
### <div id='28'/> 3.1.4.1.1.	모든 사용자 조회
본 장에서는 PaaS-TA 사용자 포탈의 7개 메뉴에 대한 설명을 기술한다.
1.	형상관리 레파지토리 목록 페이지 상단에 있는 나의 아이디를 클릭한다. 클릭 후 보이는 목록 두 번째의 “사용자 관리”를 클릭한다.  
![040]

2.	이동된 화면 목록에서 상단 관리자 기준으로 모든 사용자 아이디, 이름, 생성일, 수정일을 확인할 수 있다. 또한 목록 우측에서는 사용자의 사용 여부 확인이 가능하다.  
![041]


### <div id='29'/> 3.1.4.1.2. 사용자 생성
1.	모든 사용자 조회와 동일한 순서로 화면 이동을 한다.

2.	사용자 생성 클릭 후 이동하는 페이지에서 사용자 생성을 위한 필수 조건인 아이디, 이름, 이메일, 비밀번호 정보를 입력한다. 아이디 생성 시, 영•소문자, 숫자 포함하여 2 ~ 12자리 입력을 하고, 비밀번호는 6 ~ 16자리로 입력한다.  
![042]

3.	조건에 맞는 입력을 완료하면 하단의 “생성” 버튼을 클릭한다. 알림 메시지 “[ ]사용자 생성이 완료되었습니다” 나오면 사용자 생성이 된다. 사용자 목록에서 추가된 사용자가 확인 가능하다.


### <div id='30'/> 3.1.4.1.3. 사용자 삭제
1.	사용자 목록에서 삭제할 사용자를 클릭한다.

2.	사용자 상세/수정/삭제 페이지에서 왼쪽 하단의 “사용자 삭제” 버튼을 클릭하여 삭제한다  
![044]


### <div id='31'/> 3.1.4.1.4. 사용자 수정
1.	사용자 목록에서 수정할 사용자를 클릭한다.

2.	사용자 상세/수정/삭제 페이지에서 오른쪽 하단의 “사용자 수정” 버튼을 클릭하여 수정한다.  
![045]


### <div id='32'/> 3.1.4.1.5. 사용자 상세정보 조회
1.	사용자 목록 페이지에서 생성된 사용자와 추가된 사용자가 확인된다.

2.	레파지토리 참여자 권한 조회할 아이디를 클릭 후, 사용자 상세/수정/삭제 페이지로 이동한다.  
![041]

3.	사용자 상세/수정/삭제 페이지에서는 아이디, 이름, 이메일, 비밀번호, 사용 여부, 설명에 대한 정보를 확인할 수 있다.  
![042]


### <div id='33'/> 3.1.4.1.6. 서비스 인스턴스 레파지토리 사용자 목록 조회
1.	레파지토리 상세보기 페이지에서 “정보보기/수정” 버튼과 “신규생성” 버튼 하단의 탭 중 가장 오른쪽에 있는 참여자(Contributor)을 클릭한다.

2.	레파지토리 생성된 참여자들의 리스트가 모두 확인된다. 페이지 화면 맨 오른쪽에 있는 사용/정지 이미지와 소유자/쓰기/보기의 이미지로 쉽게 레파지토리 참여자 권한 조회를 할 수 있다.  
![017]


### <div id='34'/> 3.1.4.1.7. 인스턴스 사용자 삭제
본 장에서는 PaaS-TA 사용자 포탈의 7개 메뉴에 대한 설명을 기술한다.
1.	사용자 목록에서 참여자 권한 삭제할 사용자를 클릭한다.

2.	형상관리 레파지토리 목록 페이지 있는 레파지토리 명을 클릭한다.  
![011]

3.	클릭 후 레파지토리 상세보기 페이지로 이동한다. 레파지토리 상세보기 페이지에서 “정보보기/수정” 버튼과 “신규생성” 버튼 하단의 탭 중 가장 오른쪽에 있는 참여자(Contributor)을 클릭한다.

4.	탭 화면이 전환된 후 “참여자 추가” 버튼을 클릭한다.  
![012]

5.	[소스 컨트롤러 사용자 검색] 안에서 참여자 권한 추가할 사용자를 입력한다. 사용자 검색 목록에서 사용자를 확인 후, “-” 이미지를 클릭한다.  
![013]

6.	알림 메시지 “참여자가 삭제되었습니다.”가 나오면 인스턴스 사용자가 삭제된다.

[002]:./images/source-control/image002.png
[003]:./images/source-control/image003.png
[004]:./images/source-control/image004.png
[005]:./images/source-control/image005.png
[006]:./images/source-control/image006.png
[007]:./images/source-control/image007.png
[008]:./images/source-control/image008.png
[009]:./images/source-control/image009.png
[010]:./images/source-control/image010.png
[011]:./images/source-control/image011.png
[012]:./images/source-control/image012.png
[013]:./images/source-control/image013.png
[014]:./images/source-control/image014.png
[015]:./images/source-control/image015.png
[016]:./images/source-control/image016.png
[017]:./images/source-control/image017.png
[018]:./images/source-control/image018.png
[019]:./images/source-control/image019.png
[020]:./images/source-control/image020.png
[021]:./images/source-control/image021.png
[022]:./images/source-control/image022.png
[023]:./images/source-control/image023.png
[024]:./images/source-control/image024.png
[025]:./images/source-control/image025.png
[026]:./images/source-control/image026.png
[027]:./images/source-control/image027.png
[028]:./images/source-control/image028.png
[029]:./images/source-control/image029.png
[030]:./images/source-control/image030.png
[031]:./images/source-control/image031.png
[032]:./images/source-control/image032.png
[033]:./images/source-control/image033.png
[034]:./images/source-control/image034.png
[035]:./images/source-control/image035.png
[036]:./images/source-control/image036.png
[037]:./images/source-control/image037.png
[038]:./images/source-control/image038.png
[039]:./images/source-control/image039.png
[040]:./images/source-control/image040.png
[041]:./images/source-control/image041.png
[042]:./images/source-control/image042.png
[043]:./images/source-control/image043.png
[044]:./images/source-control/image044.png
[045]:./images/source-control/image045.png

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Source Control Service 
