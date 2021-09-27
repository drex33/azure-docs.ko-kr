---
title: 기업용 Azure DevTest Labs 도입
description: 이 문서는 기업에서 Azure DevTest Labs를 사용하는 것에 대한 규범적인 지침을 제공합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e5be75d7049d9cfdc97a764fdca8d1e63876aaf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671168"
---
# <a name="devtest-labs-in-the-enterprise"></a>기업의 DevTest Labs
기업은 민첩성, 유연성 및 경제성을 포함하는 [이점](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case)때문에 클라우드를 빠르게 채택하고 있습니다. 첫 번째 단계는 보통 개발 및 테스트 워크 로드입니다. Azure DevTest Labs는 기업에 도움이 되고 [주요 개발/테스트 시나리오](devtest-lab-guidance-get-started.md)를 지원하는 [기능](devtest-lab-concepts.md)을 제공합니다.

클라우드로 워크로드를 마이그레이션하는 기업의 일반적인 우려 사항은 다음과 같습니다.

- [개발/테스트 리소스 보안](devtest-lab-guidance-governance-policy-compliance.md)
- [비용 관리 및 이해](devtest-lab-guidance-governance-cost-ownership.md)
- 기업의 보안 및 규정 준수에 영향을 주지 않고 개발자를 위한 셀프 서비스 지원
- 추가 시나리오를 다루는 DevTest Labs의 자동화 및 확장
- [DevTest Labs 기반 솔루션을 수천 개의 리소스로 확장](devtest-lab-guidance-scale.md)
- [대규모의 DevTest Labs 배포](devtest-lab-guidance-orchestrate-implementation.md)
- [개념 증명 시작](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>대상 그룹
이 설명서는 배포를 설정/검토하고 작업을 감독하는 기업, IT 계획 담당자, 설계자 및 관리자를 대상으로 제공됩니다. 이 문서들은 전반적인 프로세스 및 권장 설계 원칙을 강조합니다. 목표는 궁극적으로 조직 내의 Azure DevTest Labs 도입을 추진하는 안전하고 안정적인 개발/테스트 환경을 조성하는 것입니다.

## <a name="enterprise-customers"></a>기업 고객

현재 많은 DevTest Labs 기업 고객이 개발 및 조직 내 워크 로드 테스트를 위해 DevTest Labs을 성공적으로 사용하고 있습니다. [자세히 알아보기](https://azure.microsoft.com/case-studies/?term=DevTest+labs).

## <a name="next-steps"></a>다음 단계
- [기업에 대한 참조 아키텍처](devtest-lab-reference-architecture.md)
