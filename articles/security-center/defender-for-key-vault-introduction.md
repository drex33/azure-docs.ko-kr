---
title: Key Vault용 Azure Defender - 이점 및 특징
description: Key Vault용 Azure Defender의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions, ignite-fall-2021
manager: rkarlin
ms.openlocfilehash: bec034539b7969a7552952eddd6d7673e35ebcaf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055917"
---
# <a name="introduction-to-microsoft-defender-for-key-vault"></a>Key Vault용 Azure Defender 소개

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure Key Vault에 대한 Azure 네이티브 고급 위협 보호를 위해 **Key Vault용 Microsoft Defender** 를 사용하여 보안 인텔리전스의 추가 계층을 제공합니다. 

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Key Vault용 Azure Defender** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-key-vault"></a>Key Vault용 Microsoft Defender의 이점은 무엇인가요?

Microsoft Defender는 Key Vault 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. 이 보호 계층을 사용하면 보안 전문가가 아니거나 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 처리할 수 있습니다.  

비정상 활동이 발생하면 Microsoft Defender에서 경고를 표시하고 필요에 따라 이메일을 통해 조직의 관련 구성원에게 보냅니다. 이러한 경고에는 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 완화하는 방법에 대한 추천 사항이 포함됩니다. 

## <a name="microsoft-defender-for-key-vault-alerts"></a>Key Vault용 Microsoft Defender 경고
Key Vault용 Microsoft Defender에서 경고를 수신하면 [Key Vault용 Microsoft Defender에 대응](defender-for-key-vault-usage.md)에 설명된 대로 경고를 조사하고 대응하는 것이 좋습니다. Key Vault용 Microsoft Defender는 애플리케이션 및 자격 증명을 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.

경고는 Key Vault의 **보안** 페이지, 워크로드 보호 및 Defender for Cloud의 경고 페이지에 나타납니다.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault의 보안 페이지":::


> [!TIP]
> [Key Vault용 Microsoft Defender에서 Azure Key Vault 위협 탐지의 유효성 검사](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)의 지침에 따라 Key Vault용 Microsoft Defender 경고를 시뮬레이션할 수 있습니다.


## <a name="respond-to-microsoft-defender-for-key-vault-alerts"></a>Key Vault용 Microsoft Defender 경고에 대응
[Key Vault용 Microsoft Defender](defender-for-key-vault-introduction.md)에서 경고를 수신하면 아래에 설명된 대로 경고를 조사하고 대응하는 것이 좋습니다. Key Vault용 Microsoft Defender는 애플리케이션 및 자격 증명을 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.  

Key Vault용 Microsoft Defender의 경고에는 다음 요소가 포함됩니다.

- 개체 ID입니다.
- 의심스러운 리소스의 사용자 계정 이름 또는 IP 주소 

발생한 액세스 *유형* 에 따라 일부 필드를 사용하지 못할 수 있습니다. 예를 들어 애플리케이션에서 Key Vault에 액세스한 경우에는 연결된 사용자 계정 이름이 표시되지 않습니다. 트래픽이 Azure 외부에서 시작된 경우에는 개체 ID가 표시되지 않습니다.

> [!TIP]
> Azure 가상 머신에는 Microsoft IP가 할당됩니다. 이는 경고가 Microsoft 외부에서 수행된 활동과 관련이 있더라도 Microsoft IP를 포함할 수 있음을 의미합니다. 따라서 경고에 Microsoft IP가 있더라도 이 페이지에 설명된 대로 조사해야 합니다.

### <a name="step-1-identify-the-source"></a>1단계. 원본 식별

