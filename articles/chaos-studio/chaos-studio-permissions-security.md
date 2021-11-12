---
title: Azure Chaos Studio에 대한 권한 및 보안
description: Azure Chaos Studio에서 사용 권한이 작동하는 방식과 실수로 인한 오류 주입으로부터 리소스를 보호하는 방법을 이해합니다.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: fa547771b125b17d05e6a615f01cecc899cba7e0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372211"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Azure Chaos Studio의 권한 및 보안

Azure Chaos Studio를 사용하면 Azure 리소스에 오류를 체계적으로 주입하여 서비스 복원력을 향상시킬 수 있습니다. 오류 주입은 서비스 복원력을 향상시키는 강력한 방법이지만 위험할 수도 있습니다. 애플리케이션에서 오류를 발생시키는 것은 원래 의도한 것보다 더 많은 영향을 미칠 수 있으며 악의적인 행위자가 애플리케이션에 침투할 수 있는 기회를 열어 놓을 수 있습니다. Chaos Studio에는 오류가 의도치 않게 또는 잘못된 행위자가 실행하는 것을 방지하는 강력한 권한 모델이 있습니다. 이 문서에서는 Chaos Studio를 사용하여 오류 주입을 대상으로 하는 리소스를 보호하는 방법을 알아봅니다.

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>Chaos Studio를 사용하여 오류를 삽입하는 기능을 제한하려면 어떻게 해야 합니까?

Chaos Studio에는 리소스에 대해 오류 주입이 발생할 수 있는 방법과 시기를 제어하는 데 도움이 되는 세 가지 수준의 보안이 있습니다.

첫째, 비정상 분기 실험은 지역, 리소스 그룹 및 구독에 배포되는 Azure 리소스입니다. 사용자는 실험을 생성, 업데이트, 시작, 취소, 삭제 또는 볼 수 있는 적절한 Azure Resource Manager 권한이 있어야 합니다. 각 권한은 ID에 세부적으로 할당되거나 와일드카드 권한이 있는 역할의 일부로 할당할 수 있는 ARM 작업입니다. 예를 들어 Azure의 기여자 역할에는 할당된 범위에서 */쓰기 권한이 있으며, 여기에는 Microsoft.Chaos/experiments/write 권한이 포함됩니다. 리소스에 대해 오류를 삽입하는 기능을 제어하려고 할 때 제한해야 하는 가장 중요한 작업은 Microsoft.Chaos/experiments/start/action입니다. 이 작업은 오류를 주입하는 비정상 상황에서 시작됩니다.

둘째, chaos 실험에는 리소스에서 오류를 실행하는 [시스템 할당 관리 ID가](../active-directory/managed-identities-azure-resources/overview.md) 있습니다. 실험을 만들 때 시스템 할당 관리 ID는 권한이 없는 Azure Active Directory 테넌트에서 만들어집니다. Chaos 실험을 실행하기 전에 ID에 모든 대상 [리소스에 적절한 권한을](chaos-studio-fault-providers.md) 부여해야 합니다. 실험 ID에 리소스에 대한 적절한 권한이 없으면 해당 리소스에 대해 오류를 실행할 수 없습니다.

마지막으로, 각 리소스는 [해당 기능이 활성화된 대상으로](chaos-studio-targets-capabilities.md)Chaos Studio에 온보딩되어야 합니다. 실행 중인 오류에 대한 대상 또는 기능이 없으면 리소스에 영향을 주지 않고 실험이 실패합니다.

## <a name="agent-authentication"></a>에이전트 인증

