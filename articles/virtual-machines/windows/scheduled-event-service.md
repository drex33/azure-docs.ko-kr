---
title: Azure에서 VM에 예약된 이벤트 모니터링하기
description: Azure Virtual Machines에서 예약된 이벤트를 모니터링하는 방법을 알아봅니다.
author: mysarn
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: b8e13f8a8bd577e3e872d3fc59b099e34fd495d1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694214"
---
# <a name="monitor-scheduled-events-for-your-azure-vms"></a>Azure VM에 예약된 이벤트 모니터링하기

**적용 대상:** VM: heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합: heavy_check_mark: 단일 확장 집합

업데이트는 매일 Azure의 다른 부분에 적용되므로 서비스를 안전하게 실행하고 최신 상태로 유지할 수 있습니다. 계획된 업데이트 외에 계획되지 않은 이벤트도 발생할 수 있습니다. 예를 들어, 하드웨어 성능 저하 또는 오류가 감지되면 Azure 서비스는 계획되지 않은 유지 관리를 수행해야 할 수 있습니다. 실시간 마이그레이션, 메모리 보존 업데이트를 사용하고 업데이트가 미치는 영향에 대해 엄격한 기준을 유지하므로, 대부분의 경우 이러한 이벤트는 고객에 게 거의 투명하게 진행되며, 가상 머신에 전혀 영향을 주지 않거나 기껏해야 몇 초 정도만 가상 머신 작동을 중단합니다. 그러나 일부 애플리케이션의 경우 가상 머신이 몇 초 정도만 중단되어도 영향을 받을 수 있습니다. 이러한 애플리케이션에 대해 최상의 환경을 보장하기 위해 예정된 Azure 유지 관리를 미리 파악하는 것이 중요합니다. [Scheduled Events 서비스](scheduled-events.md)는 프로그래밍 인터페이스를 통해 예정된 유지 관리에 대한 알림을 제공하고, 유지 관리를 정상적으로 처리할 수 있도록 합니다. 

이 문서에서는 예약된 이벤트를 사용하여 VM에 영향을 미칠 수 있는 유지 관리 이벤트에 대한 알림을 받고, 모니터링 및 분석에 도움이 되는 몇 가지 기본 자동화를 구축할 수 있는 방법을 보여 줍니다.


## <a name="routing-scheduled-events-to-log-analytics"></a>예약된 이벤트를 Log Analytics로 라우팅

Scheduled Events는 [Azure Instance Metadata Service](instance-metadata-service.md)의 일부로 제공되며 모든 Azure 가상 머신에서 사용할 수 있습니다. 고객은 가상 머신의 엔드포인트를 쿼리하여 예약된 유지 관리 알림을 찾고, 상태 저장 및 순환에서 가상 머신 제외 등의 완화 작업을 수행하기 위한 자동화를 작성할 수 있습니다. Azure 유지 관리 이벤트의 감사 로그를 유지할 수 있도록 Scheduled Events를 기록하는 자동화를 작성하는 것이 좋습니다. 

이 문서에서는 Log Analytics에 유지 관리 Scheduled Events를 캡처하는 방법을 안내합니다. 그런 다음, 팀에 메일을 보내고 가상 머신에 영향을 준 모든 이벤트의 기록 보기를 가져오는 등의 몇 가지 기본적인 알림 작업을 트리거합니다. 이벤트 집계 및 자동화를 위해 [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)를 사용하지만 다른 모니터링 솔루션을 사용하여 이러한 로그를 수집하고 자동화를 트리거할 수도 있습니다.

![이벤트 수명 주기를 보여 주는 다이어그램](./media/notifications/events.png)

## <a name="prerequisites"></a>필수 구성 요소

이 예제에서는 [가용성 집합에 Windows 가상 머신](tutorial-availability-sets.md)을 만들어야 합니다. Scheduled Events는 가용성 집합, 클라우드 서비스, 가상 머신 확장 집합 또는 독립 실행형 VM에서 가상 머신에 영향을 줄 수 있는 변경 내용에 대한 알림을 제공합니다. 가용성 집합의 다른 모든 VM에 대한 이벤트를 가져오기 위해 수집기 역할을 하는 VM 중 하나에서 예약된 이벤트를 폴링하는 [서비스](https://github.com/microsoft/AzureScheduledEventsService)를 실행할 것입니다.    

자습서가 끝난 후에 그룹 리소스 그룹을 삭제하지 마세요.

또한 가용성 집합의 VM에서 정보를 집계하는 데 사용할 [Log Analytics 작업 영역을 만들어야](../../azure-monitor/logs/quick-create-workspace.md) 합니다.

## <a name="set-up-the-environment"></a>환경 설정

이제 가용성 집합에 두 개의 초기 VM이 있어야 합니다. 이제 동일한 가용성 집합에 `myCollectorVM`이라는 세 번째 VM을 만들어야 합니다. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)에서 프로젝트의 설치 .zip 파일을 다운로드합니다.

