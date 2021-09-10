---
title: 지역에서 Azure Arc 지원 서버를 마이그레이션하는 방법
description: 한 지역에서 다른 하위 지역으로 Azure Arc 지원 서버를 마이그레이션하는 방법 알아보기.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: d0e909b789e3e0dcee4d39e22067de26daace548
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609651"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>지역에서 Azure Arc 지원 서버를 마이그레이션하는 방법

기존 Azure Arc 지원 서버를 한 하위 지역에서 다른 하위 지역으로 이동하려는 시나리오가 있습니다. 예를 들어, 머신이 잘못된 하위 지역에 등록된 것을 인식하여 관리 효율성 향상 또는 거버넌스 목적으로 이동하려는 경우입니다.

Azure Arc 지원 서버를 한 Azure 지역에서 다른 하위 지역으로 마이그레이션하려면 VM 확장을 제거하고, Azure에서 리소스를 삭제한 뒤 다른 지역에서 다시 만들어야 합니다. 이 단계를 수행하기 전에 머신을 감사하여 설치된 VM 확장을 확인해야 합니다.

> [!NOTE]
> 해당 프로시저가 완료된 이후 설치된 확장이 계속해서 실행되고 정상적으로 작업을 수행하는 동안에는 확장을 관리할 수 없습니다. 머신에 확장을 다시 배포하려고 하면 예기치 않은 동작이 발생할 수 있습니다.

## <a name="move-machine-to-other-region"></a>머신을 기타 하위 지역으로 이동하기

> [!NOTE]
> 이 작업을 수행하는 동안 마이그레이션할 때 가동 중지 시간이 발생합니다.

1. [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) 또는 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)을 사용하여 [Azure Portal](manage-vm-extensions-portal.md#uninstall-extensions)에서 설치된 VM 확장을 제거합니다.

2. **azcmagent** 도구를 [연결 끊기](manage-agent.md#disconnect) 매개 변수와 함께 사용하여 Azure Arc에서 머신의 연결을 끊고 Azure에서 머신 리소스를 삭제합니다. Arc 지원 서버에서 머신 연결을 끊으면 Connected Machine 에이전트가 제거되지 않으므로, 이 프로세스의 일부로 에이전트를 제거할 필요는 없습니다. 대화형으로 로그온하는 동안 수동으로 해당 프로세스를 실행하거나, 여러 에이전트를 온보딩할 때 사용한 동일한 서비스 주체 또는 Microsoft ID 플랫폼 [액세스 토큰](../../active-directory/develop/access-tokens.md)을 사용하여 자동화합니다. 서비스 주체를 사용하여 Azure Arc 지원 서버에 머신을 등록하지 않은 경우 서비스 주체를 만들려면 관련 [문서](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)를 참조하세요.

3. 다른 하위 지역의 Arc 지원 서버를 사용하여 Connected Machine 에이전트를 다시 등록합니다. [Connect](manage-agent.md#connect) 매개 변수와 함께 `azcmagent` 도구를 실행하여 해당 단계를 완료합니다.

4. 머신에 원래 배포되었던 VM 확장을 Arc 지원 서버에서 다시 배포합니다. Azure Policy 정의를 사용하여 VM용 Azure Monitor(인사이트) 에이전트 또는 Log Analytics 에이전트를 배포한 경우, 다음 [평가 주기](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) 후에 에이전트가 다시 배포됩니다.

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM 인사이트](../../azure-monitor/vm/vminsights-enable-policy.md) 정책으로 모니터링 사용 등).