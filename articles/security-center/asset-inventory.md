---
title: 클라우드의 Microsoft Defender의 asset inventory
description: 클라우드로 모니터링 되는 리소스에 대 한 모든 Defender에 대 한 완전 한 가시성을 제공 하는 클라우드 자산 관리 환경을 위한 Microsoft Defender에 대해 알아봅니다.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 10/18/2021
ms.service: security-center
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: cca9811423449692f0e71cdb62ade1447cba86f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010517"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>자산 인벤토리를 사용 하 여 리소스의 보안 상태 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud의 asset inventory 페이지는 클라우드 용 Microsoft Defender에 연결 된 리소스의 보안 상태를 볼 수 있는 단일 페이지를 제공 합니다. 

클라우드에 대 한 Defender는 구독에 연결 된 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다.

리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

이 뷰와 해당 필터를 사용하여 다음과 같은 질문을 해결합니다.

- 향상 된 보안 기능을 사용 하는 내 구독 중에는 해결 가능한 권장 사항이 있나요?
- ‘Production’ 태그가 있는 내 머신 중 Log Analytics 에이전트가 누락된 머신은 무엇인가요?
- 특정 태그를 사용하여 태그가 지정된 내 머신 중 몇 대에 처리되지 않은 권장 사항이 있나요?
- 특정 리소스 그룹에서 알려진 취약성 (CVE 번호 사용)이 있는 컴퓨터는 무엇입니까?

해당 도구에 대한 자산 관리 가능성은 상당히 크며 계속 증가합니다. 

> [!TIP]
> 자산 인벤토리 페이지의 보안 권장 사항은 **권장 사항** 페이지의 권장 사항과 동일하지만 여기서는 영향을 받는 리소스에 따라 표시됩니다. 권장 사항을 해결 하는 방법에 대 한 자세한 내용은 [Microsoft Defender For Cloud에서 보안 권장 사항 구현](review-security-recommendations.md)을 참조 하세요.


## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|*<br>* 인벤토리 페이지의 일부 기능을 무료로 사용할 수 있습니다. 예를 들어 [소프트웨어 인벤토리에](#access-a-software-inventory) 는 유료 솔루션이 준비 되어 있어야 합니다.|
|필요한 역할 및 권한:|모든 사용자가 액세스할 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>자산 인벤토리의 주요 기능은 무엇인가요?
인벤토리 페이지는 다음 도구를 제공합니다.

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Microsoft Defender for Cloud의 asset inventory 페이지의 주요 기능" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 - 요약
필터를 정의하기 전에 인벤토리 보기의 맨 위에 있으며 눈에 띄는 값의 스트립은 다음과 같이 표시됩니다.

- **총 리소스**: 클라우드에 대해 Defender에 연결 된 총 리소스 수입니다.
- **비정상 리소스**: 활성 보안 권장 사항이 있는 리소스입니다. [보안 권장 사항에 대해 자세히 알아보세요](review-security-recommendations.md).
- **모니터링되지 않는 리소스**: 에이전트 모니터링 문제가 있는 리소스, 즉 Log Analytics 에이전트를 배포했지만 에이전트가 데이터를 전송하지 않거나 다른 상태 문제가 있는 리소스입니다.
- **등록** 되지 않은 구독: 선택한 범위에서 클라우드에 대해 Microsoft Defender에 아직 연결 되지 않은 모든 구독입니다.

### <a name="2---filters"></a>2 - 필터
페이지 맨 위의 여러 필터는 답변하려는 질문에 따라 리소스 목록을 빠르게 구체화하는 방법을 제공합니다. 예를 들어 “'Production' 태그가 있는 내 머신 중 Log Analytics 에이전트가 누락된 머신은 무엇인가요?”라는 질문에 답변하려면 **에이전트 모니터링** 필터를 **태그** 필터와 결합할 수 있습니다.

필터를 적용하는 즉시 요약 값은 쿼리 결과와 관련하여 업데이트됩니다. 

### <a name="3---export-and-asset-management-tools"></a>3 - 내보내기 및 자산 관리 도구

**내보내기 옵션** - 인벤토리에는 선택한 필터 옵션의 결과를 CSV 파일로 내보낼 수 있는 옵션이 포함되어 있습니다. 또한 쿼리 자체를 Azure Resource Graph Explorer로 내보내 KQL(Kusto Query Language) 쿼리를 추가로 구체화, 저장 또는 수정할 수 있습니다.

