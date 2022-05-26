### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > User Portal

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
     * [3.2.1.1.4.2.  Remove Member](#24)
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
     * [3.2.1.3.2.1.  View More Events](#51)
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
     * [3.2.1.3.7.3.  Auto scaling Setting](#91)
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
     * [3.3.6.1.  Check Dashboard CAAS Deployments](#82)
     * [3.3.6.2.  Check Dashboard CAAS Pods](#83)
     * [3.3.6.3.  Check Dashboard CAAS Replica Sets](#84)
     * [3.3.6.4.  Check Dashboard CAAS CAAS Pvc](#85)
     
     

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

3.  Click “Create Account” Link.
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
1.  Enter user ID and Password. Click “SIGN IN” to log in to the user portal.
![2-3-0]

2.  After the account is created, Dashboard page will appear directly at the first login.
![dashboard]

3.  Dashboard Page ① "Organization Management" Click the link or ②" Organization Management" at the menu on the right side. After the account is created, it is moved to the Dashboard page at the first login. More information can be found in 3.2.1.1 of this document.
![2-3-1]


# <div id='8'/> 3. PaaS-TA User Portal Manual

This chapter describes the menu configuration and screen description of the PaaS-TA user portal.


### <div id='9'/> 3.1.  Construct PaaS-TA User Portal Menu

The PaaS-TA User Portal consists of a section that manages organizations, spaces, and applications, a section that generates development environments and services, and an information check section that checks documents and menus.

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
    <td rowspan="5">Check Information</td>
  </tr>
  <tr>
    <td>Document</td>
    <td>Check Documents Registered by the Administrator</td>
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
1. ① Click "Organization Management" link or ②"Organization Management" on the menu to proceed to the organization dashboard.
![2-3-1]

2.  Click “Add New Organization” to proceed to the organization creating page.
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

4.  Click the "Proced" button on the confirm pop-up to view the newly created organization on the Organization Management page.
![2-3-1-3]
![2-3-1-4]
     > 3.2.1.1.1.1-3 Screen appears when creating the first new organization for the account. Click “Create New Organization” when creating an organization afterward. 

### <div id='15'/> 3.2.1.1.1.2.  Modify Organization Name
1. ① Click "Organization Management" Link or ②"Organization Management" at the menu to proceed to organization dashboard page.
![2-3-1]

2.  Click "View details" on the  ① sub-menu at the right, then click ② "Change Name".
![3-2-1-1-1-2-1]

3.  Enter new organization name. Click "Change".
![3-2-1-1-1-2-2]

4. Click the "Change" button on the pop-up to complete the organization name change.
![3-2-1-1-1-2-3]


### <div id='16'/> 3.2.1.1.1.3.  Delete Organization
1.  Click ② "Delete" on ① sub-menu at the right side beside "View Details".
![3-2-1-1-1-3-0]

2.  Click the "Delete" button on the confirm pop-up to complete the deletion of the organization.
![3-2-1-1-1-3-1]

### <div id='17'/> 3.2.1.1.2.  Space Management
![3-2-1-2-1-0]

### <div id='18'/> 3.2.1.1.2.1.  Create Space
1. ① Click "Organization Management" Link or ②"Organization Management" at the menu to proceed to organization dashboard page.
![2-3-1]

2.  Click “View Details” on the right side.
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

2.  Click the "Delete" button on the confirm pop-up to complete the deletion of the space.
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

2.  Click the "Delete" button on the confirm pop-up to complete the domain deletion.
![3-2-1-1-3-2-1]


### <div id='22'/> 3.2.1.1.4.  User Management
![3-2-1-1-4-0]

### <div id='23'/> 3.2.1.1.4.1  Invite User

1.  Click “+Invite User”.
![3-2-1-1-4-1-0]

2.  Enter the user account to invite. Empower the organization, the space.
![3-2-1-1-4-1-1]

3.  Click “Invite” button to complete inviting.
![3-2-1-1-4-1-2]

4.  An email is sent to the user who has been invited. The invited user will have access to the organization when “Accept Invite" link has been clicked.
     > **Email Format to Receive**
![inviteEmail]

### <div id='24'/> 3.2.1.1.4.2.  Remove Member
1.  Click the "Remove Member" button of the user account to cancel the membership.<br>
**'When removing the member, the OrgManager role must be disabled to complete canceling the member.**
![3-2-1-1-4-4-0]


### <div id='29'/> 3.2.1.2  Application and Service Environment Development

### <div id='30'/> 3.2.1.2.1.  Application Management
![3-2-1-2-2-0]

### <div id='31'/> 3.2.1.2.1.1.  Create Application
1.  Click "Dashboard" menu to go to the Dashboard page. Set the organization and space of the dashboard, and select the Application tab menu.
![3-2-1-2-1-3-0]

2.  Click ① "+" and proceed to catalog menu.
![3-2-1-2-1-3-1]

3.  Click ① App Development Environment at the Catalog list. Select ② App Development Environment to create.
![3-2-2-3-0]

4.  Enter necessary inputs needed to create an app development environment.
![3-2-2-3-1]

 -  Choose Organization from **Organization List**. <br>
 -  Choose Space from**Space List**. <br>
 -  Enter **App Name**. **App URL** is entered automatically but can be modified.  <br>
 -  Choose memory capacity from **Memory List**. <br>
 -  Choose Dist capacity from **Disk List**. <br>
 -  Set **App Launch Status**. <br>
 -  Upload file in case of user app being used. <br>

5.  Click “Create User App” or “Create Sample App” to complete creating app development environment.

### <div id='32'/> 3.2.1.2.1.2.  Modify Application Name
1.  Click ① sub menu of the application of the application to rename in the list and click ② "Change App Name".
![3-2-1-2-2-4-0]

2.  Enter new name of the application. Click "Change" to complete application name change.
![3-2-1-2-2-4-1]

### <div id='33'/> 3.2.1.2.1.3.  Run/ Stop Application
1.  Click ① sub menu of the application to stop then click ②“Stop”(■) or “Run"(▶).
![3-2-1-2-2-3-0]

2.  Enter the name of the application to run. Click the "Stop" button to stop running the application.
![3-2-1-2-2-3-1]

### <div id='34'/> 3.2.1.2.1.4.  Delete Application
1.  Click  ① sub menu of the application to delete from the list and click ②“Delete”.
![3-2-1-2-2-5-0]

2.  Click the "Delete" button on the confirm pop-up to complete the application deletion.
![3-2-1-2-2-5-1]

### <div id='36'/> 3.2.1.2.2.  Service Management
![3-2-1-2-3-0]

### <div id='37'/> 3.2.1.2.2.1.  Create Service
1.  Click "Dashboard" menu to go to the Dashboard page. Set the organization and space of the dashboard, and select the Application tab menu.
![3-2-1-2-1-4-0]

2. Click ① "+" to proceed to catalog menu.
![3-2-1-2-1-4-1]

3.   Click ① App development Environment from catalog list. Select ② Service Environment to create.
![3-2-2-4-0]

4.  Enter the necessary inputs to create service.
![3-2-2-4-1]

 -  Choose location from **Location List**. <br>
 -  Choose organization from **Organization List**. <br>
 -  Choose space from **Space List**. <br>
 -  Enter **Service Name**.  <br>
 -  Enter **Service Parameter**. (Appears only when service parameter is required.)  <br>
 -  Choose an app to connect from **App List** or click ‘Start without connecting an app’. <br>
 -  Select Service Plan from **Service Specification Selection List**. <br>

5.  Click "Create”  button to complete creating.
![3-2-2-4-2]

### <div id='38'/> 3.2.1.2.2.2.  Modify Service Name
1.  Click ① sub menu of the service to rename and click ② "Modify Service".
![3-2-1-2-3-4-0]

2. Enter the new name of the service. Click "Change" button to complete the service name change.
![3-2-1-2-3-4-1]

### <div id='39'/> 3.2.1.2.2.4.  Delete Service
1.   Click ① sub menu of the service to delete from the list and click ②“delete”.
![3-2-1-2-3-5-0]

2.  Click “Delete” button on the confirm pop-up to complete deletion of the service.
![3-2-1-2-3-5-1]

### <div id='40'/> 3.2.1.2.3.1.  Create User Provided Service
1.  Click “Create User Provided”.
![3-2-1-2-3-3-0]

2.  Enter Service Name, Credentials, and Syslog Drain URL. Click “Create” button to complete creating User Provided Service.
![3-2-1-2-3-3-1]

### <div id='41'/> 3.2.1.2.3.2.  Modify User Provided Service
1.  Click ① sub menu of the User Provided Service to rename from the list and click ② "Modify Service".
![userprovide-service]

2.  Enter Service Name, Credentials, and Syslog Drain URL of the service to modify.Click “Modify” button from the confirm pop-up to complete User Provided Service Modification.
![userprovide-service2]

### <div id='42'/> 3.2.1.2.3.3.  Delete User Provided Service
1.  Click ① sub menu of the User Provided Service to delete from the list and click  ②“Delete”.
![userprovide-del]

2.  Click the "Delete" button on the confirm pop-up to complete the deletion of the User Provided service.
![userprovide-del2]

### <div id='43'/> 3.2.1.3.  Application
### <div id='44'/> 3.2.1.3.1.  Application Dashboard
Click the name of the application created in the catalog list to go to the application dashboard page.
![3-2-1-3-1-0]

### <div id='45'/> 3.2.1.3.1.1.  Modify Application Name
1.  Click ① sub menu and click list ② "Change Name".
![3-2-1-3-1-1-0]

2.  Enter the new name of the application. Click imaged button ①“Save” to complete modifying application name.
![3-2-1-3-1-1-1]

### <div id='46'/> 3.2.1.3.1.2.  Restart Application
1.  Click ① “Restage”.
![3-2-1-3-1-4-0]

### <div id='47'/> 3.2.1.3.1.3.  Run/Stop Application
1.  Click ①“Run” or “Stop” imaged button.
![3-2-1-3-1-3-0-00]

### <div id='48'/> 3.2.1.3.1.4.  Delete Application
1.  Click ① sub menu and click ② "delete" from the list.
![3-2-1-3-1-2-0]

2.  CLick “delete” button from the confirm pop-up to complete deletion of the application.
![3-2-1-3-1-2-1]

### <div id='49'/> 3.2.1.3.1.5.  Number of Instance/Memory Capacity/Disk Capacity Setting
1.  After setting ① Number of Instance, ② Memory Capacity, ③ Disk Capacity, click “Save” button from each.
![3-2-1-3-1-5-0]

### <div id='50'/> 3.2.1.3.2.  Event
1.  Click ①“Event” menu or ② View all.
![3-2-1-3-2-0]

### <div id='51'/> 3.2.1.3.2.1.  View More Events
1.  “View More” button appears when there are more than 5 events.
![3-2-1-3-2-1-0]

### <div id='52'/> 3.2.1.3.3.  Status
1.  Click ①“Status” menu or ② View all.
![3-2-1-3-3-0]

2.  Can check the following information from the status list: Status, CPU, Memory, Disk, UPTIME(updatetime), and RESTART. Instance can be restarted with "RESTART" button.
![3-2-1-3-3-1]

### <div id='52-1'/> 3.2.1.3.3.1.  SSH Access
1.  Click ①“Status” menu or ② View all.
![3-2-1-3-3-1-0]

2.  Click “SSH Connect” according to the corresponding instance number to access.
![3-2-1-3-3-1-1]

3.  Proceed with the desired operation after accessing the corresponding instance with SSH.
![3-2-1-3-3-1-2]

### <div id='53'/> 3.2.1.3.4.  Service
1.  Click ①“Service” menu or ② View all.
![3-2-1-3-4-0]

### <div id='54'/> 3.2.1.3.4.1.  Connect Service
1.  Click “+ Connect Service”.
![3-2-1-3-4-1-0]

2.  Click “Connect” button after selecting service from the list.
![3-2-1-3-4-1-1] <br>
     > **The list of services found are only for services that can be app bounded.** 
     
3.  Click “Save” button to complete connecting after checking the confirm pop-up.
![3-2-1-3-4-2-3]

4.  Click App “Restart” button from the App restart confirm pop-up screen.
![3-2-1-3-4-2-4]
     
### <div id='78'/> 3.2.1.3.4.2.  Service Information
1.  Click ① "Notepad" button to check service access information from the list.
![3-2-1-3-4-2-2]

2.  Can check the following information through the confirm pop-up screen: Hostname, jdbcUrl, Name,Port, Username, and Password.
![3-2-1-3-4-2-1]

### <div id='55'/> 3.2.1.3.4.3.  Unconnect Service
1.  Click “Unconnect” button of the service to unconnect from the list.
![3-2-1-3-4-2-0]

2.  Click "Save" button from the confirm pop-up to complete the disconnect service.
![3-2-1-3-4-2-5]

3.  Click “Restart” button from the App restart confirm pop-up.
![3-2-1-3-4-2-4]

### <div id='56'/> 3.2.1.3.5.  Environment Variable
1.  Click ①“Environment Variable” menu or ② View all.
![3-2-1-3-5-0]

### <div id='57'/> 3.2.1.3.5.1.  Add Environment Variable
1.  CLick ①“+ Add” button. Enter environment variable name and value.Click ②“Save” button to complete adding environment variable.
![3-2-1-3-5-1-0]

### <div id='58'/> 3.2.1.3.5.2.  Modify Environment Variable
1.  Click ① "Notepad" button of the environment variable to modify from the list.
![3-2-1-3-5-2-0]

2.  After modifying the value of the environment variable, click  "Save" button to complete the modification.
![3-2-1-3-5-2-1]

### <div id='59'/> 3.2.1.3.5.3.  Delete Environment Variable
1.  Click ①“Trashbin" button of the environment variable to delete from the list.
![3-2-1-3-5-3-0]

2.  Click “Delete” button from the confirm pop-up to complete delation of the environment variable.
![3-2-1-3-5-3-1]

### <div id='60'/> 3.2.1.3.6.  Route
1.  Click ①“Route” menu or ② View all.
![3-2-1-3-6-0]

### <div id='61'/> 3.2.1.3.6.1.  Add Route
1.  Click “+Connect” button.
![3-2-1-3-6-1-0]

2.  Enter the name of the route to add. Click "Save".

3.  Click "OK" button from the confirm pop-up to complete adding route.

### <div id='62'/> 3.2.1.3.6.2.  Unconnect Route
1.  Click “Unconnect” button of the route to remove from the list.
![3-2-1-3-6-2-0]

### <div id='63'/> 3.2.1.3.7.  Log
1.  Click ①“Log” menu or ② View all.
![3-2-1-3-7-0]

### <div id='64'/> 3.2.1.3.7.1.  Log Management
1.  Click ①“Log” to check the recent logs.
![3-2-1-3-7-1-0]

### <div id='90'/> 3.2.1.3.7.2.  Tail Log
1.  Clock ① **</>Tail Logs** link to check the newly opened Tail Logs page. Click ② "URL LInk". 
![3-2-1-3-7-1-1] 

2.  Check Tail Logs tab to view the output of real time log from the URL page opened.
![3-2-1-3-7-1-2]

### <div id='91'/> 3.2.1.3.7.3. Auto Scaling
```diff
#### Install monitoring provided by PaaS-TA before proceeding.
```
![3-2-1-3-7-1-3]
1.  Click monitoring of ① App Layout. Click ② Setting tab button.

2.  Number of Instances: In case of auto-scaling, input the minimum and maximum number of instances.

3.  CPU Threshold Value(%) : (③ Checked box being checked means threshold will be applied. Unchecked -> not applied) When Auto-scaling, enter minimum and maximum CPU value.\
(Number of Instances increases when CPU percentage increases than the applied maximum instance. On the other hand, the number of instances decreases when instance is lower than the minimum instance)

4. Memory Threshold Value(%)  :(③ ③ Checked box being checked means threshold will be applied. Unchecked -> not applied) When Auto-scaling, enter minimum and maximum memory value.\
(Number of Instances increases when CPU percentage increases than the applied maximum instance. On the other hand, the number of instances decreases when instance is lower than the minimum instance)

5. Time Measure(Second): Apply time measurement. The average value of each instance's memory and CPU values for the measured time is calculated and auto-scaling is applied.

6. Instance increse/decrease value: Apply the number of instances to increase during auto-scaling.

7. Virtual Machine Auto-expansion: Apply whether to automatically create an instance.

8. Virtual Machine Auto-reduction: Apply whether or not to automatically delete an instance.

9. Click Change to save the settings.

### <div id='92'/> 3.2.1.3.7.4. Notification Setting
![3-2-1-3-7-1-4]
1.  CPU Threshold Value(%) : Enter minimum and maximum CPU value of notification.\
(Notification appears when the instance increases/decreases than what is applied at the CPU value percentage.)

4. Memory Threshold Value(%):Enter the minimum and maximum memory value of notification.\
(Number of Instances increases when CPU percentage increases than the applied maximum instance. On the other hand, the number of instances decreases when instance is lower than the minimum instance)

5. Time Measure(Second): Apply time measurement. The average value of each instance's memory and CPU values for the measured time is calculated and auto-scaling is applied.

6. E-Mail: Enter an email address to receive notifications. Can be enabled/ disabled through a button.

7. Notification Use: Can be enabled/ disabled through on and off buttons.

8. Click change to save settings.

### <div id='65'/> 3.2.2.  Catalog
1.  Go to Catalogs page by clicking the “Catalog” button at the menu on top.
![3-2-2-0]

### <div id='66'/> 3.2.2.1.  Search Catalog
1.  Results searched from ① Catalog Search automatically shows its outputs. 
![3-2-2-1-0]

### <div id='67'/> 3.2.3.  Decument
1.  Click the "Documents" button on the top menu to go to the document page.
![3-2-4-0]

### <div id='68'/> 3.2.4.  Notification
1.  Click the ① "bell" image on the top menu. 
- Up to 5 notifications can be saved.
![3-2-7-1-0]

### <div id='69'/> 3.2.5.  My Account
1.  Go to My accounts dashboard page by clicking "My Account" from the menu on right.
![3-2-7-2-0]

### <div id='70'/> 3.2.5.1.  Profile
1.  Click a profile picture to pop up the profile picture change form layer. 
![3-2-7-2-2-1-0]

2.  Select a picture to use as a new profile picture then click “Apply Picture” to complete the process.
![3-2-7-2-2-1-00]

### <div id='71'/> 3.2.5.2.  Login Information
![3-2-7-2-1-0-1]

### <div id='72'/> 3.2.5.2.1. Change Password
1.  Click ①“Notepad” button.
![3-2-7-2-1-2-0]

2.  Enter recent password and new password. Click “Change” button to complete.
![3-2-7-2-1-2-1]

### <div id='73'/> 3.2.5.3.  User Information
![3-2-7-2-1-0]

### <div id='74'/> 3.2.5.3.1  Use Name
1. Enter new name. Click "Change" button to complete.
![userinfo-name]

### <div id='75'/> 3.2.5.3.2  User Contact Number
1. Only numbers can be entered.(numbers excluding "-") Click "Change" to complete the process.
![userinfo-phone]

### <div id='76'/> 3.2.5.3.3 User Postal Code
1. When entering a new postal code, up to 15 characters or less letters and number can be entered. Click "Change" button to complete the process.
![userinfo-cz]

### <div id='77'/> 3.2.5.3.3 User Address
1. When entering new address, up to 256 characters or less letters and number can be entered. Click "Change" button to complete the process.
![userinfo-add]

### <div id='81'/>  3.2.5.4.  My Organization
![3-2-7-2-1-0-2]

### <div id='79'/> 3.2.5.4.1  Leave My Organization
1.  Click ① "Trashbin" button of the organization to leave from the list.
![3-2-7-2-4-3-0]

2.  Click “Delete” button from the confirm pop-up to complete the process.
![3-2-7-2-4-3-1]

### <div id='80'/> 3.2.5.5.  Delete Account
1.  Click “Delete Account” button.
![3-2-7-2-2-2-1]

2.  Enter user ID and password at the confirm pop-up. Click “Delete” button to complete the process.
![3-2-7-2-2-2-0]

### <div id='81'/> 3.2.5.6.  Logout
1.  Click “Logout” from menu on the left side.
![3-2-7-4-0]

### <div id='82'/> 3.3.6.1.  CAAS Deployments 
1.  Click Deployment tab of the CAAS service installed organization.
![cass-dashboard1]

### <div id='83'/> 3.3.6.2.  CAAS Pods
1.  Click Pods tab of the CAAS service installed organization.
![cass-dashboard2]

### <div id='84'/> 3.3.6.3.  CAAS Replica Sets
1.  Click Replica Sets tab of the CAAS service installed organization.
![cass-dashboard3]

### <div id='85'/> 3.3.6.4.  CAAS Pvc
1.  Click Pvc tab of the CAAS service installed organization.
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


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > User Portal
