---
title: 연속 내보내기는 클라우드 경고 및 권장 사항에 대 한 Microsoft Defender를 Log Analytics 작업 영역 또는 Azure에 보낼 수 있습니다 Event Hubs
description: Log Analytics 작업 영역 또는 Azure Event Hubs 대한 보안 경고와 권장 사항의 연속 내보내기를 구성하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/18/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1557503699a47e471abee173733007cb3d0b5380
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010159"
---
# <a name="continuously-export-microsoft-defender-for-cloud-data"></a>클라우드 데이터를 위해 지속적으로 Microsoft Defender 내보내기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud는 자세한 보안 경고 및 권장 사항을 생성 합니다. 관련 내용은 포털에서 또는 프로그래밍 도구를 통해 볼 수 있습니다. 사용자 환경에서 다른 모니터링 도구를 사용해 추적하기 위해서는 이 정보의 일부 또는 전부 내보내야 할 수도 있습니다. 

**연속 내보내기** 를 사용하면 내보낼 *대상* 과 *위치* 를 완전히 사용자 지정할 수 있습니다. 예를 들면 다음과 같이 구성할 수 있습니다.

- 높은 심각도 경고를 모두 Azure Event Hub로 보내도록 구성
- SQL 서버의 취약성 평가 검사에서 발견된 중간 이상의 심각도 결과를 모두 특정 Log Analytics 작업 영역으로 보내도록 구성
- 특정 권장 사항이 생성될 때마다 Event Hub 또는 Log Analytics 작업 영역으로 전달되도록 구성 
- 제어 점수가 0.01 이상으로 변경될 때마다 구독의 보안 점수를 Log Analytics 작업 영역으로 보내도록 구성 

이 기능을 *연속* 이라고 일컫지만, 보안 점수 또는 규정 준수 데이터의 주간 스냅샷을 내보내는 옵션도 있습니다.

이 문서에서는 Log Analytics 작업 영역 또는 Azure Event Hubs로 연속 내보내기를 구성하는 방법을 설명합니다.

> [!NOTE]
> Defender for Cloud를 SIEM과 통합 해야 하는 경우 [siem, 대화 충성도 또는 IT 서비스 관리 솔루션에 대 한 경고 스트림](export-to-siem.md)을 참조 하세요.

> [!TIP]
> 또한 Defender for Cloud는 CSV로 일회성 수동 내보내기를 수행 하는 옵션을 제공 합니다. [경고 및 권장 사항을 수동으로 한 번 내보내기](#manual-one-time-export-of-alerts-and-recommendations)에서 자세히 알아보세요.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|Free|
|필요한 역할 및 권한:|<ul><li>리소스 그룹의 **보안 관리자** 또는 **소유자**</li><li>대상 리소스에 대한 쓰기 권한</li><li>아래에 설명된 Azure Policy 'DeployIfNotExist' 정책을 사용하는 경우 정책을 할당하기 위한 권한도 필요합니다.</li><li>이벤트 허브로 데이터를 내보내려면 이벤트 허브 정책에 대한 쓰기 권한이 필요합니다.</li><li>Log Analytics 작업 영역으로 내보내려면 다음을 수행합니다.<ul><li>**SecurityCenterFree 솔루션이 있는** 경우 최소한 작업 영역 솔루션에 대한 읽기 권한이 필요합니다.`Microsoft.OperationsManagement/solutions/read`</li><li>**SecurityCenterFree 솔루션이 없는** 경우 작업 영역 솔루션에 대한 쓰기 권한이 필요합니다.`Microsoft.OperationsManagement/solutions/action`</li><li>[Azure Monitor 및 Log Analytics 작업 영역 솔루션](../azure-monitor/insights/solutions.md)에 대해 자세히 알아보세요.</li></ul></li></ul>|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)| 
|||


## <a name="what-data-types-can-be-exported"></a>내보낼 수 있는 데이터 형식

연속 내보내기에서는 다음의 데이터 형식이 변경될 때마다 이를 내보낼 수 있습니다.