**myCollectorVM** 에 연결하고 .zip 파일을 가상 머신에 복사한 후 모든 파일의 압축을 풉니다. VM에서 PowerShell 프롬프트를 엽니다. 프롬프트를 `SchService.ps1`을 포함하는 폴더(예: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`)로 이동하고 서비스를 설정합니다.

```powershell
.\SchService.ps1 -Setup
```

서비스를 시작합니다.

```powershell
.\SchService.ps1 -Start
```

이제 서비스는 예약된 이벤트에 대해 10초마다 폴링을 시작하고 이벤트를 승인하여 유지 관리를 신속하게 진행합니다.  중지, 다시 부팅, 다시 배포 및 선점은 Schedule Events에서 캡처되는 이벤트입니다.   이벤트를 승인하기 전에 스크립트를 확장하여 일부 완화를 트리거할 수 있습니다.

서비스 상태를 확인하고 실행 중인지 확인합니다.

```powershell
.\SchService.ps1 -status  
```

`Running`을 반환해야 합니다.

이제 서비스는 예약된 이벤트에 대해 10초마다 폴링을 시작하고 이벤트를 승인하여 유지 관리를 신속하게 진행합니다.  중지, 다시 부팅, 다시 배포 및 선점은 Schedule Events에서 캡처되는 이벤트입니다. 이벤트를 승인하기 전에 스크립트를 확장하여 일부 완화를 트리거할 수 있습니다.

위의 이벤트가 Schedule Events 서비스에서 캡처될 경우 애플리케이션 이벤트 로그 이벤트 상태, 이벤트 유형, 리소스(가상 머신 이름) 및 NotBefore(최소 알림 기간)에 기록됩니다. 애플리케이션 이벤트 로그에서 ID가 1234인 이벤트를 찾을 수 있습니다.

해당 서비스를 설정하고 시작하면 Windows 애플리케이션 로그에 이벤트를 기록합니다.   이 작업이 진행되는지 확인하려면 가용성 집합의 가상 머신 중 하나를 다시 시작합니다. 그러면 이벤트 뷰어의 애플리케이션 로그 > Windows 로그에서 VM이 다시 시작되었음을 나타내는 이벤트가 기록된 것을 볼 수 있습니다. 

![이벤트 뷰어의 스크린샷](./media/notifications/event-viewer.png)

이벤트가 Schedule Events 서비스에서 캡처될 경우 애플리케이션 이벤트 로그에 이벤트 상태, 이벤트 유형, 리소스(VM 이름) 및 NotBefore(최소 알림 기간)와 함께 기록됩니다. 애플리케이션 이벤트 로그에서 ID가 1234인 이벤트를 찾을 수 있습니다.

> [!NOTE] 
> 이 예제에서는 가상 머신이 가용성 집합에 있으므로 단일 가상 머신을 Schedule Events를 수신하여 Log Analytics 작업 공간으로 라우팅하는 수집기로 지정할 수 있습니다. 독립 실행형 가상 머신이 있는 경우 모든 가상 머신에서 서비스를 실행한 다음, Log Analytics 작업 영역에 개별적으로 연결할 수 있습니다.
>
> 이 설치에서는 Windows를 선택했지만 Linux에서도 비슷한 솔루션을 디자인할 수 있습니다.

언제든지 `–stop` 및 `–remove`스위치를 사용하여 예약된 이벤트 서비스를 중지/제거할 수 있습니다.

## <a name="connect-to-the-workspace"></a>작업 영역에 연결


이제 Log Analytics 작업 영역을 수집기 VM에 연결하려고 합니다. Log Analytics 작업 영역은 리포지토리 역할을 하며, 수집기 VM에서 애플리케이션 로그를 캡처하도록 이벤트 로그 수집을 구성합니다. 

 예약된 이벤트를 서비스에서 애플리케이션 로그로 저장되는 이벤트 로그로 라우팅하려면 Log Analytics 작업 영역에 가상 머신을 연결해야 합니다.  
 
1. 만든 작업 영역에 대한 페이지를 엽니다.
1. **데이터 원본에 연결** 에서 **Azure VM(Virtual Machines)** 을 선택합니다.

    ![데이터 원본으로 VM에 연결](./media/notifications/connect-to-data-source.png)

1. **myCollectorVM** 을 검색한 후 선택합니다. 
1. **myCollectorVM** 에 대한 새 페이지에서 **연결** 을 선택합니다.

그러면 가상 머신에 [Microsoft Monitoring Agent](../extensions/oms-windows.md)가 설치됩니다. VM을 작업 영역에 연결하고 확장을 설치하는 데 몇 분 정도 걸립니다. 

## <a name="configure-the-workspace"></a>작업 영역 구성

1. 작업 영역에 대한 페이지를 열고 **고급 설정** 을 선택합니다.
1. 왼쪽 메뉴에서 **데이터** 를 선택하고 **Windows 이벤트 로그** 를 선택합니다.
1. **다음 이벤트 로그에서 수집** 에서 *애플리케이션* 이라고 입력한 후 목록에서 **애플리케이션** 을 선택합니다.

    ![고급 설정 선택](./media/notifications/advanced.png)

1. **오류**, **경고** 및 **정보** 를 선택한 후 **저장** 을 선택하여 설정을 저장합니다.


> [!NOTE]
> 약간 시간이 걸리며, 로그를 사용할 수 있을 때까지 최대 10분이 소요될 수 있습니다. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Azure Monitor를 사용하여 경고 규칙 만들기 


이벤트가 Log Analytics에 푸시되면 다음 [쿼리](../../azure-monitor/logs/log-analytics-tutorial.md)를 실행하여 일정 이벤트를 찾을 수 있습니다.

1. 페이지 위쪽에서 **로그** 를 선택하고 텍스트 상자에 다음을 붙여 넣습니다.

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **저장** 을 선택한 뒤, 이름에 `ogQuery`를 입력하고 유형으로 **쿼리** 를 남겨 놓고 `VMLogs`를 **범주** 에 입력한 다음, **저장** 을 선택합니다. 

    ![쿼리 저장](./media/notifications/save-query.png)

1. **새 경고 규칙** 을 선택합니다. 
1. **규칙 만들기** 페이지에서 `collectorworkspace`를 **리소스** 로 둡니다.
1. **조건** 에서 *고객 로그 검색이 있을 때마다<login undefined>* 항목을 선택합니다. **신호 논리 구성** 페이지가 열립니다.
1. **임계값** 에서 *0* 를 입력하고 **완료** 를 선택합니다.
1. **작업** 에서 **작업 그룹 만들기** 를 선택합니다. **작업 그룹 추가** 페이지가 열립니다.
1. **작업 그룹 이름** 에 *myActionGroup* 을 입력합니다.
1. **약식 이름** 에 *myActionGroup* 을 입력합니다.
1. **리소스 그룹** 에서 **myResourceGroupAvailability** 를 선택합니다.
1. 작업 아래의 **작업 이름** 에 **Email** 을 입력하고 **메일/SMS/푸시/음성** 을 선택합니다. 그러면 **메일/SMS/푸시/음성** 페이지가 열립니다.
1. **메일** 을 선택하고 메일 주소를 입력한 다음, **확인** 을 선택합니다.
1. **작업 그룹 추가** 페이지에서 **확인** 을 선택합니다. 
1. **규칙 만들기** 페이지의 **경고 정보** 에서 *경고 규칙 이름* 에 **myAlert** 를 입력하고, *설명* 에 **메일 경고 규칙** 을 입력합니다.
1. 작업이 완료되면 **경고 규칙 만들기** 를 선택합니다.
1. 가용성 집합에서 VM 중 하나를 다시 시작합니다. 몇 분 이내에 경고가 트리거된 메일을 받게 됩니다.

경고 규칙을 관리하려면 리소스 그룹으로 이동하여 왼쪽 메뉴에서 **경고** 를 선택한 다음, 페이지 맨 위에서 **경고 규칙 관리** 를 선택합니다.

     
## <a name="next-steps"></a>다음 단계

자세히 알아보려면 GitHub의 [예약된 이벤트 서비스](https://github.com/microsoft/AzureScheduledEventsService) 페이지를 참조하세요.