에이전트 기반 오류를 실행하는 경우 가상 머신 또는 가상 머신 확장 집합에 Chaos Studio 에이전트를 설치해야 합니다. 에이전트는 [사용자 할당 관리 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용하여 Chaos Studio에 인증하고 에이전트 *프로필을* 사용하여 특정 VM 리소스에 대한 관계를 설정합니다. 에이전트 기반 오류에 대한 가상 머신 또는 가상 머신 확장 집합을 온보딩할 때 먼저 에이전트 대상을 만듭니다. 에이전트 대상에는 인증에 사용할 사용자 할당 관리 ID에 대한 참조가 있어야 합니다. 에이전트 대상에는 *에이전트를* 설치할 때 구성으로 제공되는 에이전트 프로필 ID 가 포함됩니다. 에이전트 프로필은 각 대상에 고유하며 대상은 리소스별로 고유합니다.

## <a name="arm-operations-and-roles"></a>ARM 작업 및 역할

Chaos Studio에는 다음과 같은 작업이 있습니다.

| 작업(Operation) | Description |
| -- | -- |
| Microsoft.Chaos/targets/[Read,Write,Delete] | 대상을 가져오기, 만들기, 업데이트 또는 삭제합니다. |
| Microsoft.Chaos/targets/capabilities/[Read,Write,Delete] | 기능을 가져오기, 만들기, 업데이트 또는 삭제합니다. |
| Microsoft.Chaos/locations/targetTypes/Read | 모든 대상 유형을 얻습니다. |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | 모든 기능 유형을 얻습니다. |
| Microsoft.Chaos/experiments/[Read,Write,Delete] | 비정형 실험을 가져오기, 만들기, 업데이트 또는 삭제합니다. |
| Microsoft.Chaos/experiments/start/action | 비정실행 실험을 시작합니다. |
| Microsoft.Chaos/experiments/cancel/action | 비정실행 실험을 중지합니다. |
| Microsoft.Chaos/experiments/statuses/Read | 비정상 상황 실험 실행에 대한 실행 상태를 확인합니다. |
| Microsoft.Chaos/experiments/executionDetails/Read | 비정상 상황 실험 실행에 대한 실행 세부 정보(각 작업의 상태 및 오류)를 확인합니다. |

이러한 권한을 세분적으로 할당하려면 [사용자 지정 역할을 만들](../role-based-access-control/custom-roles.md)수 있습니다.

## <a name="network-security"></a>네트워크 보안

Chaos Studio와의 모든 사용자 상호 작용은 Azure Resource Manager 통해 발생합니다. 사용자가 실험을 시작하는 경우 실험은 오류에 따라 Resource Manager 이외의 엔드포인트와 상호 작용할 수 있습니다.
* 서비스 직접 오류 - 대부분의 서비스 직접 오류는 Azure Resource Manager 통해 실행됩니다. 대상 리소스에는 허용 목록에 있는 네트워크 엔드포인트가 필요하지 않습니다.
* 서비스 다이렉트 AKS Chaos Mesh 오류 - Chaos Mesh를 사용하는 Azure Kubernetes Service 대한 서비스 직접 오류는 AKS 클러스터에 공개적으로 노출된 Kubernetes API 서버가 있어야 합니다. [여기에서 AKS 네트워크 액세스를 IP 범위 집합으로 제한하는 방법을 알아볼 수 있습니다.](../aks/api-server-authorized-ip-ranges.md)
* 에이전트 기반 오류 - 에이전트 기반 오류를 사용하려면 Chaos Studio 에이전트 서비스에 대한 에이전트 액세스 권한이 필요합니다. 에이전트가 성공적으로 연결하려면 가상 머신 또는 가상 머신 확장 집합에 아웃바운드 액세스 권한이 있어야 http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 합니다.

Azure Chaos Studio는 서비스 태그 또는 Private Link 지원하지 않습니다.

## <a name="data-encryption"></a>데이터 암호화.

Chaos Studio는 기본적으로 모든 데이터를 암호화합니다. Chaos Studio는 관리 ID 개체 ID, 실험/단계/분기 이름 및 오류 매개 변수(예: 네트워크 연결 끊김 오류에서 차단할 네트워크 포트 범위)와 같은 시스템 속성에 대한 입력만 허용합니다. 이러한 속성은 결제 정보 또는 암호와 같은 중요한 데이터를 저장하는 데 사용하면 안 됩니다. Chaos Studio에서 데이터를 보호하는 방법에 대한 자세한 내용은 [Azure 고객 데이터 보호 문서를](../security/fundamentals/protection-customer-data.md)참조하세요.

## <a name="next-steps"></a>다음 단계
이제 비정실실 실험을 보호하는 방법을 이해하게 됐으므로 다음을 수행할 준비가 되었습니다.
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct-portal.md)
- [첫 번째 Azure Kubernetes Service 실험 만들기 및 실행](chaos-studio-tutorial-aks-portal.md)
