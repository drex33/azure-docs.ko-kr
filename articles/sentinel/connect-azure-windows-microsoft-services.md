---
title: Microsoft 센티널을 Azure, Windows 및 Microsoft 서비스에 커넥트 합니다.
description: Microsoft 센티널을 Azure에 연결 하 고 클라우드 서비스를 Microsoft 365 하 고 Windows 서버 이벤트 로그에 연결 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79007f7716a8a24350bbfa6083b9891c425c120e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523033"
---
# <a name="connect-microsoft-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>microsoft 센티널을 Azure, Windows, microsoft 및 Amazon services로 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft 센티널은 azure foundation을 사용 하 여 여러 azure 및 Microsoft 365 서비스, Amazon Web Services 및 다양 한 Windows 서버 서비스에서 데이터를 수집 하는 서비스 간 지원을 제공 합니다. 이러한 연결에는 몇 가지 다른 방법이 있으며 이 문서에서는 이러한 연결을 만드는 방법에 대해 설명합니다.

이 문서에서는 다음 유형의 커넥터에 대해 설명합니다.

- **API 기반** 연결
- **진단 설정** 연결(일부는 Azure Policy에서 관리)
- **Log Analytics 에이전트** 기반 연결

이 문서에서는 커넥터 그룹에 공통적인 정보를 제공합니다. 라이선스 필수 조건 및 데이터 저장을 위한 Log Analytics 표와 같이 각 커넥터에 고유한 정보는 함께 제공되는 [데이터 커넥터 참조](data-connectors-reference.md) 페이지를 참조하세요.

다음 통합은 더 독특하고 더 대중적이며 자체 문서와 함께 개별적으로 처리됩니다.

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Microsoft Defender for Cloud](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows 보안 이벤트](connect-windows-security-events.md)
- [AWS(Amazon Web Services) CloudTrail](connect-aws.md)

## <a name="api-based-connections"></a>API 기반 연결

### <a name="prerequisites"></a>필수 조건

- Log Analytics 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- Microsoft 센티널 작업 영역 테 넌 트의 전역 관리자 또는 보안 관리자 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 서비스를 선택한 다음, 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. **커넥트** 를 선택 하 여 서비스에서 Microsoft 센티널로의 스트리밍 이벤트 및/또는 경고를 시작 합니다.

1. 커넥터 페이지에 **인시던트 만들기 - 권장됨!** 이라는 섹션이 있는 경우 경고에서 인시던트를 자동으로 만들려면 **사용** 을 선택합니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스 커넥터 섹션에 나타나는 테이블 이름을 사용하여 각 서비스의 데이터를 찾고 쿼리할 수 있습니다.

## <a name="diagnostic-settings-based-connections"></a>진단 설정 기반 연결

이 유형의 일부 커넥터 구성은 Azure Policy에서 관리합니다. 지침을 보려면 아래에서 **Azure Policy** 탭을 선택합니다. 이 유형의 다른 커넥터의 경우 **독립 실행형** 탭을 선택합니다.

# <a name="standalone"></a>[독립 실행형](#tab/SA)

### <a name="prerequisites"></a>필수 조건

Microsoft 센티널에 데이터를 수집 하려면:

- Microsoft 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 리소스 종류를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 링크를 선택하여 리소스 구성 페이지를 엽니다.

    원하는 유형의 리소스 목록이 표시되면 로그를 수집하려는 리소스에 대한 링크를 선택합니다.

1. 리소스의 탐색 메뉴에서 **진단 설정** 을 선택합니다.

1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택합니다.

1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력합니다.

    **Log Analytics에 보내기** 확인란을 선택합니다. 두 개의 새 필드가 아래에 표시됩니다. Microsoft 센티널이 있는 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다.

1. 수집하려는 로그 및 메트릭 유형의 확인란을 선택합니다. [데이터 커넥터 참조](data-connectors-reference.md) 페이지의 리소스 커넥터 섹션에서 각 리소스 유형에 대한 권장 선택 사항을 참조하세요.

1. 화면 위쪽에서 **저장** 을 선택합니다.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>필수 조건

Microsoft 센티널에 데이터를 수집 하려면:

- Microsoft 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Policy를 사용하여 리소스에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 Owner 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