- 보안 경고.
- 보안 권장 사항
- 보안 결과. 이는 '하위' 권장 사항으로 간주되며 '상위' 권장 사항에 속할 수 있습니다. 예를 들면 다음과 같습니다.
    - "시스템 업데이트를 컴퓨터에 설치해야 합니다."라는 권장 사항에는 모든 미해결 시스템 업데이트에 대한 '하위' 권장 사항이 포함됩니다.
    - "가상 컴퓨터의 취약성을 교정해야 합니다."라는 권장 사항에는 취약성 스캐너로 식별된 모든 취약성에 대한 '하위' 권장 사항이 포함됩니다.
    > [!NOTE]
    > REST API로 연속 내보내기를 구성하는 경우 항상 결과에 상위 항목을 포함해야 합니다. 
- 구독당 또는 제어당 보안 점수
- 규정 준수 데이터


## <a name="set-up-a-continuous-export"></a>연속 내보내기 설정 

제공 된 Azure Policy 템플릿을 사용 하 여 Azure Portal의 클라우드 페이지에 대해 REST API를 통해 또는 대규모로 연속 내보내기를 구성할 수 있습니다. 각각에 대한 자세한 내용을 보려면 아래에서 해당하는 탭을 선택하세요.

### <a name="use-the-azure-portal"></a>[**Azure Portal 사용**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-defender-for-cloud-pages-in-azure-portal"></a>Azure Portal의 클라우드 페이지에 대해 Defender에서 연속 내보내기 구성

Log Analytics 작업 영역 또는 Azure Event Hubs로 연속 내보내기를 설정하는지 여부에 상관없이 아래의 단계가 필요합니다.

1. 클라우드 용 Defender의 메뉴에서 **환경 설정** 을 선택 합니다.

1. 데이터 내보내기를 구성할 특정 구독을 선택합니다.

1. 해당 구독에 대한 설정 페이지의 사이드바에서 **연속 내보내기** 를 선택합니다.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="클라우드의 Microsoft Defender에서 옵션을 내보냅니다.":::

    여기에 내보내기 옵션이 표시됩니다. 각각 사용 가능한 내보내기 대상 탭이 있습니다. 

1. 내보내려는 데이터 형식을 선택하고, 각 형식의 필터에서 선택합니다(예: 높은 심각도 경고만 내보내기).

1. 적절한 내보내기 빈도를 선택합니다.
    - **스트리밍** – 리소스의 성능 상태가 업데이트되면 평가를 보냅니다(업데이트되지 않으면 데이터를 보내지 않음).
    - **스냅샷** – 모든 규정 준수 평가의 현재 상태 스냅샷은 구독당 일주일에 한 번 전송됩니다. 이 미리 보기 기능은 보안 점수 및 규정 준수 데이터의 주간 스냅샷을 제공합니다. 스냅샷 데이터를 식별하려면 필드 ``IsSnapshot``을 찾습니다.

1. 필요한 경우 선택 항목에 이러한 권장 사항 중 하나가 포함되어 있으면 취약성 평가 결과를 함께 포함할 수 있습니다.
    - SQL 데이터베이스가 발견한 취약성을 해결해야 함
    - 컴퓨터의 SQL Server는 발견한 취약성을 해결해야 함
    - Azure Container Registry 이미지의 취약성을 수정해야 함(Qualys 제공)
    - 가상 머신의 취약성을 수정해야 함
    - 시스템 업데이트를 머신에 설치해야 합니다.

    이러한 권장 사항과 함께 결과를 포함하려면 **보안 결과 포함** 옵션을 사용합니다.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="연속 내보내기 구성의 보안 결과 포함 설정/해제" :::

1. ‘대상 내보내기’ 영역에서 데이터를 저장할 위치를 선택합니다. 데이터는 다른 구독(예: 중앙 Event Hub 인스턴스 또는 중앙 Log Analytics 작업 영역)의 대상에 저장할 수 있습니다.
1. **저장** 을 선택합니다.

### <a name="use-the-rest-api"></a>[**REST API 사용**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>REST API 사용하여 연속 내보내기 구성

