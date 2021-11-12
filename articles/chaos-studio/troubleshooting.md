---
title: 일반적인 Azure 비정상 스튜디오 문제 해결
description: Azure 비정상 스튜디오를 사용할 때 발생 하는 일반적인 문제를 해결 하는 방법 알아보기
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 4a5bac1261d16ee53b468c007d49d586a9bc8341
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331930"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Azure 비정상 스튜디오 문제 해결

비정상 스튜디오를 사용 하는 경우 가끔 몇 가지 문제가 발생할 수 있습니다. 이 문서에서는 일반적인 문제 및 문제 해결 단계에 대해 자세히 설명 합니다.

## <a name="general-troubleshooting-tips"></a>일반 문제 해결 팁

다음 소스는 비정상 스튜디오 문제를 해결할 때 유용 합니다.
1. **활동 로그**: [Azure 활동 로그](../azure-monitor/essentials/activity-log.md) 에는 대상 및/또는 기능을 사용 하도록 설정 하 고, 에이전트를 설치 하 고, 실험을 만들거나 실행 하는 등의 비정상 스튜디오 작업을 포함 하 여 구독의 모든 만들기, 업데이트 및 삭제 작업에 대 한 레코드가 있습니다. 활동 로그에서 오류가 발생 하면 비정상 스튜디오를 사용 하는 데 필요한 사용자 작업이 완료 되지 않았을 수 있습니다. 대부분의 서비스 직접 오류는 Azure Resource Manager 작업을 실행 하 여 오류를 삽입 하므로 활동 로그에는 일부 서비스 직접 오류를 실험 하는 동안 삽입 되는 오류 레코드도 있습니다.
2. **실험 세부 정보**: 실험 실행 세부 정보에는 개별 실험 실행의 상태 및 오류가 표시 됩니다. 실험 세부 정보에서 특정 오류를 열면 실패 한 리소스와 오류에 대 한 오류 메시지가 표시 됩니다. [실험 세부 정보에 액세스 하는 방법에 대해 자세히 알아보세요](chaos-studio-run-experiment.md#view-experiment-history-and-details).
3. **에이전트 로그**: 에이전트 기반 오류를 사용 하는 경우 가상 머신에 RDP 또는 SSH를 수행 하 여 에이전트가 오류를 실행 하지 못한 이유를 이해 해야 할 수 있습니다. 에이전트 로그에 액세스 하기 위한 지침은 운영 체제에 따라 달라 집니다.
    * 비정상 **Windows 에이전트**: 에이전트 로그는 원본 AzureChaosAgent를 사용 하 여 응용 프로그램 범주의 Windows 이벤트 로그에 있습니다. 에이전트는 오류 활동 및 일반 상태 검사 (비정상 스튜디오 에이전트 서비스를 인증 하 고이 로그에 대 한 인증 및 통신 하는 기능) 이벤트를이 로그에 추가 합니다.
    * 비정상 **linux 에이전트**: linux 에이전트는 systemd를 사용 하 여 linux 서비스로 에이전트 프로세스를 관리 합니다. 에이전트에 대 한 systemd 저널 (에이전트 서비스에서 기록 하는 이벤트)를 보려면 명령을 실행 `journalctl -u azure-chaos-agent` 합니다.
4. **Vm 확장 상태**: 에이전트 기반 오류를 사용 하는 경우 VM 확장이 설치 되어 있고 정상 상태 인지 확인 해야 할 수도 있습니다. Azure Portal에서 가상 머신으로 이동 하 고 **확장** 또는 **확장 + 응용 프로그램** 으로 이동 합니다. ChaosAgent 확장을 클릭 하 고 다음 필드를 찾습니다.
    * **상태** 에 "프로 비전이 성공 했습니다."가 표시 되어야 합니다. 다른 모든 상태는 에이전트를 설치 하지 못했음을 나타냅니다. 모든 [시스템 요구 사항이](chaos-studio-limitations.md#limitations) 충족 되었는지 확인 한 후 에이전트를 다시 설치 해 보십시오.
    * **처리기 상태가** "준비"로 표시 되어야 합니다. 다른 모든 상태는 에이전트가 설치 되어 있지만 비정상 스튜디오 서비스에 연결할 수 없음을 나타냅니다. 모든 [네트워크 요구 사항이](chaos-studio-limitations.md#limitations) 충족 되 고 사용자 할당 관리 id가 가상 컴퓨터에 추가 되었는지 확인 하 고 다시 부팅 합니다.

## <a name="issues-onboarding-a-resource"></a>리소스 온 보 딩 문제

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>리소스는 Azure Portal의 대상 목록에 표시 되지 않습니다.
비정상 스튜디오 대상 목록에서 사용 하도록 설정할 리소스가 표시 되지 않으면 다음 문제가 원인일 수 있습니다.
* 리소스가 비정상 [스튜디오에 대해 지원 되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio)에 있지 않습니다.
* 리소스가 비정상 [스튜디오에서 지원 되는 리소스 형식이](chaos-studio-fault-providers.md)아닙니다.
* 리소스는 대상 목록의 필터에서 필터링 된 구독 또는 리소스 그룹에 있습니다. 구독 및 리소스 그룹 필터를 변경 하 여 리소스를 확인 합니다.

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>대상 목록에 대상 및/또는 기능 기능이 실패 하거나 제대로 표시 되지 않음
대상 및/또는 기능을 사용 하도록 설정할 때 오류가 표시 되 면 다음 단계를 수행 합니다.
1. 등록 중인 리소스에 대 한 적절 한 권한이 있는지 확인 합니다. 대상 및/또는 기능을 사용 하도록 설정 하려면 리소스 범위에서 Microsoft./또는 권한이 필요 합니다. \* 참가자와 같은 기본 제공 역할에는 모든 Microsoft 비정상 작업에 대 한 사용 권한이 포함 된 와일드 카드 읽기 및 쓰기 권한이 있습니다.
2. 대상 및 기능 목록이 업데이트 될 때까지 몇 분 정도 기다립니다. Azure Portal는 Azure Resource Graph를 사용 하 여 대상 및 기능 온 보 딩에 대 한 정보를 수집 하 고 업데이트를 전파 하는 데 최대 5 분이 걸릴 수 있습니다.
3. 리소스에 "사용 안 함"이 계속 표시 되 면 다음 단계를 수행 합니다.
    1. 리소스를 다시 사용 하도록 설정 해 봅니다.
    2. 리소스를 계속 해 서 장애가 발생 하는 경우 활동 로그를 방문 하 여 실패 한 대상 만들기 작업을 찾아 자세한 오류 정보를 확인 합니다.
4. 리소스가 "사용"으로 표시 되 고 온 보 딩 기능이 실패 한 경우 다음 단계를 수행 합니다.
    1. 대상 목록에서 리소스의 **작업 관리** 단추를 클릭 합니다. 선택 하지 않은 기능을 확인 하 고 **저장** 을 클릭 합니다.
    2. 기능을 계속 해 서 실패 하는 경우 활동 로그를 방문 하 여 실패 한 대상 만들기 작업을 찾아 자세한 오류 정보를 확인 합니다.

## <a name="prerequisite-issues"></a>필수 조건 문제

일부 문제는 필수 구성 요소가 누락 되었기 때문에 발생 합니다. 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>가상 머신에서 에이전트 기반 오류가 발생 함
누락 된 필수 구성 요소와 관련 된 다양 한 이유로 인해 에이전트 기반 오류가 실패할 수 있습니다.
* Linux Vm에서 [CPU 압력](chaos-studio-fault-library.md#cpu-pressure), [실제 메모리 부족](chaos-studio-fault-library.md#physical-memory-pressure), 디스크 I/o [압력](chaos-studio-fault-library.md#disk-io-pressure-linux)및 [임의 스트레스](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) 오류에 대 한 모든 요구 사항을 가상 컴퓨터에 설치 해야 [합니다.](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) 스트레스를 설치 하는 방법에 대 한 자세한 내용은 오류 전제 조건 섹션을 참조 하세요.
* Linux 또는 Windows vm에서 에이전트 기반 대상 사용 중에 제공 된 사용자 할당 관리 id도 가상 머신에 추가 해야 합니다.
* Linux 또는 Windows vm에서 실험에 대 한 시스템 할당 관리 id는 vm에 대 한 읽기 권한자 역할을 부여 받아야 합니다. 가상 머신 참가자와 같은 높은 수준의 역할에는 비정상 \* 스튜디오 에이전트 서비스에서 가상 머신의 microsoft 에이전트 대상 프록시 리소스를 읽는 데 필요한/읽기 작업이 포함 되어 있지 않습니다.

### <a name="aks-chaos-mesh-faults-fail"></a>AKS 비정상 메시 오류 실패
누락 된 필수 구성 요소와 관련 된 다양 한 이유로 인해 AKS 된 메시 오류가 실패할 수 있습니다.
* AKS 비정상 메시 오류를 사용 하기 전에 AKS 클러스터에 먼저 비정상 메시를 설치 해야 합니다. 지침은 [AKS의 비정상 메시 오류 자습서](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster)에서 찾을 수 있습니다.
* 비정상 메시는 버전 2.0.4 이상을 이상 이어야 합니다. AKS 클러스터에 연결 하 고를 실행 하 여 비정상 메시 버전을 가져올 수 있습니다 `helm version chaos-mesh` .
* 비정상 상태 메시는 네임 스페이스와 함께 설치 해야 합니다 `chaos-testing` . 비정상 메시의 다른 네임 스페이스 이름은 지원 되지 않습니다.
* Azure Kubernetes Service 클러스터 관리자 역할은 비정상 실험의 시스템 할당 관리 id에 할당 되어야 합니다.

## <a name="issues-creating-or-designing-an-experiment"></a>실험 만들기 또는 디자인 문제

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>오류를 추가 하는 경우 리소스는 대상 리소스 목록에 표시 되지 않습니다.
오류를 추가 하는 경우 대상 리소스 목록에서 오류로 대상으로 지정할 리소스가 표시 되지 않으면 다음 문제가 원인일 수 있습니다.
* **구독** 필터는 대상이 배포 되는 구독을 제외 하도록 설정 되어 있습니다. 구독 필터를 클릭 하 고 선택한 구독을 수정 합니다.
* 리소스를 아직 등록 하지 않았습니다. **대상 보기를** 방문 하 여 대상을 사용 하도록 설정 합니다. 이 작업이 완료 되 면 오류 추가 창을 닫았다가 다시 열어서 업데이트 된 대상 목록을 확인 해야 합니다.
* 해당 오류의 대상 형식에 대해 리소스를 아직 사용 하도록 설정 하지 않았습니다. 오류 [라이브러리](chaos-studio-fault-library.md) 를 참조 하 여 오류에 사용 되는 대상 유형을 확인 하 고 대상 보기를 방문 하 여 해당 대상 유형을 사용 하도록 설정 합니다 (에이전트 **기반에서 다른** 모든 대상 유형의 경우에는 에이전트 기반). 이 작업이 완료 되 면 오류 추가 창을 닫았다가 다시 열어서 업데이트 된 대상 목록을 확인 해야 합니다.
* 리소스에는 해당 오류를 아직 사용할 수 있는 기능이 없습니다. 오류 [라이브러리](chaos-studio-fault-library.md) 를 참조 하 여 오류에 대 한 기능 이름을 확인 하 고 대상 **보기를 방문 하 여 대상** 리소스에 대 한 **작업 관리** 를 클릭 합니다. 실행 하려는 오류에 해당 하는 기능에 대 한 확인란을 선택 하 고 **저장** 을 클릭 합니다. 이 작업이 완료 되 면 오류 추가 창을 닫았다가 다시 열어서 업데이트 된 대상 목록을 확인 해야 합니다.
* 리소스가 최근에 등록 된 것으로, 아직 Azure 리소스 Graph에 표시 되지 않았습니다. 대상 리소스 목록은 azure 리소스 Graph에서 쿼리하고, 새 대상을 활성화 한 후 업데이트가 azure 리소스 Graph에 전파 되는 데 최대 5 분이 걸릴 수 있습니다. 몇 분 정도 기다린 후 오류 추가 창을 다시 엽니다.

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>실험을 만들 때 오류가 발생 합니다. `The microsoft:agent provider requires a managed identity`

이 오류는 에이전트가 가상 컴퓨터에 배포 되지 않은 경우에 발생 합니다. 설치 지침은 [에이전트 기반 오류를 사용 하는 실험 만들기 및 실행](chaos-studio-tutorial-agent-based-portal.md)을 참조 하세요.

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>실험을 만들 때 오류가 발생 합니다. `The content media type '<null>' is not supported. Only 'application/json' is supported.`

ARM 템플릿 또는 비정상 스튜디오 REST API를 사용 하 여 실험을 만드는 경우이 오류가 발생할 수 있습니다. 이 오류는 실험 정의에 형식이 잘못 된 JSON이 있음을 나타냅니다. 일치 하지 않는 중괄호 또는 대괄호 (및)와 같은 구문 오류가 있는지 확인 하 고 {} \[ \] Visual Studio Code JSON linter을 사용 하 여 확인 합니다.

## <a name="issues-running-an-experiment"></a>실험 실행 문제

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>시작 후 내 실험의 실행 상태는 "실패 함"입니다.

Azure Portal **실험** 목록에서 실험 이름을 클릭 하 여 **실험 개요** 를 확인 합니다. **기록** 섹션에서 실패 한 실험 실행 옆에 있는 **세부 정보** 를 클릭 하 여 자세한 오류 정보를 확인 합니다.

![실험 기록](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>오류가 발생 하 여 에이전트 기반 오류가 실패 했습니다. 대상이 올바르게 등록 실험 msi에 적절 한 읽기 권한이 제공 되는지 확인 하세요.

이 문제는 알려진 문제를 포함 하는 Azure Portal를 사용 하 여 에이전트를 등록 경우 발생할 수 있습니다. 에이전트 기반 대상을 사용 하도록 설정 해도 사용자 할당 관리 id를 가상 머신 또는 가상 머신 확장 집합에 할당 하지 않습니다.

이 문제를 해결 하려면 Azure Portal에서 가상 머신 또는 가상 머신 확장 집합으로 이동 하 고, **id** 로 이동 하 여 **사용자 할당 됨** 탭을 열고, 사용자 할당 Id를 가상 머신에 **추가** 합니다. 완료 되 면 에이전트를 연결 하기 위해 가상 머신을 다시 부팅 해야 할 수 있습니다.
