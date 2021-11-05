---
title: 데이터 민감도에 따라 보안 작업 우선 순위 지정 - Microsoft Defender for Cloud
description: Microsoft Defender for Cloud에서 Azure Purview의 데이터 민감도 분류 사용
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7f82750d9b02bba6e843390562ae2eee060cef47
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479707"
---
# <a name="prioritize-security-actions-by-data-sensitivity"></a>데이터 민감도에 따라 보안 작업 우선 순위 지정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft의 데이터 거버넌스 서비스인 [Azure Purview](../purview/overview.md)는 *데이터 민감도* 에 대한 풍부한 인사이트를 제공합니다. 데이터 검색, 중요한 데이터 분류 및 엔드투엔드 데이터 계보가 자동화된 Purview는 조직에서 하이브리드 및 다중 클라우드 환경의 데이터를 관리하고 제어하는 데 도움이 됩니다.

Azure Purview를 사용하는 Microsoft Defender for Cloud 고객은 경고 및 권장 사항에서 추가적인 중요 메타데이터 계층을 활용하여 중요한 데이터에 대한 정보를 얻을 수 있습니다. 이 정보는 심사 과제를 해결하는 데 도움이 되며 보안 전문가가 중요한 데이터에 대한 위협에 집중할 수 있도록 합니다.

이 페이지에서는 Purview의 데이터 민감도 분류 레이블을 Defender for Cloud 내에 통합하는 방법을 설명합니다.

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|데이터 민감도 분류를 만들고 검사를 실행하려면 Azure Purview 계정이 필요합니다. Defender for Cloud 사용자의 경우 무료로 검사 결과를 살펴보고 출력을 사용할 수 있습니다.|
|필요한 역할 및 권한:|**보안 관리자** 및 **보안 기여자**|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure 중국 21Vianet(**부분**: SQL 서버에 대한 경고 및 취약성 평가의 하위 집합입니다. 동작 위협 보호를 사용할 수 없습니다.)|
|||


## <a name="the-triage-problem-and-defender-for-clouds-solution"></a>심사 문제 및 Defender for Cloud 솔루션
보안 팀은 들어오는 문제의 심사 방법 때문에 주기적으로 어려움을 겪고 있습니다. 

Defender for Cloud는 권장 사항 및 보안 경고의 우선 순위를 지정하는 다음과 같은 두 가지 메커니즘을 갖고 있습니다.

