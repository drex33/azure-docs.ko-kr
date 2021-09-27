---
title: Azure Sentinel에서의 위협 지표 작업 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel 위협 인텔리전스 지표를 사용하여 위협을 보고, 만들고, 관리하고, 시각화하고, 검색하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2021
ms.author: yelevin
ms.openlocfilehash: 0ce6c7ced310fa467adbdc707d25bb5efc0f02bc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763313"
---
# <a name="work-with-threat-indicators-in-azure-sentinel"></a>Azure Sentinel에서 위협 지표 작업

다음 작업을 통해 TI(위협 인텔리전스)를 Azure Sentinel로 통합할 수 있습니다.

- 다양한 TI [플랫폼](connect-threat-intelligence-tip.md) 및 [피드](connect-threat-intelligence-taxii.md)에 **데이터 커넥터** 를 사용하여 Azure Sentinel로 **위협 인텔리전스를 가져옵니다**.

- **로그** 및 Azure Sentinel의 **위협 인텔리전스** 블레이드에서 가져온 위협 인텔리전스를 **확인 및 관리** 합니다.

- 가져온 위협 인텔리전스를 기반으로 기본 제공 **분석** 규칙 템플릿을 사용하여 **위협을 탐지** 하고 보안 경고 및 인시던트를 생성합니다.

- **위협 인텔리전스 통합 문서** 를 사용하여 Azure Sentinel에서 가져온 위협 인텔리전스에 대한 **중요 정보를 시각화** 합니다.

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Azure Sentinel에서 위협 지표 보기

### <a name="find-and-view-your-indicators-in-logs"></a>로그에서 지표 찾기 및 보기

사용한 소스 피드 또는 커넥터에 관계없이, 모든 Azure Sentinel 이벤트 데이터가 저장되는 **로그** 의 **ThreatIntelligenceIndicator** 테이블에서(**Azure Sentinel** 그룹 아래에 있음) 가져온 위협 지표를 볼 수 있습니다. 이 테이블은 Analytics 및 Workbooks와 같은 다른 Azure Sentinel 기능에서 수행하는 쿼리의 기초가 됩니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **일반** 섹션에서 **로그** 를 선택합니다.

1. **ThreatIntelligenceIndicator** 테이블은 **Azure Sentinel** 그룹 아래에 있습니다.

1. 테이블 이름 옆의 **데이터 미리 보기** 아이콘(눈)을 선택하고 **쿼리 편집기에서 확인** 단추를 선택하여 이 테이블의 레코드를 표시하는 쿼리를 실행합니다.

결과는 아래에 표시된 샘플 위협 지표와 비슷합니다.

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="샘플 쿼리 데이터":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-blade"></a>위협 인텔리전스 블레이드에서 지표 찾기 및 보기

Azure Sentinel 주 메뉴에서 액세스할 수 있는 새로운 **위협 인텔리전스** 블레이드에서도 지표를 확인하고 관리할 수 있습니다. 심지어 Log Analytics 쿼리를 작성하지 않고도 가져온 위협 지표를 정렬, 필터링 및 검색할 수 있습니다. 또한 이 기능을 통해 Azure Sentinel 인터페이스 내에서 직접 위협 지표를 만들고, 가장 일반적인 두 가지 위협 인텔리전스 관리 작업인 지표 태그 지정 및 보안 조사와 관련된 신규 지표 작성을 수행할 수 있습니다.

#### <a name="create-a-new-indicator"></a>새 지표 생성하기

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 위협 관리 섹션에서 **위협 인텔리전스** 를 선택합니다.

1. 페이지 위쪽에 있는 메뉴에서 **추가** 단추를 선택합니다.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="새 위협 지표 추가" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. 지표 유형을 선택한 다음, **신규 지표** 패널에서 양식을 작성합니다. 필수 필드에는 빨간색 별표(*)가 표시됩니다.

1. **적용** 을 선택합니다. 지표가 지표 목록에 추가되고 **로그** 의 *ThreatIntelligenceIndicator* 테이블에도 보내집니다.

