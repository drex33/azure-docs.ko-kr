---
title: 자습서 - Azure에서 Linux 가상 머신 구성 관리
description: 이 자습서에서는 Linux 가상 머신에서 변경 내용을 식별하고 패키지 업데이트를 관리하는 방법에 대해 알아봅니다.
author: mgoedtel
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fae7a6065f04b34ea952dd474919d43dad46d0df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690958"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>자습서: Azure에서 Linux 가상 머신 변경 내용 및 업데이트 모니터링

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure [변경 추적](../../automation/change-tracking/overview.md)을 사용하면 변경 내용을 쉽게 식별할 수 있으며 [업데이트 관리](../../automation/update-management/overview.md)를 사용하면 Azure Linux VM의 운영 체제 업데이트를 관리할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Linux 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.30 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-vm"></a>VM 만들기

작동 중인 진단과 메트릭을 확인하려면 VM이 필요합니다. 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupMonitor* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

이제 [az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 *myVM* 이라는 VM을 만들고 SSH 키가 *~/.ssh/* 에 없으면 생성합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>소프트웨어 업데이트 관리

업데이트 관리를 사용하면 Azure Linux VM에 대한 업데이트 및 패치를 관리할 수 있습니다.
VM에서 직접, 사용 가능한 업데이트의 상태를 빠르게 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하여 업데이트가 VM에 성공적으로 적용되었는지 확인할 수 있습니다.

가격 책정 정보에 대해서는 [업데이트 관리를 위한 Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

### <a name="enable-update-management"></a>업데이트 관리 사용

VM에 대한 업데이트 관리를 사용합니다.

1. 화면 왼쪽에서 **가상 머신** 를 선택합니다.
2. 목록에서 VM을 선택합니다.
3. VM 화면의 **작업** 섹션에서 **업데이트 관리** 를 선택합니다. **업데이트 관리 사용** 화면이 열립니다.

이 VM에 대해 업데이트 관리가 사용되도록 설정되어 있는지를 확인하기 위해 유효성 검사가 수행됩니다.
유효성 검사 중에는 Log Analytics 작업 영역 및 연결된 Automation 계정이 확인되고 솔루션이 작업 영역에 있는지 여부가 확인됩니다.

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) 작업 영역은 기능 및 서비스(예: 업데이트 관리)에서 생성된 데이터를 수집하는 데 사용됩니다.
이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.
업데이트가 필요한 VM에서 추가 작업을 수행하려면 Azure Automation에서 VM에 대해 Runbook(예: 업데이트 다운로드 및 적용)을 실행할 수 있습니다.

또한 유효성 검사 프로세스는 VM에 Log Analytics 에이전트 및 Automation 하이브리드 runbook worker가 프로비전되었는지 확인합니다. 이 에이전트는 VM과 통신하고 업데이트 상태에 대한 정보를 얻습니다.

Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용** 을 선택하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

등록 중에 다음 필수 구성 요소 중 하나가 누락된 것으로 확인되면 자동으로 추가됩니다.

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) 작업 영역
* [Automation 계정](../../automation/index.yml)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)가 VM에서 사용되도록 설정됩니다.

**업데이트 관리** 화면이 열립니다. 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용** 을 선택합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다.

![업데이트 관리 솔루션 사용](./media/tutorial-monitoring/manage-updates-update-enable.png)

솔루션을 사용하도록 설정하는 데 최대 15분이 걸릴 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다. 솔루션이 사용되도록 설정되면 VM의 누락된 업데이트에 대한 정보가 Azure Monitor 로그로 이동됩니다. 이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.

### <a name="view-update-assessment"></a>업데이트 평가 보기

**업데이트 관리** 를 사용하도록 설정하면 **업데이트 관리** 화면이 나타납니다. 업데이트 평가가 완료되면 **누락 업데이트** 탭에 누락된 업데이트 목록이 표시됩니다.

 ![업데이트 상태 보기](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

**업데이트 관리** 화면 맨 위에서 **업데이트 배포 예약** 을 클릭하여 VM에 대한 새 업데이트 배포를 예약합니다. **새 배포 업데이트** 화면에서 다음 정보를 지정합니다.

새 업데이트 배포를 만들려면 **업데이트 배포 예약** 을 선택합니다. **새 업데이트 배포** 페이지가 열립니다. 다음 표에 설명된 속성의 값을 입력하고 **만들기** 를 클릭합니다.

| 속성 | Description |
| --- | --- |
| Name |업데이트 배포를 식별하는 고유 이름입니다. |
|운영 체제| Linux 또는or Windows|
| 업데이트할 그룹 |Azure 머신의 경우 구독, 리소스 그룹, 위치 및 태그의 조합을 기반으로 쿼리를 정의하여 배포에 포함할 Azure VM의 동적 그룹을 빌드합니다. </br></br>비 Azure 머신의 경우 저장된 기존 검색을 선택하여 배포에 포함할 비 Azure 머신 그룹을 선택합니다. </br></br>자세한 내용은 [동적 그룹](../../automation/update-management/configure-groups.md)을 참조하세요.|
| 업데이트할 컴퓨터 |저장된 검색, 가져온 그룹을 선택하거나 드롭다운에서 머신을 선택하고 개별 머신을 선택합니다. **머신** 을 선택한 경우 머신의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다.</br> Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../../azure-monitor/logs/computer-groups.md)을 참조하세요. |
|업데이트 분류|필요한 모든 업데이트 분류를 선택합니다.|
|업데이트 포함/제외|**포함/제외** 페이지를 엽니다. 포함 또는 제외할 업데이트는 별도의 탭에 있습니다. 포함이 처리되는 방식에 대한 자세한 내용은 [업데이트 배포 예약](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment)을 참조하세요. |
|일정 설정|시작 시간을 선택하고 되풀이에 대해 [한 번] 또는 [정기]를 선택합니다.|
| 사전 스크립트 + 사후 스크립트|배포 전후에 실행할 스크립트를 선택합니다.|
| 유지 관리 기간 |업데이트에 대해 설정되는 시간(분)입니다. 값은 30분 이상 6시간 이하여야 합니다. |
| 다시 부팅 제어| 다시 부팅을 처리하는 방법을 결정합니다. 사용 가능한 옵션은 다음과 같습니다.</br>필요한 경우 다시 부팅(기본값)</br>항상 다시 부팅</br>다시 부팅 안 함</br>다시 부팅만 - 업데이트 설치 안 함|

업데이트 배포를 프로그래밍 방식으로 만들 수도 있습니다. REST API를 사용하여 업데이트 배포를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요. 주간 업데이트 배포를 만드는 데 사용할 수 있는 샘플 Runbook도 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

일정 구성을 완료한 후 **만들기** 단추를 클릭하여 상태 대시보드로 돌아갑니다.
**예약됨** 표에는 만든 배포 일정이 표시됩니다.

### <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작되면 **업데이트 관리** 화면의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 볼 수 있습니다.
현재 실행 중인 경우 상태가 **진행 중** 으로 표시됩니다. 성공적으로 완료되면 **성공** 으로 바뀝니다.
배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패** 로 나타납니다.
완료된 배포 업데이트를 선택하여 해당 업데이트 배포에 대한 대시보드를 확인합니다.

![특정 배포에 대한 업데이트 배포 상태 대시보드](./media/tutorial-monitoring/manage-updates-view-results.png)

**업데이트 결과** 타일에는 VM의 총 업데이트 수 및 배포 결과 요약이 표시됩니다.
오른쪽의 표에는 각 업데이트에 대한 자세한 분석과 다음 값 중 하나로 표시되는 설치 결과가 제공됩니다.

* **시도 안 함** - 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* **성공** - 업데이트했습니다.
* **실패** -업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그** 를 선택합니다.

**출력** 타일을 선택하여 대상 VM의 업데이트 배포를 관리하는 Runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류** 를 선택합니다.

## <a name="monitor-changes-and-inventory"></a>변경 내용 및 인벤토리 모니터링

컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대한 인벤토리를 수집하고 볼 수 있습니다. 컴퓨터 구성을 추적하면 환경 전체에서 작동 문제를 정확히 식별하고 컴퓨터의 상태를 더 잘 파악할 수 있습니다.

### <a name="enable-change-and-inventory-management"></a>변경 내용 및 재고 관리 사용

VM에 대해 변경 내용 및 인벤토리 관리 사용:

1. 화면 왼쪽에서 **가상 머신** 를 선택합니다.
2. 목록에서 VM을 선택합니다.
3. VM 화면의 **작업** 섹션에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다. **변경 내용 추적 및 인벤토리 사용** 화면이 열립니다.

사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용** 을 선택합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다. 메뉴에서 솔루션이 분리되어 있어도 동일한 솔루션입니다. 한 솔루션을 사용하도록 설정하면 VM에서 둘 다 사용할 수 있습니다.

![변경 내용 추적 및 인벤토리 사용](./media/tutorial-monitoring/manage-inventory-enable.png)

솔루션을 사용하도록 설정한 후 데이터가 표시되기까지 VM에서 인벤토리가 수집되는 동안 시간이 걸릴 수 있습니다.

### <a name="track-changes"></a>변경 내용 추적 작업

VM에서 **작업** 아래에 있는 **변경 내용 추적** 을 선택합니다. **설정 편집** 을 선택하면 **변경 내용 추적** 페이지가 표시됩니다. 추적할 설정의 형식을 선택한 다음, **+ 추가** 를 선택하여 설정을 구성합니다. 사용 가능한 Linux 옵션은 **Linux 파일** 입니다.

변경 내용 추적에 대한 자세한 내용은 [VM에서 변경 내용 문제 해결](../../automation/troubleshoot/change-tracking.md)을 참조하세요.

### <a name="view-inventory"></a>인벤토리 보기

VM에서 **작업** 아래에 있는 **인벤토리** 를 선택합니다. **소프트웨어** 탭에는 검색된 소프트웨어가 나열되는 테이블 목록이 있습니다. 각 소프트웨어 레코드에 대한 높은 수준의 자세한 정보는 이 테이블에서 볼 수 있습니다. 이러한 세부 정보에는 소프트웨어 이름, 버전, 게시자, 마지막으로 새로 고친 시간이 포함됩니다.

![인벤토리 보기](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>활동 로그 및 변경 내용 모니터링

VM에 대한 **변경 내용 추적** 페이지에서 **활동 로그 연결 관리** 를 선택합니다. 이 작업은 **Azure 활동 로그** 페이지를 엽니다. **연결** 을 선택하여 변경 내용 추적을 VM에 대한 Azure 활동 로그에 연결합니다.

이 설정을 사용하도록 설정하면 VM에 대한 **개요** 페이지로 이동하여 VM을 중지하기 위해 **중지** 를 선택합니다. 메시지가 표시되면 **예** 를 선택하여 VM을 중지합니다. 할당이 취소되면 **시작** 을 선택하여 VM을 다시 시작합니다.

VM을 중지하고 시작하면 활동 로그에 이벤트가 기록됩니다. **변경 내용 추적** 페이지로 다시 이동합니다. 페이지 아래쪽의 **이벤트** 탭을 선택합니다. 잠시 후에 이벤트가 차트와 테이블에 표시됩니다. 각 이벤트를 선택하여 해당 이벤트에 대한 자세한 정보를 볼 수 있습니다.

![활동 로그의 변경 내용 보기](./media/tutorial-monitoring/manage-activitylog-view-results.png)

차트에는 시간이 지남에 따라 발생한 변경 내용이 표시됩니다. 활동 로그 연결을 추가하면 위쪽의 선 그래프에 Azure 활동 로그 이벤트가 표시됩니다. 막대 그래프의 각 행은 서로 다른 추적 가능한 변경 유형을 나타냅니다. 이러한 유형은 Linux 디먼, 파일 및 소프트웨어입니다. 변경 탭에는 시각화에 표시된 변경 내용에 대한 세부 정보가 변경이 발생한 시간의 내림차순(가장 최근이 첫 번째)으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM에 대한 변경 내용 추적 및 업데이트 관리를 구성하고 검토했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹 및 VM 만들기
> * Linux 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링

VM 모니터링에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [가상 머신 모니터링](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)