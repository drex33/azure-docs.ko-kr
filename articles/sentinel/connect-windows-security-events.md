---
title: Azure Sentinel에 Windows 보안 이벤트 데이터 연결(탭 버전) | Microsoft Docs
description: Windows 보안 이벤트 커넥터를 사용하여 Windows 시스템에서 Azure Sentinel 작업 영역으로 모든 보안 이벤트를 스트리밍하는 방법 알아보기.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2021
ms.author: yelevin
ms.openlocfilehash: 133d5e01de4f5fe511677fc5226eb0aede258eab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252283"
---
# <a name="connect-to-windows-servers-to-collect-security-events"></a>Windows 서버에 연결하여 보안 이벤트 수집

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Windows 보안 이벤트 커넥터를 사용하면 Azure Sentinel 작업 영역에 연결된 모든 Windows 서버(물리적 또는 가상, 온-프레미스 또는 클라우드에 있는)에서 보안 이벤트를 스트리밍할 수 있습니다. 이렇게 하면 대시보드에서 Windows 보안 이벤트를 보고 이를 사용하여 사용자 지정 경고를 만들고 조사를 개선하여 조직의 네트워크에 대한 더 많은 인사이트를 제공하고 보안 운영 기능을 확장할 수 있습니다.

## <a name="connector-options"></a>커넥터 옵션

Windows 보안 이벤트 커넥터에서는 다음 버전을 지원합니다.

|커넥터 버전  |설명  |
|---------|---------|
|**보안 이벤트**     |Log Analytics 에이전트를 기반으로 하며 MMA(Microsoft Monitoring Agent) 또는 OMS 에이전트라고도 하는 레거시 버전입니다. <br><br>초당 10,000개의 이벤트로 제한됩니다. 최적의 성능을 보장하려면 초당 8,500개 이하의 이벤트를 유지해야 합니다.       |
|**Windows 보안 이벤트**     |최신 버전이며 현재 미리 보기 상태에 있으며 AMA(Azure Monitor 에이전트)를 기반으로 합니다.   <br><br>특정 머신 그룹에 대한 사전 수집 로그 필터링 및 개별 데이터 수집 규칙과 같은 추가 기능을 지원합니다.      |
|     |         |


> [!NOTE]
> Linux용 MMA는 로그를 여러 작업 영역으로 보내는 멀티 호밍을 지원하지 않습니다. 멀티 호밍이 필요한 경우 **Windows 보안 이벤트** 커넥터를 사용하는 것이 좋습니다.

> [!TIP]
> 여러 에이전트가 필요한 경우 로그 수집을 위해 여러 에이전트를 실행하도록 설정된 가상 머신 규모를 사용하거나 여러 머신을 사용할 수 있습니다. 그런 다음, 보안 이벤트와 Windows 보안 이벤트 커넥터 모두를 부하 분산 장치와 함께 사용하여 머신 과부하와 데이터 중복을 방지할 수 있습니다.
>

이 문서에서는 두 버전의 커넥터에 대한 정보를 제공합니다. 아래 탭에서 선택하여 선택한 커넥터와 관련된 정보를 볼 수 있습니다.


# <a name="log-analytics-agent-legacy"></a>[Log Analytics 에이전트(레거시)](#tab/LAA)

다음 <a name="event-sets"></a> 집합 중에서 스트리밍할 이벤트를 선택할 수 있습니다.

- **모든 이벤트** - 모든 Windows 보안 및 AppLocker 이벤트입니다.
- **일반** - 감사 용도 이벤트의 표준 집합입니다. 전체 사용자 감사 내역이 해당 집합에 포함됩니다. 예를 들어 사용자 로그인 및 사용자 로그아웃 이벤트(이벤트 ID 4624, 4634)가 모두 포함됩니다. 보안 그룹 변경 사항, 주요 도메인 컨트롤러 Kerberos 작업 및 허용된 모범 사례와 일치하는 기타 이벤트 유형 등도 있습니다.

    **공통** 이벤트 집합에는 일반적이지 않은 일부 이벤트 유형이 포함될 수 있습니다.  **일반적인** 집합에서는 전체 감사 내역 기능을 계속 유지하면서 이벤트 볼륨을 관리하기 쉬운 수준으로 줄이는 것이 중요하기 때문입니다.