#### <a name="tag-threat-indicators"></a>태그 위협 지표

위협 지표에 태그를 지정하면 지표를 함께 그룹화하여 손쉽게 찾을 수 있습니다. 일반적으로 특정 인시던트와 관련된 지표 또는 알려진 행위자나 잘 알려진 공격 캠페인의 위협을 나타내는 지표에 태그를 적용합니다. 위협 지표에 각각 태그를 지정할 수도 있고 여러 개를 선택하여 한 번에 모두 태그를 지정할 수도 있습니다. 인시던트 ID를 사용하여 여러 지표에 태그를 지정하는 예제는 다음과 같습니다. 태그 지정은 특정한 형식이 없기 때문에, 위협 지표 태그에 대한 표준 명명 규칙을 만드는 것이 좋습니다. 각 지표에 여러 태그를 적용할 수 있습니다.

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="위협 지표에 태그 적용" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>위협 지표 기반 분석을 사용하여 위협 탐지

Azure Sentinel과 같은 SIEM 솔루션에서 위협 지표의 가장 중요한 사용 사례는 위협 탐지를 위한 분석 규칙을 제공하는 것입니다. 이러한 지표 기반 규칙은 데이터 원본의 원시 이벤트를 위협 지표와 비교하여 조직에서 보안 위협을 탐지합니다. Azure Sentinel **분석** 에서 일정에 따라 실행되고 보안 경고를 생성하는 분석 규칙을 만듭니다. 이러한 규칙은 규칙 실행 빈도, 보안 경고 및 인시던트를 생성하는 쿼리 결과의 종류, 경고에 대한 대응으로 트리거할 자동화를 결정하는 구성과 함께 쿼리를 통해 작동합니다.

언제든지 새 분석 규칙을 처음부터 만들 수 있지만, Azure Sentinel은 Microsoft 보안 엔지니어가 만든 기본 제공 규칙 템플릿 집합을 제공하며, 이를 그대로 사용하거나 필요에 맞게 수정할 수 있습니다. 위협 지표를 사용하는 규칙 템플릿은 모두 '*TI map*...'으로 시작하므로 쉽게 식별할 수 있습니다. 이러한 모든 규칙 템플릿은 사용하는 위협 지표 유형(도메인, 이메일, 파일 해시, IP 주소 또는 URL) 및 일치시킬 이벤트 유형을 제외하고 유사하게 작동합니다. 각 템플릿에는 규칙이 작동하는 데 필요한 데이터 원본이 나열되어 있으므로 Azure Sentinel에서 필요한 이벤트를 이미 가져왔는지 확인할 수 있습니다. 기존 규칙 템플릿을 편집하고 저장하거나 새 규칙을 만들 때 기본적으로 사용하도록 설정됩니다.

### <a name="configure-a-rule-to-generate-security-alerts"></a>보안 경고를 생성하는 규칙 구성

다음 예시는 Azure Sentinel로 가져온 위협 지표를 사용하여 보안 경고를 생성하는 규칙을 사용하도록 설정하고 구성하는 방법입니다. 이 예제에서는 **TI map IP entity to AzureActivity** 라는 규칙 템플릿을 사용합니다. 이 규칙은 모든 IP 주소 유형 위협 지표를 모든 Azure 활동 이벤트와 일치시킵니다. 일치 항목이 발견되면 **경고** 가 생성되고 보안 운영 팀에서 조사하기 위한 해당 **인시던트** 도 생성됩니다. 이 분석 규칙은 위협 지표를 가져오기 위해 **위협 인텔리전스** 데이터 커넥터 중 하나 또는 둘 다 사용하도록 설정하고 Azure 구독 수준 이벤트를 가져오기 위해 **Azure 활동** 데이터 커넥터를 사용하도록 설정한 경우에만 정상적으로 작동합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Sentinel** 서비스로 이동합니다.

1. **위협 인텔리전스** 데이터 커넥터를 위협 지표를 가져오고 **Azure 활동** 데이터 커넥터를 사용하여 Azure 활동 데이터를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **구성** 섹션에서 **분석** 을 선택합니다.

