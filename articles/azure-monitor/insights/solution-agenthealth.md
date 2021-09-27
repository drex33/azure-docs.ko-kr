---
title: Azure Monitor의 에이전트 상태 솔루션 | Microsoft Docs
description: 이 솔루션을 사용 하 여 Log Analytics 또는 System Center Operations Manager에 직접 보고 하는 에이전트의 상태를 모니터링 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663443"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Azure Monitor의 에이전트 상태 솔루션
Azure의 에이전트 상태 솔루션은 응답 하지 않고 운영 데이터를 제출 하는 모니터링 에이전트를 이해 하는 데 도움이 됩니다. 여기에는 Azure Monitor의 Log Analytics 작업 영역에 직접 보고 하거나 Azure Monitor에 연결 된 System Center Operations Manager 관리 그룹에 직접 보고 하는 모든 에이전트가 포함 됩니다.

에이전트 상태 솔루션을 사용 하 여 다음을 수행할 수도 있습니다.

* 배포 된 에이전트 수와 지리적으로 분산 된 에이전트의 수를 추적 합니다.
* 다른 쿼리를 수행 하 여 Azure, 다른 클라우드 환경 또는 온-프레미스에 배포 된 에이전트의 배포에 대 한 인식을 유지 합니다.    

## <a name="prerequisites"></a>필수 구성 요소
이 솔루션을 배포 하기 전에 Log Analytics 작업 영역에 보고 하거나 작업 영역과 통합 된 [Operations Manager 관리 그룹](../agents/om-agents.md) 에 보고 하 [Windows 에이전트가](../agents/agent-windows.md) 지원 되는지 확인 합니다.

## <a name="management-packs"></a>관리 팩
Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결되면 다음 관리 팩이 Operations Manager에 설치됩니다. 이러한 관리 팩은이 솔루션을 추가한 후에 직접 연결 된 Windows 컴퓨터에도 설치 됩니다.

* Microsoft System Center Advisor 상태 평가 직접 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor 상태 평가 서버 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

이러한 관리 팩을 사용 하 여 구성 하거나 관리할 필요가 없습니다. 솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../agents/om-agents.md)을 참조하세요.

## <a name="configuration"></a>구성
[솔루션 추가](solutions.md)에 설명 된 프로세스를 사용 하 여 Log Analytics 작업 영역에 에이전트 상태 솔루션을 추가 합니다. 추가 구성은 필요하지 않습니다.

## <a name="supported-agents"></a>지원되는 에이전트
다음 표에서는이 솔루션이 지 원하는 연결 된 원본을 설명 합니다.

| 연결된 원본 | 지원됨 | Description |
| --- | --- | --- |
| Windows 에이전트 | 예 | 하트비트 이벤트는 Windows 에이전트에서 직접 수집됩니다.|
| System Center Operations Manager 관리 그룹 | 예 | 하트 비트 이벤트는 관리 그룹에 보고 하는 에이전트에서 60 초 마다 수집 된 후 Azure Monitor으로 전달 됩니다. Operations Manager 에이전트에서 Azure Monitor로의 직접 연결은 필요하지 않습니다. 하트비트 이벤트 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다.|

## <a name="using-the-solution"></a>솔루션 사용
Log Analytics 작업 영역에 솔루션을 추가 하면 **에이전트 상태** 타일이 대시보드에 추가 됩니다. 이 타일은 총 에이전트 수와 지난 24시간 동안 응답하지 않는 에이전트의 수를 표시합니다.