- **최소** - 잠재적인 위협을 나타낼 수 있는 작은 이벤트 집합입니다. 이 집합은 전체 감사 내역을 포함하지 않습니다. 보안 위반 이벤트 및 발생 빈도는 낮지만 중요한 이벤트를 다룹니다. 예를 들어 로그온 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃 정보(4634)는 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 위반 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.

- **없음** - 보안 또는 AppLocker 이벤트가 없습니다. (이 설정은 커넥터를 사용하지 않도록 설정하는 데 사용됩니다.)

> [!NOTE]
> 단일 작업 영역의 컨텍스트 내에서 보안 이벤트 수집은 Azure Security Center 또는 Azure Sentinel에서 구성할 수 있지만 둘 다 함께 구성할 수는 없습니다. 이미 Azure Security Center로부터 Azure Defender 경고를 받고 있는 작업 영역에서 Azure Sentinel을 온보딩하고 보안 이벤트를 수집하도록 설정된 경우 두 가지 옵션이 있습니다.
> - Azure Security Center의 보안 이벤트 컬렉션은 그대로 둡니다. Azure Sentinel 및 Azure Defender에서 이러한 이벤트를 쿼리하고 분석할 수 있습니다. 그러나 커넥터의 연결 상태를 모니터링하거나 Azure Sentinel에서 해당 구성을 변경할 수 없습니다. 이것이 중요하다면 두 번째 옵션을 고려합니다.
>
> - Azure Security Center에서 [보안 이벤트 수집을 사용하지 않도록 설정](../security-center/security-center-enable-data-collection.md)한 다음, Azure Sentinel에서 보안 이벤트 커넥터를 추가합니다. 첫 번째 옵션과 마찬가지로 Azure Sentinel 및 Azure Defender/ASC에서 이벤트를 쿼리하고 분석할 수 있지만, 이제 Azure Sentinel에서만 커넥터의 연결 상태를 모니터링하거나 구성을 변경할 수 있습니다.

# <a name="azure-monitor-agent-new"></a>[Azure Monitor 에이전트(신규)](#tab/AMA)

> [!IMPORTANT]
>
> - AMA(Azure Monitor Agent)을 기준으로 하는 Windows 보안 이벤트 데이터 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Monitor 에이전트](../azure-monitor/agents/azure-monitor-agent-overview.md)는 **DCR(데이터 수집 규칙)** 을 사용하여 각 에이전트에서 수집할 데이터를 정의합니다. 데이터 수집 규칙을 사용하면 수집 설정을 대규모로 관리하면서도 계속해서 머신의 하위 세트에 대해 고유하고 범위가 지정된 구성을 사용할 수 있습니다. 데이터 수집 규칙은 작업 영역 및 가상 머신을 가리지 않습니다. 즉 데이터 수집 규칙을 머신 및 환경 전체에서 한 번만 정의하면 다시 사용할 수 있습니다. [Azure Monitor 에이전트를 위한 데이터 수집 구성](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)을 참조하세요.

이전 커넥터를 사용하여 수집하도록 선택할 수 있는 미리 선택된 이벤트 세트(**모든 이벤트**, **최소** 또는 **일반**) 외에도 **데이터 수집 규칙** 을 사용하면 수집하려는 정확한 이벤트를 선택하는 사용자 지정 필터를 빌드할 수 있습니다. Azure Monitor 에이전트는 이러한 규칙을 사용하여 *원본* 의 데이터를 필터링하고 원하는 이벤트만 수집하면서 다른 이벤트는 남겨 둡니다. 이렇게 하면 데이터 수집 비용을 많이 절약할 수 있습니다.

이 문서에서는 데이터 수집 규칙을 만드는 방법을 보여 줍니다.

