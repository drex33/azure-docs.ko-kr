---
title: 클라우드용 Microsoft Defender의 자산 인벤토리
description: 모든 Defender for Cloud 모니터링 리소스에 대한 완전한 가시성을 제공하는 클라우드용 Microsoft Defender의 자산 관리 경험에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 78843f7478f1df384dce7745cf8a819211a47e81
ms.sourcegitcommit: 024fcf9a76cf238e4bfbccfd699e97cc34fa1f42
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133190420"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>자산 인벤토리를 사용하여 리소스의 보안 태세 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud의 자산 인벤토리 페이지는 Microsoft Defender for Cloud에 연결한 리소스의 보안 상태를 보기 위한 단일 페이지를 제공합니다.

Defender for Cloud는 구독에 연결된 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다.

리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

이 뷰와 해당 필터를 사용하여 다음과 같은 질문을 해결합니다.

- 보안 기능이 강화된 구독 중 미해결 권장 사항이 있는 구독은 무엇인가요?
- ‘Production’ 태그가 있는 내 머신 중 Log Analytics 에이전트가 누락된 머신은 무엇인가요?
- 특정 태그를 사용하여 태그가 지정된 내 머신 중 몇 대에 처리되지 않은 권장 사항이 있나요?
- CVE 번호를 사용하여 알려진 취약성이 있는 특정 리소스 그룹의 머신은 무엇인가요?

해당 도구에 대한 자산 관리 가능성은 상당히 크며 계속 증가합니다.