![대시보드의 에이전트 상태 타일을 보여 주는 스크린샷](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

**에이전트 상태** 타일을 선택 하 여 **에이전트 상태** 대시보드를 엽니다.  대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정 된 시간 범위에 대 한 열의 기준과 일치 하는 수 별 상위 10 개 이벤트를 나열 합니다. 각 열 아래의 **모두 보기** 를 선택 하거나 열 머리글을 선택 하 여 전체 목록을 제공 하는 로그 검색을 실행할 수 있습니다.

| 열 | Description |
|--------|-------------|
| 시간에 따른 에이전트 수 | Linux 및 Windows 에이전트에 대해 7 일 동안 에이전트 수의 추세|
| 응답하지 않는 에이전트 개수 | 지난 24 시간 동안 하트 비트를 보내지 않은 에이전트 목록|
| OS 유형별 배포 | 사용자 환경에 있는 Windows 및 Linux 에이전트 수의 파티션|
| 에이전트 버전별 배포 | 사용자 환경에 설치 된 에이전트 버전의 파티션 및 각 항목의 수|
| 에이전트 범주별 배포 | 하트 비트 이벤트를 전송 하는 에이전트 범주의 파티션에는 직접 에이전트, Operations Manager 에이전트 또는 Operations Manager 관리 서버가 있습니다.|
| 관리 그룹별 배포 | 사용자 환경에서 Operations Manager 관리 그룹의 파티션|
| 에이전트의 지리적 위치 | 에이전트가 있는 국가/지역의 파티션 및 각 국가/지역에 설치 된 에이전트 수의 총 개수|
| 설치 된 게이트웨이 수 | Log Analytics 게이트웨이가 설치 된 서버 수와 이러한 서버 목록|

![에이전트 상태 솔루션 대시보드의 예를 보여 주는 스크린샷](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드
이 솔루션은 Log Analytics 작업 영역 (하트 비트)에 한 가지 유형의 레코드를 만듭니다. 하트 비트 레코드의 속성은 다음 표에 나와 있습니다.  

| 속성 | Description |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`, `SCOM Agent`또는 `SCOM Management Server`|
| `Computer` | 컴퓨터 이름|
| `OSType` | Windows 또는 Linux 운영 체제|
| `OSMajorVersion` | 운영 체제 주 버전|
| `OSMinorVersion` | 운영 체제 부 버전|
| `Version` | 에이전트 또는 Operations Manager 에이전트 버전 Log Analytics|
| `SCAgentChannel` | `Direct` 및/또는 `SCManagementServer`|
| `IsGatewayInstalled` | `true` Log Analytics 게이트웨이가 설치 되어 있으면이 고, 그렇지 않으면입니다. 그렇지 `false`|
| `ComputerIP` | Azure 가상 컴퓨터에 대 한 공용 IP 주소 (있는 경우) 개인 IP를 사용 하는 가상 컴퓨터에 대 한 Azure SNAT 주소 (개인 IP 주소가 아님) |
| `ComputerPrivateIPs` | 컴퓨터의 개인 Ip 목록 |
| `RemoteIPCountry` | 컴퓨터가 배포 되는 지리적 위치|
| `ManagementGroupName` | Operations Manager 관리 그룹의 이름|
| `SourceComputerId` | 컴퓨터의 고유 ID|
| `RemoteIPLongitude` | 컴퓨터의 지리적 위치 경도|
| `RemoteIPLatitude` | 컴퓨터의 지리적 위치에 대 한 위도|

Operations Manager 관리 서버에 보고 하는 각 에이전트는 두 하트 비트를 보냅니다. `SCAgentChannel`속성의 값에는 `Direct` `SCManagementServer` 구독에서 사용 하도록 설정한 데이터 원본 및 모니터링 솔루션에 따라 및가 모두 포함 됩니다. 

기억 하는 경우 솔루션의 데이터는 다음 중 하나에 전송 됩니다.

* Operations Manager 관리 서버에서 Azure Monitor로 직접
* 에이전트에서 수집 된 데이터의 양에 따라 에이전트에서 Azure Monitor 합니다.

값이 있는 하트 비트 이벤트의 경우 `SCManagementServer` `ComputerIP` 값은 실제로 데이터를 업로드 하기 때문에 관리 서버의 IP 주소입니다. `SCAgentChannel`가로 설정 된 하트 비트의 경우 `Direct` 에이전트의 공용 IP 주소입니다.  

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 표에서는 솔루션이 수집 하는 레코드에 대 한 샘플 로그 검색을 제공 합니다.

| 쿼리 | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |에이전트의 총수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |지난 24시간 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |지난 15분 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |최근 24 시간 동안의 컴퓨터 온라인 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |지난 30분 동안 오프라인으로 전환된 총 에이전트 수(지난 24시간 동안) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS 유형별 시간별 에이전트 수 추세|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS 유형별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |에이전트 버전별 배포 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |에이전트 범주별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 관리 그룹별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |에이전트의 지리적 위치 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |설치된 Log Analytics 게이트웨이 수 |

## <a name="next-steps"></a>다음 단계

* Azure Monitor [의 로그 쿼리에서 경고를 생성하는](../alerts/alerts-overview.md)방법을 알아봅니다. 