> [!TIP]
> KQL 설명서에서는 언어에 대한 '느낌'을 얻을 수 있도록 샘플 데이터가 포함된 데이터베이스를 몇 가지 간단한 쿼리와 함께 제공합니다. [이 KQL 자습서에서 자세히 알아보세요](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**자산 관리 옵션** - 인벤토리를 사용하면 복잡한 검색 쿼리를 수행할 수 있습니다. 쿼리와 일치하는 리소스를 찾았으면 인벤토리는 다음과 같은 작업에 대한 바로 가기를 제공합니다.

- 필터링된 리소스에 태그 할당 - 태그를 지정할 리소스 옆에 있는 확인란을 선택합니다.
- 클라우드 용 Defender에 새 서버 등록- **비 Azure 서버 추가** 도구 모음 단추를 사용 합니다.
- Azure Logic Apps를 사용하여 워크로드 자동화 - **논리 앱 트리거** 단추를 사용하여 하나 이상의 리소스에서 논리 앱을 실행합니다. 논리 앱을 미리 준비하고 관련 트리거 유형(HTTP 요청)을 수락해야 합니다. [논리 앱에 대해 자세히 알아보세요](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>자산 인벤토리는 어떻게 작동하나요?

Asset inventory는 여러 구독에서 클라우드의 보안 상태 데이터에 대해 Defender를 쿼리 하는 기능을 제공 하는 azure 서비스인 [azure 리소스 Graph (ARG)](../governance/resource-graph/index.yml)를 활용 합니다.

ARG는 대규모로 쿼리를 하는 기능을 갖춘 효율적인 리소스 탐색을 제공하도록 설계되었습니다.

KQL ( [Kusto Query Language)](/azure/data-explorer/kusto/query/)를 사용 하 여 asset inventory는 다른 리소스 속성을 사용 하 여 클라우드 데이터에 대 한 교차 참조 Defender를 통해 심층 통찰력을 신속 하 게 생성할 수 있습니다.


## <a name="how-to-use-asset-inventory"></a>자산 인벤토리를 사용하는 방법

1. 클라우드 용 Defender의 사이드바에서 **인벤토리** 를 선택 합니다.

1. **이름으로 필터링** 상자를 사용하여 특정 리소스를 표시하거나 아래에 설명된 대로 필터를 사용합니다.

1. 필터에서 관련 옵션을 선택하여 수행하려는 특정 쿼리를 생성합니다.

    기본적으로 리소스는 활성 보안 권장 사항 수를 기준으로 정렬됩니다.

    > [!IMPORTANT]
    > 각 필터의 옵션은 현재 선택한 구독의 리소스 **및** 다른 필터에서 선택한 항목에만 적용됩니다.
    >
    > 예를 들어 구독을 하나만 선택하고 구독에 해결되지 않은 보안 권장 사항이 포함된 리소스가 없는 경우(비정상 리소스 0개) **권장 사항** 필터에 옵션이 표시되지 않습니다. 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="클라우드 자산 인벤토리에 대해 Microsoft Defender의 필터 옵션을 사용 하 여 모니터링 되지 않는 프로덕션 리소스에 대 한 리소스 필터링":::

1. **보안 결과 포함** 필터를 사용하려면 취약성 결과의 ID, 보안 검사 또는 CVE 이름에서 자유 텍스트를 입력하여 영향을 받는 리소스를 필터링합니다.

    !['보안 결과 포함' 필터](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **보안 결과 포함** 및 **태그** 필터는 단일 값만 허용합니다. 둘 이상의 값을 사용하려면 **필터 추가** 를 사용합니다.

1. **클라우드 용 Defender** 필터를 사용 하려면 하나 이상의 옵션 (해제, 설정 또는 부분)을 선택 합니다.

    - **외부** -Microsoft Defender 계획으로 보호 되지 않는 리소스입니다. 해당 항목 중 하나를 마우스 오른쪽 단추로 클릭하고 업그레이드할 수 있습니다.

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="마우스 오른쪽 단추를 클릭 하 여 리소스를 Microsoft Defender로 업그레이드 합니다." lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - Microsoft Defender 계획으로 보호 되는 **온** -리소스
    - **부분** -일부 Microsoft Defender 계획을 사용 하지 않도록 설정 하는 **구독** 에 적용 됩니다. 예를 들어 다음 구독에는 7 개의 Microsoft Defender 요금제가 비활성화 되어 있습니다.

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="구독은 Microsoft Defender 계획에 의해 부분적으로 보호 됩니다.":::

1. 쿼리 결과를 자세히 검토하려면 원하는 리소스를 선택합니다.

1. 현재 선택한 필터 옵션을 Resource Graph 탐색기에서 쿼리로 보려면 **쿼리 열기** 를 선택합니다.

    ![ARG의 인벤토리 쿼리](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 일부 필터를 정의 하 고 페이지를 연 경우 Defender for Cloud는 결과를 자동으로 업데이트 하지 않습니다. 수동으로 페이지를 다시 로드하거나 **새로 고침** 을 선택하지 않으면 리소스 변경 내용이 표시된 결과에 영향을 주지 않습니다.

## <a name="access-a-software-inventory"></a>소프트웨어 인벤토리 액세스

Microsoft Defender for Endpoint와의 통합을 사용 하도록 설정 하 고 서버에 대해 Microsoft Defender를 사용 하도록 설정한 경우 소프트웨어 인벤토리에 액세스할 수 있습니다.

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="위협 및 취약성 솔루션을 사용 하도록 설정한 경우 Defender for Cloud의 asset inventory는 설치 된 소프트웨어에 의해 리소스를 선택 하는 필터를 제공 합니다.":::

> [!NOTE]
> "Blank" 옵션은 끝점에 대해 Microsoft Defender가 없는 컴퓨터 (또는 서버에 대 한 Microsoft Defender가 없는)를 표시 합니다.

자산 인벤토리 페이지의 필터는 물론 Azure 리소스 Graph 탐색기에서 소프트웨어 인벤토리 데이터를 탐색할 수 있습니다.

Azure 리소스 Graph 탐색기를 사용 하 여 소프트웨어 인벤토리 데이터에 액세스 하 고 탐색 하는 예제:

1. **Azure Resource Graph Explorer** 를 엽니다.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph Explorer 시작** 권장 사항 페이지" :::

1. 다음 구독 범위를 선택 합니다. securityresources/리소스 인벤토리

1. 다음 쿼리를 입력 하거나 사용자 지정 하거나 직접 작성 하 고 **쿼리 실행** 을 선택 합니다.

    - 설치 된 소프트웨어의 기본 목록을 생성 하려면:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - 버전 번호로 필터링 하려면:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - 소프트웨어 제품을 조합 하 여 컴퓨터를 찾으려면 다음을 수행 합니다.

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - 다른 ASC 권장 사항을 사용 하는 소프트웨어 제품의 조합:

        (이 예제에서는 MySQL을 설치 하 고 관리 포트를 노출 하는 컴퓨터)

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```



## <a name="faq---inventory"></a>FAQ - 인벤토리

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>내 구독, 컴퓨터, 스토리지 계정 등이 모두 표시되지 않는 이유는 무엇인가요?

인벤토리 보기는 CSPM (클라우드 보안 상태 관리) 관점에서 클라우드 연결 리소스에 대 한 Defender를 나열 합니다. 필터는 사용자 환경의 모든 리소스를 반환하는 것이 아니라 해결되지 않은(즉 '활성') 권장 사항이 있는 항목만 반환합니다. 

예를 들어 다음 스크린샷은 8 개 구독에 대 한 액세스 권한이 있는 사용자를 보여 주지만 현재 7 개만 권장 됩니다. 따라서 **리소스 유형 = 구독** 을 기준으로 필터링 하는 경우 활성 권장 사항이 있는 해당 7 개의 구독만 인벤토리에 표시 됩니다.

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="활성 권장 구성이 없는 경우 일부 항목이 반환되지 않음" lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>클라우드 또는 모니터링 에이전트 열에 대해 Defender에서 일부 리소스가 빈 값을 표시 하는 이유는 무엇 인가요?

클라우드 모니터링 리소스의 모든 Defender에 에이전트가 있습니다. 예를 들어 디스크, Logic Apps, Data Lake 분석 및 이벤트 허브와 같은 Azure Storage 계정 또는 PaaS 리소스는 Defender for Cloud에서 모니터링할 에이전트가 필요 하지 않습니다.

가격 책정 또는 에이전트 모니터링이 리소스와 관련이 없는 경우 해당 인벤토리의 열에는 아무것도 표시되지 않습니다.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="일부 리소스는 모니터링 에이전트에서 빈 정보를 표시 하거나 클라우드 열에 대해 Defender를 표시 합니다." lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Cloud의 asset inventory 페이지에 대해 설명 했습니다.

관련 도구에 대한 자세한 내용은 다음 페이지를 참조하세요.

- [ARG(Azure Resource Graph)](../governance/resource-graph/index.yml)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)
