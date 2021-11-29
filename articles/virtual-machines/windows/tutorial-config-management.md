---
title: 자습서 - Azure에서 Windows 가상 머신 구성 관리
description: 이 자습서에서는 Windows 가상 머신에서 변경 내용을 식별하고 패키지 업데이트를 관리하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1c88456d9da3e9e46f6215ef9a7df6d23e116bcb
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133129465"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>자습서: Azure에서 Windows 가상 머신 변경 내용 및 업데이트 모니터링
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

Azure [변경 내용 추적](../../automation/change-tracking/overview.md) 및 [업데이트 관리](../../automation/update-management/overview.md)를 사용하면 Azure에서 Windows 가상 머신의 변경 내용을 쉽게 식별하고 해당 VM의 운영 체제 업데이트를 관리할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Windows 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

Azure Cloud Shell은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 Azure 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell에서 코드 블록을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다.

또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하고 Cloud Shell 탭에 붙여넣은 다음, Enter 키를 선택하여 코드를 실행합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이 자습서에서 Azure 모니터링을 구성하고 업데이트 관리를 수행하려면 Azure의 Windows VM이 필요합니다.

먼저 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

다음으로, [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만듭니다. 다음 예제는 `East US` 위치에 `myVM`이라는 VM을 만듭니다. 아직 없는 경우 `myResourceGroupMonitor` 리소스 그룹과 지원 네트워크 리소스가 만들어집니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

리소스 및 VM을 만드는 데 몇 분 정도 걸립니다.

## <a name="manage-windows-updates"></a>Windows 업데이트 관리

업데이트 관리를 사용하여 Azure Windows VM에 대한 업데이트 및 패치를 관리할 수 있습니다. VM에서 바로 다음 작업을 빠르게 수행할 수 있습니다.

- 사용 가능한 업데이트의 상태를 평가합니다.
- 필수 업데이트의 설치를 예약합니다.
- 배포 결과를 검토하여 업데이트가 VM에 성공적으로 적용되었는지 확인합니다.

가격 책정 정보에 대해서는 [업데이트 관리를 위한 Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

### <a name="enable-update-management"></a>업데이트 관리 사용

VM에 업데이트 관리를 사용하려면 다음을 수행합니다.

1. Azure Portal에서 VM으로 이동합니다(검색 창에서 **가상 머신** 을 검색한 다음, 목록에서 VM 선택).
1. 작업 아래에서 **게스트 + 호스트 업데이트** 를 선택합니다.
1. **업데이트 관리로 이동** 을 클릭합니다.
1. **업데이트 관리 사용** 창이 열립니다.

이 VM에 업데이트 관리를 사용하도록 설정되었는지 확인하기 위한 유효성 검사가 수행됩니다. 유효성 검사를 통해 Log Analytics 작업 영역 및 연결된 Automation 계정이 있는지, 그리고 솔루션이 작업 영역에 있는지 여부가 확인됩니다.

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) 작업 영역은 기능 및 서비스(예: 업데이트 관리)에서 생성된 데이터를 수집하는 데 사용됩니다. 이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.

업데이트가 필요한 VM에서 추가 작업을 수행하려면 Azure Automation을 사용하여 VM에 대해 Runbook을 실행하면 됩니다. 이러한 작업에는 업데이트 다운로드 또는 적용이 포함됩니다.

또한 유효성 검사 프로세스서는 VM이 MMA(Microsoft Monitoring Agent) 및 Automation Hybrid Runbook Worker를 통해 프로비저닝되는지도 확인합니다. 이 에이전트를 사용하여 VM과 통신하고 업데이트 상태에 대한 정보를 얻습니다.

**업데이트 관리 사용** 창에서 Log Analytics 작업 영역 및 Automation 계정을 선택한 다음, **사용** 을 선택합니다. 솔루션을 사용하도록 설정될 때까지 최대 15분이 걸립니다.

다음 필수 구성 요소 중에 온보딩 과정에서 누락된 것이 있으면 자동으로 추가됩니다.

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) 작업 영역
* [Automation](../../automation/index.yml)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md). VM에서 사용되도록 설정됩니다.

솔루션이 사용하도록 설정된 후에는 **업데이트 관리** 창이 열립니다. 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용** 을 선택합니다. 이러한 옵션이 흐리게 표시되면 VM에 또 다른 자동화 솔루션을 사용하도록 설정되며, 해당 솔루션의 작업 영역과 Automation 계정을 사용해야 합니다.

![업데이트 관리 솔루션 사용](./media/tutorial-monitoring/manageupdates-update-enable.png)

업데이트 관리 솔루션을 사용하도록 설정될 때까지 최대 15분이 걸립니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다. 솔루션이 사용되도록 설정되면 VM의 누락된 업데이트에 대한 정보가 Azure Monitor 로그로 이동됩니다. 데이터를 분석에 사용할 수 있게 될 때까지 30분에서 6시간까지 걸릴 수 있습니다.

