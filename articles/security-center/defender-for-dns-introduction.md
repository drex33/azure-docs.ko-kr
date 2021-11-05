---
title: Microsoft Defender for DNS - 이점 및 기능
description: Microsoft Defender for DNS의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00f783b82b9a5b1aa8e6152e513fa91ab3213929
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055866"
---
# <a name="introduction-to-microsoft-defender-for-dns"></a>Microsoft Defender for DNS 소개

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for DNS는 Azure DNS의 [Azure 제공 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 기능을 사용하는 리소스에 대한 추가 보호 계층을 제공합니다. 

Azure DNS 내에서 Defender for DNS는 이러한 리소스의 쿼리를 모니터링하고 리소스에 대한 추가 에이전트 없이 의심스러운 활동을 검색합니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Microsoft Defender for DNS** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 중국 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-dns"></a>Microsoft Defender for DNS의 이점은 무엇인가요?

Microsoft Defender for DNS는 다음과 같은 의심스러운 비정상적인 활동을 검색합니다.

- DNS 터널링을 사용하여 Azure 리소스에서 **데이터 반출**
- 명령 및 제어 서버와 통신하는 **맬웨어**
- **DNS 공격** - 악성 DNS 확인자와의 통신 
- 피싱 및 암호화 마이닝과 같은 **악의적인 활동에 사용되는 도메인과의 통신**

Microsoft Defender for DNS에서 제공하는 경고의 전체 목록은 [경고 참조 페이지](alerts-reference.md#alerts-dns)에 있습니다.

## <a name="dependencies"></a>종속성

Microsoft Defender for DNS는 에이전트를 사용하지 않습니다. 

DNS 계층을 보호하려면 [향상된 보호 사용](enable-enhanced-security.md)에 설명된 대로 각 구독에 대해 Microsoft Defender for DNS를 사용하도록 설정합니다.


## <a name="respond-to-microsoft-defender-for-dns-alerts"></a>Microsoft Defender for DNS에 응답

Microsoft Defender for DNS에서 경고를 수신하면 아래에 설명된 대로 경고를 조사하고 대응하는 것이 좋습니다. Microsoft Defender for DNS는 연결된 모든 리소스를 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.  


### <a name="step-1-contact"></a>1단계. 연락처

1. 리소스 소유자에게 문의하여 동작이 예상된 것인지 또는 의도한 것인지를 확인합니다.
1. 예상된 작업인 경우, 경고를 해제합니다.
1. 작업이 예상되지 않은 경우 다음 단계에 설명된 대로 리소스를 잠재적으로 손상 및 완화될 수 있는 것으로 처리합니다.

### <a name="step-2-immediate-mitigation"></a>2단계. 즉각적인 위협 완화 

1. 측면 이동을 방지하기 위해 네트워크에서 리소스를 격리합니다.
1. 수정 조언에 따라 리소스에서 전체 맬웨어 방지 검사를 실행합니다.
1. 리소스에 설치 및 실행 중인 소프트웨어를 검토하여 알 수 없거나 원치 않는 패키지를 제거합니다.
1. 머신을 알려진 정상 상태로 되돌리고 필요한 경우 운영 체제를 다시 설치하며 맬웨어가 없는 확인된 소스에서 소프트웨어를 복원합니다.
1. 머신에 대한 모든 Microsoft Defender for Cloud 권장 사항을 해결하고, 강조된 보안 문제를 수정하여 향후 위반을 방지합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for DNS에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [향상된 보호 사용](enable-enhanced-security.md)

관련 자료는 다음 문서를 참조하세요. 

- 보안 경고는 Defender for Cloud에서 생성되거나 다른 보안 제품에서 수신될 수 있습니다. 이러한 모든 경고를 Microsoft Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.
