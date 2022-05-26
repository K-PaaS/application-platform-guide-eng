### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Logging Service


# Table Of Contents

1. [Document Outline](#1)
	- [1.1. Purpose](#1-1)
	- [1.2. Range](#1-2)
2. [Logging Service Access](#2)
	- [2.1. PaaS-TA User Portal Access](#2-1)
	- [2.2. Logging Service Access](#2-2)
3. [Logging Service User Manual](#3)
	- [3.1. Logging Service User Menu Configuration](#3-1)
	- [3.2. Logging Service User Menu Description](#3-2)
	- [3.2.1. Discover](#3-2-1)
	- [3.2.1.1. Search New](#3-2-1-1)
	- [3.2.1.2. Save Search](#3-2-1-2)
	- [3.2.1.3. Open Search](#3-2-1-3)
	- [3.2.1.4. Share Search](#3-2-1-4)
	- [3.2.1.5. Set Search Conditions](#3-2-1-5)
	- [3.2.2. Visualize](#3-2-2)
	- [3.2.2.1. Create Visualization](#3-2-2-1)
	- [3.2.2.2. Modify Visualization](#3-2-2-2)
	- [3.2.2.3. Share Visualization](#3-2-2-3)
	- [3.2.2.4. Refresh Visualization](#3-2-2-4)
	- [3.2.2.5. Visualization Time Range Setting](#3-2-2-5)
	- [3.2.2.6. DeleteVisualization](#3-2-2-6)
	- [3.2.3. Dashboard](#3-2-3)
	- [3.2.3.1. Create Dashboard](#3-2-3-1)
	- [3.2.3.2. Modify Dashboard](#3-2-3-2)
	- [3.2.3.3. Share Dashboard](#3-2-3-3)
	- [3.2.3.4. Dashboard Options and Time Range](#3-2-3-4)
	- [3.2.3.5. Delete Dashboard](#3-2-3-5)
	- [3.2.4. Management](#3-2-4)
	- [3.2.4.1. Check Saved Objects](#3-2-4-1)
	- [3.2.4.2. Modify Saved Objects](#3-2-4-2)
	- [3.2.4.3. Saved Objects Export](#3-2-4-3)
	- [3.2.4.4. Saved Objects Import](#3-2-4-4)
	- [3.2.4.5. Delete Saved Objects](#3-2-4-5)

---

# <div id='1'/> 1. Document Outline
## <div id='1-1'/> 1.1 Purpose
This document describes how to use the Logging service.

## <div id='1-2'/> 1.2 Range
This document is written about how to use the Logging service based on a Windows environment.

# <div id='2'/> 2. Logging Service Access

## <div id='2-1'/> 2.1 PaaS-TA User Portal Access
- Click “Login” button from the accessed PaaS-TA User Portal.

![001]

- Enter User ID and Password and click “SIGN IN” button and log in to PaaS-TA User Portal.
![002]

- Click "Application Name" after logging in and go to the application details page.
![003]

## <div id='2-2'/> 2.2 Logging Service Access

- Click "Log" Menu from the PaaS-TA Portal Application Details page.
![004]

- Click "Log Service" from the Application Log page.
![005]

- Check the access to PaaS-TA Logging Service.
![006]

# <div id='3'/> 3. Logging Service User Manual
This chapter describes the menu configuration and screen description of the logging service.

## <div id='3-1'/> 3.1 Logging Service User Menu Configuration

Logging Service Menu is configured as shown below.

Menu | Description
:--- | :---
Discover | Retrieving and filtering log data
Visulize | Configuring and managing visualizations of log data
Dashboard | A collection of visualizations that users can arrange and share
Management | Manage Stored Objects

## <div id='3-2'/> 3.2 Logging Service Menu Description
This chapter describes the 4 menus of the Logging service.

### <div id='3-2-1'/> 3.2.1. Discover
This chapter describes the search and filtering of log data, and the storing and sharing of searches.

Click "Discover" on the left side.
![007]

#### <div id='3-2-1-1'/> 3.2.1.1. Search New
Click "New" from "Discover" menu.
![008]

#### <div id='3-2-1-2'/> 3.2.1.2. Save Search
Click "Save" from "Discover" menu.
![009]

Click "Save" after entering the search word.
![010]

#### <div id='3-2-1-3'/> 3.2.1.3. Open Search
Click "Open" from "Discover" menu.
![011]

Click "Search Object Name" from the search list.
![012]

#### <div id='3-2-1-4'/> 3.2.1.4. Share Search
Click "Share" from "Discover" menu.
![013]

#### <div id='3-2-1-5'/> 3.2.1.5. Set Search Conditions
- Time Range Setting  
In the "Discover" menu, click the ① area at the top to select the desired condition of Time Range.
![014]

- Query Setting  
In the "Discover" menu, click the ① area at the top to select the desired condition of query.
![015]

- Fields Setting  
Select the field in the Available Fields list from the area ① of the "Discover" menu. (②) 하여 조회 화면(③)에 표시되는 Field를 설정한다.
![016]
![017]

- Unset Fields  
"Discover" 메뉴의 ① 의 영역에서 Selected Fields 목록의 Field를 선택(②)하여 조회 설정을 해제 한다.
![018]

### <div id='3-2-2'/> 3.2.2. Visualize
본 장에서는 로그 데이터의 시각화 구성 및 관리에 대해 기술한다.

#### <div id='3-2-2-1'/> 3.2.2.1. Create Visualization
좌측의 "Visualize" 메뉴를 클릭한다.
![019]

"Visualize" 메뉴에서 "+" 또는 "+ Create a visualization" 버튼을 클릭한다.
![020]

Select visualization type 목록에서 type을 클릭한다.
![021]

색인 패턴을 클릭(①)하거나 저장된 검색 목록에서 "검색 명"을 클릭(②)한다.
![022]

데이터 및 옵션(①)을 편집 하여 시각화를 구성한다.
![023]

상단의 "Save"를 클릭한다.
![024]

시각화 명을 입력한 후, "Save" 버튼을 클릭한다.
![025]

#### <div id='3-2-2-2'/> 3.2.2.2. Modify Visualization
좌측의 "Visualize" 메뉴를 클릭한다.
![026]

시각화 목록에서 "시각화 객체 명"을 클릭한다.
![027]

데이터 및 옵션(①)을 수정 후, "Save"를 클릭한다.
![028]
![029]

#### <div id='3-2-2-3'/> 3.2.2.3. Share Visualization
좌측의 "Visualize" 메뉴를 클릭한다.
![026]

시각화 목록에서 "시각화 객체 명"을 클릭한다.
![027]

상단의 "Share"를 클릭한다.
![030]

#### <div id='3-2-2-4'/> 3.2.2.4. Refresh Visualization
좌측의 "Visualize" 메뉴를 클릭한다.
![026]

시각화 목록에서 "시각화 객체 명"을 클릭한다.
![027]

상단의 "Refresh"를 클릭한다.
![031]

#### <div id='3-2-2-5'/> 3.2.2.5. visualization Time Range 설정
좌측의 "Visualize" 메뉴를 클릭한다.
![026]

시각화 목록에서 "시각화 객체 명"을 클릭한다.
![027]

상단의 ① 영역을 클릭하여, Time Range에서 원하는 조건을 선택한다.
![032]

#### <div id='3-2-2-6'/> 3.2.2.6. visualization 삭제
좌측의 "Visualize" 메뉴를 클릭한다.
![026]

시각화 목록에서 시각화 객체를 선택(①)한 후, ② 아이콘을 클릭한다.
![033]

### <div id='3-2-3'/> 3.2.3. Dashboard
본 장에서는 저장된 검색 객체와 저장된 시각화 객체를 이용한 대시보드의 구성 및 관리에 대해 기술한다.

#### <div id='3-2-3-1'/> 3.2.3.1. Dashboard 생성
좌측의 "Dashboard" 메뉴를 클릭한다.
![034]

"+" 또는 "+ Create a dashboard" 버튼을 클릭한다.
![035]

상단의 "Add"(①) 또는 "Add" 버튼(②)을 클릭한다.
![036]

Add Panels의 "Visualization" / "Saved Search" 을 클릭하여 저장된 객체 목록을 조회한다.
![037]

조회된 저장된 객체 목록에서 "객체 명"을 클릭(①)하여 대시보드를 구성한다.
![038]

상단의 "Save"를 클릭한다.
![039]

대시보드 명을 입력한 후, "Save" 버튼을 클릭한다.
![040]

#### <div id='3-2-3-2'/> 3.2.3.2. Dashboard 수정
좌측의 "Dashboard" 메뉴를 클릭한다.
![041]

대시보드 목록에서 "대시보드 명"을 클릭한다.
![042]

대시보드를 수정 한 후 상단의 "Save"를 클릭한다.
![043]

#### <div id='3-2-3-3'/> 3.2.3.3. Dashboard 공유
좌측의 "Dashboard" 메뉴를 클릭한다.
![041]

대시보드 목록에서 "대시보드 명"을 클릭한다.
![042]

상단의 "Share"를 클릭한다.
![044]

#### <div id='3-2-3-4'/> 3.2.3.4. Dashboard 옵션 및 Time Range
좌측의 "Dashboard" 메뉴를 클릭한다.
![041]

대시보드 목록에서 "대시보드 명"을 클릭한다.
![042]

- 옵션 설정
상단의 "Options"를 클릭하고, "Use dark theme"를 선택(①)하여 테마 적용을 변경한다.
![045]
![046]

- Time Range 설정
상단의 ① 영역을 클릭하여, Time Range 영역에서 원하는 조건을 선택한다.
![047]

#### <div id='3-2-3-5'/> 3.2.3.5. Dashboard 삭제
좌측의 "Dashboard" 메뉴를 클릭한다.
![041]

대시보드 목록에서 대시보드 객체를 선택(①)한 후, ② 아이콘을 클릭한다.
![048]

### <div id='3-2-4'/> 3.2.4. Management
본 장에서는 저장된 객체 관리에 대해 기술한다.

좌측의 "Management" 메뉴를 클릭하고, "Saved Objects"를 클릭한다.
![049]
#### <div id='3-2-4-1'/> 3.2.4.1. Saved Objects 조회
"Dashboards" / "Searches" / "Visualization" 을 클릭하여 각 객체 목록을 조회한다.
![050]

#### <div id='3-2-4-2'/> 3.2.4.2. Saved Objects 수정
"Dashboards" / "Searches" / "Visualization" 의 객체 목록에서 "객체 명"(①)을 클릭한다.
![051]

객체 상세 페이지에서 내용을 수정한 후, 하단의 "Save dashboard Object" 버튼을 클릭한다.
![052]

#### <div id='3-2-4-3'/> 3.2.4.3. Saved Objects Export
- 선택 Export
"Dashboards" / "Searches" / "Visualization" 의 객체 목록에서 객체를 선택(①)한 후, "Export" 버튼을 클릭한다.
![053]

- 전체 Export
"Export Everything" 버튼을 클릭한다.
![054]

#### <div id='3-2-4-4'/> 3.2.4.4. Saved Objects Import
"Import" 버튼을 클릭한다.
![055]

디렉토리에서 객체 파일을 선택한 후, "열기" 버튼을 클릭한다.
![056]

#### <div id='3-2-4-5'/> 3.2.4.5. Saved Objects 삭제
- 객체 목록에서 삭제
"Dashboards" / "Searches" / "Visualization" 의 객체 목록에서 객체를 선택(①)한 후, ② 아이콘을 클릭한다.
![057]

- 객체 상세 페이지에서 삭제
"Dashboards" / "Searches" / "Visualization" 의 객체 목록에서 "객체 명"(①)을 클릭한다.
![058]

객체 상세 페이지에서 상단의 "Delete dashboard/search/visualization" 버튼(①)을 클릭한다.
![059]



[001]:./images/logging-service/image001.png
[002]:./images/logging-service/image002.png
[003]:./images/logging-service/image003.png
[004]:./images/logging-service/image004.png
[005]:./images/logging-service/image005.png
[006]:./images/logging-service/image006.png
[007]:./images/logging-service/image007.png
[008]:./images/logging-service/image008.png
[009]:./images/logging-service/image009.png
[010]:./images/logging-service/image010.png
[011]:./images/logging-service/image011.png
[012]:./images/logging-service/image012.png
[013]:./images/logging-service/image013.png
[014]:./images/logging-service/image014.png
[015]:./images/logging-service/image015.png
[016]:./images/logging-service/image016.png
[017]:./images/logging-service/image017.png
[018]:./images/logging-service/image018.png
[019]:./images/logging-service/image019.png
[020]:./images/logging-service/image020.png
[021]:./images/logging-service/image021.png
[022]:./images/logging-service/image022.png
[023]:./images/logging-service/image023.png
[024]:./images/logging-service/image024.png
[025]:./images/logging-service/image025.png
[026]:./images/logging-service/image026.png
[027]:./images/logging-service/image027.png
[028]:./images/logging-service/image028.png
[029]:./images/logging-service/image029.png
[030]:./images/logging-service/image030.png
[031]:./images/logging-service/image031.png
[032]:./images/logging-service/image032.png
[033]:./images/logging-service/image033.png
[034]:./images/logging-service/image034.png
[035]:./images/logging-service/image035.png
[036]:./images/logging-service/image036.png
[037]:./images/logging-service/image037.png
[038]:./images/logging-service/image038.png
[039]:./images/logging-service/image039.png
[040]:./images/logging-service/image040.png
[041]:./images/logging-service/image041.png
[042]:./images/logging-service/image042.png
[043]:./images/logging-service/image043.png
[044]:./images/logging-service/image044.png
[045]:./images/logging-service/image045.png
[046]:./images/logging-service/image046.png
[047]:./images/logging-service/image047.png
[048]:./images/logging-service/image048.png
[049]:./images/logging-service/image049.png
[050]:./images/logging-service/image050.png
[051]:./images/logging-service/image051.png
[052]:./images/logging-service/image052.png
[053]:./images/logging-service/image053.png
[054]:./images/logging-service/image054.png
[055]:./images/logging-service/image055.png
[056]:./images/logging-service/image056.png
[057]:./images/logging-service/image057.png
[058]:./images/logging-service/image058.png
[059]:./images/logging-service/image059.png


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Logging Service