- 권장 사항의 경우 각 권장 사항이 전체 보안 태세에 얼마나 중요한지 이해하는 데 도움이 되는 **보안 컨트롤** 을 제공했습니다. Defender for Cloud에는 보안 작업의 우선 순위를 정하는 데 도움이 되도록 각 컨트롤의 **보안 점수** 값이 포함되어 있습니다. [보안 컨트롤 및 해당 권장 사항](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 자세히 알아보세요.

- 경고의 경우 각 경고에 주의를 기울이는 순서의 우선 순위를 정하는 데 도움이 되도록 각 경고에 **심각도 레이블** 을 할당했습니다. [경고는 어떻게 분류되나요?](alerts-overview.md#how-are-alerts-classified)에서 자세히 알아보세요.

그러나 되도록이면 보안 팀이 조직의 **데이터** 에 대한 위험에 집중하도록 하는 것이 좋습니다. 보안 점수에 동일한 영향을 주는 권장 사항이 2개 있는데 그 중 하나는 중요한 데이터가 포함된 리소스와 관련이 있는 경우 우선 순위를 결정할 때 이 정보를 포함하는 것이 좋습니다.

Azure Purview의 데이터 민감도 분류 및 데이터 민감도 레이블은 이 정보를 제공합니다.

## <a name="discover-resources-with-sensitive-data"></a>중요한 데이터가 포함된 리소스 검색
검색된 중요한 데이터에 대한 중요 정보를 제공하고 필요할 때 해당 정보를 사용할 수 있도록, Defender for Cloud는 Purview의 정보를 여러 위치에 표시합니다.

> [!TIP]
> 한 리소스를 여러 Purview 계정에서 검사하는 경우 가장 최근 검사와 관련된 정보가 Defender for Cloud에 표시됩니다.


### <a name="alerts-and-recommendations-pages"></a>경고 및 권장 사항 페이지
권장 사항을 검토하거나 경고를 조사할 때 관련된 중요 데이터에 대한 정보가 페이지에 포함됩니다.

이 중요한 추가 메타데이터 계층은 심사 과제를 해결하는 데 도움이 되며 보안 팀이 중요한 데이터에 대한 위협에 집중할 수 있도록 합니다.



### <a name="inventory-filters"></a>인벤토리 필터
[자산 인벤토리 페이지](asset-inventory.md)에는 시나리오와 관련된 조건에 따라 해결되지 않은 경고 및 권장 사항과 함께 리소스를 그룹화하는 강력한 필터 컬렉션이 있습니다. 이러한 필터에는 **데이터 민감도 분류** 및 **데이터 민감도 레이블** 은 포함되어 있습니다. 이러한 필터를 사용하여 Purview에서 중요한 데이터를 발견한 리소스의 보안 태세를 평가할 수 있습니다.

:::image type="content" source="./media/information-protection/information-protection-inventory-filters.png" alt-text="Microsoft Defender for Cloud의 [자산 인벤토리 페이지]에 있는 정보 보호 필터의 스크린샷" lightbox="./media/information-protection/information-protection-inventory-filters.png":::

### <a name="resource-health"></a>리소스 상태 
경고, 권장 사항 또는 인벤토리 페이지에서 단일 리소스를 선택하면 해당 리소스와 관련된 중요한 보안 정보가 포함된 리소스 중심 보기를 보여주는 자세한 상태 페이지에 연결됩니다. 

리소스 상태 페이지는 단일 리소스의 전반적인 상태에 대한 스냅샷 보기를 제공합니다. 리소스에 대한 자세한 정보와 해당 리소스에 적용되는 모든 권장 사항을 검토할 수 있습니다. 또한 Microsoft Defender 플랜 중 하나를 사용하는 경우 해당 리소스의 미해결 보안 경고도 볼 수 있습니다.

특정 리소스의 상태를 검토할 때 이 페이지에 Purview 정보가 표시되며, 이 정보를 사용하여 리소스를 검사하는 데 사용된 Purview 계정과 함께 이 리소스에서 발견된 데이터를 확인할 수 있습니다.

:::image type="content" source="./media/information-protection/information-protection-resource-health.png" alt-text="Azure Purview의 정보 보호 레이블 및 분류를 보여주는 Defender for Cloud 리소스 상태 페이지의 스크린샷" lightbox="./media/information-protection/information-protection-resource-health.png":::

### <a name="overview-tile"></a>개요 타일
Defender for Cloud [개요 대시보드](overview-page.md)의 전용 **정보 보호** 타일에는 Azure Purview의 적용 범위가 표시됩니다. 또한 가장 중요한 데이터가 발견된 리소스 종류도 보여줍니다.

분류된 리소스 종류별로 권장 사항 및 경고 수를 표시하는 그래프 이 타일에는 추가 리소스를 검사하는 Azure Purview 링크도 포함되어 있습니다. Defender for Cloud의 자산 인벤토리 페이지에서 분류된 리소스를 보려면 이 타일을 선택합니다.

:::image type="content" source="./media/information-protection/overview-dashboard-information-protection.png" alt-text="Microsoft Defender for Cloud 개요 대시보드에 있는 정보 보호 타일의 스크린샷" lightbox="./media/information-protection/overview-dashboard-information-protection.png":::


## <a name="next-steps"></a>다음 단계

관련 정보는 다음을 참조하세요.

- [Azure Purview란?](../purview/overview.md)
- [Purview에서 지원되는 데이터 원본 및 파일 형식](../purview/sources-and-scans.md)과 [지원되는 데이터 저장소](../purview/purview-connector-overview.md)
- [Azure Purview 배포 모범 사례](../purview/deployment-best-practices.md)
- [Azure Purview에서 데이터에 레이블을 지정하는 방법](../purview/how-to-automatically-label-your-content.md)