이 유형의 커넥터는 Azure Policy를 사용하여 범위로 정의된 단일 유형의 리소스 컬렉션에 단일 진단 설정 구성을 적용합니다. 해당 리소스에 대한 커넥터 페이지 왼쪽의 **데이터 유형** 에서 지정된 리소스 유형에서 수집된 로그 유형을 볼 수 있습니다.

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 리소스 종류를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 표시되는 확장기를 확장하고 **Azure Policy Assignment 마법사 시작** 단추를 선택합니다.

    정책 할당 마법사가 열리고 정책 이름이 미리 채워진 상태로 새 정책을 만들 준비가 됩니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 선택하여 구독을 선택하고, 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭에서 다음을 수행합니다.
       - **입력이 필요한 매개 변수만 표시** 확인란의 선택을 취소합니다.
       - **효과** 및 **설정 이름** 필드가 표시되면 그대로 유지합니다.
       - **Log Analytics 작업 영역** 드롭다운 목록에서 Microsoft 센티널 작업 영역을 선택 합니다.
       - 나머지 드롭다운 필드는 사용 가능한 진단 로그 형식을 나타냅니다. 수집하려는 모든 로그 형식을 “True”로 표시된 채로 둡니다.

    1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

---

> [!NOTE]
>
> 이 데이터 커넥터 유형을 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆에 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지의 리소스 커넥터 섹션에 나타나는 테이블 이름을 사용하여 각 리소스 유형에 대한 데이터를 찾고 쿼리할 수 있습니다.

## <a name="windows-agent-based-connections"></a>에이전트 기반 연결 Windows

# <a name="azure-monitor-agent"></a>[Azure Monitor 에이전트](#tab/AMA)

> [!IMPORTANT]
>
> - Azure Monitor 에이전트 (AMA)를 기반으로 하는 일부 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Monitor 에이전트](../azure-monitor/agents/azure-monitor-agent-overview.md) 는 **crs (데이터 수집 규칙)** 를 사용 하 여 각 에이전트에서 수집할 데이터를 정의 합니다. 데이터 수집 규칙은 다음과 같은 두 가지 이점을 제공 합니다.

- 컴퓨터의 하위 집합에 대해 고유 하 고 범위가 지정 된 구성을 계속 허용 하면서 **대규모로 컬렉션 설정을 관리** 합니다. 데이터 수집 규칙은 작업 영역 및 가상 머신을 가리지 않습니다. 즉 데이터 수집 규칙을 머신 및 환경 전체에서 한 번만 정의하면 다시 사용할 수 있습니다. [Azure Monitor 에이전트를 위한 데이터 수집 구성](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)을 참조하세요.

- **사용자 지정 필터를 빌드하여** 수집 하려는 정확한 이벤트를 선택 합니다. Azure Monitor 에이전트는 이러한 규칙을 사용하여 *원본* 의 데이터를 필터링하고 원하는 이벤트만 수집하면서 다른 이벤트는 남겨 둡니다. 이렇게 하면 데이터 수집 비용을 많이 절약할 수 있습니다.

아래에서 데이터 수집 규칙을 만드는 방법을 참조 하세요.

### <a name="prerequisites"></a>필수 구성 요소

- Microsoft 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure 가상 컴퓨터가 아닌 시스템에서 이벤트를 수집 하려면 에이전트 기반 커넥터 Azure Monitor 사용 하도록 설정 *하기 전에* 시스템에 [**azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) 가 설치 되어 있고 사용 하도록 설정 되어 있어야 합니다.

  다음 내용이 포함됩니다.

  - 물리적 머신에 설치된 Windows 서버
  - 온-프레미스 가상 머신에 설치된 Windows 서버
  - 비 Azure 클라우드의 가상 머신에 설치된 Windows 서버

### <a name="instructions"></a>Instructions

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다. 목록에서 커넥터를 선택 하 고 세부 정보 창에서 **커넥터 페이지 열기** 를 선택 합니다. 그런 다음이 섹션의 나머지 부분에 설명된 대로 **지침** 탭의 화면 지시를 따릅니다.

1. 커넥터 페이지의 **사전 요구 사항** 섹션에 설명된 대로 적절한 사용 권한이 있는지 확인합니다.

