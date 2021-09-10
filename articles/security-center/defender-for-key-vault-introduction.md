---
title: Azure Defender for Key Vault - 이점 및 특징
description: Azure Defender for Key Vault의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 0b14b30e5ea749b53819d94fc69ff7f3d2eee0b4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743119"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Azure Defender for Key Vault 소개

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

추가 보안 인텔리전스 계층을 제공하는 Azure Key Vault용 Azure 네이티브 Advanced Threat Protection 기능을 사용하려면 **Azure Defender for Key Vault** 를 사용하도록 설정합니다. 

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Azure Defender for Key Vault** 는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Azure Defender for Key Vault의 이점은?

Azure Defender는 Key Vault 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. 이 보호 계층을 사용하면 보안 전문가가 아니거나 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 처리할 수 있습니다.  

비정상 활동이 발생하면 Azure Defender에서 경고를 표시하고 필요에 따라 이메일을 통해 조직의 관련 구성원에게 보냅니다. 이러한 경고에는 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 완화하는 방법에 대한 추천 사항이 포함됩니다. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender for Key Vault 경고
Azure Defender for Key Vault에서 경고를 수신하면 [Azure Defender for Key Vault에 대응](defender-for-key-vault-usage.md)에 설명된 대로 경고를 조사하고 대응하는 것이 좋습니다. Azure Defender for Key Vault는 애플리케이션 및 자격 증명을 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.

경고는 Key Vault의 **보안** 페이지, Azure Defender 대시보드 및 Security Center의 경고 페이지에 표시됩니다.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault의 보안 페이지":::


> [!TIP]
> [Azure Security Center에서 Azure Key Vault 위협 탐지의 유효성 검사](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)의 지침에 따라 Azure Defender for Key Vault 경고를 시뮬레이션할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for Key Vault에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- [Key Vault 보안 경고](alerts-reference.md#alerts-azurekv) - 모든 Azure Security Center 경고에 대한 참조 테이블의 Key Vault 섹션
- [Security Center 데이터 연속 내보내기](continuous-export.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)