1. **규칙 템플릿** 탭을 선택하여 사용 가능한 분석 규칙 템플릿의 목록을 확인합니다.

1. **TI map IP entity to AzureActivity** 라는 규칙을 찾아 아래와 같이 필요한 데이터 원본을 모두 연결했는지 확인합니다.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="필요한 데이터 원본":::

1. 이 규칙을 선택하고 **규칙 만들기** 단추를 선택합니다. 그러면 규칙을 구성하는 마법사가 열립니다. 여기에서 설정을 완료하고 **다음: 규칙 논리 설정 >** 단추를 선택합니다.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="분석 규칙 만들기":::

1. 마법사의 규칙 논리 부분은 다음 항목으로 미리 채워져 있습니다.
    - 규칙에 사용될 쿼리

    - **인시던트** 및 **조사** 가 이 규칙에 의해 생성된 모든 보안 경고의 데이터로 작업하는 방법을 이해하도록 Azure Sentinel에게 계정, IP 주소, URL과 같은 엔터티를 인식하는 방법을 알려주는 엔터티 매핑

    - 이 규칙을 실행할 일정

    - 보안 경고가 생성되기 전에 필요한 쿼리 결과의 수

    템플릿의 기본 설정은 다음과 같습니다.
    - 한 시간에 한 번 실행합니다.

    - **ThreatIntelligenceIndicator** 테이블의 모든 IP 주소 위협 지표를 **AzureActivity** 테이블의 마지막 1시간 동안 이벤트에서 검색된 IP 주소와 일치시킵니다.

    - 쿼리 결과가 0보다 큰 경우(즉, 일치 항목이 있는 경우) 보안 경고를 생성합니다.

    기본 설정을 그대로 두거나 요구 사항에 맞게 변경하고 인시던트 설정 탭에서 **인시던트** 생성 설정을 정의할 수 있습니다. 자세한 내용은 [위협을 감지하는 사용자 지정 분석 규칙 생성하기](detect-threats-custom.md)를 참조하세요. 완료되면 **자동화된 응답** 탭을 선택합니다.

1. 이 분석 규칙에서 보안 경고가 생성될 때 트리거하는 모든 자동화를 구성합니다. Azure Sentinel의 자동화는 Azure Logic Apps에서 제공하는 **자동화 규칙** 및 **플레이북** 을 사용하여 수행됩니다. 자세한 내용은 [자습서: Azure Sentinel에서 자동화 규칙으로 플레이북 사용](./tutorial-respond-threats-playbook.md)을 참조하세요. 완료되면 **다음: 검토 >** 단추를 선택하여 계속합니다.

1. 규칙 유효성 검사를 통과했다는 메시지가 표시되면 **생성하기** 단추를 선택하면 완료됩니다.

Azure Sentinel **분석** 섹션의 **활성 규칙** 탭에서 설정된 규칙을 찾을 수 있습니다. 여기에서 활성 규칙을 편집, 활성화, 비활성화, 복제 또는 삭제할 수 있습니다. 새 규칙은 활성화되는 즉시 실행되며, 그 다음에는 정의된 일정에 따라 실행됩니다.

규칙이 기본 설정의 일정에 따라 실행될 때마다 검색된 결과가 있으면 보안 경고가 생성됩니다. Azure Sentinel의 보안 경고는 **Azure Sentinel** 그룹 아래 **SecurityAlert** 테이블에 있는 Azure Sentinel의 **로그** 섹션에서 볼 수 있습니다.

Azure Sentinel에서 분석 규칙에서 생성된 경고는 Azure Sentinel 메뉴의 **위협 관리** 아래에 있는 **인시던트** 에서 확인할 수 있는 보안 인시던트도 생성합니다. 인시던트는 보안 운영 팀이 적절한 대응 조치를 결정하기 위해 선별 및 조사하는 것입니다. 이 [자습서: Azure Sentinel을 사용하여 인시던트 조사](./investigate-cases.md)에서 자세한 정보를 찾을 수 있습니다.