1. **구성** 에서 **+데이터 수집 규칙 추가** 를 선택합니다. **데이터 수집 규칙 만들기** 마법사가 오른쪽에 열립니다.

1. **기본 사항** 에서 **규칙 이름** 을 입력하고 DCR(데이터 수집 규칙)을 만들 **구독** 및 **리소스 그룹** 을 지정합니다. 따라서 모니터링되는 머신과 해당 연결이 동일한 테넌트에 있기만 하면 동일한 리소스 그룹 또는 구독일 필요는 없습니다.

1. **리소스** 탭에서 **+리소스 추가** 를 선택하여 데이터 수집 규칙이 적용될 머신을 추가합니다. **범위 선택** 대화 상자가 열리고 사용 가능한 구독 목록이 표시됩니다. 구독을 확장하여 해당 리소스 그룹을 확인하고 리소스 그룹을 확장하여 사용 가능한 머신을 확인합니다. 목록에 Azure 가상 머신 및 Azure Arc 지원 서버가 표시됩니다. 구독 또는 리소스 그룹의 확인란을 표시하여 포함된 모든 머신을 선택하거나 개별 머신을 선택할 수 있습니다. 모든 머신을 선택한 경우 **적용** 을 선택합니다. 이 프로세스가 끝나면 Azure Monitor 에이전트가 아직 설치되지 않은 선택한 머신에 설치됩니다.

1. **수집** 탭에서 수집 하려는 이벤트를 선택 합니다. **모든 이벤트** 또는 **사용자 지정** 을 선택 하 여 다른 로그를 지정 하거나 [XPath 쿼리](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) 를 사용 하 여 이벤트를 필터링 합니다 (아래 참고 참조). 수집할 이벤트에 대한 특정 XML 조건으로 계산되는 식을 상자에 입력한 다음, **추가** 를 선택합니다. 단일 상자에 최대 20개의 식을 입력하고 하나의 규칙에 최대 100개의 상자를 포함할 수 있습니다.

    Azure Monitor 설명서에서 [데이터 수집 규칙](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr)에 대해 자세히 알아보세요.

    > [!NOTE]
    >
    > - Windows 보안 Events 커넥터는 수집 하도록 선택할 수 있는 두 가지 [**미리 작성 된 이벤트 집합**](windows-security-event-id-reference.md) ( **공통** 및 **최소**)을 제공 합니다.
    >
    > - Azure Monitor 에이전트는 **[XPath 버전 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations)에 대한 XPath 쿼리만** 지원합니다.

1. 원하는 필터 식을 모두 추가한 후 **다음: 검토 + 만들기** 를 선택합니다.

1. "유효성 검사 통과" 메시지가 표시되면 **만들기** 를 선택합니다. 

커넥터 페이지의 **구성** 아래에서 API를 통해 생성된 규칙을 포함하는 모든 데이터 수집 규칙을 볼 수 있습니다. 여기에서 기존 규칙을 편집하거나 삭제할 수 있습니다.

> [!TIP]
> *-FilterXPath* 매개 변수와 함께 PowerShell Cmdlet **Get-WinEvent** 를 사용하여 XPath 쿼리의 유효성을 테스트합니다. 다음 스크립트는 예제를 보여 줍니다.
>
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - 이벤트가 반환되면 쿼리가 유효합니다.
> - "지정 된 선택 조건과 일치 하는 이벤트를 찾을 수 없습니다." 라는 메시지가 표시 되는 경우 쿼리는 올바르지만 로컬 컴퓨터에 일치 하는 이벤트가 없습니다.
> - "지정 된 쿼리가 잘못 되었습니다." 라는 메시지가 표시 되 면 쿼리 구문이 잘못 된 것입니다.

### <a name="create-data-collection-rules-using-the-api"></a>API를 사용하여 데이터 수집 규칙 만들기