> [!NOTE]
> - **다른 에이전트와 함께 사용 가능**
> 
>   Azure Monitor 에이전트는 기존 에이전트와 함께 사용할 수 있으므로 평가 또는 마이그레이션 중에도 레거시 커넥터를 계속 사용할 수 있습니다. 이러한 기능은 기존 솔루션에 대한 지원이 제한적이기 때문에 새 커넥터가 미리 보기 상태로 제공되는 동안 특히 중요합니다. 하지만 중복 데이터를 수집하는 경우 쿼리 결과가 왜곡되고 데이터 수집 및 보존에 대한 추가 요금이 발생할 수 있으므로 주의해야 합니다.
> 
> - **Azure가 아닌 머신에서 보안 이벤트 수집**
> 
>   Azure 가상 머신이 아닌 시스템에서 보안 이벤트를 수집하려면 Azure Monitor 에이전트 기반 커넥터를 사용하도록 설정하기 *전에* 시스템에 [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md)를 설치하고 사용하도록 설정해야 합니다.
>   
>   다음 내용이 포함됩니다.
>   
>    - 물리적 머신에 설치된 Windows 서버
>    - 온-프레미스 가상 머신에 설치된 Windows 서버
>    - 비 Azure 클라우드의 가상 머신에 설치된 Windows 서버

---

## <a name="set-up-the-windows-security-events-connector"></a>Windows 보안 이벤트 커넥터 설정하기

Azure Sentinel에서 Windows 보안 이벤트를 수집하기 위해서는

# <a name="log-analytics-agent-legacy"></a>[Log Analytics 에이전트(레거시)](#tab/LAA)

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **보안 이벤트** 를 선택한 다음, 세부 정보 창에서 **커넥터 페이지 열기** 를 선택합니다. 그런 다음이 섹션의 나머지 부분에 설명된 대로 **지침** 탭의 화면 지시를 따릅니다.

1. 커넥터 페이지의 **사전 요구 사항** 섹션에 설명된 대로 적절한 사용 권한이 있는지 확인합니다.

1. 보안 이벤트를 Azure Sentinel로 스트리밍하려는 컴퓨터에 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)(Microsoft Monitoring Agent 또는 MMA라고도 함)를 다운로드하여 설치합니다.

    Azure Virtual Machines용
    
    1. **Azure Windows 가상 머신에서 에이전트 설치** 를 선택한 다음 아래에 표시되는 링크를 클릭합니다.
    1. 연결하려는 각 가상 머신에 대해 오른쪽에 표시되는 목록에서 해당 이름을 선택하고 **연결** 을 선택합니다.

    비 Azure Windows 컴퓨터인 경우(물리적, 가상 온-프레미스 또는 다른 클라우드의 가상):

    1. **비 Azure Windows 머신에 에이전트 설치** 를 선택한 다음 아래에 표시되는 링크를 선택합니다.
    1. 오른쪽 **Windows 컴퓨터** 아래에 표시되는 적절한 다운로드 링크를 선택합니다.
    1. 다운로드한 실행 파일을 사용하여 선택한 Windows 시스템에 에이전트를 설치하고 위에서 언급한 다운로드 링크 아래에 표시되는 **작업 영역 ID 및 키** 를 사용하여 구성합니다.

    > [!NOTE]
    >
    > 인터넷이 연결되지 않은 Windows 시스템에서 Azure Sentinel로 이벤트를 스트리밍할 수 있도록 하려면 오른쪽 아래의 링크를 사용하여 별도의 머신에 **OMS 게이트웨이** 를 다운로드 및 설치하고 프록시로 작동하게 합니다.  이벤트를 수집하려는 각 Windows 시스템에는 Log Analytics 에이전트를 설치해야 합니다.
    >
    > 해당 시나리오에 대한 자세한 내용은 [**Log Analytics 게이트웨이** 설명서](../azure-monitor/agents/gateway.md)를 참조하세요.

    추가 설치 옵션 및 세부 정보는 [**Log Analytics 에이전트** 설명서](../azure-monitor/agents/agent-windows.md)를 참조하세요.

