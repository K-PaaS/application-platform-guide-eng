### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > 사용자 포털

## Table of Contents
1. [Document Outline](#1)
     * [1.1. Purpose](#2)
     * [1.2. Range](#3)
2. [Create PaaS-TA User Portal Account](#4)
     * [2.1.  Create PaaS-TA User Portal Account](#5)
     * [2.2.  Reset PaaS-TA User Portal Password](#6)
     * [2.3.  Login to PaaS-TA User Portal and Create Organization](#7)
3. [PaaS-TA User Portal Manual](#8)
     * [3.1.  Construct PaaS-TA User Portal Menu](#9)
     * [3.2.  PaaS-TA User Portal Menu Description](#10)
     * [3.2.1.  Dashboard](#11)
     * [3.2.1.1.  Organization and Space](#12)
     * [3.2.1.1.1.  Organization Management](#13)
     * [3.2.1.1.1.1.  Create Organization](#14)
     * [3.2.1.1.1.2.  Modify Organization Name](#15)
     * [3.2.1.1.1.3.  Delete Organization](#16)
     * [3.2.1.1.2.  Space Management](#17)
     * [3.2.1.1.2.1.  Create Space](#18)
     * [3.2.1.1.2.2.  Modify Space Name](#27)
     * [3.2.1.1.2.3.  Delete Space](#28)
     * [3.2.1.1.3.  Domain Management](#19)
     * [3.2.1.1.3.1.  Add Domain](#20)
     * [3.2.1.1.3.2.  Delete Domain](#21)
     * [3.2.1.1.4.  User Management](#22)
     * [3.2.1.1.4.1.  Invite User](#23)
     * [3.2.1.1.4.2.  Cancel Member](#24)
     * [3.2.1.2  Application and Service Environment Development](#29)
     * [3.2.1.2.1.  Application Management](#30)
     * [3.2.1.2.1.1.  Create Application](#31)
     * [3.2.1.2.1.2.  Modify Application Name](#32)
     * [3.2.1.2.1.3.  Run/Stop Application](#33)
     * [3.2.1.2.1.4.  Delete Application](#34)
     * [3.2.1.2.2.  Service Management](#36)
     * [3.2.1.2.2.1.  Create Service](#37)
     * [3.2.1.2.2.2.  Modify Service Name](#38)
     * [3.2.1.2.2.4.  Delete Service](#39)
     * [3.2.1.2.3.1.  Create User Provided Service](#40)
     * [3.2.1.2.3.2.  Modify User Provided Service](#41)
     * [3.2.1.2.3.3.  Delete User Provided Service](#42)
     * [3.2.1.3.  Application](#43) 
     * [3.2.1.3.1.  Application Dashboard](#44)
     * [3.2.1.3.1.1.  Modify Application Name](#45)
     * [3.2.1.3.1.2.  Restart Application](#46)
     * [3.2.1.3.1.3.  Run/Stop Application](#47)
     * [3.2.1.3.1.4.  Delete Application](#48)
     * [3.2.1.3.1.5.  Number of Instance/Memory Capacity/Disk Capacity Setting](#49)
     * [3.2.1.3.2.  Event](#50)
     * [3.2.1.3.2.1.  View More Event List](#51)
     * [3.2.1.3.3.  Status](#52)
     * [3.2.1.3.3.1.  SSH Access](#52-1)
     * [3.2.1.3.4.  Service](#53)
     * [3.2.1.3.4.1.  Connect Service](#54)
     * [3.2.1.3.4.2.  Service Information](#78)
     * [3.2.1.3.4.2.  Disconnect Servie](#55)
     * [3.2.1.3.5.  Environment Variable](#56)
     * [3.2.1.3.5.1.  Add Environment Variable](#57)
     * [3.2.1.3.5.2.  Modify Environment Variable](#58)
     * [3.2.1.3.5.3.  Delete Environment Variable](#59)
     * [3.2.1.3.6.  Route](#60)
     * [3.2.1.3.6.1.  Add Route](#61)
     * [3.2.1.3.6.2.  Unconnect Route](#62)
     * [3.2.1.3.7.  Log](#63)
     * [3.2.1.3.7.1.  Log Management](#64)
     * [3.2.1.3.7.2.  Tail Log](#90)
     * [3.2.1.3.7.3.  Auto scailing Setting](#91)
     * [3.2.1.3.7.4.  Notification Setting](#92)
     * [3.2.2.  Catalog](#65)
     * [3.2.2.1.  Search Catalog](#66)
     * [3.2.3.  Document](#67)
     * [3.2.4.  Notification](#68)
     * [3.2.5.  My Account](#69)
     * [3.2.5.1.  Profile](#70)
     * [3.2.5.2.  Login Information](#71) 
     * [3.2.5.2.1. Change Password](#72)
     * [3.2.5.3.  User Information](#73)
     * [3.2.5.3.1.  User Name](#74)
     * [3.2.5.3.2.  User Contact Number](#75)
     * [3.2.5.3.3.  User Postal Code](#76)
     * [3.2.5.3.1.  User Address](#77)
     * [3.2.5.4.  My Organization](#81)
     * [3.2.5.4.1  Leave My Organization](#79) 
     * [3.2.5.6.  Logout](#80)
     * [3.3.6.1.  Inquire Dashboard CAAS Deployments](#82)
     * [3.3.6.2.  Inquire Dashboard CAAS Pods](#83)
     * [3.3.6.3.  Inquire Dashboard CAAS Replica Sets](#84)
     * [3.3.6.4.  Inquire Dashboard CAAS CAAS Pvc](#85)
     
     

# <div id='1'/> 1. Document Outline


### <div id='2'/> 1.1. Purpose
      
This document describes how to use the PaaS-TA user portal.


### <div id='3'/> 1.2. Range
      
This document is written on how to use the PaaS-TA User Portal based on Windows environments.


# <div id='4'/> 2.  Create PaaS-TA User Portal Account

This chapter describes the account creation and password reset screen for using the PaaS-TA user portal.


### <div id='5'/> 2.1.  Create PaaS-TA User Portal Account

1.  Access to PaaS-TA User Portal.
![2-1-0]

2.  Click “Login”. 
![2-1-1]

3.  Click “Create account” Link.
![2-1-2]

4.  Enter the E-mail address to use. Click “Send authentication mail”.
![2-1-3]

5.  Click the "Verify Email Address" link in the received email.<br>

     > **Email Format to Receive**
     > ![email]

6.  Enter Username and Password. Click “Create Account" to complete the account creation.

![2-1-4]


### <div id='6'/> 2.2.  Reset PaaS-TA User Portal Password
1.  Click “Reset password” link.
![2-2-0]

2.  Enter User's email address. Click “Reset Password”.
![2-2-1]

3.  Click the "Reset Password" link in the received email.

     > **Email Format to Receive**
     > ![passwordemail]

4.  Enter new password. Click “Change” button to complete password reset. 
![2-2-2]


### <div id='7'/> 2.3.  Login to PaaS-TA User Portal and Create Organization
1.  Enter user ID and Password. Click “SIGN IN” to login to the user portal.
![2-3-0]

2.  After the account is created, Dashboard page will apear dirrectly at the first login.
![dashboard]

3.  Dashboard Page ① "Organization Management" Click the link or ②"Organization Management" at the menu on the right side. After the account is created, it is moved to the Dashboard page at the first login. More information can be found in 3.2.1.1 of this document.
![2-3-1]


# <div id='8'/> 3. PaaS-TA User Portal Manual

This chapter describes the menu configuration and screen description of the PaaS-TA user portal.


### <div id='9'/> 3.1.  Construct PaaS-TA User Portal Menu

The PaaS-TA User Portal consists of a section that manages organizations, spaces, and applications, a section that generates development environments and services, and an information inquiry section that inquires documents and menus.

<table>
  <tr>
    <td>Category</td>
    <td>Menu</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>Organization, Space, Application Management</td>
    <td>Dashboard</td>
    <td>Manages Organizaion, Space Creation and Application, Service, Domain, Invite User and etc.</td>
  </tr>
  <tr>
    <td>Development Environment, Create Service</td>
    <td>Catalog</td>
    <td>Application Development Envicronment and Create Service</td>
  </tr>
  <tr>
    <td rowspan="5">Inquire Information</td>
  </tr>
  <tr>
    <td>Document</td>
    <td>Inquire Documents Registered by the Administrator</td>
  </tr>
  <tr>
    <td>My Menu</td>
    <td>Manage My Account and Organization</td>
  </tr>
</table>


### <div id='10'/> 3.2.  PaaS-TA User Portal Menu Description

This chapter describes the three main categories of PaaS-TA user portals.


### <div id='11'/> 3.2.1.   Dashboard
1.  Click the "Dashboard" button on the top menu to go to the Dashboard page.
![3-2-1-0]


### <div id='12'/> 3.2.1.1.  Organization and Space
### <div id='13'/> 3.2.1.1.1.  Organization Management
① Click "Organization Management" link or ②"Organization Management" on menu to proceed to organization dashboard page.
![2-3-1]

### <div id='14'/> 3.2.1.1.1.1.  Create Organization
1. ① Click "Organization Management" link or ②"Organization Management" on the menu to proceed to organization dashboard.
![2-3-1]

2.  Click “Add New Organization” to proceed to organization creating page.
![2-3-1-1]

     > **To enable "Add New Organization"** button, [PaaS-TA Portal Deployment Guide Document](https://github.com/PaaS-TA/Guide-3.0-Penne-/blob/master/Use-Guide/portal/PaaS-TA%20Portal%20%EB%B0%B0%ED%8F%AC%20%EA%B0%80%EC%9D%B4%EB%93%9C_v1.1.md#7-1) Refer to **2.2. Enable create user organization Flag**.

3.  Enter Organization Name. Click "Create Organization" to complete. 
![2-3-1-2]
 -  **[Quota]** <br>
 -  **Memory** : Memory Quota (ex.) 1024M, 1G ,10G <br>
 -  **Instance Memory** : Maximum quota a application instance can have (ex.) 1024M, 1G ,10G <br>
 -  **Route** : Maximum number of routes <br>
 -  **Service Instance** : Maximum number of service instance <br>
 -  **Price** :  Billing servcie <br>
 -   **Instance** : Maximum number of instance  <br>
 -  **Route Port** : Reserved route port number <br>

4.  Click the "Proced" button on the confirmation pop-up to view the newly created organization on the Organization Management page.
![2-3-1-3]
![2-3-1-4]
     > 3.2.1.1.1.1-3 Screen appears when creating the first new organization for the account. Click “Create New Organization” when creating an organization afterward. 

### <div id='15'/> 3.2.1.1.1.2.  Modify Organization Name
1. ① Click "Organization Management" Link or ②"Organization Management" at the menu to proceed to organization dashboard page.
![2-3-1]

2.  Click "View details" on the  ① sub-menu at the right,, then click ② "Change Name".
![3-2-1-1-1-2-1]

3.  Enter new organization name. Click "Change".
![3-2-1-1-1-2-2]

4. Click the "Change" button on the pop-up to complete the organization name change.
![3-2-1-1-1-2-3]


### <div id='16'/> 3.2.1.1.1.3.  Delete Organization
1.  Click ② "Delete" on ① sub-menu at the right side beside "View Details".
![3-2-1-1-1-3-0]

2.  Click the "Delete" button on the confirmation pop-up to complete the deletion of the organization.
![3-2-1-1-1-3-1]

### <div id='17'/> 3.2.1.1.2.  Space Management
![3-2-1-2-1-0]

### <div id='18'/> 3.2.1.1.2.1.  Create Space
1. ① Click "Organization Management" Link or ②"Organization Management" at the menu to proceed to organization dashboard page.
![2-3-1]

2.  Click “View Details” at the right side.
![3-2-1-1-2-1-0]

3.  Click "+ Create Space" then enter space name.
![3-2-1-1-2-1-1]

4.  Click “Create” to complete creating space.
![3-2-1-1-2-1-2]

### <div id='27'/> 3.2.1.1.2.2.  Modify Space Name
1.  Click  ① "Notepad" to modify space name. 
![3-2-1-2-1-1-0]

2. Enter new space name. Click "Change” to complete changing space name.
![3-2-1-2-1-1-1]

### <div id='28'/> 3.2.1.1.2.3.  Delete Space
1.  Click ①“Trash bin”.
![3-2-1-2-1-2-0]

2.  Click the "Delete" button on the confirmation pop-up to complete the deletion of the space.
![3-2-1-2-1-2-1]

### <div id='19'/> 3.2.1.1.3.  Domain Management
![3-2-1-1-3-0]

### <div id='20'/> 3.2.1.1.3.1.  Add Domain
1.  Click “+Add Domain”.
![3-2-1-1-3-1-0]

2.  Enter the domain. Click the "Add" button to complete adding a domain.
![3-2-1-1-3-1-1]

### <div id='21'/> 3.2.1.1.3.2.  Delete Domain
1.  Click the ① "Trash bin"button of the domain to delete.
![3-2-1-1-3-2-0]

2.  확인 팝업의 “삭제”버튼을 클릭하여 도메인 삭제를 완료한다.
![3-2-1-1-3-2-1]


### <div id='22'/> 3.2.1.1.4.  사용자 관리
![3-2-1-1-4-0]

### <div id='23'/> 3.2.1.1.4.1  사용자 초대

1.  “+사용자 초대” 버튼을 클릭한다.
![3-2-1-1-4-1-0]

2.  초대 할 사용자 계정을 입력한다. 조직, 공간의 권한을 부여한다.
![3-2-1-1-4-1-1]

3.  “초대” 버튼을 클릭하여 사용자 초대를 완료한다.
![3-2-1-1-4-1-2]

4.  초대 요청한 사용자에게 이메일이 수신된다. 초대에 요청된 사용자가 수신된 이메일의“초대 수락하기" 링크를 클릭하면 조직에 접근 가능하다.
     > **수신 된 이메일 형식**
![inviteEmail]

### <div id='24'/> 3.2.1.1.4.2.  멤버 취소
1.  멤버 취소 할 사용자 계정의 “멤버 취소” 버튼을 클릭한다.<br>
**'멤버취소'시 사용자 OrgManager(조직 관리자) 역할 체크를 해제하고, 멤버 취소를 실행해야 취소 설정이 완료된다.**
![3-2-1-1-4-4-0]


### <div id='29'/> 3.2.1.2  애플리케이션 및 서비스 개발환경

### <div id='30'/> 3.2.1.2.1.  애플리케이션 관리
![3-2-1-2-2-0]

### <div id='31'/> 3.2.1.2.1.1.  애플리케이션 생성
1.  "대시보드" 메뉴를 클릭하여 대시보드 페이지로 이동한다. 대시보의 조직과 공간을 설정하고, 애플리케이션 탭 메뉴를 선택한다.
![3-2-1-2-1-3-0]

2.  ① "+" 클릭하여 카탈로그 메뉴로 이동한다.
![3-2-1-2-1-3-1]

3.  카탈로그 목록 ① 앱 개발환경을 클릭한다. 생성 할 ② 앱 개발 환경을 선택한다.
![3-2-2-3-0]

4.  앱 개발환경 생성에 필요한 항목을 입력한다.
![3-2-2-3-1]

 -  **조직 목록**에서 조직을 선택한다. <br>
 -  **공간 목록**에서 공간을 선택한다. <br>
 -  **앱 이름**을 입력한다. **앱 URL**은 자동으로 입력되지만 수정가능하다.  <br>
 -  **메모리 목록**에서 메모리 용량을 선택한다. <br>
 -  **디스크 목록**에서 디스크 용량을 선택한다. <br>
 -  **앱 시작여부**를 설정한다. <br>
 -  사용자 앱을 사용 할 경우 파일을 업로드한다. <br>

5.  “사용자 앱 생성” 버튼 또는 “샘플 앱 생성” 버튼을 클릭하여 앱 개발환경 생성을 완료한다.

### <div id='32'/> 3.2.1.2.1.2.  애플리케이션 이름 변경
1.  목록에서 이름을 변경하려는 애플리케이션의 ① 서브메뉴를 클릭하여 ② "앱 이름 변경"을 클릭한다.
![3-2-1-2-2-4-0]

2.  변경 할 애플리케이션 이름을 입력한다. “변경” 버튼을 클릭하여 애플리케이션 이름 변경을 완료한다.
![3-2-1-2-2-4-1]

### <div id='33'/> 3.2.1.2.1.3.  애플리케이션 실행/정지
1.  정지하려는 애플리케이션의 애플리케이션의 ① 서브메뉴를 클릭하여 ②“정지”(■) 또는 “실행"(▶)을 클릭한다.
![3-2-1-2-2-3-0]

2.  실행 할 애플리케이션 이름을 입력한다. “정지” 버튼을 클릭하여 애플리케이션 실행을 정지한다.
![3-2-1-2-2-3-1]

### <div id='34'/> 3.2.1.2.1.4.  애플리케이션 삭제
1.  목록에서 삭제하려는 애플리케이션의 ① 서브메뉴를 클릭하여 ②“삭제”를 클릭한다.
![3-2-1-2-2-5-0]

2.  확인 팝업의 “삭제” 버튼을 클릭하여 애플리케이션 삭제를 완료한다.
![3-2-1-2-2-5-1]

### <div id='36'/> 3.2.1.2.2.  서비스 관리
![3-2-1-2-3-0]

### <div id='37'/> 3.2.1.2.2.1.  서비스 생성
1.  "대시보드" 메뉴를 클릭하여 대시보드 페이지로 이동한다. 대시보의 조직과 공간을 설정하고, 서비스 탭 메뉴를 선택한다.
![3-2-1-2-1-4-0]

2. ① "+" 클릭하여 카탈로그 메뉴로 이동한다.
![3-2-1-2-1-4-1]

3.   카탈로그 목록 ① 앱 개발환경을 클릭한다. 생성 할 ② 서비스 환경을 선택한다.
![3-2-2-4-0]

4.  서비스 생성에 필요한 항목을 입력한다.
![3-2-2-4-1]

 -  **지역 목록**에서 지역을 선택한다. <br>
 -  **조직 목록**에서 조직을 선택한다. <br>
 -  **공간 목록**에서 공간을 선택한다. <br>
 -  **서비스 이름**을 입력한다.  <br>
 -  **서비스 파라미터**를 입력한다. (서비스 파라미터 필요한 경우에만 표시된다.)  <br>
 -  **앱 목록**에서 연결 할 앱 또는 ‘연결 없이 시작’을 선택한다. <br>
 -  **서비스 이용사양 선택 목록**에서 서비스 플랜을 선택한다. <br>

5.  ‘생성” 버튼을 클릭하여 서비스 생성을 완료한다.
![3-2-2-4-2]

### <div id='38'/> 3.2.1.2.2.2.  서비스 이름 변경
1.  목록에서 이름을 변경하려는 서비스의 ① 서브메뉴를 클릭하여 ② "서비스 수정"을 클릭한다.
![3-2-1-2-3-4-0]

2.  변경 할 서비스 이름을 입력한다. “변경” 버튼을 클릭하여 서비스 이름 변경을 완료한다.
![3-2-1-2-3-4-1]

### <div id='39'/> 3.2.1.2.2.4.  서비스 삭제
1.   목록에서 삭제하려는 서비스의 ① 서브메뉴를 클릭하여 ②“삭제”를 클릭한다.
![3-2-1-2-3-5-0]

2.  확인 팝업의 “삭제” 버튼을 클릭하여 서비스 삭제를 완료한다.
![3-2-1-2-3-5-1]

### <div id='40'/> 3.2.1.2.3.1.  User Provided 서비스 생성
1.  “User Provided 생성” 버튼을 클릭한다.
![3-2-1-2-3-3-0]

2.  서비스 명, Credentials, Syslog Drain URL을 입력한다. “생성” 버튼을 클릭하여 User Provided 서비스 생성을 완료한다.
![3-2-1-2-3-3-1]

### <div id='41'/> 3.2.1.2.3.2.  User Provided 서비스 수정
1.  목록에서 이름을 변경하려는 User Provided 서비스의 ① 서브메뉴를 클릭하여 ② "서비스 수정"을 클릭한다.
![userprovide-service]

2.  변경 할 서비스 서비스 명, Credentials, Syslog Drain URL을 입력한다. 확인 팝업의 “변경” 버튼을 클릭하여  User Provided 서비스 변경을 완료한다.
![userprovide-service2]

### <div id='42'/> 3.2.1.2.3.3.  User Provided 서비스 삭제
1.  목록에서 삭제하려는 User Provided 서비스의 ① 서브메뉴를 클릭하여 ②“삭제”를 클릭한다.
![userprovide-del]

2.  확인 팝업의 “삭제” 버튼을 클릭하여  User Provided 서비스 삭제를 완료한다.
![userprovide-del2]

### <div id='43'/> 3.2.1.3.  애플리케이션
### <div id='44'/> 3.2.1.3.1.  애플리케이션 대시보드
카탈로그 목록에서 생성한 애플리케이션 이름을 클릭하여 애플리케이션 대시보드 페이지로 이동한다.
![3-2-1-3-1-0]

### <div id='45'/> 3.2.1.3.1.1.  애플리케이션 이름 변경
1.  ① 서브 메뉴를 클릭하여 리스트 ② "이름변경"을 클릭한다.
![3-2-1-3-1-1-0]

2.  변경할 애플리케이션 이름을 입력한다. ①“저장”이미지 버튼을 클릭하여 애플리케이션 이름 변경을 완료한다.
![3-2-1-3-1-1-1]

### <div id='46'/> 3.2.1.3.1.2.  애플리케이션 재시작
1.  ① “리스테이지” 버튼을 클릭한다.
![3-2-1-3-1-4-0]

### <div id='47'/> 3.2.1.3.1.3.  애플리케이션 실행/중지
1.  ①“실행” 또는 “중지”이미지 버튼을 클릭한다.
![3-2-1-3-1-3-0-00]

### <div id='48'/> 3.2.1.3.1.4.  애플리케이션 삭제
1.  ① 서브 메뉴를 클릭하여 리스트 ② "삭제"을 클릭한다.
![3-2-1-3-1-2-0]

2.  확인 팝업의 “삭제”버튼을 클릭하여 애플리케이션 삭제를 완료한다.
![3-2-1-3-1-2-1]

### <div id='49'/> 3.2.1.3.1.5.  인스턴스 수/메모리 용량/디스크 용량 설정
1.  ① 인스턴스 수, ② 메모리 용량, ③ 디스크 용량을 설정한 후, 각각의“저장” 버튼을 클릭한다.
![3-2-1-3-1-5-0]

### <div id='50'/> 3.2.1.3.2.  이벤트
1.  ①“이벤트”메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-2-0]

### <div id='51'/> 3.2.1.3.2.1.  이벤트 목록 더 보기
1.  이벤트 개수가 5개 이상일 경우 “더 보기” 버튼이 활성화 된다.
![3-2-1-3-2-1-0]

### <div id='52'/> 3.2.1.3.3.  상태
1.  ①“상태” 메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-3-0]

2.  상태 리스트에서 Status, CPU, Memory, Disk, UPTIME(updatetime), RESTART를 확인 할 수 있다. "RESTART" 버튼을 클릭하면 인스턴스 재 시작이 가능하다.
![3-2-1-3-3-1]

### <div id='52-1'/> 3.2.1.3.3.1.  SSH 접속
1.  ①“상태” 메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-3-1-0]

2.  접속하고 싶은 인스턴스 번호에 맞게 “SSH Connect” 버튼을 클릭한다.
![3-2-1-3-3-1-1]

3.  해당 인스턴스에 SSH 접속 후 원하는 작업을 진행한다.
![3-2-1-3-3-1-2]

### <div id='53'/> 3.2.1.3.4.  서비스
1.  ①“서비스”메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-4-0]

### <div id='54'/> 3.2.1.3.4.1.  서비스 연결
1.  “+서비스 연결” 버튼을 클릭한다 .
![3-2-1-3-4-1-0]

2.  목록에서 서비스를 선택 후 “연결” 버튼을 클릭한다.
![3-2-1-3-4-1-1] <br>
     > **여기서 확인되는 서비스 목록은 앱 바인드가 가능한 서비스만 해당됩니다.** 
     
3.  서비스 연결 확인 팝업“저장”버튼을 하여 서비스 연결을 완료한다.
![3-2-1-3-4-2-3]

4.  App 재시작 확인 팝업“재시작”버튼을 클릭한다.
![3-2-1-3-4-2-4]
     
### <div id='78'/> 3.2.1.3.4.2.  서비스 정보
1.  목록에서 서비스 접속 정보 확인 하기 위해 ① "메모장" 버튼을 클릭한다.
![3-2-1-3-4-2-2]

2.  확인 팝업을 통해 Hostname, jdbcUrl, Name,Port, Username, Password 를 확인 할 수 있다.
![3-2-1-3-4-2-1]

### <div id='55'/> 3.2.1.3.4.3.  서비스 연결 해제
1.  목록에서 연결 해제하려는 서비스의 “연결해제” 버튼을 클릭한다.
![3-2-1-3-4-2-0]

2.  서비스 연결해제 확인 팝업“저장”버튼을 클릭하여 서비스 연결 해제를 완료한다.
![3-2-1-3-4-2-5]

3.  App 재시작 확인 팝업“재시작”버튼을 클릭한다.
![3-2-1-3-4-2-4]

### <div id='56'/> 3.2.1.3.5.  환경변수
1.  ①“환경변수”메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-5-0]

### <div id='57'/> 3.2.1.3.5.1.  환경변수 추가
1.  ①“+ 추가” 버튼을 클릭한다. 환경변수 이름과 값을 입력한다. ②“저장” 버튼을 클릭하여 환경변수 추가를 완료한다.
![3-2-1-3-5-1-0]

### <div id='58'/> 3.2.1.3.5.2.  환경변수 수정
1.  목록에서 수정하려는 환경변수의 ① "메모장" 버튼을 클릭한다.
![3-2-1-3-5-2-0]

2.  환경변수 값을 수정한 후, “저장” 버튼을 클릭하여 환경변수 수정을 완료한다.
![3-2-1-3-5-2-1]

### <div id='59'/> 3.2.1.3.5.3.  환경변수 삭제
1.  목록에서 수정하려는 환경변수의 ①“휴지통" 버튼을 클릭한다.
![3-2-1-3-5-3-0]

2.  확인 팝업의 “삭제” 버튼을 클릭하여 환경변수 삭제를 완료한다.
![3-2-1-3-5-3-1]

### <div id='60'/> 3.2.1.3.6.  라우트
1.  ①“라우트”메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-6-0]

### <div id='61'/> 3.2.1.3.6.1.  라우트 추가
1.  “+연결”버튼을 클릭한다.
![3-2-1-3-6-1-0]

2.  추가 할 라우트 이름을 입력한다.  "저장" 버튼을 클릭한다.

3.  확인 팝업의 "확인" 버튼을 클릭하여 라우트 추가를 완료한다.

### <div id='62'/> 3.2.1.3.6.2.  라우트 연결 해제
1.  목록에서 연결 해제 하는 라우트의 “연결해제” 버튼을 클릭한다.
![3-2-1-3-6-2-0]

### <div id='63'/> 3.2.1.3.7.  로그
1.  ①“로그”메뉴 또는 ② 전체보기를 클릭한다.
![3-2-1-3-7-0]

### <div id='64'/> 3.2.1.3.7.1.  로그 관리
1.  ①“로그” 버튼을 클릭하여 최근 로그를 확인한다.
![3-2-1-3-7-1-0]

### <div id='90'/> 3.2.1.3.7.2.  Tail Log
1.  ① **</>Tail Logs**링크를 클릭하여 Tail Logs 새 창을 확인한다. ② "URL LInk" 버튼을 클릭한다. 
![3-2-1-3-7-1-1] 

2.  이동한 URL 홈페이지에서 Tail Logs 창을 확인하면 실시간으로 log가 출력된다.
![3-2-1-3-7-1-2]

### <div id='91'/> 3.2.1.3.7.3. 오토 스케일링
```diff
#### PaaS-TA 에서 제공하고있는 모니터링을 미리 설치를 한 후에 진행해야 한다.
```
![3-2-1-3-7-1-3]
1.  ① 앱 레이아웃의 모니터링을 클릭한다. ② 설정 탭 버튼을 클릭한다.

2.  인스턴스 수  : 오토 스케일링 할시 최소, 최대로 늘어날 인스턴스 수를 입력한다.

3.  CPU 임계 값(%) : (③ 체크박스를 체크하면 임계값 적용을 한다. 미체크 -> 미적용) 오토스케일링할시 최소, 최대 CPU값을 입력한다.\
(인스턴스가 적용한 CPU보다 %가 높아질 경우 인스턴스수가 늘어난다. 반대로 최소%보다 낮아질 경우 인스턴스 수가 줄어든다)

4. 메모리 임계 값(%)  :(③ 체크박스를 체크하면 임계값 적용을 한다. 미체크 -> 미적용) 오토스케일링할시 최소, 최대 메모리값을 입력한다.\
(인스턴스가 적용한 메모리보다 %가 높아질 경우 인스턴스수가 늘어난다. 반대로 최소 %보다 낮아질 경우 인스턴스 수가 줄어든다)

5. 측정시간(초) : 측정시간을 적용한다. 측정시간 만큼 각 인스턴스의 메모리 및 cpu 값의 평균치를 구해 오토스케일링을 적용한다.

6. 인스턴스 증감값 : 오토스케일링시 인스턴스가 늘어날 갯수를 적용한다.

7. 가상머신 자동확장 : 인스턴스를 자동 생성 여부를 적용한다.

8. 가상머신 자동축소 : 인스턴스를 자동 삭제 여부를 적용한다.

9. 변경을 누르면 설정이 적용된다.

### <div id='92'/> 3.2.1.3.7.4. 알람 설정
![3-2-1-3-7-1-4]
1.  CPU 임계 값(%) : 알람 최소, 최대 CPU값을 입력한다.\
(인스턴스가 적용한 CPU보다 %가 높아지거나 최소%보다 낮아질 경우 알람이 발생한다.)

4. 메모리 임계 값(%)  :알람 최소, 최대 메모리값을 입력한다.\
(인스턴스가 적용한 메모리보다 %가 높아질 경우 인스턴스수가 늘어난다. 반대로 최소 %보다 낮아질 경우 인스턴스 수가 줄어든다)

5. 측정시간(초) : 측정시간을 적용한다. 측정시간 만큼 각 인스턴스의 메모리 및 cpu 값의 평균치를 구해 알람을 적용한다.

6. E-Mail : 알람 메세지를 받을 이메일을 입력한다. 버튼으로 키거나 끌수 있다.

7. 알람 사용 : 버튼 온오프로 사용 미사용을 적용할수 있다.

8. 변경을 누르면 설정이 적용된다.

### <div id='65'/> 3.2.2.  카탈로그
1.  상단 메뉴의 “카탈로그” 버튼을 클릭하면 카탈로그 화면페이지로 이동한다.
![3-2-2-0]

### <div id='66'/> 3.2.2.1.  카탈로그 검색
1.  ① Catalog Search 에서 검색어를 입력하면 자동으로 검색 결과가 출력된다. 
![3-2-2-1-0]

### <div id='67'/> 3.2.3.  문서
1.  상단 메뉴의 “문서” 버튼을 클릭하면 문서 화면페이지로 이동한다.
![3-2-4-0]

### <div id='68'/> 3.2.4.  알림
1.  상단 메뉴의 ① "종"이미지를 클릭한다. 
- 알림 메시지는 5개까지 저장된다.
![3-2-7-1-0]

### <div id='69'/> 3.2.5.  내 계정
1.  우측 메뉴의 "내 계정"을 클릭하여 내계정 대시보드로 이동한다.
![3-2-7-2-0]

### <div id='70'/> 3.2.5.1.  프로필
1.  프로필 사진을 클릭하여 프로필 사진 변경 폼 레이어를 팝업 한다. 
![3-2-7-2-2-1-0]

2.  변경할 프로필 사진을 선택한 후, “사진등록”버튼을 클릭하여 프로필 사진 변경을 완료한다.
![3-2-7-2-2-1-00]

### <div id='71'/> 3.2.5.2.  로그인 정보
![3-2-7-2-1-0-1]

### <div id='72'/> 3.2.5.2.1. 비밀번호 변경
1.  ①“메모장” 버튼을 클릭한다.
![3-2-7-2-1-2-0]

2.  현재 비밀번호와 새 비밀번호를 입력한다. “변경하기” 버튼을 클릭하여 비밀번호 변경을 완료한다.
![3-2-7-2-1-2-1]

### <div id='73'/> 3.2.5.3.  사용자 정보
![3-2-7-2-1-0]

### <div id='74'/> 3.2.5.3.1  사용자 이름
1. 새 이름을 입력한다. "변경하기" 버튼을 클릭하여 이름 변경을 완료한다.
![userinfo-name]

### <div id='75'/> 3.2.5.3.2  사용자 전화번호
1. 새 전화번호를 입력할 때 숫자 입력만 가능하다.("-"제외 한 숫자만 입력) "변경하기" 버튼을 클릭하여 전화번호 변경을 완료한다.
![userinfo-phone]

### <div id='76'/> 3.2.5.3.3 사용자 우편번호
1. 새 우편번호를 입력할 때 15자 이하의 숫자와 영문 입력만 가능하다. "변경하기" 버튼을 클릭하여 우편번호 변경을 완료한다.
![userinfo-cz]

### <div id='77'/> 3.2.5.3.3 사용자 주소
1.새 주소를 입력할 때 256자 이하만 가능하다. "변경하기" 버튼을 클릭하여 이름 변경을 완료한다.
![userinfo-add]

### <div id='81'/>  3.2.5.4.  나의 조직
![3-2-7-2-1-0-2]

### <div id='79'/> 3.2.5.4.1  나의 조직 탈퇴
1.  목록에서 탈퇴 할 조직의 ① "휴지통 버튼"을 클릭한다.
![3-2-7-2-4-3-0]

2.  확인 팝업의 “삭제”버튼을 클릭하여 조직 탈퇴를 완료한다.
![3-2-7-2-4-3-1]

### <div id='80'/> 3.2.5.5.  계정 삭제
1.  “계정삭제” 버튼을 클릭한다.
![3-2-7-2-2-2-1]

2.  계정 삭제 확인 팝업에서 사용자 계정과 비밀번호를 입력한다. “삭제”버튼을 클릭하여 계정 삭제를 완료한다.
![3-2-7-2-2-2-0]

### <div id='81'/> 3.2.5.6.  로그아웃
1.  좌측 메뉴의 “로그아웃”을 클릭한다.
![3-2-7-4-0]

### <div id='82'/> 3.3.6.1.  CAAS Deployments 
1.  CAAS 서비스 설치한 조직의 Deployment 탭을 클릭한다.
![cass-dashboard1]

### <div id='83'/> 3.3.6.2.  CAAS Pods
1.  CAAS 서비스 설치한 조직의 Pods 탭을 클릭한다.
![cass-dashboard2]

### <div id='84'/> 3.3.6.3.  CAAS Replica Sets
1.  CAAS 서비스 설치한 조직의  Replica Sets탭을 클릭한다.
![cass-dashboard3]

### <div id='85'/> 3.3.6.4.  CAAS Pvc
1.  CAAS 서비스 설치한 조직의 Pvc 탭을 클릭한다.
![cass-dashboard4]

[2-1-0]:./images/user-portal/2-1-0.png
[2-1-1]:./images/user-portal/2-1-1.png
[2-1-2]:./images/user-portal/2-1-2.png
[2-1-3]:./images/user-portal/2-1-3.png
[2-1-4]:./images/user-portal/2-1-4.png
[2-2-0]:./images/user-portal/2-2-0.png
[2-2-1]:./images/user-portal/2-2-1.png
[2-2-2]:./images/user-portal/2-2-2.png
[2-3-0]:./images/user-portal/2-3-0.png
[2-3-1]:./images/user-portal/2-3-1.png
[2-3-1-1]:./images/user-portal/2-3-1-1.png
[2-3-1-2]:./images/user-portal/2-3-1-2.png
[2-3-1-3]:./images/user-portal/2-3-1-3.png
[2-3-1-4]:./images/user-portal/2-3-1-4.png
[3-2-1-0]:./images/user-portal/3-2-1-1-0.png
[3-2-1-1-0]:./images/user-portal/3-2-1-1-0.png
[3-2-1-1-1-0]:./images/user-portal/3-2-1-1-1-0.png
[3-2-1-1-1-1-0]:./images/user-portal/3-2-1-1-1-1-0.png
[3-2-1-1-1-1-1]:./images/user-portal/3-2-1-1-1-1-1.png
[3-2-1-1-1-2-0]:./images/user-portal/3-2-1-1-1-2-0.png
[3-2-1-1-1-2-1]:./images/user-portal/3-2-1-1-1-2-1.png
[3-2-1-1-1-2-2]:./images/user-portal/3-2-1-1-1-2-2.png
[3-2-1-1-1-2-3]:./images/user-portal/3-2-1-1-1-2-3.png
[3-2-1-1-1-3-0]:./images/user-portal/3-2-1-1-1-3-0.png
[3-2-1-1-1-3-1]:./images/user-portal/3-2-1-1-1-3-1.png
[3-2-1-1-1-4-1]:./images/user-portal/3-2-1-1-1-4-1.png
[3-2-1-1-2-0]:./images/user-portal/3-2-1-1-2-0.png
[3-2-1-1-2-1-0]:./images/user-portal/3-2-1-1-2-1-0.png
[3-2-1-1-2-1-1]:./images/user-portal/3-2-1-1-2-1-1.png
[3-2-1-1-2-1-2]:./images/user-portal/3-2-1-1-2-1-2.png
[3-2-1-1-3-0]:./images/user-portal/3-2-1-1-3-0.png
[3-2-1-1-3-1-0]:./images/user-portal/3-2-1-1-3-1-0.png
[3-2-1-1-3-1-1]:./images/user-portal/3-2-1-1-3-1-1.png
[3-2-1-1-3-2-0]:./images/user-portal/3-2-1-1-3-2-0.png
[3-2-1-1-3-2-1]:./images/user-portal/3-2-1-1-3-2-1.png
[3-2-1-1-4-0]:./images/user-portal/3-2-1-1-4-0.png
[3-2-1-1-4-1-0]:./images/user-portal/3-2-1-1-4-1-0.png
[3-2-1-1-4-1-1]:./images/user-portal/3-2-1-1-4-1-1.png
[3-2-1-1-4-1-2]:./images/user-portal/3-2-1-1-4-1-2.png
[3-2-1-1-4-4-0]:./images/user-portal/3-2-1-1-4-4-0.png
[3-2-1-2-0]:./images/user-portal/3-2-1-2-0.png
[3-2-1-2-1-0]:./images/user-portal/3-2-1-2-1-0.png
[3-2-1-2-1-1-0]:./images/user-portal/3-2-1-2-1-1-0.png
[3-2-1-2-1-1-1]:./images/user-portal/3-2-1-2-1-1-1.png
[3-2-1-2-1-2-0]:./images/user-portal/3-2-1-2-1-2-0.png
[3-2-1-2-1-2-1]:./images/user-portal/3-2-1-2-1-2-1.png
[3-2-1-2-1-3-0]:./images/user-portal/3-2-1-2-1-3-0.png
[3-2-1-2-1-3-1]:./images/user-portal/3-2-1-2-1-3-1.png
[3-2-1-2-1-4-0]:./images/user-portal/3-2-1-2-1-4-0.png
[3-2-1-2-2-0]:./images/user-portal/3-2-1-2-2-0.png
[3-2-1-2-2-1-0]:./images/user-portal/3-2-1-2-2-1-0.png
[3-2-1-2-2-2-0]:./images/user-portal/3-2-1-2-2-2-0.png
[3-2-1-2-2-3-0]:./images/user-portal/3-2-1-2-2-3-0.png
[3-2-1-2-2-3-1]:./images/user-portal/3-2-1-2-2-3-1.png
[3-2-1-2-2-4-0]:./images/user-portal/3-2-1-2-2-4-0.png
[3-2-1-2-2-4-1]:./images/user-portal/3-2-1-2-2-4-1.png
[3-2-1-2-2-5-0]:./images/user-portal/3-2-1-2-2-5-0.png
[3-2-1-2-2-5-1]:./images/user-portal/3-2-1-2-2-5-1.png
[3-2-1-2-3-0]:./images/user-portal/3-2-1-2-3-0.png
[3-2-1-2-3-3-0]:./images/user-portal/3-2-1-2-3-3-0.png
[3-2-1-2-3-3-1]:./images/user-portal/3-2-1-2-3-3-1.png
[userprovide-service]:./images/user-portal/userprovide-service.png
[userprovide-service2]:./images/user-portal/userprovide-service2.png
[userprovide-del]:./images/user-portal/userprovide-del.png
[userprovide-del2]:./images/user-portal/userprovide-del2.png
[3-2-1-2-3-4-0]:./images/user-portal/3-2-1-2-3-4-0.png
[3-2-1-2-3-4-1]:./images/user-portal/3-2-1-2-3-4-1.png
[3-2-1-2-3-5-0]:./images/user-portal/3-2-1-2-3-5-0.png
[3-2-1-2-3-5-1]:./images/user-portal/3-2-1-2-3-5-1.png
[3-2-1-3-0]:./images/user-portal/3-2-1-3-0.png
[3-2-1-3-1-0]:./images/user-portal/3-2-1-3-1-0.png
[3-2-1-3-1-1-0]:./images/user-portal/3-2-1-3-1-1-0.png
[3-2-1-3-1-1-1]:./images/user-portal/3-2-1-3-1-1-1.png
[3-2-1-3-1-2-0]:./images/user-portal/3-2-1-3-1-2-0.png
[3-2-1-3-1-2-1]:./images/user-portal/3-2-1-3-1-2-1.png
[3-2-1-3-1-3-0]:./images/user-portal/3-2-1-3-1-3-0.png
[3-2-1-3-1-3-0-00]:./images/user-portal/3-2-1-3-1-3-0-00.png
[3-2-1-3-1-4-0]:./images/user-portal/3-2-1-3-1-4-0.png
[3-2-1-3-1-5-0]:./images/user-portal/3-2-1-3-1-5-0.png
[3-2-1-3-2-0]:./images/user-portal/3-2-1-3-2-0.png
[3-2-1-3-2-1-0]:./images/user-portal/3-2-1-3-2-1-0.png
[3-2-1-3-3-0]:./images/user-portal/3-2-1-3-3-0.png
[3-2-1-3-3-1]:./images/user-portal/3-2-1-3-3-1.png
[3-2-1-3-3-1-0]:./images/user-portal/3-2-1-3-3-1-0.png
[3-2-1-3-4-0]:./images/user-portal/3-2-1-3-4-0.png
[3-2-1-3-4-1-0]:./images/user-portal/3-2-1-3-4-1-0.png
[3-2-1-3-4-1-1]:./images/user-portal/3-2-1-3-4-1-1.png
[3-2-1-3-4-1-1-example]:./images/user-portal/3-2-1-3-4-1-1-example.png
[3-2-1-3-4-2-0]:./images/user-portal/3-2-1-3-4-2-0.png
[3-2-1-3-4-2-1]:./images/user-portal/3-2-1-3-4-2-1.png
[3-2-1-3-4-2-2]:./images/user-portal/3-2-1-3-4-2-2.png
[3-2-1-3-4-2-3]:./images/user-portal/3-2-1-3-4-2-3.png
[3-2-1-3-4-2-4]:./images/user-portal/3-2-1-3-4-2-4.png
[3-2-1-3-4-2-5]:./images/user-portal/3-2-1-3-4-2-5.png
[3-2-1-3-5-0]:./images/user-portal/3-2-1-3-5-0.png
[3-2-1-3-5-1-0]:./images/user-portal/3-2-1-3-5-1-0.png
[3-2-1-3-5-2-0]:./images/user-portal/3-2-1-3-5-2-0.png
[3-2-1-3-5-2-1]:./images/user-portal/3-2-1-3-5-2-1.png
[3-2-1-3-5-3-0]:./images/user-portal/3-2-1-3-5-3-0.png
[3-2-1-3-5-3-1]:./images/user-portal/3-2-1-3-5-3-1.png
[3-2-1-3-6-0]:./images/user-portal/3-2-1-3-6-0.png
[3-2-1-3-6-1-0]:./images/user-portal/3-2-1-3-6-1-0.png
[3-2-1-3-6-2-0]:./images/user-portal/3-2-1-3-6-2-0.png
[3-2-1-3-7-0]:./images/user-portal/3-2-1-3-7-0.png
[3-2-1-3-7-1-0]:./images/user-portal/3-2-1-3-7-1-0.png
[3-2-1-3-7-1-1]:./images/user-portal/3-2-1-3-7-1-1.png
[3-2-1-3-7-1-2]:./images/user-portal/3-2-1-3-7-1-2.png
[3-2-1-3-7-1-3]:./images/user-portal/3-2-1-3-7-1-3.png
[3-2-1-3-7-1-4]:./images/user-portal/3-2-1-3-7-1-4.png
[3-2-2-0]:./images/user-portal/3-2-2-0.png
[3-2-2-1-0]:./images/user-portal/3-2-2-1-0.png
[3-2-2-2-0]:./images/user-portal/3-2-2-2-0.png
[3-2-2-2-1]:./images/user-portal/3-2-2-2-1.png
[3-2-2-2-2]:./images/user-portal/3-2-2-2-2.png
[3-2-2-3-0]:./images/user-portal/3-2-2-3-0.png
[3-2-2-3-1]:./images/user-portal/3-2-2-3-1.png
[3-2-2-3-2]:./images/user-portal/3-2-2-3-2.png
[3-2-2-4-0]:./images/user-portal/3-2-2-4-0.png
[3-2-2-4-1]:./images/user-portal/3-2-2-4-1.png
[3-2-2-4-2]:./images/user-portal/3-2-2-4-2.png
[3-2-1-2-1-4-1]:./images/user-portal/3-2-1-2-1-4-1.png
[3-2-4-0]:./images/user-portal/3-2-4-0.png 
[3-2-7-1-0]:./images/user-portal/3-2-7-1-0.png
[3-2-7-1-1-0]:./images/user-portal/3-2-7-1-1-0.png
[3-2-7-1-2-0]:./images/user-portal/3-2-7-1-2-0.png
[3-2-7-2-0]:./images/user-portal/3-2-7-2-0.png
[3-2-7-2-1-0]:./images/user-portal/3-2-7-2-1-0.png 
[3-2-7-2-1-0-1]:./images/user-portal/3-2-7-2-1-0-1.png 
[3-2-7-2-1-0-2]:./images/user-portal/3-2-7-2-1-0-2.png 
[3-2-7-2-1-2-0]:./images/user-portal/3-2-7-2-1-2-0.png
[3-2-7-2-1-2-1]:./images/user-portal/3-2-7-2-1-2-1.png
[3-2-7-2-2-0]:./images/user-portal/3-2-7-2-2-0.png
[3-2-7-2-2-1-0]:./images/user-portal/3-2-7-2-2-1-0.png
[3-2-7-2-2-1-00]:./images/user-portal/3-2-7-2-2-1-00.png
[3-2-7-2-2-1-1]:./images/user-portal/3-2-7-2-2-1-1.png
[3-2-7-2-2-2-0]:./images/user-portal/3-2-7-2-2-2-0.png
[3-2-7-2-2-2-1]:./images/user-portal/3-2-7-2-2-2-1.png
[3-2-7-2-4-0]:./images/user-portal/3-2-7-2-4-0.png
[3-2-7-2-4-3-0]:./images/user-portal/3-2-7-2-4-3-0.png
[3-2-7-2-4-3-1]:./images/user-portal/3-2-7-2-4-3-1.png
[3-2-7-4-0]:./images/user-portal/3-2-7-4-0.png
[3-2-1-3-3-1-0]:./images/user-portal/3-2-1-3-3-1-0.png
[3-2-1-3-3-1-1]:./images/user-portal/3-2-1-3-3-1-1.png
[3-2-1-3-3-1-2]:./images/user-portal/3-2-1-3-3-1-2.png
[web-ide-create]:./images/user-portal/web-ide-create.png
[email]:./images/user-portal/email.png
[passwordemail]:./images/user-portal/passwordemail.png
[dashboard]:./images/user-portal/dashboard.png
[inviteEmail]:./images/user-portal/inviteEmail.png
[userinfo-add]:./images/user-portal/userinfo-add.png
[userinfo-cz]:./images/user-portal/userinfo-cz.png
[userinfo-phone]:./images/user-portal/userinfo-phone.png
[userinfo-name]:./images/user-portal/userinfo-name.png
[cass-dashboard1]:./images/user-portal/cass-dashboard1.png
[cass-dashboard2]:./images/user-portal/cass-dashboard2.png
[cass-dashboard3]:./images/user-portal/cass-dashboard3.png
[cass-dashboard4]:./images/user-portal/cass-dashboard4.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > 사용자 포털
