---
title: 일반적인 Azure Chaos Studio 문제 해결
description: Azure Chaos Studio를 사용할 때 일반적인 문제를 해결하는 방법을 알아봅니다.
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 9d56666f42d837f59ec22031646d46e22f0b298d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372495"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Azure Chaos Studio 문제 해결

Chaos Studio를 사용하면 경우에 따라 몇 가지 문제가 발생할 수 있습니다. 이 문서에서는 일반적인 문제 및 문제 해결 단계에 대해 자세히 설명합니다.

## <a name="general-troubleshooting-tips"></a>일반 문제 해결 팁

다음 원본은 Chaos Studio 문제를 해결할 때 유용합니다.
1. **활동 로그:** [Azure 활동 로그에는](../azure-monitor/essentials/activity-log.md) 대상 및/또는 기능 사용, 에이전트 설치, 실험 만들기 또는 실행과 같은 Chaos Studio 작업을 포함하여 구독의 모든 만들기, 업데이트 및 삭제 작업에 대한 레코드가 있습니다. 활동 로그의 오류는 Chaos Studio를 사용하는 데 필수적인 사용자 작업을 완료하지 못했음을 나타냅니다. 대부분의 서비스 직접 오류는 Azure Resource Manager 작업을 실행하여 오류를 삽입하므로 활동 로그에는 일부 서비스 직접 오류에 대한 실험 중에 삽입되는 오류 레코드도 있습니다.
2. **실험 세부 정보:** 실험 실행 세부 정보는 개별 실험 실행의 상태 및 오류를 표시합니다. 실험 세부 정보에서 특정 오류를 열면 실패한 리소스와 오류에 대한 오류 메시지가 표시됩니다. [실험 세부 정보 에 액세스하는 방법에 대해 자세히 알아봅니다.](chaos-studio-run-experiment.md#view-experiment-history-and-details)
3. **에이전트 로그:** 에이전트 기반 오류를 사용하는 경우 가상 머신에 RDP 또는 SSH를 입력하여 에이전트가 오류를 실행하지 못한 이유를 파악해야 할 수 있습니다. 에이전트 로그에 액세스하기 위한 지침은 운영 체제에 따라 달라집니다.
    * **Chaos Windows 에이전트:** 에이전트 로그는 원본 AzureChaosAgent가 있는 애플리케이션 범주의 Windows 이벤트 로그에 있습니다. 에이전트는 오류 활동 및 정기적인 상태 검사(Chaos Studio 에이전트 서비스에 인증하고 통신하는 기능) 이벤트를 이 로그에 추가합니다.
    * **Chaos Linux 에이전트:** Linux 에이전트는 systemd를 사용하여 Linux 서비스로 에이전트 프로세스를 관리합니다. 에이전트에 대한 systemd 저널(에이전트 서비스에서 기록한 이벤트)을 보려면 명령을 `journalctl -u azure-chaos-agent` 실행합니다.
4. **VM 확장 상태:** 에이전트 기반 오류를 사용하는 경우 VM 확장이 설치되어 있고 정상 상태인지 확인해야 할 수도 있습니다. Azure Portal 가상 머신으로 이동하고 **확장** 또는 **확장 + 애플리케이션으로 이동합니다.** ChaosAgent 확장을 클릭하고 다음 필드를 찾습니다.
    * **상태가** "프로비전 성공"으로 표시되어야 합니다. 다른 상태는 에이전트를 설치하지 못했음을 나타냅니다. 모든 [시스템 요구 사항이](chaos-studio-limitations.md#limitations) 충족되는지 확인하고 에이전트를 다시 설치해 보세요.
    * **처리기 상태에** "Ready"가 표시되어야 합니다. 다른 상태는 에이전트가 설치되었지만 Chaos Studio 서비스에 연결할 수 없음을 나타냅니다. 모든 [네트워크 요구 사항이](chaos-studio-limitations.md#limitations) 충족되고 사용자 할당 관리 ID가 가상 머신에 추가되었는지 확인하고 다시 부팅해 보세요.

## <a name="issues-onboarding-a-resource"></a>리소스 온보딩 문제

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>리소스가 Azure Portal 대상 목록에 표시되지 않습니다.
Chaos Studio 대상 목록에서 사용하도록 설정하려는 리소스가 표시되지 않는 경우 다음 문제 중 때문일 수 있습니다.
* 리소스가 Chaos Studio 에 [대해 지원되는 지역에](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio)있지 않습니다.
* 리소스는 [Chaos Studio에서 지원되는 리소스 유형이](chaos-studio-fault-providers.md)아닙니다.
* 리소스는 대상 목록에 대한 필터에서 필터링된 구독 또는 리소스 그룹에 있습니다. 구독 및 리소스 그룹 필터를 변경하여 리소스를 확인합니다.

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>대상 및/또는 기능 사용이 실패하거나 대상 목록에 올바르게 표시되지 않습니다.
대상 및/또는 기능을 사용하도록 설정할 때 오류가 표시되면 다음 단계를 시도합니다.
1. 온보딩 중인 리소스에 대한 적절한 권한이 있는지 확인합니다. 대상 및/또는 기능을 사용하도록 설정하려면 리소스 범위에서 Microsoft.Chaos/ \* 권한이 필요합니다. 기여자와 같은 기본 제공 역할에는 모든 Microsoft.Chaos 작업에 대한 권한을 포함하는 와일드카드 읽기 및 쓰기 권한이 있습니다.
2. 대상 및 기능 목록이 업데이트되기까지 몇 분 정도 기다립니다. Azure Portal Azure Resource Graph 사용하여 대상 및 기능 온보딩에 대한 정보를 수집하며 업데이트가 전파하는 데 최대 5분이 걸릴 수 있습니다.
3. 리소스에 여전히 "사용 안 됨"이 표시되면 다음 단계를 시도합니다.
    1. 리소스를 다시 사용하도록 설정합니다.
    2. 리소스 사용이 여전히 실패하는 경우 활동 로그를 방문하여 실패한 대상 만들기 작업을 찾아 자세한 오류 정보를 확인합니다.
4. 리소스에 "사용"이 표시되었지만 온보딩 기능이 실패한 경우 다음 단계를 시도합니다.
    1. 대상 목록의 리소스에서 **작업 관리** 단추를 클릭합니다. 선택되지 않은 기능을 확인하고 **저장을** 클릭합니다.
    2. 기능 사용이 여전히 실패하는 경우 활동 로그를 방문하여 실패한 대상 만들기 작업을 찾아 자세한 오류 정보를 확인합니다.

## <a name="prerequisite-issues"></a>필수 조건 문제

일부 문제는 필수 구성 조건이 누락되어 발생합니다. 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>가상 머신에서 에이전트 기반 오류 실패
누락된 필수 조건과 관련된 다양한 이유로 에이전트 기반 오류가 실패할 수 있습니다.
* Linux VM에서 [CPU 압력,](chaos-studio-fault-library.md#cpu-pressure) [물리적 메모리 압력,](chaos-studio-fault-library.md#physical-memory-pressure) [디스크 I/O 압력](chaos-studio-fault-library.md#disk-io-pressure-linux)및 임의 스트레스 스트레스 오류는 모두 가상 머신에 [stress-ng](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) [유틸리티를](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) 설치해야 합니다. stress-ng를 설치하는 방법에 대한 자세한 내용은 오류 필수 구성 조건 섹션을 참조하세요.
* Linux 또는 Windows VM에서 에이전트 기반 대상 사용 중에 제공된 사용자 할당 관리 ID도 가상 머신에 추가해야 합니다.
* Linux 또는 Windows VM에서 실험에 대한 시스템 할당 관리 ID에는 VM에 대한 읽기 권한자 역할이 부여되어야 합니다(Virtual Machine 기여자처럼 보이는 상승된 \* 역할에는 Chaos Studio 에이전트 서비스가 가상 머신의 microsoft-agent 대상 프록시 리소스를 읽는 데 필요한 /Read 작업이 포함되지 않음).

### <a name="aks-chaos-mesh-faults-fail"></a>AKS Chaos Mesh 오류 실패
AKS Chaos Mesh 오류는 누락된 필수 조건과 관련된 다양한 이유로 실패할 수 있습니다.
* Chaos Mesh는 AKS Chaos Mesh 오류를 사용하기 전에 먼저 AKS 클러스터에 설치해야 합니다. 지침은 [AKS의 Chaos Mesh 오류 자습서 에서](chaos-studio-tutorial-aks-portal.md#set-up-chaos-mesh-on-your-aks-cluster)찾을 수 있습니다.
* Chaos Mesh는 버전 2.0.4 이상이어야 합니다. AKS 클러스터에 연결하고 를 실행하여 Chaos Mesh 버전을 얻을 수 `helm version chaos-mesh` 있습니다.
* Chaos Mesh는 네임스페이스와 함께 설치해야 `chaos-testing` 합니다. Chaos Mesh의 다른 네임스페이스 이름은 지원되지 않습니다.
* 비정상 Azure Kubernetes Service 클러스터 관리자 역할은 시스템 할당 관리 ID에 할당되어야 합니다.

## <a name="issues-creating-or-designing-an-experiment"></a>실험 만들기 또는 디자인 문제

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>오류를 추가할 때 내 리소스가 대상 리소스 목록에 표시되지 않음
오류를 추가할 때 대상 리소스 목록에 오류가 있는 대상으로 지정할 리소스가 표시되지 않으면 다음 문제 중 때문일 수 있습니다.
* **구독** 필터는 대상이 배포된 구독을 제외하도록 설정됩니다. 구독 필터를 클릭하고 선택한 구독을 수정합니다.
* 리소스가 아직 온보딩되지 않았습니다. **대상** 보기를 방문하여 대상을 사용하도록 설정합니다. 이 작업을 완료한 후에는 오류 추가 창을 닫고 다시 열어 업데이트된 대상 목록을 확인해야 합니다.
* 해당 오류의 대상 유형에 대한 리소스가 아직 활성화되지 않았습니다. 오류 [라이브러리를](chaos-studio-fault-library.md) 참조하여 오류에 사용되는 대상 유형을 확인하고 **대상** 뷰를 방문하여 해당 대상 유형을 사용하도록 설정합니다(microsoft-agent 오류의 경우 에이전트 기반 또는 다른 모든 대상 형식의 경우 service-direct). 이 작업을 완료한 후에는 오류 추가 창을 닫고 다시 열어 업데이트된 대상 목록을 확인해야 합니다.
* 리소스에는 해당 오류에 대한 기능이 아직 활성화되어 있지 않습니다. 오류 [라이브러리를](chaos-studio-fault-library.md) 참조하여 오류에 대한 기능 이름을 확인하고 대상 보기로 가서 **대상** 리소스에 대한 **작업 관리를** 클릭합니다. 실행하려는 오류에 해당하는 기능 확인란을 선택하고 **저장을** 클릭합니다. 이 작업을 완료한 후에는 오류 추가 창을 닫고 다시 열어 업데이트된 대상 목록을 확인해야 합니다.
* 리소스가 최근에 온보딩되었으며 아직 Azure Resource Graph 표시되지 않았습니다. 대상 리소스 목록은 Azure Resource Graph 쿼리되며, 새 대상을 사용하도록 설정한 후 업데이트가 Azure Resource Graph 전파되는 데 최대 5분이 걸릴 수 있습니다. 몇 분 정도 기다렸다가 오류 추가 창을 다시 엽니다.

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>실험을 만들 때 오류가 발생합니다. `The microsoft:agent provider requires a managed identity`

이 오류는 에이전트가 가상 머신에 배포되지 않은 경우에 발생합니다. 설치 지침은 [에이전트 기반 오류를 사용하는 실험 만들기 및 실행을 참조하세요.](chaos-studio-tutorial-agent-based-portal.md)

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>실험을 만들 때 오류가 발생합니다. `The content media type '<null>' is not supported. Only 'application/json' is supported.`

ARM 템플릿 또는 Chaos Studio REST API 사용하여 실험을 만드는 경우 이 오류가 발생할 수 있습니다. 오류는 실험 정의에 잘못된 형식의 JSON이 있음을 나타냅니다. Visual Studio Code 같은 {} \[ JSON linter를 사용하여 일치하지 않는 중괄호 또는 대괄호( 및 )와 같은 구문 오류가 있는지 \] 확인합니다.

## <a name="issues-running-an-experiment"></a>실험 실행 문제

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>시작한 후 실험의 실행 상태는 "실패"입니다.

Azure Portal **실험** 목록에서 실험 이름을 클릭하여 실험 개요 를 **확인합니다.** **기록** 섹션에서 실패한 실험 실행 옆의 **세부 정보를** 클릭하여 자세한 오류 정보를 확인합니다.

![실험 기록](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>오류로 에이전트 기반 오류가 발생했습니다. 대상이 올바르게 온보딩되고 실험 msi에 적절한 읽기 권한이 제공되었는지 확인합니다.

이 문제는 알려진 문제가 있는 Azure Portal 사용하여 에이전트를 온보딩한 경우 발생할 수 있습니다. 에이전트 기반 대상을 사용하도록 설정해도 가상 머신 또는 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하지 않습니다.

이 문제를 해결하려면 Azure Portal 가상 머신 또는 가상 머신 확장 **집합으로** 이동하고 ID로 이동하여 사용자 할당 탭을 열고 가상 머신에 사용자 **할당** ID를 **추가합니다.** 완료되면 에이전트가 연결하기 위해 가상 머신을 다시 부팅해야 할 수 있습니다.