1. 스트리밍할 이벤트 집합(모두, 공통 또는 최소)을 선택합니다. 최소 및 공통 이벤트 세트에서 [포함된 이벤트 ID 목록](#event-id-reference)을 참조하세요.

1. **업데이트** 를 선택합니다.

1. Windows 보안 이벤트에 대한 Log Analytics에서 관련 스키마를 사용하려면 쿼리 창에 `SecurityEvent`를 입력합니다.

# <a name="azure-monitor-agent-new"></a>[Azure Monitor 에이전트(신규)](#tab/AMA)

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **Windows 보안 이벤트(미리 보기)** 를 선택한 다음, 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 선택합니다. 그런 다음이 섹션의 나머지 부분에 설명된 대로 **지침** 탭의 화면 지시를 따릅니다.

1. 커넥터 페이지의 **사전 요구 사항** 섹션에 설명된 대로 적절한 사용 권한이 있는지 확인합니다.

    1. Windows 보안 이벤트를 수집할 작업 영역 및 모든 데이터 원본에 대한 읽기 및 쓰기 권한이 있어야 합니다.

    1. Azure VM이 아닌 Windows 머신에서 이벤트를 수집하려면 머신(물리적 또는 가상)에 Azure Arc를 설치하고 사용하도록 설정해야 합니다. [자세한 정보](../azure-monitor/agents/azure-monitor-agent-install.md).

1. **구성** 에서 **+데이터 수집 규칙 추가** 를 선택합니다. **데이터 수집 규칙 만들기** 마법사가 오른쪽에 열립니다.

1. **기본 사항** 에서 **규칙 이름** 을 입력하고 DCR(데이터 수집 규칙)을 만들 **구독** 및 **리소스 그룹** 을 지정합니다. 따라서 모니터링되는 머신과 해당 연결이 동일한 테넌트에 있기만 하면 동일한 리소스 그룹 또는 구독일 필요는 없습니다.

1. **리소스** 탭에서 **+리소스 추가** 를 선택하여 데이터 수집 규칙이 적용될 머신을 추가합니다. **범위 선택** 대화 상자가 열리고 사용 가능한 구독 목록이 표시됩니다. 구독을 확장하여 해당 리소스 그룹을 확인하고 리소스 그룹을 확장하여 사용 가능한 머신을 확인합니다. 목록에 Azure 가상 머신 및 Azure Arc 지원 서버가 표시됩니다. 구독 또는 리소스 그룹의 확인란을 표시하여 포함된 모든 머신을 선택하거나 개별 머신을 선택할 수 있습니다. 모든 머신을 선택한 경우 **적용** 을 선택합니다. 이 프로세스가 끝나면 Azure Monitor 에이전트가 아직 설치되지 않은 선택한 머신에 설치됩니다.

1. **수집** 탭에서 수집하려는 [이벤트 세트](#event-id-reference)를 선택하거나 **사용자 지정** 을 선택하여 다른 로그를 지정하거나 [XPath 쿼리](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)를 사용하여 이벤트를 필터링합니다. 수집할 이벤트에 대한 특정 XML 조건으로 계산되는 식을 상자에 입력한 다음, **추가** 를 선택합니다. 단일 상자에 최대 20개의 식을 입력하고 하나의 규칙에 최대 100개의 상자를 포함할 수 있습니다.

    Azure Monitor 설명서에서 [데이터 수집 규칙](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr)에 대해 자세히 알아보세요.

    > [!NOTE]
    > - Windows 보안 및 AppLocker 로그만 쿼리해야 합니다. 다른 Windows 로그 또는 다른 환경의 보안 로그에서 발생한 이벤트는 Windows 보안 이벤트 스키마를 따르지 않을 수 있으며 제대로 구문 분석되지 않습니다. 이 경우 작업 영역에 수집되지 않습니다.
    >
    > - Azure Monitor 에이전트는 **[XPath 버전 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations)에 대한 XPath 쿼리만** 지원합니다.

1. 원하는 필터 식을 모두 추가한 후 **다음: 검토 + 만들기** 를 선택합니다.

1. "유효성 검사 통과" 메시지가 표시되면 **만들기** 를 선택합니다. 

커넥터 페이지의 **구성** 아래에서 API를 통해 생성된 규칙을 포함하는 모든 데이터 수집 규칙을 볼 수 있습니다. 여기에서 기존 규칙을 편집하거나 삭제할 수 있습니다.

> [!TIP]
> *-FilterXPath* 매개 변수와 함께 PowerShell Cmdlet **Get-WinEvent** 를 사용하여 XPath 쿼리의 유효성을 테스트합니다. 다음 스크립트는 예제를 보여 줍니다.
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - 이벤트가 반환되면 쿼리가 유효합니다.
> - 지정된 선택 조건과 일치하는 이벤트를 찾을 수 없다는 메시지가 표시되는 경우 쿼리는 유효하지만 로컬 컴퓨터에 일치하는 이벤트가 없습니다.
> - 지정된 쿼리가 잘못되었습니다 메시지를 받으면 쿼리 구문이 잘못된 것입니다.

### <a name="create-data-collection-rules-using-the-api"></a>API를 사용하여 데이터 수집 규칙 만들기

API를 사용하여 데이터 수집 규칙을 만들 수도 있습니다([스키마 참조](/rest/api/monitor/data-collection-rules)). 많은 규칙을 만드는 경우(예를 들어 MSSP인 경우) 이 방식을 사용하면 더 쉽게 작업할 수 있습니다. 규칙을 만들기 위한 템플릿으로 사용할 수 있는 예제는 다음과 같습니다.

**요청 URL 및 헤더**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```
**요청 URL 및 헤더**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```



---

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 약 20분 정도 소요될 수 있습니다. 

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>비정상적인 RDP 로그인 검색에 대한 보안 이벤트/Windows 보안 이벤트 커넥터 구성

> [!IMPORTANT]
> 비정상적인 RDP 로그인 검색은 현재 퍼블릭 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel은 ML(기계 학습)을 보안 이벤트 데이터에 적용하여 비정상적인 RDP(원격 데스크톱 프로토콜) 로그인 활동을 식별할 수 있습니다. 다음과 같은 시나리오가 있습니다.

- **비정상적인 IP** -지난 30일 동안 IP 주소가 거의 또는 전혀 관찰되지 않았습니다.

- **비정상적인 지리적 위치** -지난 30일 동안 IP 주소, 구/군/시, 국가 및 ASN이 거의 또는 전혀 관찰되지 않았습니다.

- **새 사용자** -새 사용자가 IP 주소 및 지리적 위치에서 로그인하거나 둘 다 또는 어느 쪽도 30일 이전 데이터를 기반으로 하여 표시되지 않습니다.

**구성 지침**

1. **보안 이벤트** 또는 **Windows 보안 이벤트** 데이터 커넥터를 통해 RDP 로그인 데이터(이벤트 ID 4624)를 수집해야 합니다. "없음" 이외의 [이벤트 세트](#event-id-reference)를 선택했거나 이 이벤트 ID를 포함하는 데이터 수집 규칙을 만들어 Azure Sentinel로 스트리밍했는지 확인합니다.

1. Azure Sentinel 포털에서 **분석** 을 선택한 다음, **규칙 템플릿** 탭을 선택합니다. **(미리 보기) 비정상적인 RDP 로그인 검색** 규칙을 선택하고 **상태** 슬라이더를 **사용** 으로 움직입니다.

    > [!NOTE]
    > 기계 학습 알고리즘은 사용자 동작의 기준 프로필을 컴파일하는 데 30일 분량의 데이터가 필요하므로 인시던트 이전에 30일간의 Windows 보안 이벤트 데이터를 수집해야 합니다.

## <a name="event-id-reference"></a>이벤트 ID 참조

다음 목록은 각 집합별 전체 보안 내역 및 App Locker 이벤트 ID를 제공합니다.

| 이벤트 집합 | 수집된 이벤트 ID |
| --- | --- |
| **최소** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **일반** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 보안 이벤트를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](detect-threats-built-in.md) 또는 [사용자 지정](detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색을 시작합니다.

