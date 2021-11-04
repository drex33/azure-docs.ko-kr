---
title: Azure에 대 한 사용 권한 및 보안 비정상 스튜디오
description: Azure의 비정상 스튜디오에서 사용 권한이 작동 하는 방식 및 실수로 인 한 오류 주입 으로부터 리소스를 보호 하는 방법을 이해 합니다.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8502000dde601f6b42a8f770513e13f171dcf5d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053761"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Azure의 사용 권한 및 보안 비정상 스튜디오

Azure 비정상 스튜디오를 사용 하면 Azure 리소스에 오류를 체계적으로 삽입 하 여 서비스 복원 력을 향상 시킬 수 있습니다. 오류 주입은 서비스 복원 력을 향상 시킬 수 있는 강력한 방법 이지만 위험할 수도 있습니다. 응용 프로그램에서 오류가 발생 하는 경우 악의적인 행위자가 응용 프로그램을 군사 하기 위해 원래 의도 하 고 오픈 기회 보다 더 많은 영향을 줄 수 있습니다. 비정상 스튜디오에는 실수로 또는 잘못 된 행위자에 의해 오류가 실행 되지 않도록 하는 강력한 권한 모델이 있습니다. 이 문서에서는 비정상 스튜디오를 사용 하 여 오류 주입을 대상으로 하는 리소스를 보호 하는 방법을 설명 합니다.

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>비정상 스튜디오를 사용 하 여 오류를 삽입 하는 기능을 제한 하려면 어떻게 해야 하나요?

비정상 스튜디오에는 리소스에 대해 오류 삽입이 발생할 수 있는 방법과 시기를 제어 하는 데 도움이 되는 세 가지 수준의 보안이 있습니다.

첫째, 비정상 실험은 지역, 리소스 그룹 및 구독에 배포 되는 Azure 리소스입니다. 사용자에 게 실험을 생성, 업데이트, 시작, 취소, 삭제 또는 볼 수 있는 적절 한 Azure Resource Manager 권한이 있어야 합니다. 각 사용 권한은 id에 할당 되거나 와일드 카드 사용 권한으로 역할의 일부로 할당 될 수 있는 ARM 작업 세부적으로. 예를 들어 Azure의 참가자 역할에는 할당 된 범위에서 */쓰기 권한이 있습니다. 여기에는 Microsoft. n a m e/실험/쓰기 권한이 포함 됩니다. 리소스에 대해 오류를 삽입 하는 기능을 제어 하려고 할 때 제한 하는 가장 중요 한 작업은 오류를 주입 하는 비정상 실험을 시작 하므로 Microsoft. 비정상/실험/시작/동작입니다.