> [!TIP]
> 자산 인벤토리 페이지의 보안 권장 사항은 **권장 사항** 페이지의 권장 사항과 동일하지만 여기서는 영향을 받는 리소스에 따라 표시됩니다. 권장 사항을 해결하는 방법에 대한 자세한 내용은 [클라우드용 Microsoft Defender에서 보안 권장 사항 구현을 참조하세요.](review-security-recommendations.md)

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|무료 *<br>* [소프트웨어](#access-a-software-inventory) 인벤토리와 같은 인벤토리 페이지의 일부 기능을 사용하려면 유료 솔루션이 있어야 합니다.|
|필요한 역할 및 권한:|모든 사용자가 액세스할 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-key-features-of-asset-inventory"></a>자산 인벤토리의 주요 기능은 무엇인가요?

인벤토리 페이지는 다음 도구를 제공합니다.

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Microsoft Defender for Cloud에 있는 자산 인벤토리 페이지의 주요 기능입니다." lightbox="media/asset-inventory/highlights-of-inventory.png":::

### <a name="1---summaries"></a>1 - 요약

필터를 정의하기 전에 인벤토리 보기의 맨 위에 있으며 눈에 띄는 값의 스트립은 다음과 같이 표시됩니다.

- **총 리소스:** Defender for Cloud에 연결된 총 리소스 수입니다.
- **비정상 리소스**: 활성 보안 권장 사항이 있는 리소스입니다. [보안 권장 사항에 대해 자세히 알아보세요](review-security-recommendations.md).
- **모니터링되지 않는 리소스**: 에이전트 모니터링 문제가 있는 리소스, 즉 Log Analytics 에이전트를 배포했지만 에이전트가 데이터를 전송하지 않거나 다른 상태 문제가 있는 리소스입니다.
- **등록되지 않은 구독:** 선택한 범위에서 Microsoft Defender for Cloud에 아직 연결되지 않은 모든 구독입니다.

### <a name="2---filters"></a>2 - 필터

페이지 맨 위의 여러 필터는 답변하려는 질문에 따라 리소스 목록을 빠르게 구체화하는 방법을 제공합니다. 예를 들어 “'Production' 태그가 있는 내 머신 중 Log Analytics 에이전트가 누락된 머신은 무엇인가요?”라는 질문에 답변하려면 **에이전트 모니터링** 필터를 **태그** 필터와 결합할 수 있습니다.

필터를 적용하는 즉시 요약 값은 쿼리 결과와 관련하여 업데이트됩니다.

### <a name="3---export-and-asset-management-tools"></a>3 - 내보내기 및 자산 관리 도구

**내보내기 옵션** - 인벤토리에는 선택한 필터 옵션의 결과를 CSV 파일로 내보낼 수 있는 옵션이 포함되어 있습니다. 또한 쿼리 자체를 Azure Resource Graph Explorer로 내보내 KQL(Kusto Query Language) 쿼리를 추가로 구체화, 저장 또는 수정할 수 있습니다.

> [!TIP]
> KQL 설명서에서는 언어에 대한 '느낌'을 얻을 수 있도록 샘플 데이터가 포함된 데이터베이스를 몇 가지 간단한 쿼리와 함께 제공합니다. [이 KQL 자습서에서 자세히 알아보세요](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**자산 관리 옵션** - 쿼리와 일치하는 리소스를 찾은 경우 인벤토리는 다음과 같은 작업에 대한 바로 가기를 제공합니다.

- 필터링된 리소스에 태그 할당 - 태그를 지정할 리소스 옆에 있는 확인란을 선택합니다.
- 새 서버를 Defender for Cloud에 온보딩 - **비 Azure 서버 추가** 도구 모음 단추를 사용합니다.
- Azure Logic Apps를 사용하여 워크로드 자동화 - **논리 앱 트리거** 단추를 사용하여 하나 이상의 리소스에서 논리 앱을 실행합니다. 논리 앱을 미리 준비하고 관련 트리거 유형(HTTP 요청)을 수락해야 합니다. [논리 앱에 대해 자세히 알아보세요](../logic-apps/logic-apps-overview.md).

## <a name="how-does-asset-inventory-work"></a>자산 인벤토리는 어떻게 작동하나요?

자산 인벤토리는 여러 구독에서 Defender for Cloud의 보안 태세 데이터를 쿼리하는 기능을 제공하는 Azure 서비스인 [ARG(Azure Resource Graph)를](../governance/resource-graph/index.yml)활용합니다.

ARG는 대규모로 쿼리를 하는 기능을 갖춘 효율적인 리소스 탐색을 제공하도록 설계되었습니다.

[KQL(Kusto 쿼리 언어)을](/azure/data-explorer/kusto/query/)사용하면 자산 인벤토리는 다른 리소스 속성과 함께 클라우드용 Defender 데이터를 상호 참조하여 심도 있는 인사이트를 빠르게 생성할 수 있습니다.

## <a name="how-to-use-asset-inventory"></a>자산 인벤토리를 사용하는 방법

1. Defender for Cloud의 사이드바에서 **인벤토리를** 선택합니다.

1. **이름으로 필터링** 상자를 사용하여 특정 리소스를 표시하거나 아래에 설명된 대로 필터를 사용합니다.

1. 필터에서 관련 옵션을 선택하여 수행하려는 특정 쿼리를 생성합니다.

    기본적으로 리소스는 활성 보안 권장 사항 수를 기준으로 정렬됩니다.

    > [!IMPORTANT]
    > 각 필터의 옵션은 현재 선택한 구독의 리소스 **및** 다른 필터에서 선택한 항목에만 적용됩니다.
    >
    > 예를 들어 구독을 하나만 선택하고 구독에 해결되지 않은 보안 권장 사항이 포함된 리소스가 없는 경우(비정상 리소스 0개) **권장 사항** 필터에 옵션이 표시되지 않습니다.

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="클라우드용 Microsoft Defender 자산 인벤토리의 필터 옵션을 사용하여 모니터링되지 않는 프로덕션 리소스로 리소스 필터링":::

1. **보안 결과 포함** 필터를 사용하려면 취약성 결과의 ID, 보안 검사 또는 CVE 이름에서 자유 텍스트를 입력하여 영향을 받는 리소스를 필터링합니다.

    !['보안 결과 포함' 필터](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **보안 결과 포함** 및 **태그** 필터는 단일 값만 허용합니다. 둘 이상의 값을 사용하려면 **필터 추가** 를 사용합니다.

1. <a id="onoffpartial"></a>**Defender for Cloud** 필터를 사용하려면 하나 이상의 옵션(끄기, 켜기 또는 부분)을 선택합니다.

    - **Off** - Microsoft Defender 플랜으로 보호되지 않는 리소스입니다. 해당 항목 중 하나를 마우스 오른쪽 단추로 클릭하고 업그레이드할 수 있습니다.

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="마우스 오른쪽 단추를 클릭하여 관련 Microsoft Defender 계획으로 보호할 리소스를 업그레이드합니다." lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **켜기** - Microsoft Defender 플랜으로 보호되는 리소스
    - **부분** - 일부 Microsoft Defender 계획이 비활성화된 **구독에** 적용됩니다. 예를 들어 다음 구독에는 7개의 Microsoft Defender 플랜이 비활성화되어 있습니다.

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="구독은 Microsoft Defender 계획에 의해 부분적으로 보호됩니다.":::

1. 쿼리 결과를 자세히 검토하려면 원하는 리소스를 선택합니다.

1. 현재 선택한 필터 옵션을 Resource Graph 탐색기에서 쿼리로 보려면 **쿼리 열기** 를 선택합니다.

    ![ARG의 인벤토리 쿼리](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 일부 필터를 정의하고 페이지를 열어 두면 Defender for Cloud가 결과를 자동으로 업데이트하지 않습니다. 수동으로 페이지를 다시 로드하거나 **새로 고침** 을 선택하지 않으면 리소스 변경 내용이 표시된 결과에 영향을 주지 않습니다.

## <a name="access-a-software-inventory"></a>소프트웨어 인벤토리 액세스

엔드포인트용 Microsoft Defender와의 통합을 사용하도록 설정했고 서버용 Microsoft Defender를 사용하도록 설정한 경우 소프트웨어 인벤토리에 액세스할 수 있습니다.

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="위협 및 취약성 솔루션을 사용하도록 설정한 경우 Defender for Cloud의 자산 인벤토리는 설치된 소프트웨어로 리소스를 선택하는 필터를 제공합니다.":::

> [!NOTE]
> "빈" 옵션은 엔드포인트용 Microsoft Defender가 없거나 서버용 Microsoft Defender가 없는 머신을 표시합니다.

자산 인벤토리 페이지의 필터뿐만 아니라 Azure Resource Graph Explorer에서 소프트웨어 인벤토리 데이터를 탐색할 수 있습니다.

Azure Resource Graph Explorer를 사용하여 소프트웨어 인벤토리 데이터에 액세스하고 탐색하는 예제:

1. **Azure Resource Graph Explorer** 를 엽니다.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph Explorer 시작** 권장 사항 페이지" :::

1. 다음 구독 범위를 선택합니다. securityresources/softwareinventories

1. 다음 쿼리 중 원하는 쿼리를 입력하거나 사용자 지정하거나 직접 작성합니다.) **쿼리 실행을** 선택합니다.

    - 설치된 소프트웨어의 기본 목록을 생성하려면 다음을 수행합니다.

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - 버전 번호로 필터링하려면 다음을 수행합니다.

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - 소프트웨어 제품을 조합하여 컴퓨터를 찾으려면 다음을 수행합니다.

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - 소프트웨어 제품과 다른 보안 권장 사항의 조합:

        (이 예제에서는 MySQL을 설치하고 관리 포트를 노출하는 머신)

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

인벤토리 보기에는 CSPM(Cloud Security Posture Management) 관점에서 클라우드용 Defender 연결 리소스가 나열됩니다. 필터는 사용자 환경의 모든 리소스를 반환하는 것이 아니라 해결되지 않은(즉 '활성') 권장 사항이 있는 항목만 반환합니다.

예를 들어 다음 스크린샷은 8개의 구독에 액세스할 수 있지만 현재 7개만 권장 사항을 가지고 있는 사용자를 보여줍니다. 따라서 **리소스 종류 = 구독으로** 필터링하는 경우 활성 권장 사항이 있는 7개의 구독만 인벤토리에 표시됩니다.

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="활성 권장 구성이 없는 경우 일부 항목이 반환되지 않음" lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>일부 리소스가 Defender for Cloud 또는 모니터링 에이전트 열에 빈 값을 표시하는 이유는 무엇인가요?

모든 Defender for Cloud 모니터링 리소스에 에이전트가 있는 것은 아닙니다. 예를 들어 디스크, Logic Apps, Data Lake Analysis 및 Event Hub와 같은 Azure Storage 계정 또는 PaaS 리소스는 Defender for Cloud에서 모니터링할 에이전트가 필요하지 않습니다.

가격 책정 또는 에이전트 모니터링이 리소스와 관련이 없는 경우 해당 인벤토리의 열에는 아무것도 표시되지 않습니다.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="일부 리소스는 모니터링 에이전트 또는 Defender for Cloud 열에 빈 정보를 표시합니다." lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Cloud의 자산 인벤토리 페이지에 대해 설명했습니다.

관련 도구에 대한 자세한 내용은 다음 페이지를 참조하세요.

- [ARG(Azure Resource Graph)](../governance/resource-graph/index.yml)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)