### <a name="view-an-update-assessment"></a>업데이트 평가 보기

업데이트 관리를 사용하도록 설정하면 **업데이트 관리** 창이 나타납니다. 업데이트 평가가 완료되면 **누락 업데이트** 탭에 누락된 업데이트 목록이 표시됩니다.

 ![업데이트 상태 보기](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택합니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

VM에 대한 새 업데이트 배포를 예약하려면 **업데이트 관리** 창 맨 위에서 **업데이트 배포 예약** 을 선택합니다. **새 배포 업데이트** 창에서 다음 정보를 지정합니다.

| 옵션 | Description |
| --- | --- |
| **이름** |업데이트 배포를 식별하는 고유 이름을 제공합니다. |
|**운영 체제**| **Linux** 또는 **Windows** 를 선택합니다.|
| **업데이트할 그룹** |Azure에 호스팅되는 VM의 경우 구독, 리소스 그룹, 위치 및 태그의 조합을 기반으로 쿼리를 정의합니다. 이 쿼리는 배포에 포함할 Azure 호스팅 VM의 동적 그룹을 만듭니다. </br></br>Azure에 호스팅되지 않는 VM의 경우 저장된 기존 검색을 선택합니다. 이 검색을 사용하여 배포에 포함할 VM 그룹을 선택할 수 있습니다. </br></br> 자세한 내용은 [동적 그룹](../../automation/update-management/configure-groups.md)을 참조하세요.|
| **업데이트할 머신** |**저장된 검색**, **가져온 그룹** 또는 **머신** 을 선택합니다.<br/><br/>**머신** 을 선택하는 경우 드롭다운 목록에서 개별 머신을 선택할 수 있습니다. 각 머신의 준비 상태는 테이블의 **업데이트 에이전트 준비** 열에 표시됩니다.</br></br> Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../../azure-monitor/logs/computer-groups.md)을 참조하세요. |
|**업데이트 분류**|필요한 모든 업데이트 분류를 선택합니다.|
|**업데이트 포함/제외**|**포함/제외** 창을 열려면 이 옵션을 선택합니다. 포함할 업데이트와 제외할 업데이트는 별도의 탭에 있습니다. 포함이 처리되는 방식에 대한 자세한 내용은 [업데이트 배포 예약](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment)을 참조하세요. |
|**일정 설정**|시작 시간을 선택하고, **한 번** 또는 **정기** 를 선택합니다.|
| **사전 스크립트 + 사후 스크립트**|배포 전후에 실행할 스크립트를 선택합니다.|
| **유지 관리 기간** | 업데이트에 설정할 시간(분)을 입력합니다. 유효한 값의 범위는 30~360분입니다. |
| **다시 부팅 제어**| 다시 부팅을 처리할 방법을 선택합니다. 선택 가능한 항목은 다음과 같습니다.<ul><li>**필요한 경우 다시 부팅**</li><li>**항상 다시 부팅**</li><li>**다시 부팅 안 함**</li><li>**다시 부팅만**</li></ul>**필요한 경우 다시 부팅** 이 기본값입니다. **다시 부팅만** 을 선택하면 업데이트가 설치되지 않습니다.|

일정 구성을 완료한 후 **만들기** 를 클릭하여 상태 대시보드로 돌아갑니다. **예약됨** 표에는 만든 배포 일정이 표시됩니다.

업데이트 배포를 프로그래밍 방식으로 만들 수도 있습니다. REST API를 사용하여 업데이트 배포를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요. 주간 업데이트 배포를 만드는 데 사용할 수 있는 샘플 Runbook도 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

### <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작되면 **업데이트 관리** 창의 **업데이트 배포** 탭에서 배포 상태를 볼 수 있습니다.

배포가 현재 실행 중이면 상태가 "진행 중"으로 표시됩니다. 성공적으로 완료되면 상태가 "성공"으로 변경됩니다. 배포에서 하나 이상의 업데이트가 실패하면 상태가 "부분적으로 실패"로 표시됩니다.

완료된 업데이트 배포를 선택하여 해당 배포에 대한 대시보드를 확인합니다.

![특정 배포에 대한 업데이트 배포 상태 대시보드](./media/tutorial-monitoring/manageupdates-view-results.png)

**업데이트 결과** 타일에는 VM의 총 업데이트 수 및 배포 결과 요약 정보가 표시됩니다. 오른쪽 표에는 각 업데이트에 대한 자세한 분석 및 설치 결과가 표시됩니다. 각 결과의 값은 다음 중 하나입니다.

* **시도 안 함**: 업데이트가 설치되지 않았습니다. 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않았습니다.
* **성공**: 업데이트했습니다.
* **실패**: 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그** 를 선택합니다.

**출력** 타일을 선택하여 대상 VM의 업데이트 배포를 관리하는 Runbook의 작업 스트림을 확인합니다.