Microsoft Defender for Cloud [자동화 API](/rest/api/securitycenter/automations)를 통해 연속 내보내기를 구성 하 고 관리할 수 있습니다. 이 API를 사용하여 다음의 가능한 대상으로 내보내기 위한 규칙을 만들거나 업데이트합니다.

- Azure Event Hub
- Log Analytics 작업 영역
- Azure Logic Apps 

API는 Azure Portal에서 사용할 수 없는 추가 기능을 제공합니다. 예를 들면 다음과 같습니다.

* **더 많은 볼륨** - API를 사용하면 단일 구독에 내보내기 구성을 여러 개 만들 수 있습니다. 클라우드의 포털 UI에 대 한 Defender의 **연속 내보내기** 페이지는 구독 당 하나의 내보내기 구성만 지원 합니다.

* **추가 기능** - API는 UI에 표시되지 않은 추가 매개 변수를 제공합니다. 예를 들어 automation 리소스에 태그를 추가 하 고, 클라우드의 포털 UI에 대 한 Defender의 **연속 내보내기** 페이지에서 제공 되는 것 보다 광범위 한 경고 및 권장 사항 속성 집합을 기반으로 내보내기를 정의할 수 있습니다.

* **더 집중된 범위** - API는 내보내기 구성 범위에 보다 세분화된 수준을 제공합니다. API로 내보내기를 정의할 때 리소스 그룹 수준에서 정의할 수 있습니다. 클라우드의 포털 UI에 대해 Defender에서 **연속 내보내기** 페이지를 사용 하는 경우 구독 수준에서 정의 해야 합니다.

    > [!TIP]
    > API를 사용 하 여 여러 내보내기 구성을 설정 했거나 API 전용 매개 변수를 사용한 경우 Defender for Cloud UI에 이러한 추가 기능이 표시 되지 않습니다. 대신 다른 구성이 있음을 알려 주는 배너가 표시됩니다.

[REST API 설명서](/rest/api/securitycenter/automations)에서 자동화 API에 대해 자세히 알아보세요.

### <a name="deploy-at-scale-with-azure-policy"></a>[**Azure Policy를 사용하여 대규모로 배포**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>제공된 정책을 사용하여 대규모로 연속 내보내기 구성

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

조직 전체에 연속 내보내기 구성을 배포하려면 아래에 설명된 대로 제공된 Azure Policy 'DeployIfNotExist' 정책을 사용하여 연속 내보내기 절차를 만들고 구성합니다.

**이러한 정책을 구현하는 방법**

