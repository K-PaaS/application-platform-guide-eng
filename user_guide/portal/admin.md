### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Operators Portal

## Table of Contents
1. [Document Outline](#1)
    * [1.1. Purpose](#1.1)
    * [1.2. Range](#1.2)
    * [1.3. Install Operators Portal](#1.3)
2. [Start PaaS-TA Operator Portal](#2)
    * [2.1. PaaS-TA Operator Portal Login](#2.1)
    * [2.2. PaaS-TA Operator Portal Log](#2.2)
3. [PaaS-TA Operator Portal Dashboard](#3)
    * [3.1. Check Dashboard Information](#3.1)
4. [Operation Managing Menu](#4)
    * [4.1. Check Organization and Space](#4.1)    
    * [4.2. Client](#4.2)    
    * [4.3. Catalog](#4.3)    
    * [4.3.1 Catalog Managing App Template](#4.3.1)
    * [4.3.2. Catalog Managing App Development Environment](#4.3.2)
    * [4.3.3. Catalog Management Service](#4.3.3)
    * [4.4. User Management](#4.4)    
    * [4.5. Authorization Management](#4.5)    
    * [4.6. Code Management](#4.6)   
    * [4.7. Setting Information](#4.7)
    * [4.8. Quota Management](#4.8)      
    * [4.9. Isolation Management](#4.9)  
5. [Service Management](#5)
    * [5.1. Buildpack](#5.1)
    * [5.2. Service Broker](#5.2)
    * [5.3. Service Control](#5.3)
6. [Security Management](#6)
    * [6.1. Security Group](#6.1)


## <a name="1"/>1.  Document Outline

### <a name="1.1"/>   1.1. Purpose

This document describes how to use and manage the PaaS-TA Operator Portal website written in JAVA.

### <a name="1.2"/>   1.2. Range

Describes how to use the PaaS-TA operator portal website that is already installed.

### <a name="1.3"/>  1.3. Install Operators Portal

This User Guide does not describe the installation process.

 Refer to    [PaaS-TA Portal Deployment Guide Document](https://github.com/PaaS-TA/Guide-3.0-Penne-/blob/v3.5/Install-Guide/Portal/PaaS-TA%20Portal%20%EC%84%A4%EC%B9%98%EA%B0%80%EC%9D%B4%EB%93%9C.md) for Operators Portal Installation.

## <a name="2"/>2.  Start PaaS-TA Operator Portal

Access to the PaaS-TA Operators Portal Web site.

### <a name="2.1"/>  2.1. PaaS-TA Operator Portal Login

1. Accessing the PaaS-TA Operator Portal will bring up a login screen for authentication.
![01]

2. Enter User ID and Password then click "Login".
![02]

### <a name="2.2"/>  2.2. PaaS-TA Operator Portal Logout

1. Click the ① "Logout" button from (the logged-in) main page.
![03]

## <a name="3"/>3.  PaaS-TA Operator Portal Dashboard

Shows the Organization, Space, App, Users statistic Information registered in PaaS-TA.

### <a name="3.1"/>  3.1. Check Dashboard Information

1. Shows Organizations ①Space, ②APP, and ③ User information.<br>
![04] 

2. Detailed information on the Organizations domain can be checked by clicking ①Organization Name.<br>
![05]

## <a name="4"/>4.  Operation Managing Menu

A menu to perform the management necessary for the operation of the PaaS-TA portal.<br>

### <a name="4.1"/>  4.1. Check Organization and Space

**The administrator manages the organization and space of all users.<br>**

1. Check ①Organization and ②Space. ③Search Terms can also be checked.<br>
![06]

2. Click the ① Details button beside the organization name. Check detailed information of the ② Organization.<br>
![07]

3. Click the ①Detail button beside Space Name. Check detailed information on ②Space.<br>
![08]


### <a name="4.2"/>  4.2. Client

**The Administrator Menu provides client Check, register, and delete functions.<br>**

1. Check the Client list.<br>    
![09]

2. Proceed to the Client Details page by clicking **Client ID**. The client ID can be deleted by clicking the "Delete" button on the client detail page.<br>
![10]

3. Click the ①"Register Client" button. Register by clicking the ②"Register" button after entering the client ID, Password, Option, and Option Guide.<br>
![11]

### <a name="4.3"/>  4.3. Catalog

**The Administrator Menu provides catalog app template, app development environment, app service check, register, modify, and delete functions.<br>**

![12]

### <a name="4.3.1"/>  4.3.1 Catalog Managing App Template
1. Checklist of Catalog App Template. It shows Name, Outline, Classification, and Public Availability.    
In the search window, search types, search items, disclosure status, and search terms can be used.
![13]

2. A page with detail appears when clicking the app template name. In need of modification, input the updated data then click ①"Save" button.
![14]

3. Delete App Template by clicking the ②"Delete" button.
![15]

4. Click the ①"Register App Template" button when registering an app template. Enter the following at the App Template Register pop-up: Name, Classification, App Development Environment, Service Thumbnail, Public Availability, Summary, and Description. A new App Template will be registered by Clicking the ②"Register" button.
![16]

※ The catalog management code is required when registering and editing app templates, When there is no code available as of the moment, follow the instruction below.
- ① Click "Manage Code" > ② Click "STARTER_TAG" in "Group Table" > ③ Click "Register" button in "Detail Table" to add and use the catalog management code.

  ![catalog-code-starter]

### <a name="4.3.2"/>  4.3.2 Catalog Managing App Development Environment
1. Check the Catalog App Development Environment list. It shows Name, Outline, Classification, and Public Availability.
One can search by the type, item, or name, whether it's private or public in the search engine.
![17]

2. A page with detail appears when clicking the App Development Environment Name. Input data to be modified then click the ①"Save" button to complete the modification.
![18]

3. Delete App Development Environment by clicking the ②"Delete" button.
![19]

4. Click the ①"Register App Development Environment" button from the Catalog page to Register. Enter the following at the App Development Environment Registering pop-up: Name, Classification, App Development Environment, Service, Thumbnail, Public Availability, Summary, and Description. Complete registering by clicking the ②"Register" button.
![20]

※ The catalog management code is required when registering and editing app development environment, When there is no code available as of the moment, follow the instruction below.
- ① Click "Manage Code" > ② Click "BUILD_PACK_TAG" in "Group Table" > ③ Click "Register" button in "Detail Table" to add and use the catalog management code.
  ![catalog-code-buildpack]

### <a name="4.3.3"/>  4.3.3 Catalog Management Service 
1. Check Catalog Service List. Can check name, outline, classification, and public availability. One can search by the type, item, or name, whether it's private or public in the search engine.<br>
![21]

2. Detail page shows up when clicking the service name. After modifying, click the ①"Save" button to save.<br>
![22]

3. Delete the service by clicking the ②"Delete" button.<br>
![23]

4. When registering an app service on the catalog page, click the ①"Register App Service" button. 
Enter name, classification, service, thumbnail, parameter, pubic availability, outline, and description. Click ②"Register" to complete registering the service. <br>
![24]

※ The catalog management code is required when registering and editing app service, When there is no code available as of the moment, follow the instruction below.
- ① Click "Manage Code" > ② Click "SERVICE_PACK_TAG" in "Group Table" > ③ Click "Register" button in "Detail Table" to add and use the catalog management code.
  ![catalog-code-servicepack]

※ **delivery-pipeline**, an example of registering an app service 
1. Input necessary data for registering an app service at the registering form screen.
2. Enter the name to register an app service. 
3. Set classification as "Development Support Tool".
4. Set service as "delivery-pipeline-v2".
5. Select an image the user wants to use for the thumbnail. 
     > **The applied Thumbnail can be checked at the 'PaaS-TA User' of Catalog service list and service registration.**<br>
6. Insert document URL. 
     > (Ex.) https://github.com/PaaS-TA/DELIVERY-PIPELINE-UI <br>
     > **When the Document URL is applied, the 'PaaS-TA User' Catalog Service Registration 'View Document' button gets activated.**
     > ![pipeline-app-service-create]

7. Set needed parameter value when creating a service.
    (Ex.) owner : 'Automatic-input' /  org_name : 'Automatic-input'
8. Input "N" for App bind Usage.
9. Requires "Y" when the dashboard is being used. Setting the dashboard as "N" disables the dashboard button.
     > **When the dashboard is applied, catalog app service "Dashboard" button is activated in 'PaaS-TA User'**
     
    ![pipeline-app-dashboard]

10. In addition, enter public availability, outline, description, and catalog management code. Click the "Save" button to complete service registration.
     ![pipeline]

     
※ **source control**, an Example of App Service Registration
1. Enter the contents necessary to register the app service on the app service registration form screen.
2. Enter the App Service name to register. 
3. Set classification as "Development Support Tool".
4. Set Service as "paasta-sourcecontrol".
5. Select an image the user wants to use for thumbnail.
6. Insert Document URL.
     > (Ex.) https://github.com/PaaS-TA/Guide-3.0-Penne-/blob/master/Service-Guide/Tools/PaaS-TA%20%ED%98%95%EC%83%81%EA%B4%80%EB%A6%AC%20%EC%84%9C%EB%B9%84%EC%8A%A4%ED%8C%A9%20%EC%84%A4%EC%B9%98%20%EA%B0%80%EC%9D%B4%EB%93%9C_v1.0.md <br>
     > **When Document URL is applied, 'PaaS-TA User' Catalog Service Registration 'View Document' button gets activated.**<br>
7. Set needed parameter value when creating service.
    (Ex.) owner : 'Automatic-input' /  org_name : 'Automatic-input'
8. Input "N" for App bind Usage.
9. Requires "Y" when the dashboard is being used. Setting the dashboard as "N" disables the dashboard button.
     > **When dashboard is applied, catalog app service "Dashboard" button is activated in 'PaaS-TA User'**
10. In addition, enter public availability, outline, description, and catalog management code. Click the "Save" button to complete service registration.
     ![sourcecontroller]

### <a name="4.4"/>  4.4. User Management

**The administrator checks the user's information and approves the account.<br>**

1. Look up the User list. User account, name, phone number, presence or absence of the administrator, and approval status can be checked. can search with search terms in the search window.<br>
![25]

2. Click the ①"Manage" button and click "Reset Password" to reset the password.<br>
![26]

3. Click ①"Manage" button then "Delete Operator Authority" to delete Operating Authority.<br>
![28]
![27]
     > When checking the user management list after clicking the "Run" button, [Administrator] changes from  **Y** to **N**. If User Management List [Administrator] is **N** , Click  ①"Management" button then "Give Operator Authority". <br>
     
     ![27-1]

4. After clicking the ①"Management" button, Click " Cancel sign-in" to block CF login.  Approve CF login by clicking "Approve Sign in" when it's not approved.<br>
![30]
![31]
![31-2]
     > When checking the user management list by clicking "Modify", [Status] changes from **Approve** to **Disapprove**. Cannot log in to CF and Portal when its disapprove<br>
     
     ![31-1]
     
5. Click the ①"Manage" button then click "Delete Account" to delete the Account.<br>
![32]
![33]

### <a name="4.5"/>  4.5. Authorization Management

**Administrator menu provides and manages authority to Cloud Foundry security groups Check, modify, and delete.<br>**

1. Checks user and authorized groups.<br>
![34]

2. Enter the group name by clicking ①"Create Group". Click ②"Save" button to complete creating authorized group.   
![35]

3. Select the group to remove from ① Authorized group. When Confirm form layer pops up, click "Delete" to delete the authorized group.
![36]

4. Check the user of the appropriate group by clicking ① Authorized group from the list. Click ②"Register User". Click the selected user from the  ③ List. Register user by clicking the ④"Save" button.
![37]

5. Confirm the user from ① Authorized Group's list. Check the ② Check Box of the user to delete from the list. Click the ③ enabled "Delete User" button. Complete deletion by clicking ④ "Delete" from the confirm pop-up screen.
![38]

### <a name="4.6"/>  4.6. Code Management

**Administrator manages the PaaS-TA Portal Code.<br>**

1. Check the code used in Paas-TA Portal by code-group. Can check whether it is used, search type, and search term.<br>
![39]

2. Check the detailed code information by clicking the Code ID from the code group.<br>
![40] 

3. Click the ①"Register" button from **Group Table**. Enter Code ID and Code Name at the pop-up tab then click the ②"Register" button.
![41]
![42]

4. Click ①"Register" from **Detail Table**. Enter Key, Value, Summary, and usage from the code detail registering pop-up tab, then click the ②"Register" button to complete registering.<br>
![43]
![44]

5. Click appropriate code(Key, Value, Summary, Usage) from **Detail Table**. Complete deleting the code by clicking the ①"Delete" Button at the code detail pop-up tab.
![45]

### <a name="4.7"/>  4.7. Setting Information

**Administrator menu provides ta function to retrieve, register, and modify settings information on the Paas-ta portal.<br>**

1. Check setting information used in PaaS-TA Portal.
![60]

2. Modify and save the settings used at PaaS-TA Portal.
![62]

### <a name="4.8"/>  4.8. Quota Management

**Operator Menu provides checking, registering, modifying, and deleting of PaaS-TA Portal's organization and space quota.<br>**

![46]

1. Click "Register Organization Quota" to register the organization's quota.<br>
![47]

2. Enter Name, Memory, Instance, Route, Service Instance, App Instance, Public Availability, and reserved route at the Organization quota registering pop-up tab. Click "Register" and register the organization quota.<br>
![48]

3. Click the organization quota to modify. Save the modified organization quota by clicking ① "Save".
![49]

4. Select the organization quota to delete. Complete deleting the organization quota by clicking the ② "Delete" button from the organization quota details tab.
![49]

5. Register space quota by clicking "Register Space Quota".<br>
![58]

6. Enter the Name, Organization Name, Memory, Instance Memory, Route, Service Instance, App Instance, Public Availability, and Reserved Route of the space to register at the pop-up tab. Complete registering space quota by clicking the "Register" button.<br>
![59]

7. Click the quota of the space to modify. Modify from the space quota details pop-up tab and click the ①"Save" button to complete.
![61]

8. Select the space quota to delete. Click ② "Delete" button from the space quota details pop-up tab to Delete.
![61]

### <a name="4.9"/>  4.9. Isolation Management

**The Administrator creates a new isolation segment, after an admin creates a new isolation segment, the admin can then create and manage relationships between the orgs and spaces of a Cloud Foundry deployment and the new isolation segment.<br>**

1. You can look up the isolation management history used in PaaS-TA portal.<br>
![isolationSegments01]

2. Enter the name to be used when creating at Segment Name text box. Register by clicking the "Register Segment" button.<br>
![isolationSegments02]

3. Selected Organization's information retrieves when clicked from the isolation management list. Add organization from  ① "Add Organization" list.
![isolationSegments03]

4. Organization details can be checked by clicking Organization Name from the list. Organizations included in all isolation management can be checked in the  ① "Isolation Segments Name" list.
![isolationSegments04]

5. Click the ①"Delete" button to delete isolation management.
![isolationSegments05]


## <a name="5"/>5.  Service Management

This is a menu for performing service management on the PaaS-TA portal.<br>

### <a name="5.1"/>  5.1. Buildpack

**The administrator manages the retrieving, modification, use, and lock of the build pack.<br>**

1. Check the information of the buildpack being used at the PaaS-TA Portal.<br>
![50]

2. Change the value in the Combobox to change the order of buildpack..<br>
![51]

3. Click the usage checkbox to change the usage status of the buildpack.<br>
![52]

4. Click the Lock checkbox to change the Lock status of the buildpack.<br>
![53]

### <a name="5.2"/>  5.2 Service Broker

**Administrator's menu provides service broker URL registration, broker retrieve, modify and delete .<br>**

1. Look up the service broker list used in PaaS-TA Portal.<br>
![54]

2. Click on the service name in the service broker list to view the details of the service broker. When there are modifications, click ①"Save" button after modifying to save the changes.<br>
![55]

3. **5.2- 2** Click ②"Delete" button to delete the service broker.<br>

4.  Click the ①"Register Service Broker" button from the service broker list. Enter Service Broker Name, User Name, User Password, and URL information. Click the ②"Register" button to complete registering the service broker.<br>
![57]

### <a name="5.3"/>  5.3 Service Control

**Administrators control service plans and manage limited service availability available to users.<br>**

1. Look up the service list used in PaaS-TA Portal.<br>
![63]

2. Click a service from the service control list to check the authority activation of the service or the service plan. Can modify the  Click ①Access Combobox to modify the authority activation of the selected service. ② Organization authority can be granted to the selected service or service plan.<br>
**When "Access" is ALL(Activated), Service plan of One or all the organization's service or service plan access should be changed to 'NONE(Deactivated)'.**<br>
![64]

3. Add organization to give authority from the ① "Add Organization" list by clicking.
![65]

4. Delete Organization by clicking ①"Delete". When the deletion is completed, the initial access status will be changed to "ALL". <br>
![66]
![67]


## <a name="6"/>6.  Security Management

A menu for security management of the PaaS-TA portal.<br>
    
### <a name="6.1"/>  6.1 Security Group

**Administrator manages access control of a specific space and prepares tasks and apps by using staging and running rules.**<br>

1. Check the security group information used in PaaS-TA Portal.<br>
![securitygroups01]

2. Register the security group by clicking ①"Register Security Group". <br>
![securitygroups02]

3. Enter Name(required), staging default(required), running default(required) in the security registering tab, and check the checkbox. Fill out description, rules(required), log, port, and protocol. To add more information, click ① "+" to add more tabs. To delete the tab, click the ② "Delete Tab" button.<br>
![securitygroups03]

4. Click the ①"Export File" button from the security registering tab to check the information of the security group as ②.txt file.
![securitygroups04]

5. Security group information can be checked as ②.txt file by clicking ①"Open File" at the security group registering tab. The imported file will be opened in a new tab.
![securitygroups05]

6. Click ①"org" from the security group register pop-up. Click ②"space". Register org and space by clicking the ③"Register" button.
![securitygroups06]

7. Click ①"Delete" button from the security group register pop up to delete org and space. Save the group information by clicking ②"Register".
![securitygroups07]

8. Select the security group to modify. Click ①"Modify" from the group details pop up to modify the security group.<br>
![securitygroups08]

9. Select a security group to delete. Complete deleting the security group by clicking the ①"Delete" button from the details pop up.<br>
![securitygroups09] 



[01]:./images/admin-portal/portal-web-admin-01.png
[02]:./images/admin-portal/portal-web-admin-02.png					
[03]:./images/admin-portal/portal-web-admin-03.png					
[04]:./images/admin-portal/portal-web-admin-04.png					
[05]:./images/admin-portal/portal-web-admin-05.png					
[06]:./images/admin-portal/portal-web-admin-06.png					
[07]:./images/admin-portal/portal-web-admin-07.png					
[08]:./images/admin-portal/portal-web-admin-08.png					
[09]:./images/admin-portal/portal-web-admin-09.png					
[10]:./images/admin-portal/portal-web-admin-10.png					
[11]:./images/admin-portal/portal-web-admin-11.png					
[12]:./images/admin-portal/portal-web-admin-12.png					
[13]:./images/admin-portal/portal-web-admin-13.png					
[14]:./images/admin-portal/portal-web-admin-14.png					
[15]:./images/admin-portal/portal-web-admin-15.png					
[16]:./images/admin-portal/portal-web-admin-16.png					
[17]:./images/admin-portal/portal-web-admin-17.png					
[18]:./images/admin-portal/portal-web-admin-18.png					
[19]:./images/admin-portal/portal-web-admin-19.png					
[20]:./images/admin-portal/portal-web-admin-20.png	
[21]:./images/admin-portal/portal-web-admin-21.png
[22]:./images/admin-portal/portal-web-admin-22.png					
[23]:./images/admin-portal/portal-web-admin-23.png					
[24]:./images/admin-portal/portal-web-admin-24.png					
[25]:./images/admin-portal/portal-web-admin-25.png					
[26]:./images/admin-portal/portal-web-admin-26.png					
[27]:./images/admin-portal/portal-web-admin-27.png
[27-1]:./images/admin-portal/portal-web-admin-27-1.png	
[28]:./images/admin-portal/portal-web-admin-28.png					
[29]:./images/admin-portal/portal-web-admin-29.png					
[30]:./images/admin-portal/portal-web-admin-30.png					
[31]:./images/admin-portal/portal-web-admin-31.png		
[31-1]:./images/admin-portal/portal-web-admin-31-1.png
[31-2]:./images/admin-portal/portal-web-admin-31-2.PNG	
[32]:./images/admin-portal/portal-web-admin-32.png					
[33]:./images/admin-portal/portal-web-admin-33.png					
[34]:./images/admin-portal/portal-web-admin-34.png					
[35]:./images/admin-portal/portal-web-admin-35.png					
[36]:./images/admin-portal/portal-web-admin-36.png					
[37]:./images/admin-portal/portal-web-admin-37.png					
[38]:./images/admin-portal/portal-web-admin-38.png					
[39]:./images/admin-portal/portal-web-admin-39.png					
[40]:./images/admin-portal/portal-web-admin-40.png					
[41]:./images/admin-portal/portal-web-admin-41.png
[42]:./images/admin-portal/portal-web-admin-42.png					
[43]:./images/admin-portal/portal-web-admin-43.png					
[44]:./images/admin-portal/portal-web-admin-44.png					
[45]:./images/admin-portal/portal-web-admin-45.png					
[46]:./images/admin-portal/portal-web-admin-46.png					
[47]:./images/admin-portal/portal-web-admin-47.png					
[48]:./images/admin-portal/portal-web-admin-48.png					
[49]:./images/admin-portal/portal-web-admin-49.png					
[50]:./images/admin-portal/portal-web-admin-50.png					
[51]:./images/admin-portal/portal-web-admin-51.png					
[52]:./images/admin-portal/portal-web-admin-52.png					
[53]:./images/admin-portal/portal-web-admin-53.png					
[54]:./images/admin-portal/portal-web-admin-54.png					
[55]:./images/admin-portal/portal-web-admin-55.png					
[56]:./images/admin-portal/portal-web-admin-56.png					
[57]:./images/admin-portal/portal-web-admin-57.png					
[58]:./images/admin-portal/portal-web-admin-58.png					
[59]:./images/admin-portal/portal-web-admin-59.png	
[60]:./images/admin-portal/portal-web-admin-60.png	
[61]:./images/admin-portal/portal-web-admin-61.png	
[62]:./images/admin-portal/portal-web-admin-62.png	
[63]:./images/admin-portal/portal-web-admin-63.png	
[64]:./images/admin-portal/portal-web-admin-64.png	
[65]:./images/admin-portal/portal-web-admin-65.png	
[66]:./images/admin-portal/portal-web-admin-66.png	
[67]:./images/admin-portal/portal-web-admin-67.png	
[pipeline]:./images/admin-portal/portal-web-admin-pipeline.png
[pipeline-app-service-create]:./images/admin-portal/pipeline-app-service-create.png
[pipeline-app-dashboard]:./images/admin-portal/pipeline-app-dashboard.png
[sourcecontroller]:./images/admin-portal/portal-web-admin-sourcecontroller.png
[securitygroups01]:./images/admin-portal/securitygroups01.png			
[securitygroups02]:./images/admin-portal/securitygroups02.png	
[securitygroups03]:./images/admin-portal/securitygroups03.png	
[securitygroups04]:./images/admin-portal/securitygroups04.png	
[securitygroups05]:./images/admin-portal/securitygroups05.png	
[securitygroups06]:./images/admin-portal/securitygroups06.png	
[securitygroups07]:./images/admin-portal/securitygroups07.png	
[securitygroups08]:./images/admin-portal/securitygroups08.png	
[securitygroups09]:./images/admin-portal/securitygroups09.png	
[isolationSegments01]:./images/admin-portal/isolationSegments01.png	
[isolationSegments02]:./images/admin-portal/isolationSegments02.png	
[isolationSegments03]:./images/admin-portal/isolationSegments03.png	
[isolationSegments04]:./images/admin-portal/isolationSegments04.png	
[isolationSegments05]:./images/admin-portal/isolationSegments05.png
[catalog-code-starter]:./images/admin-portal/catalog-code-starter.png
[catalog-code-buildpack]:./images/admin-portal/catalog-code-buildpack.png
[catalog-code-servicepack]:./images/admin-portal/catalog-code-servicepack.png

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Operator Portal
