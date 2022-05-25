### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Pipeline Service


# [PaaS-TA Pipeline Deployment Service User Guide]

## Table of Contents
1. [Document Outline](#1)
	* [1.1. Purpose](#1-1)
	* [1.2. Range](#1-2)
2. [Pipeline Deployment Service Registration ](#2)
	* [2.1. PaaS-TA User Portal Portal Access](#2-1)
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
	* [3.2.2.4.3.6.    Add Test Job](#3-2-2-4-3-6)
	* [3.2.2.4.3.7.    Test Job Quality Issue Results](#3-2-2-4-3-7)
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
	* [3.2.2.4.5. Job Sorting Icon](#3-2-2-4-5)
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

## <div id='2-1'/> 2.1 PaaS-TA User Portal Access
1. Click log-in from the PaaS-Ta User Portal.  
![002]

2. Login to the Portal by entering User ID and Password then click “LOGIN”.  
![003]

3. Go to Catalog page after logging in.  
![004]


## <div id='2-2'/> 2.2 Pipeline Deployment Service Registration
1. Select Pipelin Deployment Service.  
![005]

2. Select a space to apply for. In the ORG that already uses pipeline deployment, an error will occur as shown in the picture bellow.  
![006]

3. Enter Service Name. If the name of the requested pipeline deployment already exists, an error will occur as shown in the picture bellow..  
![007]

4. After applying, go to service tab of the space page to check if the pipeline service has been successfully created.  
![008]

## <div id='2-3'/> 2.3 Pipeline Deployment Access
1. Click “Dashboard” button of the applied pipeline deployment from the PaaS-TA Portals Space page and access.  
![009]

2. Check the connection of pipeline deployment.  
![010]

3. At this, ***The user who first created the pipeline deployment becomes the administrator.*** Go to user dashboard by clicking user management menu at the right top .

***※ Users who connect for the first time will not be able to access without administrator approval.***


# <div id='3'/> 3. Pipeline Deployment User Manual
This chapter describes the menu configuration and screen description of the distribution pipeline.

## <div id='3-1'/> 3.1 Pipeline Deployment User's Menu Configuration
The Pipeline deployment service consists of the following parts: Pipeline search and management, Cloud Foundry account  information management, source code quality management, and user managment that administrates the users accoring to the given authorities.

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
		<td>Manages coding rules that serve as standard for analyzing the quality of source code</td>
	</tr>
	<tr>
		<td>Quality Gate</td>
		<td>Manages criteria for quality analysis of source codes that exceed or fall short of a set quality reference value</td>
	</tr>
</table>


## <div id='3-2'/> 3.2 Pipeline Deployment User Menu Setting
This chapter describes about the 4 menus in the pipeline deployment.

### <div id='3-2-1'/> 3.2.1. User Management
The user management menu is visible only to the administrators. This chapter describes about the authorization management and information check, and manages users of pipeline deployment service.

#### <div id='3-2-1-1'/> 3.2.1.1. User Dashboard

##### <div id='3-2-1-1-1'/> 3.2.1.1.1. Check User List Search
1. CLick “User Management” button at the top right menu.  
![011]

2. Go to user dashboard.  
![012]

3. There are two conditions that can be searched in the user dashboard list: a search with a user ID as a search term and a search whose type is an administrator/user. Click "Enter" or "Magnifying class" button to search.  
![013]  
![014]

##### <div id='3-2-1-1-2'/> 3.2.1.1.2. User Detail Check/ Modify
1. Select ID from the user dashboard and proceed to user details check/ modify page.  
![015]

2. Check the detailed information of the user.  
![016]

3. When you want to modify the information, enter a new input value. Authority can be selected between administrator/user.  
![017]

4. After entering the new value, click “Modify” button.  
![018]

5. Check the modified information.    
![019]


### <div id='3-2-2'/> 3.2.2. Pipeline
This chapter describes the two methods of how to manage pipeline.

***※ Users can only see Pipeline and cannot create, modify, check, and delete until when they have contributors authorization.***

#### <div id='3-2-2-1'/> 3.2.2.1. My Pipeline
1.	No matter which page you are on, click the My Pipeline button on the drop down menu to go to the dashboard.  
![020]  
![021]
2.	Click the name of the pipeline from My Pipeline's list and proceed to the selected pipeline's detailed page.  
![022]  
![023]

#### <div id='3-2-2-2'/> 3.2.2.2. Create New Pipeline
##### <div id='3-2-2-2-1'/> 3.2.2.2.1. Create New Pipeline(1)
1. Click “Pipeline List” from the menu on top to create a new Pipeline just by entering the name.  
![024]
2. After clicking register button, the added pipeline can be checked at the dashboard.  
![025]
3. The registered Pipeline can also be checked from the Drop down Menu.  
![026]
##### <div id='3-2-2-2-2'/> 3.2.2.2.2. Create New Pipeline(2)
1.	Click “Create New” button at the top right.
***※  “Create New” button is not activated to the Users.***  
![027]
2.	Go to Create New Pipeline Page.    
![028]
3.	Pipeline name is REQUIRED. Make sure to enter the Pipeline name and click “Create” button.    
![029]
4.	Go to Pipeline dashboard page and check the created pipelines name.    
![030]

##### <div id='3-2-2-2-3'/> 3.2.2.2.3. Create New Pipeline(3)
***※ Users does not have authority as a contributors of the pipeline and cannot proceed to the pipeline's detaild page.***
1. Click “Create New” button at the top right.    
![031]
2. Go to Create New Pipeline Page.    
![032]
3. Refer to 3.2.2.2.2. Create New Pipeline(2) for the next procedures.

#### <div id='3-2-2-3'/> 3.2.2.3. Pipeline Dashboard
##### <div id='3-2-2-3-1'/> 3.2.2.3.1. Pipeline List Search Check
1. A desired pipeline can be searched by entering the Pipeline name. Or the Pipeline list could be arranged by the sequence of created dates, updated dates, and alphabetical order..  
![033]
2.	Click “ENTER” or “Magnifying glass” image button to search.  
![034]
3.	If you select a search type filter for both a list inquire/search list inquire, it will be reflected immediately.  
![035]

##### <div id='3-2-2-3-2'/> 3.2.2.3.2. Pipeline Detail Information Check
1.	Go to Pipeline details page by clicking the pipeline's name from the pipeline dashboard.  
![036]
2.	Click “View/Modify information” button at the top right of pipeline details page.  
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
***※	Administrators are pipeline creators, so they are contributors with creation authority by default..***
1.	Select Contributors tab from the pipeline details page.  
![043]
2.	Click “Add Contributor” button at the top right.  
![044]
3.	Go to the Add Contributors page to search for users who are currently invited to the pipeline deployment and select. Select one of the authority between: view, create, or execute and click the "Add" button.  
![045]
4.	Check the added contributor at the Contributors tab.  
![046]

###### <div id='3-2-2-4-1-2'/> 3.2.2.4.1.2. Check Contributors List Search
1.	The condition that can be searched in the contributor list is one contributor ID.  
![047]
2.	After entering the search word, click “ENTER” or “Magnifying glass” image button to check.  
![048]

###### <div id='3-2-2-4-1-3'/> 3.2.2.4.1.3.	Check/ Modify Contributors Detail Information
1.	Select the ID of the contributor to check from the contributor list.  
![049]
2.	Go to the check/modify page for contributor details and check the contributors details.  
![050]
3.	Select the authority to modify and click "Modify" button.  
![051]
4.	Check the modified authority of the contributor from the contributors list.  
![052]


###### <div id='3-2-2-4-1-4'/> 3.2.2.4.1.4.	Delete Contributor
1.	Select a contributor to delete from the list.  
![053]
2.	Go to detailed information check/modify page of the selected contributor.  
![053-2]
3.	Click “Delete Contributor” button.  
![054]
4.	Check from the list if the Contributor is deleted.  
![055]


##### <div id='3-2-2-4-2'/> 3.2.2.4.2. Build Job
###### <div id='3-2-2-4-2-1'/> 3.2.2.4.2.1. Create Build Job 
1.	Click ‘click here to add new Job’ button from the pipeline details page.  
![056]
2.	Proceed to configuration details page.  
![057]
3. Select a job type and select the desired repository as the input type. Enter the ID and password of the selected repository, and the path of the repository to click the "Retrieve" button. Select the retrieved Branch and choose the suitable job trigger.<br>  
![058]

4.	Check the newly created Build Job from the Pipeline details page.  
![059]

***※ Creating a Build Job can only be done by administrator and authorized contributors of pipeline.***

###### <div id='3-2-2-4-2-2'/> 3.2.2.4.2.2. Check/ Modify Build Job Configuration
1. Click the “Configuration” icon of the created Build Job.<br>  
![060]
2. Go to the configuration details page and look up the configuration informations saved when creating.  
![061]
3. When modifying, re-enter the information to modify in each input form and click "Save" button.  
![062]
4. Go to the configuration detail page and check the modified information.  
![063]

***※ All pipeline Contributors can check the build job configuration. However, modifications can only be made by administrators and pipeline Contributors with creation authority.***

###### <div id='3-2-2-4-2-3'/> 3.2.2.4.2.3. Run Build Job
1. Click “Run” icon from the Pipeline details page.<br>  
![064]
2. Once it starts running, the bar turns blue and blinks. (View logs in real time by clicking the "log/history" icon while running.)  
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
1.	 Logs can be seen real time by clicking the "log/history" icon when the build job is running.  
![069]
2.	Go to log check page. Check the logs in real time.  
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
2.	Built files will be downloaded(ex. war, jar, zip and etc.).  
![077]

***※	Downloading the build Job log is permitted to administrators and all pipeline Contributors.***

###### <div id='3-2-2-4-2-7'/> 3.2.2.4.2.7.	Add Build Job
1.	click the "Add" button on the Build Job on the Pipeline Details page.  
![078]
2.	Go to the Configuration Details page.  
![079]
3.	Enter the appropriate values in the input form, such as creating a build job, and click the "Save" button.(Job types can be selected aside from build. Such as Test or deployment.)  
![080]
4.	Check the added Build Job.<br>  
![081]
5.	If you choose 'Make a new job group for this Job' for the Job Trigger, the Job will be added into a new group. (This will be dealt in details later in the explanation of 'Add a new Job group'.)

***※	Adding a build job is only possible for administrators and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-2-8'/> 3.2.2.4.2.8.	Duplicate Build Job
1.	On the Pipeline Details page, click the "Duplicate" button on the Build Job.  
![082]
2. Check the duplicated Build Job.  
![083]

***※	Build Job Duplication can only be done by the administrator and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-2-9'/> 3.2.2.4.2.9.	Delete Build Job
1.	Click “Delete” button of Build job from the pipeline details page.  
![084]
2.	Check if the Build Job has been deleted.  
![084-2]

***※	Build Job deletion can only be done by the administrator and pipeline contributors with creation authority.***


##### <div id='3-2-2-4-3'/> 3.2.2.4.3. Test Job
###### <div id='3-2-2-4-3-1'/> 3.2.2.4.3.1. Create Test Job
1.	Click “Add” button from the Job.  
![084-3]
2.	Go to configuration page and select job type as Test then enter the desired quality profile, quality gate, and Job group. Select suitable job trigger.  
![085]
3.	Click “Save” button and check the created test job at the pipeline details page.  
![086]

***※	Creating of Test Job can only be done by the administrator and pipeline contributors with creation authority***

###### <div id='3-2-2-4-3-2'/> 3.2.2.4.3.2. Check/ Modify Test Job Configuration
1.	Click the “configuration” icon of the created test job.  
![087]
2.	Go to the configuration details page and look up the configuration information saved when creating.  
![087-2]
3.	When modifying, re-enter the information to modify in each input form and click "Save" button.  
![088]
4.	Go to the configuration detail page and check the modified information.  
![089]

***※	All pipeline Contributors can check the Test job configuration. However, modifications can only be made by administrators and pipeline Contributors with creation authority.***

###### <div id='3-2-2-4-3-3'/> 3.2.2.4.3.3.	Run Test Job
1.	Click “Run” icon of Test Job from the Pipeline details page.  
![090]
2.	Once it starts running, the bar turns blue and blinks. (View logs in real time by clicking the "log/history" icon while running.)  
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
1.	Logs can be seen real time by clicking the "log/history" icon when the Test job is running.  
![094]
2.	Go to log check page. Check the logs in real time.  
![095]
3.	Confirm that the Test job execution is complete, and verify the history.  
![096]
4.	For “Run”, “Cancel”, “Configuration”, “List” buttons, check 3.2.2.4.2.5. Build Job log/history.

***※	The build Job log/history is visible to the administrator and all pipeline Contributors, but the Run and Stop buttons can only be seen by Contributors with creation and execution authority.***


###### <div id='3-2-2-4-3-6'/> 3.2.2.4.3.6.	Test Job Quality Issue Results
1.	Click the Log/History "Quality Issue Results" button of the test Job to go to the Quality Management Dashboard that manages the error resolution status, level of error, and activation status of the source code performed.  
![097]
2.	Refer to 3.2.4 for quality control dashboards.

***※	Test Job uality Issue Results can be checked by all pipeline contributors and administrators.***

###### <div id='3-2-2-4-3-7'/> 3.2.2.4.3.7.	Add Test Job
1.	click the "Add" button on the Test Job on the Pipeline Details page.  
![098]
2.	Refer to 3.2.2.4.2.7. Add Build Job  for the next procedures.

***※	Adding a Test job is only possible for administrators and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-3-8'/> 3.2.2.4.3.8.	Duplicate Test Job
1.	On the Pipeline Details page, click the "Duplicate" button on the Test Job.  
![099]
2.	Check the duplicated Test Job.  
![100]

***※	Test Job Duplication can only be done by the administrator and pipeline contributors with creation authority.***

###### <div id='3-2-2-4-3-8'/> 3.2.2.4.3.9.	Delete Test Job
1.	Click “Delete” button of Test job from the pipeline details page.  
![101]
2.	Check if the Test Job has been deleted.  
![102]

***※	Test Job deletion can only be done by the administrator and pipeline contributors with creation authority.***


##### <div id='3-2-2-4-4'/> 3.2.2.4.4.	Deployment Job
###### <div id='3.2.2.4.4.1'/> 3.2.2.4.4.1.	Create Deployment Job
1. Click the “Add” button from Job.<br>  
![103]
2.	Go to configuration page and select the job type to Deploy, and the Cloud Foundry information that has been saved during Pipeline management. (Procedures for bringing Cloud Foundry information has to be done first. Refer to 3.2.3.1. Cloud Foundry information management). Select wheter to use MANIFEST or not. then enter input type and job trigger.  
![104]
3.	Click “Save” button and check the newly created deployment Job from the pipeline details page.  
![105]

***※	Creating a Deployment Job can only be done by administrator and authorized pipeline contributors.***

###### <div id='3-2-2-4-4-2'/> 3.2.2.4.4.2.	Check/ Modify Deployment Job Configuration
1.	Click the “Configuration” icon of the created Deployment Job.  
![106]
2.	Go to Configuration details page and check the configured informations saved.  
![104]
3.	To modify, re-enter the inputs and click “Save” button.  
![107]
4.	Check the modified informations at the configuration details page.

***※	 All pipeline contributors can check out the Deployment Job configurations. However, modifying is only permitted to the administrators and the contributors with the permission to create.***

###### <div id='3-2-2-4-4-3'/> 3.2.2.4.4.3.	Run Deployment Job
1.	Click “Run” icon of Deployment Job from the pipeline details page.  
![108]
2.	Once it starts running, the bar turns blue and blinks. (View logs in real time by clicking the "log/history" icon while running.)  
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
1.	Logs can be seen real time by clicking the "log/history" icon when the deployment job is running.  
![113]
2.	Go to log check page to check the logs in real-time.  
![114]
3.	Confirm that the deployment job execution is complete, and verify the history.  
![115]
4.	As a result of creating and deploying a Cloud Foundry account with the PaaS-TA portal, you can see that an application called 'testtest' was deployed in the application portion of the space in the PaaS-TA portal dashboard.  
![116]

***※	The deployment Job log/history is visible to the administrator and all pipeline Contributors, but the Run and Stop buttons can only be seen by Contributors with creation and execution authrity.***

###### <div id='3-2-2-4-4-6'/> 3.2.2.4.4.6.	Deployment Job Rollback to Current Job
1.	Click “Rollback to Current Job” button of Deployment Job at the Log/History page.  
![117]
2.	A window appears to roll back to the current task and allows you to modify Cloud Foundry information and organizational/space input values.
As an example, set the application name as ‘test-hrjin’ and click “Rollback” button.  
![118]
3.	Progress Rollback.  
![119]
4.	After the rollback, an application named 'test-hrjin' was successfully deployed at the PaaS-TA Portal.  
![120]

***※	Deployment Job Rollback to Current Job is permitted only to administrators and contributors with creation and execution authorities.***

###### <div id='3-2-2-4-4-7'/> 3.2.2.4.4.7.	배포 Job 추가
1.	파이프라인 상세페이지에서 테스트 Job의 “추가” 버튼을 클릭한다.  
![121]
2.	그 이후의 과정은 3.2.2.4.2.7. 빌드 Job 추가, 3.2.2.4.3.7. 테스트 Job 추가 항목을 참고한다.

***※	배포 Job 추가는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.***

###### <div id='3-2-2-4-4-8'/> 3.2.2.4.4.8.	배포 Job 복제
1.	파이프라인 상세페이지에서 배포 Job의 “복제” 버튼을 클릭한다.  
![122]
2.	배포 Job 이 복제된 것을 확인한다.  
![123]

***※	배포 Job 복제는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.***

###### <div id='3-2-2-4-4-9'/> 3.2.2.4.4.9.	배포 Job 삭제
1.	파이프라인 상세페이지에서 배포 Job의 “삭제” 버튼을 클릭한다.  
![124]
2.	배포 Job 이 삭제된 것을 확인한다.  
![125]

***※	배포 Job 삭제는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.***

##### <div id='3-2-2-4-5'/> 3.2.2.4.5. Job 작업 정렬
1.	파이프라인 상세페이지에서 각 Job의 “작업 정렬” 아이콘을 클릭한다.  
![126]
2.	현재 작업 그룹 내에서 정렬할 수 있는 나머지 Job의 번호의 목록이 drop down 메뉴로 보인다.  
![127]
3.	그중 정렬하고자 하는 번호를 클릭 시 그 번호의 Job 과 위치가 서로 바뀌게 되며 정렬된다.  
![128]

***※	Job 작업 정렬은 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.***

##### <div id='3-2-2-4-6'/> 3.2.2.4.6. 새 작업 그룹 추가
1.	파이프라인 상세페이지에서 “새 작업 그룹 추가” 버튼을 클릭한다.  
![129]
2.	Job 구성 상세페이지로 이동한다.  
![130]
3.	새로운 Job 한 개를 생성한 뒤 파이프라인 상세페이지로 이동한다. 이전 작업 그룹 아래로 점선이 생기며 새로 생성한 Job이 새로운 그룹 내로 나눠진 것을 확인할 수 있다.  
![131]

***※	Job 새 작업  그룹 추가는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.***

### <div id='3-2-3'/> 3.2.3. 파이프라인 관리
본 장에서는 Cloud Foundry 정보를 등록하여 Job을 배포할 Cloud Foundry target URL을 연동하는 과정에 대하여 기술한다.
#### <div id='3-2-3-1'/> 3.2.3.1. Cloud Foundry 정보 관리
###### <div id='3-2-3-1-1'/> 3.2.3.1.1. Cloud Foundry 계정등록
1. Cloud Foundry 정보 관리 대시보드에서 우측 상단에 “Cloud Foundry 계정 등록” 버튼을 클릭한다.  
![132]
2. Cloud Foundry 계정 등록 페이지로 이동한다.  
![133]
3. 계정 명을 입력한 후 아이디와 비밀번호에는 Cloud Foundry 로그인에 필요한 아이디와 비밀번호를 입력한다.  
![134]
4. URL에는 “URL 관리” 버튼을 클릭한 후 팝업 창이 뜨면 “URL 등록” 버튼을 클릭하여 Cloud Foundry 계정 정보를 등록하도록 한다.<br>  
![135]
5. Cloud Foundry API 명(ex. api)을 입력하고, Job 배포 계정으로 사용하고자 하는 Cloud Foundry target API URL(ex. https://api.115.68.46.186.nip.io) 을 입력한 후 “URL 저장” 버튼을 클릭한다.  
![136]
6. URL이 등록되었음을 확인한다.<br>  
![137]
7. 다시 계정 등록 화면으로 돌아와 등록한 URL을 선택 후 나머지 값을 입력한다. 마지막으로 “등록” 버튼을 클릭한다.  
![138]
8. Cloud Foundry 정보 관리 대시보드에서 Cloud Foundry 계정 정보가 정상적으로 등록되었음을 확인한다.  
![139]

###### <div id='3-2-3-1-2'/> 3.2.3.1.2. Cloud Foundry 계정수정
1.	Cloud Foundry 정보 관리 대시보드에서 수정하고자 하는 계정을 클릭한다.  
![140]
2.	수정할 값들을 입력하고 “수정” 버튼을 클릭한다.  
![141]
3.	다시 대시보드를 통해 계정 상세 페이지로 이동 후 수정되었는지 확인한다.

### <div id='3-2-4'/> 3.2.4. 품질 관리
본 장에서는 테스트 Job을 통해 검사한 소스 코드와 관련하여 품질 이슈와 코딩 규칙, 품질 프로파일, 품질 게이트에 대한 설명을 기술한다.
#### <div id='3-2-4-1'/> 3.2.4.1. 품질 이슈
1.	품질 관리 메뉴에서 품질 이슈를 클릭하여 품질 이슈 대시보드로 이동한다.  
![142]
2.	“규칙상세” 버튼을 클릭한다.  
![143]
3.	오른쪽 상단의 “목록” 버튼을 클릭하면 Job 테스트 목록이 있는 품질 이슈 대시보드로 이동한다.

#### <div id='3-2-4-2'/> 3.2.4.2. 코딩 규칙
1.	품질 관리 메뉴에서 코딩 규칙을 클릭하여 코딩 규칙 대시보드로 이동한다.  
![144]
2.	“프로파일에 추가” 버튼을 클릭한 뒤 프로파일 추가 팝업 창에서 이슈 수준을 정한 후 “추가” 버튼을 클릭한다.  
![145]
3. 왼쪽 하단에 품질 프로파일 메뉴에서 이전 단계에서 선택한 Default^Default-QualityProfle 을 클릭하여 추가한 코딩 규칙이 추가되었는지 확인한다.<br>  
![146]
4.	“프로파일에 제거” 버튼을 클릭한다.  
![147]
5.	제거한 코딩 규칙이 품질 프로파일에서 삭제된 것을 확인한다.  
![148]

#### <div id='3-2-4-3'/> 3.2.4.3. 품질 프로파일
##### <div id='3-2-4-3-1'/> 3.2.4.3.1. 품질 프로파일 생성
1.	품질 관리 메뉴에서 품질 프로파일을 선택하여 품질 프로파일 대시보드로 이동한다.  
![149]
2.	우측 상단의 “생성” 버튼을 클릭한다.  
![150]
3.	품질 프로파일 명을 입력하고, 개발언어를 선택하여 “생성” 버튼을 클릭한다.  
![151]
4.	품질 프로파일이 생성된 것을 확인한다.  
![152]

##### <div id='3-2-4-3-2'/> 3.2.4.3.2. 품질 프로파일 복제
1.	품질 프로파일 대시보드에서 “복제” 버튼을 클릭한다.  
![153]
2.	복제할 품질 프로파일의 이름을 입력하고, “복제” 버튼을 클릭한다.  
![154]
3.	복제된 품질 프로파일을 확인한다.  
![155]

##### <div id='3-2-4-3-3'/> 3.2.4.3.3. 품질 프로파일 수정
1.	품질 프로파일 대시보드에서 “수정” 버튼을 클릭한다.  
![156]
2.	수정할 품질 프로파일 팝업창이 뜨면 품질 프로파일 명을 수정하고 “수정” 버튼을 클릭한다.  
![157]
3. 품질 프로파일 명이 수정되었음을 확인한다.  
![158]

##### <div id='3-2-4-3-4'/> 3.2.4.3.4. 품질 프로파일 프로젝트 연결
1.	품질 프로파일 대시보드에서 연결된 프로젝트 항목을 확인한다. (첫 번째 사진은 테스트 Job 구성 조회 시 품질 프로파일을 Default-QualityProfile 로 설정한 것이다. 그러므로 두 번째 사진에서 예시로 새로 생성한 품질 프로파일[QualityProfile_hrjin]에는 연결된 프로젝트가 보이지 않는다.)  
![159]
2.	연결된 프로젝트가 없을 경우 “미연결” 탭을 클릭한다.  
![160]
3.	해당 품질 프로파일과 연결할 테스트 Job 프로젝트를 선택한다. 품질 프로파일 1개 당 여러 프로젝트 연결이 가능하다.  
![161]
##### <div id='3-2-4-3-5'/> 3.2.4.3.5. 품질 프로파일 삭제
1.	품질 프로파일 대시보드에서 “삭제” 버튼을 클릭한다.  
![162]
2.	품질 프로파일이 삭제되었음을 확인한다.  
![163]


#### <div id='3-2-4-4'/> 3.2.4.4. 품질 게이트
##### <div id='3-2-4-4-1'/> 3.2.4.4.1. 품질 게이트 생성
1.	품질 관리 메뉴에서 품질 게이트를 선택하여 품질 게이트 대시보드로 이동한다.  
![164]
2.	우측 상단의 “생성” 버튼을 클릭한다.  
![165]
3.	품질 게이트 명을 입력하고, “생성” 버튼을 클릭한다.  
![166]
4.	품질 게이트가 생성된 것을 확인한다.  
![167]

##### <div id='3-2-4-4-2'/> 3.2.4.4.2. 품질 게이트 복제
1.	품질 게이트 대시보드에서 “복제” 버튼을 클릭한다.  
![168]
2.	복제할 품질 게이트의 이름을 입력하고, “복제” 버튼을 클릭한다.  
![169]
3.	복제된 품질 게이트를 확인한다.  
![170]

##### <div id='3-2-4-4-3'/> 3.2.4.4.3. 품질 게이트 수정
1.	품질 게이트 대시보드에서 “수정” 버튼을 클릭한다.  
![171]
2.	수정할 품질 게이트 팝업창이 뜨면 품질 게이트 명을 수정하고 “수정” 버튼을 클릭한다.  
![172]
3.	품질 게이트 명이 수정되었음을 확인한다.  
![173]

##### <div id='3-2-4-4-4'/> 3.2.4.4.4. 품질 게이트 조건추가
1.	품질 게이트 대시보드에서 Job 테스트 시 통과 기준이 되는 조건을 설정할 수 있는 조건 추가 부분을 확인한다. 사용자가 직접 조건을 추가하고 기준 설정이 가능하다.  
![174]
2.	조건에 따라 어느 기준 이상/이하가 될 시에 테스트를 통과시키도록 한다.  
![175]
3.	현재 ‘Default-QualityGate’ 가 기본 품질 게이트로 설정되어 있는데 이것을 참고로 한다.  
![176]

##### <div id='3-2-4-4-5'/> 3.2.4.4.5. 품질 게이트 프로젝트 연결
1. 품질 게이트 대시보드에서 연결된 프로젝트 항목을 확인한다. (첫 번째 사진은 테스트 Job 구성 조회 시 품질 게이트를 test-QualityGate 로 설정한 것이다. 그러므로 두번째 사진에서 연결된 프로젝트에 test2 파이프라인의 테스트 Job 이 보인다.)<br>  
![159]
2. 품질 게이트도 품질 프로파일과 마찬가지로 품질 게이트 1개당 여러 프로젝트 연결이 가능하다.  
![177]

##### <div id='3-2-4-4-6'/> 3.2.4.4.6. 품질 게이트 삭제
1.	품질 게이트 대시보드에서 “삭제” 버튼을 클릭한다.  
![178]
2.	품질 게이트가 삭제되었음을 확인한다.  
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

### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Pipeline Service 
