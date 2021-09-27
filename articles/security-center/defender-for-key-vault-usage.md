---
title: Key Vault용 Azure Defender 경고에 응답하는 방법
description: Key Vault용 Azure Defender에서 경고에 응답하기 위해 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 09/13/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 45ed38f3325ae66c72fff7f0aec35347b5e28ace
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664982"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Key Vault용 Azure Defender 경고에 응답
[Key Vault 대한 Azure Defender](defender-for-key-vault-introduction.md)경고를 받으면 아래 설명된 대로 경고를 조사하고 대응하는 것이 좋습니다. Azure Defender for Key Vault는 애플리케이션 및 자격 증명을 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.  

Key Vault 대한 Azure Defender 경고에는 다음 요소가 포함됩니다.

- 개체 ID입니다.
- 의심스러운 리소스의 사용자 보안 주체 이름 또는 IP 주소 

발생한 액세스 *유형에* 따라 일부 필드를 사용할 수 없을 수 있습니다. 예를 들어 애플리케이션에서 Key Vault에 액세스한 경우에는 연결된 사용자 계정 이름이 표시되지 않습니다. 트래픽이 Azure 외부에서 시작된 경우에는 개체 ID가 표시되지 않습니다.

> [!TIP]
> Azure 가상 머신에는 Microsoft IP가 할당됩니다. 즉, 경고는 Microsoft 외부에서 수행된 활동과 관련이 있더라도 Microsoft IP를 포함할 수 있습니다. 따라서 경고에 Microsoft IP가 있더라도 이 페이지에 설명된 대로 조사해야 합니다.

## <a name="step-1-identify-the-source"></a>1단계: 원본 식별

1. 트래픽이 Azure 테넌트 내에서 시작되었는지를 확인합니다. 키 자격 증명 모음 방화벽이 활성화된 경우, 경고를 트리거한 사용자 또는 애플리케이션에 대한 액세스를 제공했을 가능성이 큽니다.
1. 트래픽의 원본을 확인할 수 없는 경우 [2단계로 계속 진행합니다. 그에 따라 응답합니다.](#step-2-respond-accordingly)
1. 테넌트에서 트래픽의 소스를 식별할 수 있는 경우, 애플리케이션의 사용자 또는 소유자에게 문의하세요. 

> [!CAUTION]
> Key Vault용 Azure Defender는 도난당한 자격 증명으로 인한 의심스러운 활동을 식별하는 데 유용하도록 설계되었습니다. 사용자 또는 애플리케이션을 인식하기 때문에 경고를 해제하지 **마십시오**. 애플리케이션의 소유자 또는 사용자에게 문의하여 합법적인 활동인지 확인합니다. 필요에 따라 노이즈를 제거하는 제거 규칙을 만들 수 있습니다. [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)에서 자세히 알아봅니다.


## <a name="step-2-respond-accordingly"></a>2단계. 그에 따라 응답 
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

## <a name="step-3-measure-the-impact"></a>3단계: 영향 측정
이벤트가 완화되면 영향을 받은 키 자격 증명 모음의 비밀을 조사합니다.
1. Azure Key Vault에서 **보안** 페이지를 열고 트리거된 경고를 확인합니다.
1. 트리거된 특정 경고를 선택합니다.
    액세스된 비밀 및 타임스탬프 목록을 검토합니다.
1. 키 자격 증명 모음의 진단 로그가 사용하도록 설정된 경우, 필요에 따라 해당 호출자 IP, 사용자 계정 또는 개체 ID에 대한 이전 작업을 검토합니다.  

## <a name="step-4-take-action"></a>4단계. 작업 수행 
의심스러운 사용자 또는 애플리케이션이 액세스한 비밀, 키 및 인증서 목록을 컴파일하는 경우 해당 개체를 즉시 회전해야 합니다.

1. 영향을 받은 비밀은 키 자격 증명 모음에서 사용하지 않도록 설정하거나 삭제해야 합니다.
1. 특정 애플리케이션에서 자격 증명이 사용된 경우, 다음과 같이 수행합니다.
    1. 애플리케이션의 관리자에게 손상된 자격 증명이 손상된 후 해당 자격 증명을 사용하는 환경에 대한 감사를 요청합니다.
    1. 손상된 자격 증명이 사용된 경우, 애플리케이션 소유자는 액세스한 정보를 식별하여 영향을 완화해야 합니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Key Vault용 Azure Defender의 경고에 응답하는 프로세스를 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [Azure Defender for Key Vault 소개](defender-for-key-vault-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터 연속 내보내기](continuous-export.md)