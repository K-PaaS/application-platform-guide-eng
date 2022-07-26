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
This document is written on how to use the Configuration Management User Portal based on the Windows environment.

# <div id='4'/> 2. PaaS-TA User Portal Service

### <div id='5'/> 2.1. Create PaaS-TA Portal Account
Before creating a configuration management service, creating of PaaS-TA user portal account is required. For creating a user account or resetting the password for PaaS-TA, refer to PaaS-TA User Portal Guide.


# <div id='6'/> 3. Configuration Management User Portal Service


### <div id='7'/> 3.1. Preparation Needed before Creating Configuration Management Service
One Configuration Management Service can be created per organization. Refer to PaaS-TA User Portal Guide for details on creating a configuration management service.


### <div id='8'/>  3.1.1. Login
1.	Access to PaaS-TA User Portal.  
![002]

2. Click the "Login" button.  
![003]

3.	Enter the email address and password to use and click the “SIGN IN” button.  
![004]

4.	When logged in, it goes to the dashboard page. Enter the organization name to create and click the “Create Organization” button.  
![005]

5.	When an Organization is created, organization, space, domain, and user appear on the screen. Click the “Catalog” menu on top and proceed to the catalog list page.
  !![006]

6.	At the very bottom part of the catalog's [Integrated Development Tool] List, click the “Configuration Management Service” image to go to the configuration management's details page.  
![007]

7.	On the detailed page of the configuration management service, input the content for creating the configuration management service. Enter the service name in English and click the "Create" button to complete creating the configuration management service instance and proceeds to the space dashboard.  
![008]

8.	Check the Configuration Management Service Instance that was entered at the space dashboard. Go to Configuration Management Dashboard by clicking the “Dashboard” button on the right side of Configuration Management Service Instance.  
![009]

9.	When using the dashboard for the first time after creating a Configuration Management Service, enter a password between 6~16 letters.  
![010]

### <div id='9'/> 3.1.2. Repository Contributor Authority Management
The purpose of managing the Repository Contributor Authority is to give authority to the contributors. The management is the followings: 
Add, delete, and check Repository Contributor authority, check Instance User authority, search User ID repository contributor, and search instance ID through filtering.

### <div id='10'/> 3.1.2.1.1. Add Repository Contributor Authority
1.	On the Configuration Management Repository List page, click the name of the repository to add authority.  
![011]

2.	Once it's clicked, it will take you to the Repository Details page. Among the three tabs on the page, click the rightmost tab, the Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

3.	Click the “Add Contributor” button once the page appears.  
![012]

4.	Enter the User to give authority within [Search Source Controller User]. After checking the user in the user search list, click the "+" icon.  
![013]

5.	Bellow [Search Source Controller User], [Invite Contributor], Authority and descriptions are shown.
After checking the information of User ID, Name, and Email added in [Invite Contributor], Give the required authority (Modify/View) and click “Invite” when completed.  
![014]

6.	When notification “[Contributor has been Invited]” appears, the repository contributor authority has been given.  
![015]


### <div id='11'/> 3.1.2.1.2. Delete Repository Contributor Authority
1.	Click the contributor to remove authority from the user's list.

2.	Click the repository name from the contribution management repository list.

3.	Once it's clicked, it will take you to the Repository Details page. Among the three tabs on the page, click the rightmost tab, the Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

4.	Once the screen appears, click the repository contributor authority to delete.  
![016]

    Or, adding authority to Repository Contributors likewise, enter the name of the contributor whose authority is to be removed in [Search Source Controller User]. Check the searched user and click the '-' icon beside it.  
![013]

5.	When the Notification “Contributor has been Deleted.” appears, the repository contributor authority has been deleted successfully.


### <div id='12'/> 3.1.2.1.3. Check Repository Contributor Authority
1.	From the Repository Details page. Among the three tabs on the page, click the rightmost tab, the Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

