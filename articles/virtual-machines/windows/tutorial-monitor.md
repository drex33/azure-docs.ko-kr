---
title: 자습서 - Azure에서 Windows 가상 머신 모니터링
description: 이 자습서에서는 Windows 가상 머신에서 실행되는 성능 및 검색된 애플리케이션 구성 요소를 모니터링하는 방법에 대해 알아봅니다.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0834fa6fd7eff41f37eb8f16dc3429189be284cb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688679"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>자습서: Azure에서 Windows 가상 머신 모니터링
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

Azure 모니터링은 에이전트를 사용하여 Azure VM에서 부팅 및 성능 데이터를 수집하고 이 데이터를 Azure Storage에 저장하며 포털, Azure PowerShell 모듈 및 Azure CLI를 통해 액세스할 수 있도록 합니다. 고급 모니터링은 성능 메트릭을 수집하고, VM에 설치된 애플리케이션 구성 요소를 검색하고, 성능 차트와 종속성 맵을 포함하여 VM용 Azure Monitor와 함께 제공됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * VM 호스트 메트릭 보기
> * VM용 Azure Monitor 사용
> * VM 성능 메트릭 보기
> * 경고 만들기

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-virtual-machine"></a>가상 머신 만들기

이 자습서에서 Azure 모니터링을 구성하고 업데이트 관리를 수행하려면 Azure의 Windows VM이 필요합니다. 먼저 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myVM* 이라는 VM을 만듭니다. 아직 없는 경우 *myResourceGroupMonitorMonitor* 리소스 그룹 및 지원 네트워크 리소스가 만들어집니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

리소스 및 VM을 만드는 데 몇 분 정도 걸립니다.

## <a name="view-boot-diagnostics"></a>부트 진단 보기

Windows 가상 머신을 부팅하면 부트 진단 에이전트가 문제 해결 목적에 사용할 수 있는 화면 출력을 캡처합니다. 이 기능은 기본적으로 사용하도록 설정되어 있습니다. 캡처한 스크린샷은 기본적으로 생성되는 Azure Storage 계정에 저장됩니다.

[Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata) 명령으로 부트 진단 데이터를 가져올 수 있습니다. 다음 예제에서는 부트 진단이 *c:\* 드라이브의 루트에 다운로드됩니다.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>호스트 메트릭 보기

Windows VM에는 Azure에서 상호 작용하는 전용 호스트 VM이 있습니다. 이 호스트에 대한 메트릭이 자동으로 수집되며, Azure Portal에서 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹** 을 클릭하고 **myResourceGroupMonitor** 를 선택한 다음 리소스 목록에서 **myVM** 을 선택합니다.
2. 호스트 VM이 어떻게 수행되는지 확인하려면 VM 블레이드에서 **메트릭** 을 클릭한 다음 **사용 가능한 메트릭** 에서 호스트 메트릭 중 하나를 선택합니다.

    ![호스트 메트릭 보기](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>고급 모니터링 사용

VM용 Azure Monitor를 사용하여 Azure VM의 모니터링을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal에서 **리소스 그룹** 을 클릭하고 **myResourceGroupMonitor** 를 선택한 다음 리소스 목록에서 **myVM** 을 선택합니다.

2. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

3. **인사이트(미리 보기)** 페이지에서 **지금 시도해 보기** 를 선택합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    이 목록은 구독에서 VM이 배포된 기본 작업 영역 및 위치를 미리 선택합니다. 

    >[!NOTE]
    >VM의 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. 작업 영역은 [지원 지역](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 중 하나에 속해야 합니다.

모니터링을 사용하도록 설정한 후 몇 분 정도 기다려야 VM에 대한 성능 메트릭을 볼 수 있습니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VM 성능 메트릭 보기

VM용 Azure Monitor는 가상 머신이 얼마나 잘 실행되고 있는지 확인하기 위한 여러 가지 KPI(핵심 성과 지표)를 대상으로 하는 성능 차트 집합을 포함하고 있습니다. VM에서 액세스하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **리소스 그룹** 을 클릭하고 **myResourceGroupMonitor** 를 선택한 다음 리소스 목록에서 **myVM** 을 선택합니다.

2. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

3. **성능** 탭을 선택합니다.

이 페이지에는 성능 사용률 차트뿐 아니라 검색된 각 논리 디스크, 용량, 사용률, 측정별 총 평균 사용률에 대한 표가 포함되어 있습니다.

## <a name="create-alerts"></a>경고 만들기

특정 성능 메트릭을 기반으로 하는 경고를 만들 수 있습니다. 예를 들어 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지면 경고를 사용하여 사용자에게 알릴 수 있습니다. 경고는 Azure Portal에 표시되거나 전자 메일을 통해 보낼 수 있습니다. 또한 생성되는 경고에 대한 응답으로 Azure Automation Runbook 또는 Azure Logic Apps를 트리거할 수도 있습니다.

다음 예제에서는 평균 CPU 사용량에 대한 경고를 만듭니다.

1. Azure Portal에서 **리소스 그룹** 을 클릭하고 **myResourceGroupMonitor** 를 선택한 다음 리소스 목록에서 **myVM** 을 선택합니다.

2. VM 블레이드에서 **경고 규칙** 을 클릭한 다음, 경고 블레이드 위쪽에 있는 **메트릭 경고 추가** 를 클릭합니다.

3. *myAlertRule* 과 같이 경고에 대한 **이름** 을 입력합니다.

4. CPU 사용률이 5분 동안 1.0을 초과할 때 경고를 트리거하려면 다른 모든 기본값을 선택한 상태로 둡니다.

5. 필요한 경우 전자 메일 알림을 보내도록 *전자 메일 소유자, 참가자 및 읽기 권한자* 의 확인란을 선택합니다. 기본 작업은 포털에서 알림을 제공하는 것입니다.

6. **확인** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM의 성능을 구성하고 확인했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹 및 VM 만들기
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM용 Azure Monitor 사용
> * VM 메트릭 보기
> * 경고 만들기

Azure Security Center에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VM 보안 관리](../../security/fundamentals/overview.md)
