---
title: Azure Defender for Resource Manager - 이점 및 기능
description: Azure Defender for Resource Manager의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 07/14/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 38eeaa02bf34f27580b29662f75455358c83ccb7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732565"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager 소개

[Azure Resource Manager](../azure-resource-manager/management/overview.md)는 Azure용 배포 및 관리 서비스입니다. Azure 계정에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 배포 이후 액세스 제어, 잠금 및 태그와 같은 관리 기능을 사용하여 리소스를 보호하고 구성합니다.

클라우드 관리 계층은 모든 클라우드 리소스에 연결된 중요한 서비스입니다. 이로 인해 공격자의 잠재적 대상이 될 수도 있습니다. 따라서 보안 운영 팀이 리소스 관리 계층을 면밀히 모니터링하는 것이 좋습니다. 

Azure Defender for Resource Manager는 Azure Portal, Azure REST API, Azure CLI 또는 다른 Azure 프로그래밍 클라이언트를 통해 수행되는지 여부와 관계없이 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 고급 보안 분석을 실행하여 위협을 감지하고 의심스러운 활동에 대해 경고합니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Azure Defender for Resource Manager** 는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 중국 21Vianet|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager의 이점은?

Azure Defender for Resource Manager는 다음을 비롯한 문제를 방지합니다.

- **의심스러운 리소스 관리 작업**(예: 악성 IP 주소에서의 작업, 맬웨어 방지 및 VM 확장에서 실행되는 의심스러운 스크립트 비활성화)
- Microburst 또는 PowerZure와 같은 **악용 도구 키트 사용**
- Azure 관리 계층에서 Azure 리소스 데이터 평면으로의 **측면 이동**

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager 개요 다이어그램.":::

Azure Defender for Resource Manager에서 제공하는 경고의 전체 목록은 [경고 참조 페이지](alerts-reference.md#alerts-resourcemanager)에 있습니다.


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager에서 경고를 조사하는 방법

Azure Defender for Resource Manager의 보안 경고는 Azure Resource Manager 작업을 모니터링하여 감지된 위협을 기반으로 합니다. Azure Defender는 Azure Resource Manager의 내부 로그 원본과 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그인 Azure 활동 로그를 사용합니다.

[Azure 활동 로그](../azure-monitor/essentials/activity-log.md)에 대해 자세히 알아보세요.

Azure Defender for Resource Manager에서 경고를 조사하려면 다음을 수행합니다.

1. Azure 활동 로그를 엽니다.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Azure 활동 로그를 여는 방법.":::

1. 다음과 같이 이벤트를 필터링합니다.
    - 경고에 언급된 구독
    - 검색된 작업의 기간
    - 관련 사용자 계정(관련된 경우)

1. 의심스러운 활동을 찾습니다.

> [!TIP]
> 더 풍부하고 다양한 조사 환경을 위해 [Azure 활동 로그에서 데이터 연결](../sentinel/connect-azure-activity.md)에 설명된 대로 Azure 활동 로그를 Azure Sentinel로 스트리밍합니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for Resource Manager에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](enable-azure-defender.md)

관련 자료는 다음 문서를 참조하세요. 

- 보안 경고는 Security Center에서 생성되거나 Security Center에서 다른 보안 제품으로부터 수신할 수 있습니다. 이러한 모든 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.