2.	A list of all the contributors who created their repositories can be checked. Whether the contributor is managing configuration can be checked by the Active/Inactive image located on the right side of the page. The status of Repository Contributor Authority can be checked by Owner/Write/View images next to the Active/Inactive images.  
![017]

3.	Detailed Contributor Authorities can be checked by clicking the contributor's ID. Detailed information includes ID, Name, Email, Authority, and description of the user.  
![018]


### <div id='13'/> 3.1.2.1.4. Check Instance User Authority
1.	Click the repository name to check the contributor authority from the repository list.  
![019]

2.	Contributor's list can be checked by clicking “Contributor” located on the right side of the repository details tab. A contributor can be searched with the conditions of three following Combobox:  “Contributor ID search”, “All/View/Modify", or “All/Active/Inactive”.  
![020]


### <div id='14'/> 3.1.2.1.5. Search for User ID Repository Contributors
1.	Repository Contributor can be searched at the search box located on the left side of the Users list page. Enter ID or Name and click the magnifying glass image to look up to the list.  
![021]

2.	Using of capital letters doesn't matter on the User's list page. Contributors can be searched according to an authority other than the search function (All/Administrator/User) and (All/Active/Inactive).  
![022]

3.	Message saying “No data has been checked.” will appear when the created User ID or Name is not added to the Users list page.  
![023]


### <div id='15'/> 3.1.2.1.6. Search for user information by instance ID search criteria
1.	Repository Contributors can be searched from the search tab on the left side of the Users list page. Enter or click the magnifying glass after entering the ID or Name to search.  
![024]

2.	Using of capital letters doesn't matter on the User's list page. Contributors can be searched according to an authority other than the search function (All/Administrator/User) and (All/Active/Inactive).  
![025]


### <div id='16'/> 3.1.3. Create Repository
1.	Access to PaaS-TA Developers Portal. (Skip when already logged in.)

2.	After logging in, click “Dashboard” from the top of creating organization page's menu to proceed to the organization dashboard page. Click the “dev” space name located at the center of the screen to go to the space dashboard.  
![026]

3.	Click the “Dashboard” button to go to the configuration management dashboard from the space dashboard configuration management service instance.  
![027]

4.	When using the dashboard for the first time after creating a Configuration Management Service, enter a password between 6~16 letters. (When registering created configuration management service, it automatically goes to the configuration management dashboard.)  
![028]

5.	Click the “Create New” button from the menu on top.  
![029]

6.	Input the repository name in English at the create new repository page, select type(Git, SVN), and click the “Create” button. Click “OK” when a notification with the message “Repository was newly created.” appears.
  - Repository Name: XXX (English)
  - Type: Git
  - Repository Description : XXX  
![030]


### <div id='17'/> 3.1.3.1.1. Check All Repositories
1.	Check the created repository list from the repository list page.


### <div id='18'/> 3.1.3.1.2. Check User Repository List
1.	Check the user's repository list by searching the Repository List page, Combobox configuration management type, all repositories, and updating sequence from the repository list page.


### <div id='19'/> 3.1.3.1.3. Delete Repository
1.	Go to the repository details page by clicking the repository name on the repository list page.  
![019]

2.	Click the “View information/Modify” button on the right side of the repository details page.  
![031]

3.	Click the “Delete Repository” button at the right-bottom part of the view information/modify the page.  
![032]


### <div id='20'/>  3.1.3.1.4. Modify Repository
1.	Click the repository name from the repository list and proceed to the repository details page.  
![019]

2.	Modify by clicking the “Modify” button on the right side of the view repository details page. Modification is completed when a notification with the message “Modification Completed.” appears.  
![033]


### <div id='21'/> 3.1.3.1.5. Check Repository Details
Can check the File, Commit, and Contributor on the repository details page.


### <div id='22'/> 3.1.3.1.6. Check Repository File List
1.	Click “File” on the left side of the repository details page tab to check information about the branch, tag, and contributor.  
![034]

2.	Folder/File Name, Final Commit, File size, and last updates can be checked from the “File” List as default.