## <a name="detect-threats-using-matching-analytics-public-preview"></a>일치 분석을 사용하여 위협 감지(공개 미리 보기)

기본 제공 **Microsoft 위협 인텔리전스 일치 분석** 규칙 템플릿을 사용하여 Microsoft에서 생성된 위협 인텔리전스 데이터를 Azure Sentinel에서 수집한 로그와 일치시키는 [규칙을 생성합니다](detect-threats-built-in.md#use-built-in-analytics-rules).

위협 인텔리전스 데이터를 로그와 일치시키면 적절한 심각도가 적용된 고화질 경고 및 인시던트 생성에 도움이 됩니다. 일치가 발견되면 생성된 모든 경고가 인시던트로 그룹화됩니다.

경고는 24시간 동안 관찰 가능한 기준으로 그룹화됩니다. 예를 들어, `abc.com` 도메인과 일치하는 24시간 동안 생성된 모든 경고는 단일 인시던트로 그룹화됩니다.

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>일치 분석으로 생성된 인시던트 심사

일치가 발견되면 생성된 모든 경고가 인시던트로 그룹화됩니다.

다음 단계를 사용하여 **Microsoft 위협 인텔리전스 일치 분석** 규칙에서 생성된 인시던트 심사를 수행합니다.

1. **Microsoft 위협 인텔리전스 일치 분석** 규칙을 사용하도록 설정한 Azure Sentinel 작업 영역에서 **인시던트** 를 선택하고 **Microsoft 위협 인텔리전스 분석** 을 검색합니다.

    발견된 인시던트 모두 그리드에 표시됩니다.

1. 엔터티 및 인시던트 관련 기타 세부 정보(예: 특정 경고)를 보려면 **전체 세부 정보 보기** 를 선택합니다.

    예를 들면 다음과 같습니다.

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="샘플 일치 분석 세부 정보입니다.":::

일치가 발견되면 지표가 Log Analytics **ThreatIntelligenceIndicators** 에도 게시되고 **위협 인텔리전스** 페이지에 표시됩니다. 이 규칙에서 게시된 모든 지표의 경우, 원본은 **Microsoft Threat Intelligence Analytics** 로 정의됩니다.

예: **ThreatIntelligenceIndicators** 로그

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="ThreatIntelligenceIndicators 로그에 표시되는 일치 분석.":::

**위협 인텔리전스** 페이지:

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="위협 인텔리전스 페이지에 표시되는 일치 분석.":::

### <a name="supported-log-sources-for-matching-analytics"></a>일치 분석에 지원되는 로그 원본

**Microsoft 위협 인텔리전스 일치 분석** 규칙은 현재 다음 로그 원본에 대해 지원됩니다.

|로그 원본  |설명  |
|---------|---------|
|[CEF](connect-common-event-format.md)     |  일치는 `DeviceVendor`이 `Cisco`로 나열된 모든 위치를 제외하고 Log Analytics **CommonSecurityLog** 테이블에 포함된 모든 CEF 로그에 대해 수행됩니다. <br><br>Microsoft에서 생성한 위협 인텔리전스를 CEF 로그와 일치하려면, CEF 로그의 `RequestURL` 필드에 도메인을 매핑해야 합니다.      |
|[DNS](./data-connectors-reference.md#domain-name-server)     | 일치는 클라이언트에서 DNS 서비스(`SubType == "LookupQuery"`)로 DNS 쿼리를 조회하는 모든 DNS 로그에 대해 수행됩니다. DNS 쿼리는 IPv4(`QueryType=”A”`) 및 IPv6 쿼리(`QueryType=” AAAA”`)에 대해서만 처리됩니다.<br><br>모든 열이 Windows DNS 서버의 표준이고 도메인이 기본적으로 `Name` 열에 있기 때문에, Microsoft에서 생성한 위협 인텔리전스를 DNS 로그와 일치시키기 위해 열의 수동 매핑이 필요하지 않습니다.   |
|[Syslog](connect-syslog.md)     |  일치는 현재 `Facility`이 `cron`인 Syslog 이벤트에 대해서만 수행됩니다. <br><br>Microsoft에서 생성한 위협 인텔리전스를 Syslog와 일치시키기 위해 열의 수동 매핑이 필요하지 않습니다. 세부 정보는 기본적으로 Syslog의 `SyslogMessage` 필드에 있으며, 규칙은 SyslogMessage에서 직접 도메인을 구문 분석합니다.     |
|     |         |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>통합 문서는 위협 인텔리전스에 대한 정보를 제공합니다.

Azure Sentinel에서 특별 제작 Azure Sentinel 통합 문서를 사용하여 위협 인텔리전스에 대한 주요 정보를 시각화할 수 있으며, 통합 문서를 비즈니스 요구 사항에 따라 쉽게 사용자 지정할 수 있습니다.
Azure Sentinel에서 제공된 위협 인텔리전스 통합 문서를 찾는 방법과 통합 문서를 편집하여 사용자 지정하는 방법의 예시를 살펴봅니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **위협 관리** 섹션에서 **통합 문서** 를 선택합니다.

1. **Threat Intelligence** 라는 통합 문서로 이동하여 아래와 같이 **ThreatIntelligenceIndicator** 테이블에 데이터가 있는지 확인합니다.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="데이터 확인":::

1. **저장** 단추를 선택하고 통합 문서를 저장할 Azure 위치를 선택합니다. 이 단계는 어떤 방식으로든 통합 문서를 수정하고 변경 내용을 저장하려는 경우에 필요합니다.

1. 이제 **저장된 통합 문서 보기** 단추를 선택하여 보기 및 편집을 위해 통합 문서를 엽니다.

1. 이제 템플릿에서 제공하는 기본 차트가 표시됩니다. 차트를 수정하려면 페이지 맨 위에 있는 **편집** 단추를 선택하여 통합 문서 편집 모드로 전환합니다.

1. 위협 유형별 위협 지표라는 새 차트를 추가합니다. 페이지의 아래쪽으로 스크롤하여 **쿼리 추가** 를 선택합니다.

1. **Log Analytics 작업 영역 로그 쿼리** 텍스트 상자에 다음 텍스트를 추가합니다.
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **시각화** 드롭다운에서 **막대형 차트** 를 선택합니다.

1. **편집 완료** 단추를 선택합니다. 통합 문서에 대한 새 차트를 만들었습니다.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="가로 막대형 차트":::

통합 문서는 Azure Sentinel의 모든 측면에 대한 정보를 제공하는 강력한 대화형 대시보드를 제공합니다. 통합 문서를 사용하면 많은 작업을 수행할 수 있으며, 제공된 템플릿이 좋은 출발점이지만, 이러한 템플릿을 사용자 지정하거나, 다양한 데이터 원본을 결합하는 새로운 대시보드를 만들어 데이터를 고유한 방식으로 시각화할 수 있습니다. Azure Sentinel 통합 문서는 Azure Monitor 통합 문서를 기반으로 하기 때문에 이미 광범위한 설명서를 사용할 수 있으며 더 많은 템플릿이 있습니다. [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법에 대한 이 문서에서 시작하는 것이 좋습니다. 

또한 GitHub에는 추가 템플릿을 다운로드하고 자체 템플릿을 제공할 수 있는 활발한 [Azure Monitor 통합 문서](https://github.com/microsoft/Application-Insights-Workbooks) 커뮤니티가 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 전체에서 위협 인텔리전스 지표로 작업할 수 있는 모든 방법을 배웠습니다. Azure Sentinel 위협 인텔리전스에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Sentinel의 위협 인텔리전스 이해](understand-threat-intelligence.md).
- Azure Sentinel을 [STIX/TAXII 위협 인텔리전스 피드](./connect-threat-intelligence-taxii.md)에 연결.
- [위협 인텔리전스 플랫폼을 Azure Sentinel에 연결](./connect-threat-intelligence-tip.md).
- Azure Sentinel과 즉시 통합할 수 있는 [TIP 플랫폼, TAXII 피드 및 보강 기능](threat-intelligence-integration.md).