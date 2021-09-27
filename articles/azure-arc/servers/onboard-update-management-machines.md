---
title: Azure Automation 업데이트 관리에서 컴퓨터 커넥트
description: 이 문서에서는 Automation 업데이트 관리에서 관리 하는 Azure Arc에 하이브리드 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634320"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>Automation 업데이트 관리에서 Azure로 하이브리드 컴퓨터 커넥트

Azure Automation 업데이트 관리으로 관리 되는 온-프레미스 또는 기타 클라우드 환경에서 호스트 되는 하나 이상의 Windows 또는 Linux 가상 머신 또는 물리적 서버에 대해 Azure Arc 사용 서버를 사용 하도록 설정할 수 있습니다. 이 온 보 딩 프로세스는 [연결 된 컴퓨터 에이전트](agent-overview.md)의 다운로드 및 설치를 자동화 합니다. 컴퓨터를 Azure Arc 사용 서버에 연결 하기 위해 컴퓨터에 [대화형으로 연결](onboard-portal.md) 하는 데 사용 되는 권한 있는 id 대신 Azure Active Directory [서비스 주체가](../../active-directory/develop/app-objects-and-service-principals.md) 사용 됩니다. 이 서비스 주체는 이러한 컴퓨터에 대 한 온 보 딩 프로세스의 일부로 자동으로 생성 됩니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. 지원되는 지역 및 기타 관련 고려 사항에 대한 자세한 내용은 [Azure 지원 지역](overview.md#supported-regions)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="how-it-works"></a>작동 방식

온 보 딩 프로세스가 시작 되 면 테 넌 트에서 Active Directory [서비스 주체가](../../active-directory/fundamentals/service-accounts-principal.md) 만들어집니다. 

대상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하 고 구성 하려면 **AzureConnectedMachines** 이라는 마스터 Runbook이 Azure 샌드박스에서 실행 됩니다. 마스터 runbook은 컴퓨터에서 검색 된 운영 체제에 따라 컴퓨터에서 직접 시스템 [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) 역할에 따라 실행 되는 **AzureConnectedMachineWindows** 또는 **AzureConnectedMachineLinux** 라는 자식 runbook을 호출 합니다. Runbook 작업 출력은 작업 기록에 기록 되며, [Azure Portal](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal) 또는 [Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell)를 사용 하 여 해당 [상태 요약](../../automation/automation-runbook-execution.md#job-statuses) 을 확인 하거나 특정 runbook 작업의 세부 정보를 자세히 살펴볼 수 있습니다. Azure Automation에서 Runbook을 실행하면 Automation 계정의 활동 로그에 세부 정보가 기록됩니다. 로그 사용에 대한 자세한 내용은 [활동 로그에서 세부 정보 검색](../../automation/manage-runbooks.md#retrieve-details-from-activity-log)을 참조하세요.

마지막 단계에서는 서비스 주체를 사용 하 여 azure에 대 한 연결을 설정 하 고,이 명령을 사용 하 여 `azcmagent` azure에서 컴퓨터를 리소스로 등록 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법에서는 automation 계정에서 runbook 작업을 만들 수 있도록 [Automation 작업 운영자](../../automation/automation-role-based-access-control.md#automation-job-operator) 역할 이상의 멤버 여야 합니다. 

Azure Policy 사용 하도록 설정 하 여 [runbook 실행을 관리](../../automation/enforce-job-execution-hybrid-worker.md) 하 고 Hybrid Runbook Worker 그룹에 대해 runbook 실행의 대상을 적용 하는 경우이 정책을 사용 하지 않도록 설정 해야 합니다. 그렇지 않으면 컴퓨터를 Arc 사용 서버에 등록 하는 runbook 작업은 실패 합니다. 

## <a name="add-machines-from-the-azure-portal"></a>Azure Portal에서 컴퓨터 추가

Arc 사용 서버를 사용 하 여 하이브리드 컴퓨터를 구성 하려면 다음 단계를 수행 합니다. 프로세스를 성공적으로 완료 하려면 서버 또는 컴퓨터의 전원이 켜져 있고 온라인 상태 여야 합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **서버-Azure Arc** 페이지로 이동한 다음 왼쪽 위에 있는 **추가** 를 선택 합니다.

1. **방법 선택** 페이지에서 **업데이트 관리에서 관리 되는 서버 추가 (미리 보기)** 타일을 선택 하 고 **서버 추가** 를 선택 합니다.

1. **기본 사항** 페이지에서 다음을 구성 합니다.

    1. **리소스 그룹** 드롭다운 목록에서 머신을 관리할 리소스 그룹을 선택합니다.
    1. **지역** 드롭다운 목록에서 서버 메타데이터를 저장할 Azure 지역을 선택합니다.
    1. 머신이 인터넷에 연결하기 위해 프록시 서버를 통해 통신하는 경우 프록시 IP 주소 또는 머신에서 프록시 서버와 통신하는 데 사용할 이름과 포트 번호를 지정합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.
    1. **다음: 컴퓨터** 를 선택 합니다.

1. **컴퓨터** 페이지의 드롭다운 목록에서 업데이트 관리 기능이 사용 하도록 설정 된 구독 및 **Automation 계정을** 선택 하 고 Azure Arc 사용 서버에 **등록** 하려는 컴퓨터를 포함 합니다.

   Automation 계정을 지정한 후 아래 목록은 해당 Automation 계정에 대해 업데이트 관리에서 관리 하는 비 Azure 컴퓨터를 반환 합니다. Windows 및 Linux 컴퓨터가 모두 나열 되 고 각각에 대해 **추가** 를 선택 합니다.

   **선택 항목 검토** 를 선택 하 고 컴퓨터를 제거 하려는 경우 **작업** 열에서 **제거** 를 선택 하 여 선택 항목을 검토할 수 있습니다. 

   선택 항목을 확인 한 후 **다음: 태그** 를 선택 합니다.

1. **태그** 페이지에서 하나 이상의 **이름** / **값** 쌍을 지정 하 여 표준을 지원 합니다. **다음: 검토 + 추가** 를 선택 합니다.

1. **검토 _ 추가** 페이지에서 요약 정보를 검토 하 고 **컴퓨터 추가** 를 선택 합니다. 그래도 변경해야 하는 경우 **이전** 을 선택합니다.

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치 되 고 Azure Arc 사용 서버에 연결 하도록 구성 된 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

- [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md)과 머신을 관리하는 방법을 알아봅니다. 머신이 예상되는 Log Analytics 작업 영역에 보고되는지, [VM 인사이트](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 사용하는지 그리고 기타 등등을 확인하세요.