3.	URL can be copied from View Repository Details through “Repository Clonegb”.  
![035]


### <div id='23'/> 3.1.3.1.6.1. Check Repository Branch List
1.	Click “Branch”: drop-down to check the branch used before. To search quickly, enter the branch name in the branch search box.  
![036]


### <div id='24'/> 3.1.3.1.6.2. Check Repository Tag List
1.	Click “Tag”: drop-down to check the Tag used before. To search quickly, enter the Tag name in the Tag search box.  
![037]


### <div id='25'/> 3.1.3.1.7. Check Repository Browser List
1.	Folder/File Name, Final Commit, File and size, and last updates can be checked from the Repository Browser List. SRC and Target can be seen on the first screen with the folder image.  
![038]

2.	Click [Folder] image to see other sources and information including the main.
“[Folder]…” performs functions as going back to the previous page.


### <div id='26'/>  3.1.3.1.8. Check Repository Modified List
1.	Click “Commit” at the center of View Repository Details to check the list of the modified repository.  
![039]


### <div id='27'/> 3.1.4. User Management
### <div id='28'/> 3.1.4.1.1.	Check All Users
This chapter describes the usage of 7 menus of the PaaS-TA User Portal.
1.	Click My ID on the Configuration Management Repository List page. Click “User Management” shown on the second part of the list.  
![040]

2.	On the next screen, the list of all user IDs, Names, Created Dates, and Modified dates appears. On the right side of the list, the Active/Inactive images show whether the users using configuration management.  
![041]


### <div id='29'/> 3.1.4.1.2. Create User
1.	Follow the procedures done to go to the Create Users page.

2.	Click Create User and enter the following information required for creating User: ID, Name, E-mail, and Password. When creating a user ID, input from 2 to 12 small letters of the alphabet including numbers. and 6~16 for the password.  
![042]

3.	Click the "Create" button at the bottom after entering the ID and Password that meets the conditions. The process is completed when the notification message “[User has been Created Successfully]” appears. The created user can be checked from the user list.


### <div id='30'/> 3.1.4.1.3. Delete User
1.	Click the user to delete it from the user list.

2.	Click the “Delete User” button on the bottom-left part of the User Detail/Modify/Delete Page.  
![044]


### <div id='31'/> 3.1.4.1.4. Modify User
1.	Click the user to modify from the user list.

2.	Click the “Modify User” button on the bottom-left part of the User Detail/Modify/Delete Page.  
![045]


### <div id='32'/> 3.1.4.1.5. Check User's Detailed Information
1.	Users created and added are identified on the Users List page.

2.	Click the ID of the Repository Contributor to check the Authority and go to the User Detail/Modify/Delete page.  
![041]

3.	Information such as Name, ID, Email, Password, whether the user is active in using the configuration management, and descriptions can be checked from the User Detail/Modify/Delete Page.  
![042]


### <div id='33'/> 3.1.4.1.6. Check Users of Service Instance Repository
1.	From the Repository Details page. Among the three tabs on the page, click the rightmost tab, the Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

2.	A list of all the contributors who created their repositories can be checked. Whether the contributor is managing configuration can be checked by the Active/Inactive image located on the right side of the page. The status of Repository Contributor Authority can be checked by Owner/Write/View images next to the Active/Inactive images.  
![017]


### <div id='34'/> 3.1.4.1.7. Delete Instance User
1.	Click the user to delete authority from the User List.

2.	Click the Repository Name from the Configuration Management Repository List page.  
![011]

3.	Once clicked, it goes to the Repository details page. Among the three tabs on the page, click the rightmost tab, the Contributor tab, which is located below the 'View/Edit Details' and 'Create New' buttons.

4.	Click the “Add Contributor” button from the page.  
![012]

5.	 Enter the name of the contributor whose authority is to be removed in [Search Source Controller User]. Check the searched user and click the '-' icon beside it.  
![013]

6.	Instance User has been deleted if notification message “Contributor has been Deleted.” appears.

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