배포 오류에 대한 자세한 정보를 보려면 **오류** 를 선택합니다.

## <a name="monitor-changes-and-inventory"></a>변경 내용 및 인벤토리 모니터링

컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대한 인벤토리를 수집하고 볼 수 있습니다. 머신의 구성을 추적하면 환경 전체에서 작동 문제를 정확히 식별하고 머신의 상태를 보다 정확하게 파악할 수 있습니다.

### <a name="enable-change-and-inventory-management"></a>변경 내용 및 인벤토리 관리 사용

VM에 변경 내용 및 인벤토리 관리를 사용하도록 설정하려면 다음을 수행합니다.

1. 창의 맨 왼쪽에서 **가상 머신** 을 선택합니다.
1. 목록에서 VM을 선택합니다.
1. VM 창의 **작업** 에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다.
1. **변경 내용 추적 및 인벤토리 사용** 창이 열립니다.

사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용** 을 선택합니다. 옵션이 흐리게 표시되면 이미 VM에 자동화 솔루션을 사용하도록 설정된 것입니다. 이 경우 이미 설정된 작업 영역 및 Automation 계정을 사용해야 합니다.

메뉴에서 솔루션이 따로 표시되더라도 동일한 솔루션입니다. 한 솔루션을 사용하도록 설정하면 VM에서 둘 다 사용할 수 있습니다.

![변경 내용 추적 및 인벤토리 사용](./media/tutorial-monitoring/manage-inventory-enable.png)

솔루션을 사용하도록 설정한 후 VM에 인벤토리가 수집되고 데이터가 표시될 때까지 다소 시간이 걸릴 수 있습니다.

### <a name="track-changes"></a>변경 내용 추적 작업

VM의 **작업** 에서 **변경 내용 추적** 을 선택하고 **설정 편집** 을 선택합니다. **변경 내용 추적** 창이 열립니다. 추적할 설정의 형식을 선택한 다음, **+ 추가** 를 선택하여 설정을 구성합니다.

Windows에 사용 가능한 설정은 다음과 같습니다.

* Windows 레지스트리
* Windows 파일

변경 내용 추적에 대한 자세한 내용은 [VM에서 변경 내용 문제 해결](../../automation/troubleshoot/change-tracking.md)을 참조하세요.

### <a name="view-inventory"></a>인벤토리 보기

VM에서 **작업** 아래에 있는 **인벤토리** 를 선택합니다. **소프트웨어** 탭에는 검색된 소프트웨어를 나열하는 표가 있습니다. 각 소프트웨어 레코드에 대한 개략적인 정보가 이 표에 표시됩니다. 이러한 세부 정보에는 소프트웨어 이름, 버전, 게시자, 마지막으로 새로 고친 시간이 포함됩니다.

![인벤토리 보기](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>활동 로그 및 변경 내용 모니터링

VM의 **변경 내용 추적** 창에서 **활동 로그 연결 관리** 를 선택하여 **Azure 활동 로그** 창을 엽니다. **연결** 을 선택하여 변경 내용 추적을 VM의 Azure 활동 로그에 연결합니다.

변경 내용 추적이 사용하도록 설정된 후에는 VM의 **개요** 창으로 이동하고, **중지** 를 선택하여 VM을 중지합니다. 메시지가 표시되면 **예** 를 선택하여 VM을 중지합니다. VM 할당이 취소되면 **시작** 을 선택하여 VM을 다시 시작합니다.

VM을 중지하고 다시 시작하면 활동 로그에 이벤트가 기록됩니다. **변경 내용 추적** 창으로 돌아가서 창 맨 아래에 있는 **이벤트** 탭을 선택합니다. 잠시 후 차트와 표에 이벤트가 표시됩니다. 각 이벤트를 선택하여 해당 이벤트에 대한 자세한 정보를 볼 수 있습니다.

![활동 로그의 변경 내용 보기](./media/tutorial-monitoring/manage-activitylog-view-results.png)

이전 차트는 시간에 따라 발생한 변경 내용을 표시합니다. Azure 활동 로그 연결을 추가하면 위쪽의 선 그래프에 Azure 활동 로그 이벤트가 표시됩니다.

막대 그래프의 각 행은 다른 추적 가능한 변경 유형을 나타냅니다. 이러한 유형은 Linux 디먼, 파일, Windows 레지스트리 키, 소프트웨어 및 Windows 서비스입니다. **변경 내용** 탭에는 변경 세부 정보가 표시됩니다. 변경 내용은 발생한 순서대로 표시되며, 가장 최근의 변경 내용이 가장 먼저 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM에 대한 변경 내용 추적 및 업데이트 관리를 구성하고 검토했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹 및 VM 만들기
> * Windows 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링

다음 자습서로 넘어가서 VM 모니터링에 대해 알아보세요.

> [!div class="nextstepaction"]
> [가상 머신 모니터링](tutorial-monitor.md)