API를 사용하여 데이터 수집 규칙을 만들 수도 있습니다([스키마 참조](/rest/api/monitor/data-collection-rules)). 많은 규칙을 만드는 경우(예를 들어 MSSP인 경우) 이 방식을 사용하면 더 쉽게 작업할 수 있습니다. 다음은 규칙을 만들기 위한 템플릿으로 사용할 수 있는 AMA 커넥터를 [통한 Windows 보안 이벤트](data-connectors-reference.md#windows-security-events-via-ama) 에 대 한 예제입니다.

**요청 URL 및 헤더**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```

**요청 본문**

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

Azure Monitor 설명서에서 [데이터 수집 규칙에 대 한 전체 설명을](../azure-monitor/agents/data-collection-rule-overview.md) 참조 하세요.

# <a name="log-analytics-agent-legacy"></a>[Log Analytics 에이전트(레거시)](#tab/LAA)

### <a name="prerequisites"></a>필수 조건

- Log Analytics 작업 영역과 로그를 수집할 컴퓨터가 포함된 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- Microsoft 센티널 역할 외에도 해당 작업 영역에서 SecurityInsights (Microsoft 센티널) 솔루션에 대 한 **Log Analytics 참가자** 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 서비스(**DNS** 또는 **Windows 방화벽**)를 선택한 다음 **커넥터 페이지 열기** 를 선택합니다.

1. 로그를 생성하는 디바이스에 에이전트를 설치하고 온보딩합니다.

    | 머신 형식  | Instructions  |
    | --------- | --------- |
    | **Azure Windows VM의 경우** | 1. **에이전트를 설치할 위치 선택** 에서 **Azure Windows 가상 머신에 에이전트 설치** 를 확장합니다. <br><br>2. **Azure Windows Virtual Machine용 에이전트 다운로드 및 설치 >** 링크를 선택합니다. <br><br>3. **가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다. 연결하려는 각 VM에 이 단계를 반복합니다. |
    | **기타 Windows 컴퓨터의 경우** | 1. **에이전트를 설치할 위치 선택** 에서 **Azure가 아닌 Windows 컴퓨터에 에이전트 설치** 를 확장합니다. <br><br>2. **비 Azure Windows 컴퓨터용 에이전트 다운로드 및 설치** 링크를 선택합니다.  <br><br>3. **에이전트 관리** 블레이드의 **Windows 서버** 탭에서 다음과 같이 32비트 또는 64비트 시스템용 **Windows 에이전트 다운로드** 링크를 적절하게 선택합니다.  <br><br>4. 다운로드 한 실행 파일을 사용 하 여 선택한 Windows 시스템에 에이전트를 설치 하 고 이전 단계의 다운로드 링크 아래에 표시 되는 **작업 영역 ID 및 키** 를 사용 하 여 구성 합니다. |
    | | |

> [!NOTE]
>
> 필요한 인터넷 연결 없이도 Windows 시스템에서 Microsoft 센티널로 이벤트를 스트리밍할 수 있도록 하려면 **에이전트 관리** 페이지에서 **Log Analytics 게이트웨이 다운로드** 링크를 사용 하 여 프록시 역할을 하는 별도의 컴퓨터에 **Log Analytics 게이트웨이** 를 다운로드 하 여 설치 합니다.  이벤트를 수집 하려는 각 Windows 시스템에 Log Analytics 에이전트를 설치 해야 합니다.
>
> 해당 시나리오에 대한 자세한 내용은 [**Log Analytics 게이트웨이** 설명서](../azure-monitor/agents/gateway.md)를 참조하세요.

추가 설치 옵션 및 세부 정보는 [**Log Analytics 에이전트** 설명서](../azure-monitor/agents/agent-windows.md)를 참조하세요.


#### <a name="determine-the-logs-to-send"></a>보낼 로그 확인

Windows DNS 서버 및 Windows 방화벽 커넥터에 대해 **솔루션 설치** 단추를 선택 합니다. 레거시 보안 이벤트 커넥터에서 전송 하려는 [**이벤트 집합**](windows-security-event-id-reference.md) 을 선택 하 고 **업데이트** 를 선택 합니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지의 해당 섹션에 있는 테이블 이름을 사용 하 여 이러한 서비스에 대 한 데이터를 찾고 쿼리할 수 있습니다.

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Amazon Web Services 뿐만 아니라 Azure, microsoft 및 Windows 서비스를 Microsoft 센티널에 연결 하는 방법을 알아보았습니다. 
- 일반적으로 [Microsoft 센티널 데이터 커넥터](connect-data-sources.md) 에 대해 알아봅니다.
- [Microsoft 센티널 데이터 커넥터를 찾습니다](data-connectors-reference.md).
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