1. 트래픽이 Azure 테넌트 내에서 시작되었는지를 확인합니다. 키 자격 증명 모음 방화벽이 활성화된 경우, 경고를 트리거한 사용자 또는 애플리케이션에 대한 액세스를 제공했을 가능성이 큽니다.
1. 트래픽 소스를 확인할 수 없으면 [2단계. 적절하게 대응](#step-2-respond-accordingly)을 계속합니다.
1. 테넌트에서 트래픽의 소스를 식별할 수 있는 경우, 애플리케이션의 사용자 또는 소유자에게 문의하세요. 

> [!CAUTION]
> Key Vault용 Microsoft Defender는 도난당한 자격 증명으로 인한 의심스러운 활동을 식별하는 데 유용하도록 설계되었습니다. 사용자 또는 애플리케이션을 인식하기 때문에 경고를 해제하지 **마십시오**. 애플리케이션의 소유자 또는 사용자에게 문의하여 합법적인 활동인지 확인합니다. 필요에 따라 노이즈를 제거하는 제거 규칙을 만들 수 있습니다. 자세한 내용은 [보안 경고 표시 안 함](alerts-suppression-rules.md)에서 자세히 알아보세요.


### <a name="step-2-respond-accordingly"></a>2단계. 적절하게 대응 
사용자 또는 애플리케이션을 인식할 수 없거나 액세스 권한이 부여되지 않은 것으로 생각되는 경우, 다음을 수행합니다.

- 트래픽이 인식할 수 없는 IP 주소에서 발생한 경우 다음을 수행합니다.
    1. [Azure Key Vault 방화벽 및 가상 네트워크 구성](../key-vault/general/network-security.md)에 설명된 대로 Azure Key Vault 방화벽을 사용하도록 설정합니다.
    1. 신뢰할 수 있는 리소스 및 가상 네트워크를 사용하여 방화벽을 구성합니다.

- 경고의 소스가 권한이 없는 애플리케이션 또는 의심스러운 사용자인 경우, 다음을 수행합니다.
    1. 키 자격 증명 모음의 액세스 정책 설정을 엽니다.
    1. 해당 보안 주체를 제거하거나 보안 주체에서 수행할 수 있는 작업을 제한합니다.  

- 경고의 소스에 테넌트의 Azure Active Directory 역할이 있는 경우, 다음을 수행합니다.
    1. 관리자에게 문의하십시오.
    1. Azure Active Directory의 사용 권한을 줄이거나 취소해야 하는지를 확인합니다.

### <a name="step-3-measure-the-impact"></a>3단계: 영향 측정
이벤트가 완화되면 영향을 받은 키 자격 증명 모음의 비밀을 조사합니다.
1. Azure Key Vault에서 **보안** 페이지를 열고 트리거된 경고를 확인합니다.
1. 트리거된 특정 경고를 선택합니다.
    액세스된 비밀 및 타임스탬프 목록을 검토합니다.
1. 키 자격 증명 모음의 진단 로그가 사용하도록 설정된 경우, 필요에 따라 해당 호출자 IP, 사용자 계정 또는 개체 ID에 대한 이전 작업을 검토합니다.  

### <a name="step-4-take-action"></a>4단계. 작업 수행 
의심스러운 사용자 또는 애플리케이션이 액세스한 비밀, 키 및 인증서 목록을 컴파일하는 경우 해당 개체를 즉시 회전해야 합니다.

1. 영향을 받은 비밀은 키 자격 증명 모음에서 사용하지 않도록 설정하거나 삭제해야 합니다.
1. 특정 애플리케이션에서 자격 증명이 사용된 경우, 다음과 같이 수행합니다.
    1. 애플리케이션의 관리자에게 손상된 자격 증명이 손상된 후 해당 자격 증명을 사용하는 환경에 대한 감사를 요청합니다.
    1. 손상된 자격 증명이 사용된 경우, 애플리케이션 소유자는 액세스한 정보를 식별하여 영향을 완화해야 합니다.





## <a name="next-steps"></a>다음 단계

이 문서에서는 Key Vault용 Microsoft Defender에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- [Key Vault 보안 경고](alerts-reference.md#alerts-azurekv) - 모든 클라우드용 Microsoft Defender 경고에 대한 참조 테이블의 Key Vault 섹션
- [Defender for Cloud 데이터를 지속적으로 내보내기](continuous-export.md)
- [보안 경고 표시 안 함](alerts-suppression-rules.md)
