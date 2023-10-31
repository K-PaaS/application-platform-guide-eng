### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Pipeline Service


# [K-PaaS AP Pipeline Deployment Service User Guide]

## Table of Contents
1. [Document Outline](#1)
	* [1.1. Purpose](#1-1)
	* [1.2. Range](#1-2)
2. [Pipeline Deployment Service Registration ](#2)
	* [2.1. K-PaaS AP User Portal Portal Access](#2-1)
	* [2.2. Pipeline Deployment Service Registration](#2-2)
	* [2.3. Pipeline Deployment Access](#2-3)
3. [Pipeline Deployment User Manual](#3)
	* [3.1. Pipeline Deployment User's Menu Configuration](#3-1)
	* [3.2. Pipeline Deployment User Menu Setting](#3-2)
	* [3.2.1. User Management](#3-2-1)
	* [3.2.1.1. User Dashboard](#3-2-1-1)
	* [3.2.1.1.1. Check User List Search](#3-2-1-1-1)
	* [3.2.1.1.2. User Detail Check/ Modify](#3-2-1-1-2)
	* [3.2.2. Pipeline](#3-2-2)
	* [3.2.2.1. My Pipeline](#3-2-2-1)
	* [3.2.2.2. Create New Pipeline](#3-2-2-2)
	* [3.2.2.2.1. Create New Pipeline(1)](#3-2-2-2-1)
	* [3.2.2.2.2. Create New Pipeline(2)](#3-2-2-2-2)
	* [3.2.2.2.3. Create New Pipeline(3)](#3-2-2-2-3)
	* [3.2.2.3.   Pipeline Dashboard](#3-2-2-3)
	* [3.2.2.3.1. Pipeline List Search Check](#3-2-2-3-1)
	* [3.2.2.3.2. Pipeline Detail Information Check](#3-2-2-3-2)
	* [3.2.2.3.3. Modify Pipeline](#3-2-2-3-3)
	* [3.2.2.3.4. Delete Pipeline](#3-2-2-3-4)
	* [3.2.2.4.   Pipeline Details](#3-2-2-4)
	* [3.2.2.4.1. Manage Contributors](#3-2-2-4-1)
	* [3.2.2.4.1.1. Add Contributors](#3-2-2-4-1-1)
	* [3.2.2.4.1.2.    Check Contributors List Search](#3-2-2-4-1-2)
	* [3.2.2.4.1.3.    Check/ Modify Contributors Detail Information](#3-2-2-4-1-3)
	* [3.2.2.4.1.4.    Delete Contributors](#3-2-2-4-1-4)
	* [3.2.2.4.2. Build Job](#3-2-2-4-2)
	* [3.2.2.4.2.1.    Create Build Job ](#3-2-2-4-2-1)
	* [3.2.2.4.2.2.    Check/ Modify Build Job Configuration](#3-2-2-4-2-2)
	* [3.2.2.4.2.3.    Run Build Job](#3-2-2-4-2-3)
	* [3.2.2.4.2.4.    Stop Build Job](#3-2-2-4-2-4)
	* [3.2.2.4.2.5.    Build Job Log/ History](#3-2-2-4-2-5)
	* [3.2.2.4.2.6.    Build Job Log Download](#3-2-2-4-2-6)
	* [3.2.2.4.2.7.    Add Build Job](#3-2-2-4-2-7)
	* [3.2.2.4.2.8.    Duplicate Build Job](#3-2-2-4-2-8)
	* [3.2.2.4.2.9.    Delete Build Job](#3-2-2-4-2-9)
	* [3.2.2.4.3. Test Job](#3-2-2-4-3)
	* [3.2.2.4.3.1.    Create Test Job](#3-2-2-4-3-1)
	* [3.2.2.4.3.2.    Check/ Modify Test Job Configuration](#3-2-2-4-3-2)
	* [3.2.2.4.3.3.    Run Test Job](#3-2-2-4-3-3)
	* [3.2.2.4.3.4.    Stop Test Job](#3-2-2-4-3-4)
	* [3.2.2.4.3.5.    Test Job Log/ History](#3-2-2-4-3-5)
    * [3.2.2.4.3.6.    Test Job Quality Issue Results](#3-2-2-4-3-6)
	* [3.2.2.4.3.7.    Add Test Job](#3-2-2-4-3-7)
	* [3.2.2.4.3.8.    Duplicate Test Job](#3-2-2-4-3-8)
	* [3.2.2.4.3.9.    Delete Test Job](#3-2-2-4-3-9)
	* [3.2.2.4.4. Deployment Job](#3-2-2-4-4)
	* [3.2.2.4.4.1.    Create Deployment Job](#3-2-2-4-4-1)
	* [3.2.2.4.4.2.    Check/ Modify Deployment Job Configuration](#3-2-2-4-4-2)
	* [3.2.2.4.4.3.    Run Deployment Job](#3-2-2-4-4-3)
	* [3.2.2.4.4.4.    Stop Deployment Job](#3-2-2-4-4-4)
	* [3.2.2.4.4.5.    Deployment Job Log/ History](#3-2-2-4-4-5)
	* [3.2.2.4.4.6.    Deployment Job Rollback to Current Job](#3-2-2-4-4-6)
	* [3.2.2.4.4.7.    Add Deployment Job](#3-2-2-4-4-7)
	* [3.2.2.4.4.8.    Duplicate Deployment Job](#3-2-2-4-4-8)
	* [3.2.2.4.4.9.    Delete Deployment Job](#3-2-2-4-4-9)
	* [3.2.2.4.5. Job Sorting](#3-2-2-4-5)
	* [3.2.2.4.6. Add New Job Group](#3-2-2-4-6)
	* [3.2.3.     Pipeline Management](#3-2-3)
	* [3.2.3.1.   Cloud Foundry Information Management](#3-2-3-1)
	* [3.2.3.1.1. Cloud Foundry Account Registration](#3-2-3-1-1)
	* [3.2.3.1.2. Cloud Foundry Account Modification](#3-2-3-1-2)
	* [3.2.4.     Quality Management](#3-2-4)
	* [3.2.4.1.   Quality Issue](#3-2-4-1)
	* [3.2.4.2.   Coding Rules](#3-2-4-2)
	* [3.2.4.3.   Quality Profile](#3-2-4-3)
	* [3.2.4.3.1. Create Quality Profile](#3-2-4-3-1)
	* [3.2.4.3.2. Duplicate Quality Profile](#3-2-4-3-2)
	* [3.2.4.3.3. Modify Quality Profile](#3-2-4-3-3)
	* [3.2.4.3.4. Connect Quality Profile Project](#3-2-4-3-4)
	* [3.2.4.3.5. Delete Quality Profile](#3-2-4-3-5)
	* [3.2.4.4.   Quality Gate](#3-2-4-4)
	* [3.2.4.4.1. Create Quality Gate](#3-2-4-4-1)
	* [3.2.4.4.2. Duplicate Quality Gate](#3-2-4-4-2)
	* [3.2.4.4.3. Modify Quality Gate](#3-2-4-4-3)
	* [3.2.4.4.4. Add Quality Gate Conditions](#3-2-4-4-4)
	* [3.2.4.4.5. Connect Quality Gate Project](#3-2-4-4-5)
	* [3.2.4.4.6. Delete Quality Gate](#3-2-4-4-6)

# <div id='1'/> 1. Document Outline

## <div id='1-1'/> 1.1 Purpose
This document describes how to use the pipeline deployment service.

## <div id='1-2'/> 1.2 Range
This document is written about how to use the deployment pipeline service based on the Windows environment.

# <div id='2'/> 2. Pipeline Deployment Service Registration

## <div id='2-1'/> 2.1 K-PaaS AP User Portal Access
1. Click log-in from the K-PaaS AP User Portal.  
![002]

2. Log in to the Portal by entering User ID and Password then click “LOGIN”.  
![003]

3. Go to the Catalog page after logging in.  
![004]


## <div id='2-2'/> 2.2 Pipeline Deployment Service Registration
1. Select Pipeline Deployment Service.  
![005]

2. Select a space to apply for. In the ORG that already uses pipeline deployment, an error will occur as shown in the picture below.  
![006]

3. Enter Service Name. If the name of the requested pipeline deployment already exists, an error will occur as shown in the picture below.  
![007]

4. After applying, go to the service tab of the space page to check if the pipeline service has been successfully created.  
![008]

## <div id='2-3'/> 2.3 Pipeline Deployment Access
1. Click the “Dashboard” button of the applied pipeline deployment from the K-PaaS AP Portals Space page and access.  
![009]

2. Check the connection of pipeline deployment.  
![010]

3. At this, ***the user who first created the pipeline deployment becomes the administrator.*** Go to the user dashboard by clicking the user management menu at the right top.

***※ Users who connect for the first time will not be able to access without administrator approval.***


# <div id='3'/> 3. Pipeline Deployment User Manual
This chapter describes the menu configuration and screen description of the distribution pipeline.

## <div id='3-1'/> 3.1 Pipeline Deployment User's Menu Configuration
The Pipeline deployment service consists of the following parts: Pipeline search and management, Cloud Foundry account information management, source code quality management, and user management that administrates the users according to the given authorities.

***※ Users cannot see: user management, pipeline management, and quality management menu.***
<table>
  <tr>
    <td>Menu</td>
    <td>Classification</td>
		<td>Description</td>
  </tr>
  <tr>
    <td>User Management</td>
    <td>User Dashboard</td>
	  <td>Check and manages the information for pipeline users</td>
  </tr>
  <tr>
		<td>Pipeline</td>
		<td>Pipeline Dashboard</td>
		<td>Manages the functions of Pipeline registration,list and details search, delete, and  contributors authority</td>
  </tr>
	<tr>
		<td>Pipeline Management</td>
		<td>Cloud Foundry Information Mangement</td>
		<td>Manages the account information of Cloud Foundry</td>
  </tr>
	<tr>
		<th rowspan="4">Quality Management</th>
		<td>Quatlitu Issue</td>
		<td>Manages whether or not the source code performed is error tolerance and resolution, activation status</td>
  </tr>
	<tr>
		<td>Coding Rules</td>
		<td>Manage coding forms for standardized source code</td>
	</tr>
	<tr>
		<td>Quality Profile</td>
		<td>Manages coding rules that serve as standards for analyzing the quality of source code</td>
	</tr>
	<tr>
		<td>Quality Gate</td>
		<td>Manages criteria for quality analysis of source codes that exceed or fall short of a set quality reference value</td>
	</tr>
</table>


## <div id='3-2'/> 3.2 Pipeline Deployment User Menu Setting
This chapter describes the 4 menus in the pipeline deployment.

### <div id='3-2-1'/> 3.2.1. User Management
The user management menu is visible only to the administrators. This chapter describes the authorization management and information check and manages users of pipeline deployment service.

#### <div id='3-2-1-1'/> 3.2.1.1. User Dashboard

##### <div id='3-2-1-1-1'/> 3.2.1.1.1. Check User List Search
1. Click the “User Management” button at the top-right menu.  
![011]

2. Go to the user dashboard.  
![012]

3. Two conditions can be searched in the user dashboard list: a search with a user ID as a search term and a search whose type is an administrator/user. Click the "Enter" or "Magnifying class" button to search.  
![013]  
![014]

##### <div id='3-2-1-1-2'/> 3.2.1.1.2. User Detail Check/ Modify
1. Select ID from the user dashboard and proceed to the user details check/ modify the page.  
![015]

2. Check the detailed information of the user.  
![016]

3. When you want to modify the information, enter a new input value. Authority can be selected as administrator or user.  
![017]

4. After entering the new value, click the “Modify” button.  
![018]

5. Check the modified information.    
![019]


### <div id='3-2-2'/> 3.2.2. Pipeline
This chapter describes the two methods of how to manage pipeline.

***※ Users can only see Pipeline and cannot create, modify, check, and delete until when they have contributors' authorization.***

#### <div id='3-2-2-1'/> 3.2.2.1. My Pipeline
1.	No matter which page you are on, click the My Pipeline button on the drop-down menu to go to the dashboard.  
![020]  
![021]
2.	Click the name of the pipeline from My Pipeline's list and proceed to the selected pipeline's detailed page.  
![022]  
![023]

#### <div id='3-2-2-2'/> 3.2.2.2. Create New Pipeline
##### <div id='3-2-2-2-1'/> 3.2.2.2.1. Create New Pipeline(1)
1. Click “Pipeline List” from the menu on top to create a new Pipeline just by entering the name.  
![024]
2. After clicking the register button, the added pipeline can be checked on the dashboard.  
![025]
3. The registered Pipeline can also be checked from the Dropdown Menu.  
![026]
##### <div id='3-2-2-2-2'/> 3.2.2.2.2. Create New Pipeline(2)
1.	Click “Create New” button at the top right.
***※  “Create New” button is not activated for the Users.***  
![027]
2.	Go to Create New Pipeline Page.    
![028]
3.	Pipeline name is REQUIRED. Make sure to enter the Pipeline name and click the “Create” button.    
![029]
4.	Go to the Pipeline dashboard page and check the created pipeline's name.    
![030]

##### <div id='3-2-2-2-3'/> 3.2.2.2.3. Create New Pipeline(3)
***※ Users do not have authority as contributors to the pipeline and cannot proceed to the pipeline's details page.***
1. Click the “Create New” button at the top right.    
![031]
2. Go to Create New Pipeline Page.    
![032]
3. Refer to 3.2.2.2.2. Create New Pipeline(2) for the next procedures.

#### <div id='3-2-2-3'/> 3.2.2.3. Pipeline Dashboard
##### <div id='3-2-2-3-1'/> 3.2.2.3.1. Pipeline List Search Check
1. A desired pipeline can be searched by entering the Pipeline name. Or the Pipeline list could be arranged by the sequence of created dates, updated dates, and alphabetical order.  
![033]
2.	Click the “ENTER” or “Magnifying glass” image button to search.  
![034]
3.	If you select a search type filter for both a list inquire/search list inquire, it will be reflected immediately.  
![035]

##### <div id='3-2-2-3-2'/> 3.2.2.3.2. Pipeline Detail Information Check
1.	Go to the Pipeline details page by clicking the pipeline's name from the pipeline dashboard.  
![036]
2.	Click the “View/Modify information” button at the top right of the pipeline details page.  
![037]
3.	Check the details of the selected Pipeline from the view/modify Pipeline Information page.  
![038]


##### <div id='3-2-2-3-3'/> 3.2.2.3.3. Modify Pipeline
1.	On the View/Modify Pipeline Information page, modify the input value and click the "Modify" button.  
![039]

2.	Once modified, go to the Pipeline Details page. Click the "View/Modify information" button again to check the changed value.  
![040]

##### <div id='3-2-2-3-4'/> 3.2.2.3.4. Delete Pipeline
1.	On the View/Modify Pipeline Information page, click the "Delete Pipeline" button.  
![041]
2.	Go to the pipeline dashboard to verify that the pipeline has been deleted.  
![042]


#### <div id='3-2-2-4'/> 3.2.2.4. Pipeline Details
This chapter describes overall contributor management, such as adding, modifying, deleting, and authorizing Contributors Contributing in the pipeline. Also talks about how to create, execute, delete, log/history check, and manage contributors for one pipeline.
##### <div id='3-2-2-4-1'/> 3.2.2.4.1. Manage Contributors
###### <div id='3-2-2-4-1-1'/> 3.2.2.4.1.1. Add Contributors
***※	 Administrators are pipeline creators, so they are contributors with creation authority by default.***
1.	Select the Contributors tab from the pipeline details page.  
![043]
2.	Click the “Add Contributor” button at the top right.  
![044]
3.	Go to the Add Contributors page to search for users who are currently invited to the pipeline deployment and select. Select one of the authorities among view, create, or execute and click the "Add" button. 
![045]
4.	Check the added contributor at the Contributors tab.  
![046]

###### <div id='3-2-2-4-1-2'/> 3.2.2.4.1.2. Check Contributors List Search
1.	The condition that can be searched in the contributor list is one contributor ID.  
![047]
2.	After entering the search word, click the “ENTER” or “Magnifying glass” image button to check.  
![048]

###### <div id='3-2-2-4-1-3'/> 3.2.2.4.1.3.	Check/ Modify Contributor's Detail Information
1.	Select the ID of the contributor to check from the contributor list.  
![049]
2.	Go to the check/modify page for contributor details and check the contributor's details.  
![050]
3.	Select the authority to modify and click the "Modify" button.  
![051]
4.	Check the modified authority of the contributor from the contributor's list.  
![052]


###### <div id='3-2-2-4-1-4'/> 3.2.2.4.1.4.	Delete Contributor
1.	Select a contributor to delete from the list.  
![053]
2.	Go to the detailed information and check/modify the page of the selected contributor.  
![053-2]
3.	Click the “Delete Contributor” button.  
![054]
4.	Check from the list if the Contributor is deleted.  
![055]


##### <div id='3-2-2-4-2'/> 3.2.2.4.2. Build Job
###### <div id='3-2-2-4-2-1'/> 3.2.2.4.2.1. Create Build Job 
1.	Click the ‘click here to add new Job’ button from the pipeline details page.  
![056]
2.	Proceed to the configuration details page.  
![057]
3. Select a job type and select the desired repository as the input type. Enter the ID and password of the selected repository, and the path of the repository to click the "Retrieve" button. Select the retrieved Branch and choose the suitable job trigger.<br>  
![058]

4.	Check the newly created Build Job from the Pipeline details page.  
![059]

***※ Creating a Build Job can only be done by the administrator and authorized contributors of the pipeline.***

###### <div id='3-2-2-4-2-2'/> 3.2.2.4.2.2. Check/ Modify Build Job Configuration
1. Click the “Configuration” icon of the created Build Job.<br>  
![060]
2. Go to the configuration details page and look up the configuration information saved when creating.  
![061]
3. When modifying, re-enter the information to modify in each input form and click the "Save" button.  
![062]
4. Go to the configuration detail page and check the modified information.  
![063]

***※ All pipeline Contributors can check the build job configuration. However, modifications can only be made by administrators and pipeline Contributors with creation authority.***

###### <div id='3-2-2-4-2-3'/> 3.2.2.4.2.3. Run Build Job
1. Click the “Run” icon from the Pipeline details page.<br>  
![064]
2. Once it starts running, the bar turns blue and blinks. (View logs in real-time by clicking the "log/history" icon while running.)  
![065]
3. Once the execution is done, the bar turns green and the Build status changes into Build(Completed).  
![066]

***※	Build Job execution is only permitted to administrators and pipeline Contributors with creation and execution authority.***


###### <div id='3-2-2-4-2-4'/> 3.2.2.4.2.4. Stop Build Job
1.	Click the "Stop" icon when you want to stop or cancel a running build Job.  
![067]
2.	The stopped Build Job's bar turns orange.  
![068]

***※	Stopping of Build Job is only permitted to administrators and pipeline Contributors with creation and execution authority.***


###### <div id='3-2-2-4-2-5'/> 3.2.2.4.2.5.	Build Job Log/History
1.	 Logs can be seen in real-time by clicking the "log/history" icon when the build job is running.  
![069]
2.	Go to the log check page. Check the logs in real-time.  
![070]
3.	Confirm that the build job execution is complete, and verify the history.  
![071]
4.	Cancel and run a Job by clicking the "Cancel" or "Run" button at the top of the Log/History page.  
![072]  
![073]
5.	Click the "Configure" button on the Log/History page to go to the Build Job Configuration check page.  
![074]
6.	Click the "List" button on the Log/History page to go to the Pipeline Details page.  
![075]

***※	The build Job log/history is visible to the administrator and all pipeline Contributors, but the Run and Stop buttons can only be seen by Contributors with creation and execution authority.***

###### <div id='3-2-2-4-2-6'/> 3.2.2.4.2.6.	Build Job Log Download
1.	If the build job execution is successful, the "Download" button is activated on the Log/History page. Click the "Download" button enabled .<br>  
![076]
2.	Built files will be downloaded(ex. war, jar, zip, etc.).  
![077]

***※	Downloading the build Job log is permitted to administrators and all pipeline Contributors.***

###### <div id='3-2-2-4-2-7'/> 3.2.2.4.2.7.	Add Build Job
1.	click the "Add" button on the Build Job on the Pipeline Details page.  
![078]
2.	Go to the Configuration Details page.  
![079]
3.	Enter the appropriate values in the input-form, such as creating a build job, and click the "Save" button. (Job types can be selected aside from build. Such as Test or deployment.)  
![080]
4.	Check the added Build Job.<br>  
![081]
5.	If you choose 'Make a new job group for this Job' for the Job Trigger, the Job will be added to a new group. (This will be dealt in detail later in the explanation of 'Add a new Job group'.)

***※	Adding a build job is only possible for administrators and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-2-8'/> 3.2.2.4.2.8.	Duplicate Build Job
1.	On the Pipeline Details page, click the "Duplicate" button on the Build Job.  
![082]
2. Check the duplicated Build Job.  
![083]

***※	Build Job Duplication can only be done by the administrator and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-2-9'/> 3.2.2.4.2.9.	Delete Build Job
1.	Click the “Delete” button of the Build job from the pipeline details page.  
![084]
2.	Check if the Build Job has been deleted.  
![084-2]

***※	Build Job deletion can only be done by the administrator and pipeline contributors with creation authority.***


##### <div id='3-2-2-4-3'/> 3.2.2.4.3. Test Job
###### <div id='3-2-2-4-3-1'/> 3.2.2.4.3.1. Create Test Job
1.	Click the “Add” button from the Job.  
![084-3]
2.	Go to the configuration page and select job type as Test then enter the desired quality profile, quality gate, and Job group. Select a suitable job trigger.  
![085]
3.	Click the “Save” button and check the created test job on the pipeline details page.  
![086]

***※	Creating of Test Job can only be done by the administrator and pipeline contributors with creation authority***

###### <div id='3-2-2-4-3-2'/> 3.2.2.4.3.2. Check/ Modify Test Job Configuration
1.	Click the “configuration” icon of the created test job.  
![087]
2.	Go to the configuration details page and look up the configuration information saved when creating.  
![087-2]
3.	When modifying, re-enter the information to modify in each input form and click the "Save" button.  
![088]
4.	Go to the configuration detail page and check the modified information.  
![089]

***※	All pipeline Contributors can check the Test job configuration. However, modifications can only be made by administrators and pipeline Contributors with creation authority.***

###### <div id='3-2-2-4-3-3'/> 3.2.2.4.3.3.	Run Test Job
1.	Click the “Run” icon of Test Job from the Pipeline details page.  
![090]
2.	Once it starts running, the bar turns blue and blinks. (View logs in real-time by clicking the "log/history" icon while running.)  
![091]
3. Once the execution is done, the bar turns green and the Build status changes into Build(Completed).

***※	Test Job execution is only permitted to administrators and pipeline Contributors with creation and execution authority.***

###### <div id='3-2-2-4-3-4'/> 3.2.2.4.3.4.	Stop Test Job
1.	Click the "Stop" icon when you want to stop or cancel a running build Job.  
![092]
2.	The stopped Test Job's bar turns orange.  
![093]

***※	Stopping of Test Job is only permitted to administrators and pipeline Contributors with creation and execution authority.***

###### <div id='3-2-2-4-3-5'/> 3.2.2.4.3.5.	Test Job Log/ History
1.	Logs can be seen in real-time by clicking the "log/history" icon when the Test job is running.  
![094]
2.	Go to the log check page. Check the logs in real-time.  
![095]
3.	Confirm that the Test job execution is complete, and verify the history.  
![096]
4.	For “Run”, “Cancel”, “Configuration”, and “List” buttons, check 3.2.2.4.2.5. Build Job log/history.

***※	The build Job log/history is visible to the administrator and all pipeline Contributors, but the Run and Stop buttons can only be seen by Contributors with creation and execution authority.***


###### <div id='3-2-2-4-3-6'/> 3.2.2.4.3.6.	Test Job Quality Issue Results
1.	Click the Log/History "Quality Issue Results" button of the test Job to go to the Quality Management Dashboard that manages the error resolution status, level of error, and activation status of the source code performed.  
![097]
2.	Refer to 3.2.4 for quality control dashboards.

***※	Test Job quality Issue Results can be checked by all pipeline contributors and administrators.***

###### <div id='3-2-2-4-3-7'/> 3.2.2.4.3.7.	Add Test Job
1.	click the "Add" button on the Test Job on the Pipeline Details page.  
![098]
2.	Refer to 3.2.2.4.2.7. Add Build Job for the next procedures.

***※	Adding a Test job is only possible for administrators and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-3-8'/> 3.2.2.4.3.8.	Duplicate Test Job
1.	On the Pipeline Details page, click the "Duplicate" button on the Test Job.  
![099]
2.	Check the duplicated Test Job.  
![100]

***※	Test Job Duplication can only be done by the administrator and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-3-8'/> 3.2.2.4.3.9.	Delete Test Job
1.	Click the “Delete” button of the Test job from the pipeline details page.  
![101]
2.	Check if the Test Job has been deleted.  
![102]

***※	Test Job deletion can only be done by the administrator and pipeline contributors with creation authority.***


##### <div id='3-2-2-4-4'/> 3.2.2.4.4.	Deployment Job
###### <div id='3.2.2.4.4.1'/> 3.2.2.4.4.1.	Create Deployment Job
1. Click the “Add” button from Job.<br>  
![103]
2.	Go to the configuration page and select the job type to Deploy, and the Cloud Foundry information that has been saved during Pipeline management. (Procedures for bringing Cloud Foundry information have to be done first. Refer to 3.2.3.1. Cloud Foundry information management). Select whether to use MANIFEST or not. then enter input type and job trigger.  
![104]
3.	Click the “Save” button and check the newly created deployment Job from the pipeline details page.  
![105]

***※	Creating a Deployment Job can only be done by the administrator and authorized pipeline contributors.***

###### <div id='3-2-2-4-4-2'/> 3.2.2.4.4.2.	Check/ Modify Deployment Job Configuration
1.	Click the “Configuration” icon of the created Deployment Job.  
![106]
2.	Go to the configuration detail page and inquire the configuration information stored at the time of creation.
![104]
3.	To modify, re-enter the inputs and click “the Save” button.  
![107]
4.	Check the modified information on the configuration details page.

***※	 All pipeline contributors can check out the Deployment Job configurations. However, modifying is only permitted to the administrators and the contributors with the permission to create.***

###### <div id='3-2-2-4-4-3'/> 3.2.2.4.4.3.	Run Deployment Job
1.	Click the “Run” icon of Deployment Job from the pipeline details page.  
![108]
2.	Once it starts running, the bar turns blue and blinks. (View logs in real-time by clicking the "log/history" icon while running.)  
![109]
3. Once the execution is done, the bar turns green and the Build status changes into Build(Completed).  
![110]

***※	Deployment Job execution is only permitted to administrators and pipeline Contributors with creation and execution authority.***

###### <div id='3-2-2-4-4-4'/> 3.2.2.4.4.4.	Stop Deployment Job
1.	Click the "Stop" icon when you want to stop or cancel a running deployment Job.  
![111]
2.	The stopped Deployment Job's bar turns orange.  
![112]

***※	Stopping of Deployment Job is only permitted to administrators and pipeline Contributors with creation and execution authority.***

###### <div id='3-2-2-4-4-5'/> 3.2.2.4.4.5.	Deployment Job Log/History
1.	Logs can be seen in real-time by clicking the "log/history" icon when the deployment job is running.  
![113]
2.	Go to the log check page to check the logs in real-time.  
![114]
3.	Confirm that the deployment job execution is complete, and verify the history.  
![115]
4.	As a result of creating and deploying a Cloud Foundry account with the K-PaaS AP portal, you can see that an application called 'testtest' was deployed in the application portion of the space in the K-PaaS AP portal dashboard.  
![116]

***※	The deployment Job log/history is visible to the administrator and all pipeline Contributors, but the Run and Stop buttons can only be seen by Contributors with creation and execution authority.***

###### <div id='3-2-2-4-4-6'/> 3.2.2.4.4.6.	Deployment Job Rollback to Current Job
1.	Click the “Rollback to Current Job” button on Deployment Job on the Log/History page.  
![117]
2.	A window appears to roll back to the current task and allows you to modify Cloud Foundry information and organizational/space input values.
As an example, set the application name as ‘test-hrjin’ and click the “Rollback” button.  
![118]
3.	Progress Rollback.  
![119]
4.	After the rollback, an application named 'test-hrjin' was successfully deployed at the K-PaaS AP Portal.  
![120]

***※	Deployment Job Rollback to Current Job is permitted only to administrators and contributors with creation and execution authorities.***

###### <div id='3-2-2-4-4-7'/> 3.2.2.4.4.7.	Add Deployment Job
1.	Click the “Add” button of the deployment job from the Pipeline details page.  
![121]
2.	Refer to 3.2.2.4.2.7. Add Build Job and 3.2.2.4.3.7. Add Test Job for the next procedures.

***※	Adding of Deployment Job is permitted to Administrators and contributors with creation authority only.***

###### <div id='3-2-2-4-4-8'/> 3.2.2.4.4.8.	Duplicate Deployment Job
1.	Click the “Duplicate” button of Deployment Job from the pipeline details page.  
![122]
2.	Check the duplicated Deployment Job.  
![123]

***※	Deployment Job Duplication is permitted only to Administrators and Contributors with creation authority.***

###### <div id='3-2-2-4-4-9'/> 3.2.2.4.4.9.	Delete Deployment Job
1.	Click the “Delete” button of Deployment Job from the pipeline details page.  
![124]
2.	Check the deleted deployment Job.  
![125]

***※	Deletion of Deployment Job is permitted to Administrator and Contributors with creation authority only.***

##### <div id='3-2-2-4-5'/> 3.2.2.4.5. Job Sorting
1.	From the pipeline details page, click the “Job sorting” icon for each Job.  
![126]
2.	A list of the remaining Job numbers that can be sorted within the current workgroup appears in the drop-down menu.  
![127]
3.	Click the number of the Job to re-arrange and they will interchange places.  
![128]

***※	Job Sorting is permitted to Administrators and Contributors with creation authority only.***

##### <div id='3-2-2-4-6'/> 3.2.2.4.6. Add New Job Group
1.	Click the “Add New Job Group” button from the pipeline details page.  
![129]
2.	Go to the Job configuration details page.  
![130]
3.	Create a new Job and go back to the pipeline details page. A dotted line will be created below the previous Job group and the newly created Job is separated.  
![131]

***※	Adding of New Job Group is permitted only to Administrators and Contributors with creation authority only.***

### <div id='3-2-3'/> 3.2.3. Pipeline Management
This chapter describes the process of linking the Cloud Foundry target URL to deploy the Job by registering Cloud Foundry information.
#### <div id='3-2-3-1'/> 3.2.3.1. Cloud Foundry Information Management
###### <div id='3-2-3-1-1'/> 3.2.3.1.1. Cloud Foundry Account Registration
1. Click the “Register Cloud Foundry Account” button from the top right of the Cloud Foundry Information Management Dashboard.  
![132]
2. Go to Cloud Foundry Account Registering Page.  
![133]
3. After entering the account name, enter the ID and password required for the Cloud Foundry login.  
![134]
4. Click the "URL Management" button on the URL and when the pop-up window appears, click the "URL Register" button to register the Cloud Foundry account information.<br>  
![135]
5. Enter Cloud Foundry API Name(ex. API), and Job deployment account to use as Cloud Foundry target API URL(ex. https://api.115.68.46.186.nip.io) and click “Save URL”.  
![136]
6. Verify the registered URL.<br>  
![137]
7. Go back to the account registering page's registered URL and input the remaining values. Click “Register” to complete the process.  
![138]
8. Verify if the Cloud Foundry account information was successfully registered at Cloud Foundry Information Management Dashboard.  
![139]

###### <div id='3-2-3-1-2'/> 3.2.3.1.2. Cloud Foundry Account Modification
1.	Select the account to modify from the Cloud Foundry information management dashboard.  
![140]
2.	Input the values to modify and click the “Modify” button.  
![141]
3.	Go back to the account details page through the dashboard and check if the modification was completely done.

### <div id='3-2-4'/> 3.2.4. Quality Management
This chapter describes quality issues and coding rules, quality profiles, and quality gates in relation to source codes inspected through the test Job.
#### <div id='3-2-4-1'/> 3.2.4.1. Quality Issue
1.	Go to Quality Issue Dashboard by clicking Quality Issue from the Quality Management Menu.  
![142]
2.	Click the “Rule Details” button.  
![143]
3.	Click the “List” button at the top right to proceed to the quality issue dashboard with the Job test list.

#### <div id='3-2-4-2'/> 3.2.4.2. Coding Rules
1.	On the Quality Management menu, click Coding Rules to navigate to the Coding Rules dashboard.  
![144]
2.	Click the "Add to Profile" button, set the issue level in the Add Profile pop-up window, and click the "Add" button.  
![145]
3. In the lower-left corner of the Quality Profile menu, click Default^Default-QualityProfile selected in the previous step to verify that the coding rule has been added.<br>  
![146]
4.	Click the "Remove from Profile" button.  
![147]
5.	Confirm if the coding rule has been removed from the quality profile.  
![148]

#### <div id='3-2-4-3'/> 3.2.4.3. Quality Profile
##### <div id='3-2-4-3-1'/> 3.2.4.3.1. Create Quality Profile
1.	From the quality management menu, click a quality profile and proceed to the quality profile dashboard.  
![149]
2.	Click the “create” button at the top right.  
![150]
3.	Enter the quality profile name and select the development language then click the “Create” button.  
![151]
4.	Check the created quality profile.  
![152]

##### <div id='3-2-4-3-2'/> 3.2.4.3.2. Duplicate Quality Profile
1.	Click the “Duplicate” button from the quality profile dashboard.  
![153]
2.	Enter the quality profile to duplicate then click the “Duplicate” button.  
![154]
3.	Check the duplicated quality profile.  
![155]

##### <div id='3-2-4-3-3'/> 3.2.4.3.3. Modify Quality Profile
1.	Click the “Modify” button from the quality profile dashboard.  
![156]
2.	Modify the quality profile name from the pop-up window and click the “Modify” button.  
![157]
3. 	Check the modified quality profile name.  
![158]

##### <div id='3-2-4-3-4'/> 3.2.4.3.4. Connect Quality Profile Project
1.	Check the connected project list from the quality profile dashboard. (The first picture shows the quality profile set to Default-Quality Profile when retrieving the test Job configuration. Therefore, the newly created quality profile example [QualityProfile_hrjin] in the second picture does not show any connected projects.)  
![159]
2.	When there is no project connected, click the “Not Connected” tab.  
![160]
3.	Select a test Job project to connect with the corresponding quality profile. Multiple project connections are possible per quality profile.  
![161]
##### <div id='3-2-4-3-5'/> 3.2.4.3.5. Delete Quality Profile
1.	Click the “Delete” button from the quality profile dashboard.  
![162]
2.	Check the deleted quality profile.  
![163]


#### <div id='3-2-4-4'/> 3.2.4.4. Quality Gate
##### <div id='3-2-4-4-1'/> 3.2.4.4.1. Create Quality Gate
1.	From the quality management menu, click quality gate and proceed to the quality gate dashboard.  
![164]
2.	Click the “Create” button at the top right.  
![165]
3.	Enter Quality Gate Name and click the “Create” button.  
![166]
4.	Check the created quality gate.  
![167]

##### <div id='3-2-4-4-2'/> 3.2.4.4.2. Duplicate Quality Gate
1.	Click the “Duplicate” button from the quality gate dashboard.  
![168]
2.	Enter the quality gate to duplicate then click the “Duplicate” button.  
![169]
3.	Check the duplicated quality gate.  
![170]

##### <div id='3-2-4-4-3'/> 3.2.4.4.3. Modify Quality Gate
1.	Click the “Modify” button from the quality gate dashboard.  
![171]
2.	Modify the quality gate name from the pop-up window and click the “Modify” button.  
![172]
3.	Check the modified quality gate name.  
![173]

##### <div id='3-2-4-4-4'/> 3.2.4.4.4. Add Quality Gate Conditions
1.	Check the additional conditions that can set the conditions for passing the Job test from the quality gate dashboard. The user can add conditions and set criteria.  
![174]
2.	Based on the criteria, if the test result meets the standard score, it may pass.  
![175]
3.	Refer to ‘Default-QualityGate’ which is the default standard set for the quality gate as of the moment.  
![176]

##### <div id='3-2-4-4-5'/> 3.2.4.4.5. Connect Quality Gate Project
1. Check the list of connected projects from the quality gate dashboard. (The first picture shows the quality gate set to test-QualityGate when retrieving the configuration of the test Job. Therefore, in the second picture, it is visible that the test Job of the test2 pipeline in the connected project.)<br>  
![159]
2. The quality gate, like the quality profile, can connect multiple projects per quality gate.  
![177]

##### <div id='3-2-4-4-6'/> 3.2.4.4.6. Delete Quality Gate
1.	Click the “Delete” button from the quality gate dashboard.  
![178]
2.	Check the deleted quality gate.  
![179]



[002]:./images/pipeline/image002.png
[003]:./images/pipeline/image003.jpg
[004]:./images/pipeline/image004.jpg
[005]:./images/pipeline/image005.png
[006]:./images/pipeline/image006.jpg
[007]:./images/pipeline/image007.jpg
[008]:./images/pipeline/image008.png
[009]:./images/pipeline/image009.png
[010]:./images/pipeline/image010.jpg
[011]:./images/pipeline/image011.png
[012]:./images/pipeline/image012.jpg
[013]:./images/pipeline/image013.png
[014]:./images/pipeline/image014.png
[015]:./images/pipeline/image015.png
[016]:./images/pipeline/image016.jpg
[017]:./images/pipeline/image017.jpg
[018]:./images/pipeline/image018.png
[019]:./images/pipeline/image019.jpg
[020]:./images/pipeline/image020.png
[021]:./images/pipeline/image021.jpg
[022]:./images/pipeline/image022.png
[023]:./images/pipeline/image023.png
[024]:./images/pipeline/image024.png
[025]:./images/pipeline/image025.png
[026]:./images/pipeline/image026.png
[027]:./images/pipeline/image027.png
[028]:./images/pipeline/image028.png
[029]:./images/pipeline/image029.png
[030]:./images/pipeline/image030.png
[031]:./images/pipeline/image031.png
[032]:./images/pipeline/image032.jpg
[033]:./images/pipeline/image033.png
[034]:./images/pipeline/image034.png
[035]:./images/pipeline/image035.png
[036]:./images/pipeline/image036.jpg
[037]:./images/pipeline/image037.png
[038]:./images/pipeline/image038.png
[039]:./images/pipeline/image039.jpg
[040]:./images/pipeline/image040.jpg
[041]:./images/pipeline/image041.png
[042]:./images/pipeline/image042.jpg
[043]:./images/pipeline/image043.png
[044]:./images/pipeline/image044.png
[045]:./images/pipeline/image045.png
[046]:./images/pipeline/image046.png
[047]:./images/pipeline/image047.png
[048]:./images/pipeline/image048.jpg
[049]:./images/pipeline/image049.png
[050]:./images/pipeline/image050.jpg
[051]:./images/pipeline/image051.png
[052]:./images/pipeline/image052.png
[053]:./images/pipeline/image053.png
[053-2]:./images/pipeline/image053(2).png
[054]:./images/pipeline/image054.png
[055]:./images/pipeline/image055.jpg
[056]:./images/pipeline/image056.png
[057]:./images/pipeline/image057.jpg
[058]:./images/pipeline/image058.jpg
[059]:./images/pipeline/image059.png
[060]:./images/pipeline/image060.png
[061]:./images/pipeline/image061.jpg
[062]:./images/pipeline/image062.png
[063]:./images/pipeline/image063.jpg
[064]:./images/pipeline/image064.png
[065]:./images/pipeline/image065.jpg
[066]:./images/pipeline/image066.jpg
[067]:./images/pipeline/image067.png
[068]:./images/pipeline/image068.jpg
[069]:./images/pipeline/image069.png
[070]:./images/pipeline/image070.jpg
[071]:./images/pipeline/image071.jpg
[072]:./images/pipeline/image072.png
[073]:./images/pipeline/image073.png
[074]:./images/pipeline/image074.png
[075]:./images/pipeline/image075.png
[076]:./images/pipeline/image076.png
[077]:./images/pipeline/image077.png
[078]:./images/pipeline/image078.png
[079]:./images/pipeline/image079.jpg
[080]:./images/pipeline/image080.jpg
[081]:./images/pipeline/image081.png
[082]:./images/pipeline/image082.png
[083]:./images/pipeline/image083.jpg
[084]:./images/pipeline/image084.png
[084-2]:./images/pipeline/image084(2).png
[084-3]:./images/pipeline/image084(3).png
[085]:./images/pipeline/image085.jpg
[086]:./images/pipeline/image086.png
[087]:./images/pipeline/image087.png
[087-2]:./images/pipeline/image087(2).png
[088]:./images/pipeline/image088.png
[089]:./images/pipeline/image089.jpg
[090]:./images/pipeline/image090.png
[091]:./images/pipeline/image091.jpg
[092]:./images/pipeline/image092.png
[093]:./images/pipeline/image093.jpg
[094]:./images/pipeline/image094.png
[095]:./images/pipeline/image095.jpg
[096]:./images/pipeline/image096.jpg
[097]:./images/pipeline/image097.jpg
[098]:./images/pipeline/image098.png
[099]:./images/pipeline/image099.png
[100]:./images/pipeline/image100.jpg
[101]:./images/pipeline/image101.png
[102]:./images/pipeline/image102.jpg
[103]:./images/pipeline/image103.png
[104]:./images/pipeline/image104.jpg
[105]:./images/pipeline/image105.png
[106]:./images/pipeline/image106.png
[107]:./images/pipeline/image107.png
[108]:./images/pipeline/image108.png
[109]:./images/pipeline/image109.jpg
[110]:./images/pipeline/image110.jpg
[111]:./images/pipeline/image111.png
[112]:./images/pipeline/image112.jpg
[113]:./images/pipeline/image113.png
[114]:./images/pipeline/image114.jpg
[115]:./images/pipeline/image115.jpg
[116]:./images/pipeline/image116.png
[117]:./images/pipeline/image117.png
[118]:./images/pipeline/image118.png
[119]:./images/pipeline/image119.jpg
[120]:./images/pipeline/image120.png
[121]:./images/pipeline/image121.png
[122]:./images/pipeline/image122.png
[123]:./images/pipeline/image123.jpg
[124]:./images/pipeline/image124.png
[125]:./images/pipeline/image125.jpg
[126]:./images/pipeline/image126.png
[127]:./images/pipeline/image127.png
[128]:./images/pipeline/image128.jpg
[129]:./images/pipeline/image129.png
[130]:./images/pipeline/image130.jpg
[131]:./images/pipeline/image131.png
[132]:./images/pipeline/image132.png
[133]:./images/pipeline/image133.jpg
[134]:./images/pipeline/image134.png
[135]:./images/pipeline/image135.png
[136]:./images/pipeline/image136.png
[137]:./images/pipeline/image137.png
[138]:./images/pipeline/image138.png
[139]:./images/pipeline/image139.png
[140]:./images/pipeline/image140.jpg
[141]:./images/pipeline/image141.png
[142]:./images/pipeline/image142.jpg
[143]:./images/pipeline/image143.jpg
[144]:./images/pipeline/image144.jpg
[145]:./images/pipeline/image145.png
[146]:./images/pipeline/image146.png
[147]:./images/pipeline/image147.png
[148]:./images/pipeline/image148.png
[149]:./images/pipeline/image149.jpg
[150]:./images/pipeline/image150.png
[151]:./images/pipeline/image151.png
[152]:./images/pipeline/image152.png
[153]:./images/pipeline/image153.png
[154]:./images/pipeline/image154.png
[155]:./images/pipeline/image155.png
[156]:./images/pipeline/image156.png
[157]:./images/pipeline/image157.png
[158]:./images/pipeline/image158.png
[159]:./images/pipeline/image159.jpg
[160]:./images/pipeline/image160.png
[161]:./images/pipeline/image161.png
[162]:./images/pipeline/image162.png
[163]:./images/pipeline/image163.png
[164]:./images/pipeline/image164.jpg
[165]:./images/pipeline/image165.png
[166]:./images/pipeline/image166.png
[167]:./images/pipeline/image167.jpg
[168]:./images/pipeline/image168.png
[169]:./images/pipeline/image169.png
[170]:./images/pipeline/image170.png
[171]:./images/pipeline/image171.png
[172]:./images/pipeline/image172.png
[173]:./images/pipeline/image173.png
[174]:./images/pipeline/image174.png
[175]:./images/pipeline/image175.png
[176]:./images/pipeline/image176.jpg
[177]:./images/pipeline/image177.png
[178]:./images/pipeline/image178.png
[179]:./images/pipeline/image179.png

### [Index](https://github.com/K-PaaS/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Pipeline Service 
