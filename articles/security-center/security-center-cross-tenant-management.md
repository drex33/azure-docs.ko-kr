---
title: Azure Security Center의 테넌트 간 관리 | Microsoft Docs
description: Azure Lighthouse를 사용하여 Security Center에서 여러 테넌트의 보안 상태를 관리하기 위해 테넌트 간 관리를 설정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: e4207bbc045f451b45f8b5da42fba08b406caeca
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237567"
---
# <a name="cross-tenant-management-in-security-center"></a>Security Center의 테넌트 간 관리

테넌트 간 관리를 사용하면 [Azure Lighthouse](../lighthouse/overview.md)를 활용하여 Security Center에서 여러 테넌트의 보안 상태를 보고 관리할 수 있습니다. 각 테넌트의 디렉터리에 로그인할 필요 없이 단일 보기에서 여러 테넌트를 효율적으로 관리합니다.

- 서비스 공급자는 자체 테넌트 내에서 여러 고객에 대한 리소스의 보안 상태를 관리할 수 있습니다.

- 여러 테넌트가 있는 조직의 보안 팀은 단일 위치에서 보안 상태를 보고 관리할 수 있습니다.

## <a name="set-up-cross-tenant-management"></a>교차 테넌트 관리 설정

[Azure 위임 리소스 관리](../lighthouse/concepts/architecture.md)는 Azure Lighthouse의 주요 구성 요소 중 하나입니다. Azure Lighthouse 설명서: [Azure Lighthouse에 고객 온보딩](../lighthouse/how-to/onboard-customer.md)의 지침에 따라 관리되는 테넌트의 리소스에 대한 액세스를 자체 테넌트에 위임하여 테넌트 간 관리를 설정합니다.


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Security Center에서 테넌트 간 관리의 작동 방식

단일 테넌트에서 여러 구독을 관리하는 것과 동일한 방식으로 여러 테넌트에서 구독을 검토하고 관리할 수 있습니다.

상단 메뉴 모음에서 필터 아이콘을 클릭하고 각 테넌트의 디렉터리에서 보려는 구독을 선택합니다.

  ![테넌트를 필터링합니다.](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

보기와 작업은 기본적으로 동일합니다. 다음은 몇 가지 예입니다.

- **보안 정책 관리:** 하나의 보기에서 [정책](tutorial-security-policy.md)을 통해 많은 리소스의 보안 상태를 관리하고, 보안 권장 사항을 통해 작업을 수행하고, 보안 관련 데이터를 수집하고 관리합니다.
- **보안 점수 및 규정 준수 상태 개선:** 테넌트 간 가시성을 통해 모든 테넌트의 전반적인 보안 상태와 각 테넌트에 대한 [보안 점수](secure-score-security-controls.md) 및 [규정 준수 상태](security-center-compliance-dashboard.md)를 가장 잘 개선할 수 있는 위치와 방법을 볼 수 있습니다.
- **권장 사항 수정:** 다양한 테넌트의 많은 리소스에 대한 [권장 사항](security-center-recommendations.md)을 한 번에 모니터링하고 수정합니다. 그런 다음, 모든 테넌트에서 가장 높은 위험을 제공하는 취약성을 즉시 해결할 수 있습니다.
- **경고 관리**: 다른 테넌트 전체에서 [경고](security-center-alerts-overview.md)를 검색합니다. 실행 가능한 [수정 단계](security-center-managing-and-responding-alerts.md)를 준수하지 않는 리소스에 대해 조치를 취합니다.

- **고급 클라우드 방어 기능 및 기타 관리**: [JIT(Just-In-Time) VM 액세스](security-center-just-in-time.md), [적응형 네트워크 강화](security-center-adaptive-network-hardening.md), [적응형 애플리케이션 제어](security-center-adaptive-application.md) 등의 다양한 위협 방지 서비스를 관리합니다.
 
## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 테넌트 간 관리가 작동하는 방식을 설명합니다. Azure Lighthouse가 여러 Azure AD 테넌트를 사용하는 엔터프라이즈 내에서 테넌트 간 관리를 단순화할 수 있는 방법을 알아보려면 [엔터프라이즈 시나리오의 Azure Lighthouse](../lighthouse/concepts/enterprise.md)를 참조하세요.