둘째, 비정상 실험에는 리소스에서 오류를 실행 하는 [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 가 있습니다. 실험을 만들면 권한이 없는 Azure Active Directory 테 넌 트에서 시스템 할당 관리 id가 만들어집니다. 비정상 실험을 실행 하기 전에 id에 모든 대상 리소스에 대 [한 적절 한 권한을](chaos-studio-fault-providers.md) 부여 해야 합니다. 실험 id가 리소스에 대 한 적절 한 사용 권한이 없는 경우 해당 리소스에 대해 오류를 실행할 수 없습니다.

마지막으로, 각 리소스 [는 해당 기능이 사용 하도록 설정 된 대상](chaos-studio-targets-capabilities.md)으로 비정상 상태 스튜디오로 등록 되어야 합니다. 실행 중인 오류의 대상이 나 기능이 없으면 실험은 리소스에 영향을 주지 않고 실패 합니다.

## <a name="agent-authentication"></a>에이전트 인증

에이전트 기반 오류를 실행 하는 경우 가상 머신 또는 가상 머신 확장 집합에 비정상 스튜디오 에이전트를 설치 해야 합니다. 에이전트는 [사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 비정상 스튜디오 및 *에이전트 프로필* 을 인증 하 고 특정 VM 리소스와의 관계를 설정 합니다. 에이전트 기반 오류에 대 한 가상 머신 또는 가상 머신 확장 집합을 온 보 딩 하는 경우 먼저 에이전트 대상을 만듭니다. 에이전트 대상에는 인증에 사용 되는 사용자 할당 관리 id에 대 한 참조가 있어야 합니다. 에이전트 대상에는 에이전트를 설치할 때 구성으로 제공 되는 *에이전트 프로필 ID* 가 포함 되어 있습니다. 에이전트 프로필은 각 대상에 대해 고유 하며 대상은 리소스 마다 고유 합니다.

## <a name="arm-operations-and-roles"></a>ARM 작업 및 역할

비정상 스튜디오의 작업은 다음과 같습니다.

| 작업(Operation) | Description |
| -- | -- |
| Microsoft. n e t/대상/[읽기, 쓰기, 삭제] | 대상을 가져오거나 만들거나 업데이트 하거나 삭제 합니다. |
| Microsoft. n e t/대상/기능/[읽기, 쓰기, 삭제] | 기능 가져오기, 만들기, 업데이트 또는 삭제 |
| Microsoft. s e r/위치/targetTypes/Read | 모든 대상 유형을 가져옵니다. |
| Microsoft. 비정상/위치/targetTypes/capabilityTypes/Read | 모든 기능 유형을 가져옵니다. |
| Microsoft. 감사/실험/[읽기, 쓰기, 삭제] | 비정상 실험을 가져오거나 만들거나 업데이트 하거나 삭제 합니다. |
| Microsoft. 감사/실험/시작/작업 | 비정상 실험을 시작 합니다. |
| Microsoft. 감사 안/실험/취소/작업 | 비정상 실험을 중지 합니다. |
| Microsoft. 감사/실험/상태/읽기 | 비정상 실험의 실행에 대 한 실행 상태를 가져옵니다. |
| Microsoft. n e t/실험/executionDetails/Read | 비정상 실험의 실행에 대 한 실행 세부 정보 (각 작업의 상태 및 오류)를 가져옵니다. |

이러한 사용 권한을 할당 하려면 세부적으로 [사용자 지정 역할을 만들](../role-based-access-control/custom-roles.md)수 있습니다.

## <a name="network-security"></a>네트워크 보안

비정상 스튜디오와의 모든 사용자 상호 작용은 Azure Resource Manager를 통해 수행 됩니다. 사용자가 실험을 시작 하는 경우 실험은 오류에 따라 리소스 관리자 이외의 끝점과 상호 작용할 수 있습니다.
* 서비스 직접 오류-대부분의 서비스-직접 오류는 Azure Resource Manager를 통해 실행 됩니다. 대상 리소스에는 나열 된 네트워크 끝점이 필요 하지 않습니다.
* 비정상 메시를 사용 하는 Azure Kubernetes 서비스에 대 한 서비스 직접 AKS 인 Azure 서비스에 대 한 직접 오류는 AKS 클러스터에 공개적으로 노출 된 Kubernetes API 서버가 있어야 합니다. [여기에서 IP 범위 집합에 대 한 AKS 네트워크 액세스를 제한 하는 방법을 배울 수 있습니다.](../aks/api-server-authorized-ip-ranges.md)
* 에이전트 기반 오류-에이전트 기반 오류의 경우 비정상 스튜디오 에이전트 서비스에 대 한 에이전트 액세스가 필요 합니다. 에이전트가 성공적으로 연결 하려면 가상 머신 또는 가상 머신 확장 집합에 대 한 아웃 바운드 액세스 권한이 있어야 합니다 http://agentcommunicationservice-frontdoor-canary.trafficmanager.net .

Azure 비정상 스튜디오는 서비스 태그 또는 개인 링크를 지원 하지 않습니다.

## <a name="data-encryption"></a>데이터 암호화

비정상 스튜디오는 기본적으로 모든 데이터를 암호화 합니다. 비정상 스튜디오는 관리 id 개체 Id, 실험/단계/분기 이름 및 오류 매개 변수 (예: 네트워크 연결 끊기 오류에서 차단할 네트워크 포트 범위)와 같은 시스템 속성에 대 한 입력만 허용 합니다. 이러한 속성은 지불 정보 또는 암호와 같은 중요 한 데이터를 저장 하는 데 사용할 수 없습니다. 비정상 스튜디오에서 데이터를 보호 하는 방법에 대 한 자세한 내용은 [Azure 고객 데이터 보호 문서](../security/fundamentals/protection-customer-data.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이제 비정상 실험을 보호 하는 방법을 이해 했으므로 다음을 수행할 준비가 되었습니다.
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct.md)
- [첫 번째 Azure Kubernetes Service 실험 만들기 및 실행](chaos-studio-tutorial-aks.md)
