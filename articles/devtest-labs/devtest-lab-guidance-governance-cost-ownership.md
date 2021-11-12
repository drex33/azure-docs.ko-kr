---
title: 비용 및 소유권 관리
description: 이 문서에서는 비용을 최적화하고 현재 환경에서 소유권을 조정하는 데 유용한 정보를 제공합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: a62ba95203ffea3162f7e79360bd36eb0f8f9733
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397606"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure DevTest Labs 인프라의 거버넌스 - 비용 및 소유권 관리
비용 및 소유권은 개발 및 테스트 환경의 구축을 고려할 때 주요 관심사입니다. 이 섹션에서는 비용을 최적화하고 사용자 환경에서 소유권을 조정하는 데 유용한 도움이 되는 정보를 찾을 수 있습니다.

## <a name="optimize-for-cost"></a>비용 최적화

### <a name="question"></a>질문
DevTest Labs 환경 내에서 비용을 최적화하려면 어떻게 해야 하나요?

### <a name="answer"></a>답변
DevTest Labs의 몇 가지 기본 제공 기능을 활용 하 여 비용을 최적화할 수 있습니다. 사용자 작업을 제한하려면 [비용 관리, 임계값](devtest-lab-configure-cost-management.md) [ 및 정책](devtest-lab-set-lab-policy.md) 문서를 참조하세요. 

개발 및 테스트 워크 로드에 DevTest Labs를 사용 하는 경우 기업계약의 일부인 [Enterprise 개발/테스트 구독 혜택](https://azure.microsoft.com/offers/ms-azr-0148p/) 을 사용 하는 것이 좋습니다. 또는 종 량 제 고객 인 경우 [종 량 제 DevTest 제품](https://azure.microsoft.com/offers/ms-azr-0023p/)을 고려 하세요.

이 방법은 다음과 같은 몇 가지 이점을 제공 합니다.

- Windows Virtual Machines, Cloud Services, HDInsight, App Service 및 Logic Apps에 대한 저렴한 특별 개발/테스트 요금
- 다른 Azure 서비스에 대한 높은 EA(기업계약) 요금
- Windows 8.1 및 Windows 10 등 갤러리의 전용 개발/테스트 이미지에 액세스
 
현재 Visual Studio를 구독(표준 구독, 연간 클라우드 구독 및 월간 클라우드 구독) 중인 경우에만 Enterprise 개발/테스트 구독 내에서 실행되는 Azure 리소스를 사용할 수 있습니다. 그러나 최종 사용자는 응용 프로그램에 액세스 하 여 피드백을 제공 하거나 승인 테스트를 수행할 수 있습니다. 응용 프로그램을 개발 하 고 테스트 하는 경우에만이 구독 내에서 리소스를 사용할 수 있습니다. 작동 시간은 보장 되지 않습니다.

DevTest 제품을 사용 하기로 결정 한 경우 응용 프로그램 개발 및 테스트에만이 혜택을 사용 합니다. 구독 내에서는 Azure DevOps 및 HockeyApp를 사용 하는 경우를 제외 하 고는 재정적 지원 SLA를 사용 하지 않습니다.

## <a name="define-role-based-access-across-your-organization"></a>조직 전체에서 역할 기반 액세스 정의
### <a name="question"></a>질문
개발자/테스트 담당자가 해당 작업을 수행하는 동안 IT 부서에서 관리할 수 있도록 내 DevTest Labs의 Azure 역할 기반 액세스 제어를 정의하려면 어떻게 해야 하나요? 

### <a name="answer"></a>답변
광범위 한 패턴이 있지만 세부 정보는 조직에 따라 다릅니다.

Central IT는 필요한 사항만 소유 하 고 프로젝트 및 응용 프로그램 팀에서 필요한 제어 수준을 갖도록 합니다. 이에 따라 일반적으로 중앙 IT 부서는 구독을 소유하고 네트워킹 구성과 같은 핵심 IT 기능을 처리합니다. 구독의 **소유자** 집합은 소규모인 것이 좋습니다. 이러한 소유자는 필요할 때 다른 소유자를 추천 하거나, 구독 수준 정책을 적용할 수 있습니다 (예: "공용 IP 없음").

계층 1 또는 계층 2 지원과 같이 구독 전반에서 액세스 권한이 필요한 사용자 하위 집합도 있을 수 있습니다. 이 경우 이러한 사용자에게 리소스를 관리할 수 있도록 **참가자** 액세스 권한을 부여하되, 사용자 액세스 또는 정책 조정 권한은 부여하지 않는 것이 좋습니다.

DevTest Labs 리소스 소유자는 프로젝트 또는 응용 프로그램 팀에 가까워야 합니다. 이러한 소유자는 컴퓨터 및 소프트웨어 요구 사항을 이해 합니다. 대부분의 조직에서 DevTest Labs 리소스의 소유자는 프로젝트 또는 개발 리드입니다. 이 소유자는 랩 환경 내에서 사용자 및 정책을 관리할 수 있으며 DevTest Labs 환경의 모든 가상 컴퓨터를 관리할 수 있습니다.

DevTest Labs Users 역할에 프로젝트 및 응용 프로그램 팀 멤버를 추가 합니다. 이러한 사용자는 랩 및 구독 수준 정책에 기반 하 여 가상 컴퓨터를 만들 수 있습니다. 사용자는 자신의 가상 컴퓨터를 관리할 수도 있지만 다른 사용자에 게 속한 가상 컴퓨터는 관리할 수 없습니다.

자세한 내용은 [Azure enterprise 스 캐 폴드 – 규범적 구독 거 버 넌 스](/azure/architecture/cloud-adoption/appendix/azure-scaffold)를 참조 하세요.


## <a name="next-steps"></a>다음 단계
[회사 정책 및 규정 준수](devtest-lab-guidance-governance-policy-compliance.md)를 참조하세요.
