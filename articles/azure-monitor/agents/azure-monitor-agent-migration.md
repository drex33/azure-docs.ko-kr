---
title: 레거시 에이전트에서 새 Azure Monitor 에이전트로 마이그레이션
description: 기존 레거시 에이전트에서 새 AMA(Azure Monitor 에이전트) 및 DCR(데이터 수집 규칙)로 마이그레이션하기 위한 지침입니다.
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 01c2f7b49b051e9166ad1ceef99fc816e611f579
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567887"
---
# <a name="migrate-from-log-analytics-agents"></a>Log Analytics 에이전트에서 마이그레이션
이 문서에서는 새 AMA(Azure Monitor 에이전트) 및 DCR(데이터 수집 규칙)으로 마이그레이션하는 시기와 방법에 대한 대략적인 지침을 제공합니다. 이 문서는 새 마이그레이션 도구를 사용할 수 있을 때 업데이트됩니다.


## <a name="review"></a>검토
- 지금 또는 나중에 새 Azure Monitor 에이전트로 마이그레이션해야 하는지 결정하는 데 도움이 되도록 [Azure Monitor 에이전트 개요](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent)의 지침을 참조하세요.
- Azure Monitor 에이전트의 경우 새 기능, 즉 기존 기능, 서비스 및 솔루션의 가용성과 [Azure Monitor 에이전트 개요](./agents-overview.md#azure-monitor-agent)의 현재 제한 사항을 검토합니다.


## <a name="test-migration-by-using-the-azure-portal"></a>Azure Portal을 사용하여 마이그레이션 테스트
1. 마이그레이션 중에 안전하게 배포하려면 기존 Log Analytics 에이전트를 실행하는 비프로덕션 환경에서 몇 가지 리소스로 테스트를 시작합니다. 해당 테스트 리소스에서 수집된 데이터의 유효성을 검사한 후 동일한 단계를 수행하여 프로덕션으로 롤아웃합니다.
1. **모니터** > **설정** > **데이터 수집 규칙** 으로 이동하여 [새 데이터 수집 규칙 만들기](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)를 수행하여 기존 데이터 형식 수집을 시작합니다. 포털 GUI를 사용하는 경우 모든 대상 리소스에 다음 단계가 수행됩니다.
    - 시스템이 할당한 관리 ID 사용
    - Azure Monitor 에이전트 확장 설치
    - 데이터 수집 규칙 연결을 만들고 배포
1. Azure Monitor 에이전트를 통해 데이터가 예상대로 흐르는지 유효성을 검사합니다. **하트비트** 표에서 새 에이전트 버전 값을 확인합니다. 기존 Log Analytics 에이전트를 통해 흐르는 데이터와 일치하는지 확인합니다.


## <a name="at-scale-migration-by-using-policies"></a>정책을 사용하여 대규모 마이그레이션
1. 다음 조건을 사용하여 MMA/OMS로 현재 모니터링 설정을 분석하는 것부터 시작합니다.
    - 가상 머신, 가상 머신 확장 집합 및 온-프레미스 서버와 같은 원본
    - 성능 카운터, Windows 이벤트 로그 및 Syslog와 같은 데이터 원본
    - 대상(예: Log Analytics 작업 영역)
1. 위의 구성을 사용하여 [새 데이터 수집 규칙을 만듭니다](/rest/api/monitor/datacollectionrules/create#examples). Windows 및 Linux 원본에 대해 별도의 데이터 수집 규칙을 두는 것이 가장 좋습니다. 또는 데이터 수집 요구 사항이 다른 개별 팀에 대해 별도의 데이터 수집 규칙을 원할 수 있습니다.
1. 대상 리소스에서 [시스템이 할당한 관리 ID를 사용합니다](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity).
1. Azure Monitor 에이전트 확장을 설치합니다. [기본 제공 정책 이니셔티브](../deploy-scale.md#built-in-policy-initiatives)를 사용하여 모든 대상 리소스에 데이터 수집 규칙 연결을 배포합니다. 앞의 데이터 수집 규칙을 입력 매개 변수로 제공합니다. 
1. Azure Monitor 에이전트를 통해 데이터가 예상대로 흐르는지 유효성을 검사합니다. **하트비트** 표에서 새 에이전트 버전 값을 확인합니다. 기존 Log Analytics 에이전트를 통해 흐르는 데이터와 일치하는지 확인합니다.
1. 대시보드, 경고 및 Runbook worker와 같은 모든 다운스트림 종속성의 유효성을 검사합니다. 이제 새 에이전트의 데이터를 사용하여 통합 문서가 모두 계속 작동합니다.
1. 리소스에서 [Log Analytics 에이전트를 설치 제거합니다](./agent-manage.md#uninstall-agent). System Center Operations Manager 시나리오 또는 Azure Monitor 에이전트에서 아직 사용할 수 없는 다른 솔루션에 사용해야 하는 경우 제거하지 마세요.
1. 이전에 Log Analytics 에이전트에서 사용한 구성 파일, 작업 영역 키 또는 인증서를 정리합니다.