1. 아래의 표에서 적용하려는 정책을 선택합니다.

    |목표  |정책  |정책 ID  |
    |---------|---------|---------|
    |이벤트 허브로 연속 내보내기|[클라우드용 Microsoft Defender용 Event Hub에 내보내기 배포 경고 및 권장 사항](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Log Analytics 작업 영역으로 연속 내보내기|[클라우드용 Microsoft Defender 경고 및 권장 사항에 대한 Log Analytics 작업 영역으로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > 또한 Azure Policy를 검색하여 찾을 수 있습니다.
    > 1. Azure Policy를 엽니다.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Azure Policy에 액세스":::
    > 2. Azure Policy 메뉴에서 **정의** 를 선택하고 이름으로 검색합니다. 

1. 관련 Azure Policy 페이지에서 **할당** 을 선택합니다.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Azure Policy 할당":::

1. 각 탭을 열고 원하는 대로 매개 변수를 설정합니다.
    1. **기본** 탭에서 정책 범위를 설정합니다. 중앙 집중식 관리를 사용하려면 연속 내보내기 구성을 사용하는 구독을 포함한 관리 그룹에 정책을 할당합니다. 
    1. **매개 변수** 탭에서 리소스 그룹 및 데이터 형식 세부 정보를 설정합니다. 
        > [!TIP]
        > 각 매개 변수에는 제공되는 옵션을 알려 주는 도구 설명이 있습니다.
        >
        > Azure Policy 매개 변수 탭(1)은 Defender for Cloud의 연속 내보내기 페이지(2)와 유사한 구성 옵션에 대한 액세스를 제공합니다.
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Azure Policy와 연속 내보내기의 매개 변수 비교" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. 필요한 경우 이 할당을 기존 구독에 적용하려면 **수정** 탭을 열고 수정 작업을 만드는 옵션을 선택합니다.
1. 요약 페이지를 검토하고 **만들기** 를 선택합니다.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역으로 내보내기에 대한 정보

Log Analytics 작업 영역 내에서 클라우드용 Microsoft Defender 데이터를 분석하거나 Defender for Cloud 경고와 함께 Azure 경고를 사용하려면 Log Analytics 작업 영역으로 연속 내보내기를 설정합니다.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 테이블 및 스키마

보안 경고 및 권장 사항은 *SecurityAlert* 및 *SecurityRecommendation* 테이블에 각각 저장됩니다. 

이러한 테이블을 포함하는 Log Analytics 솔루션의 이름은 보안('보안 및 감사') 또는 SecurityCenterFree와 같은 향상된 보안 기능을 사용하도록 설정했는지 여부에 따라 달라집니다. 

> [!TIP]
> 대상 작업 영역에서 데이터를 보려면 이러한 솔루션, 즉 **보안 및 감사** 또는 **SecurityCenterFree** 중 하나를 사용해야 합니다.

![Log Analytics의 *SecurityAlert* 테이블](./media/continuous-export/log-analytics-securityalert-solution.png)

내보낸 데이터 형식의 이벤트 스키마를 보려면 [Log Analytics 테이블 스키마](https://aka.ms/ASCAutomationSchemas)를 참조하세요.


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Azure Monitor에서 내보낸 경고 및 권장 사항 보기

[Azure Monitor](../azure-monitor/alerts/alerts-overview.md)에서 내보낸 보안 경고 및/또는 권장 사항을 보도록 선택할 수도 있습니다. 

Azure Monitor는 진단 로그, 메트릭 경고 및 Log Analytics 작업 영역 쿼리를 기반으로 하는 사용자 지정 경고 등 다양한 Azure 경고에 대해 통합된 경고 환경을 제공합니다.

Azure Monitor Defender for Cloud의 경고 및 권장 사항을 보려면 Log Analytics 쿼리(로그 경고)를 기반으로 경고 규칙을 구성합니다.

1. Azure Monitor의 **경고** 페이지에서 **새 경고 규칙** 을 선택합니다.

    ![Azure Monitor의 경고 페이지](./media/continuous-export/azure-monitor-alerts.png)

1. 규칙 만들기 페이지에서 새 규칙을 구성합니다([Azure Monitor에서 로그 경고 규칙](../azure-monitor/alerts/alerts-unified-log.md)을 구성하는 것과 같은 방식).

    * **리소스** 에서 보안 경고 및 권장 사항을 내보낸 Log Analytics 작업 영역을 선택합니다.

    * **조건** 에서 **사용자 지정 로그 검색** 을 선택합니다. 표시되는 페이지에서 쿼리, 되돌아보기 기간 및 빈도 기간을 구성합니다. 검색 쿼리에서 *SecurityAlert* 또는 *SecurityRecommendation을* 입력하여 Log Analytics로 연속 내보내기 기능을 사용하도록 설정할 때 Defender for Cloud가 지속적으로 내보내는 데이터 형식을 쿼리할 수 있습니다. 
    
    * 필요한 경우 트리거할 [작업 그룹](../azure-monitor/alerts/action-groups.md)을 구성합니다. 작업 그룹은 이메일 보내기, ITSM 티켓, WebHook 등을 트리거할 수 있습니다.
    ![Azure Monitor 경고 규칙](./media/continuous-export/azure-monitor-alert-rule.png)

이제 작업 그룹(제공된 경우)의 자동 트리거와 함께 Azure Monitor 경고에 새 Microsoft Defender for Cloud 경고 또는 권장 사항(구성된 연속 내보내기 규칙 및 Azure Monitor 경고 규칙에서 정의한 조건에 따라)이 표시됩니다.

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>경고 및 권장 사항을 수동으로 한 번 내보내기

경고 또는 권장 사항에 대한 CSV 보고서를 다운로드하려면 **보안 경고** 또는 **권장 사항** 페이지를 열고 **CSV 보고서 다운로드** 단추를 선택합니다.

> [!TIP]
> Azure 리소스 Graph 제한으로 인해 보고서는 13K 행의 파일 크기로 제한됩니다. 너무 많은 데이터를 내보내는 것과 관련된 오류가 표시되는 경우 내보낼 더 작은 구독 집합을 선택하여 출력을 제한해 보세요.

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="CSV 파일로 경고 데이터 다운로드" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> 이러한 보고서에는 현재 선택한 구독의 리소스에 대한 경고 및 권장 사항이 포함되어 있습니다.


## <a name="faq---continuous-export"></a>FAQ - 연속 내보내기

### <a name="what-are-the-costs-involved-in-exporting-data"></a>데이터를 내보내는 데 드는 비용은 얼마인가요?

연속 내보내기를 사용하기 위한 비용은 없습니다. 사용자의 구성에 따라 Log Analytics 작업 영역에서 데이터를 수집하고 보존하는 데 비용이 발생할 수 있습니다. 

[Log Analytics 작업 영역 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아보세요.

[Azure Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)에 대해 자세히 알아보세요.


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>내보내기에 모든 리소스의 현재 상태에 대한 데이터가 포함되나요?

아니요. 연속 내보내기는 **이벤트** 스트리밍용으로 빌드되었습니다.

- 내보내기를 사용하기 전에 받은 **경고** 는 내보내지 않습니다.
- **권장 사항** 은 리소스의 준수 상태가 변경될 때마다 전송됩니다. 예로 리소스가 정상에서 비정상으로 전환되는 경우를 들 수 있습니다. 따라서 경고와 마찬가지로 내보내기를 사용한 이후 상태를 변경하지 않은 리소스에 대한 권장 사항은 내보내지 않습니다.
- 보안 제어 또는 구독당 **보안 점수** 는 보안 제어의 점수가 0.01 이상으로 변경될 때 전송됩니다. 
- **규정 준수 상태** 는 리소스의 준수 상태가 변경될 때 전송됩니다.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>권장 사항이 다른 간격으로 전송되는 이유는 무엇인가요?

권장 사항마다 준수 평가 간격이 다르며, 몇 분부터 며칠 간격까지 다양할 수 있습니다. 결과적으로 권장 사항이 내보내기에 표시되는 데 걸리는 시간이 다릅니다.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>연속 내보내기는 BCDR(비즈니스 연속성 또는 재해 복구) 시나리오를 지원하나요?

대상 리소스에 중단 또는 기타 재해가 발생하는 BCDR 시나리오의 환경에 대비하는 경우 Azure Event Hubs, Log Analytics 작업 영역 및 Logic App의 지침에 따라 백업을 설정하여 데이터 손실을 방지하는 것은 조직의 책임입니다.

[Azure Event Hubs - 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md)에 대해 자세히 알아보세요.


### <a name="is-continuous-export-available-for-free"></a>연속 내보내기는 무료로 사용할 수 있나요?

예 많은 경고는 고급 보호를 사용하도록 설정한 경우에만 제공됩니다. 내보낸 데이터에서 얻을 수 있는 경고를 미리 보는 좋은 방법은 Azure Portal Defender for Cloud의 페이지에 표시된 경고를 보는 것입니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 권장 사항 및 경고의 연속 내보내기를 구성하는 방법을 알아보았습니다. 또한 경고 데이터를 CSV 파일로 다운로드하는 방법을 알아보았습니다. 

관련 자료는 다음 문서를 참조하세요. 

- [워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)에 대해 자세히 알아보세요.
- [Azure Event Hubs 설명서](../event-hubs/index.yml)
- [Microsoft Sentinel 설명서](../sentinel/index.yml)
- [Azure Monitor 설명서](../azure-monitor/index.yml)
- [데이터 형식 스키마 내보내기](https://aka.ms/ASCAutomationSchemas)
