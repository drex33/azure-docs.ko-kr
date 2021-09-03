---
title: VM 인사이트 문제 해결
description: VM 인사이트 설치 문제를 해결합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: a7bcce4d18f6bcfe299a31dae3eb036bef12da9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535772"
---
# <a name="troubleshoot-vm-insights"></a>VM 인사이트 문제 해결
이 문서에서는 VM 인사이트를 사용하거나 사용하도록 설정하는 데 문제가 있는 경우에 대한 해결 정보를 제공합니다.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>머신에서 VM 인사이트를 사용하도록 설정할 수 없음
Azure Portal에서 Azure 가상 머신을 온보딩하면 다음과 같은 단계가 이루어집니다.

- 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
- 에이전트가 이미 설치되어 있는 경우 VM 확장을 사용하여 Log Analytics 에이전트가 Azure 가상 머신에 설치됩니다.
- 필요한 경우 종속성 에이전트가 확장을 사용하여 Azure 가상 머신에 설치됩니다.
  
온보딩 프로세스 중에는 해당 단계마다 검증이 이루어지며 알림 상태가 포털에 표시됩니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 데이터를 볼 수 있을 때까지 5~10분이 소요됩니다.

온보딩 프로세스를 수행한 후 가상 머신을 온보딩해야 한다는 메시지가 표시되는 경우 해당 프로세스가 완료될 때까지 최대 30분이 걸릴 수 있습니다. 문제가 지속되면 다음 섹션에서 가능한 원인을 확인합니다.

### <a name="is-the-virtual-machine-running"></a>가상 머신이 실행되고 있나요?
 가상 머신이 잠시 동안 꺼져 있거나 현재 꺼져 있거나 최근에 켜진 경우 데이터가 도착할 때까지 표시할 데이터가 없습니다.

### <a name="is-the-operating-system-supported"></a>해당 운영 체제가 지원되나요?
운영 체제가 [지원되는 운영 체제](vminsights-enable-overview.md#supported-operating-systems)의 목록에 없는 경우 확장 설치가 실패하고 데이터가 도착할 때까지 기다리는 중이라는 메시지가 표시됩니다.

### <a name="did-the-extension-install-properly"></a>확장이 제대로 설치되었나요?
가상 머신을 온보딩해야 한다는 메시지가 표시되면 확장 중 하나 또는 둘 모두를 올바르게 설치하지 못했을 수 있습니다. Azure Portal에서 가상 머신에 대한 **확장** 페이지를 확인하여 다음 확장이 목록에 있는지 확인합니다.

| 운영 체제 | 에이전트 | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentLinux |

설치된 확장 목록에 운영 체제에 대한 두 개의 확장이 표시되지 않으면 이를 설치해야 합니다. 확장이 목록에 있지만 상태가 ‘프로비저닝 성공’으로 표시되지 않으면 확장을 제거하고 다시 설치해야 합니다.

### <a name="do-you-have-connectivity-issues"></a>연결 문제가 있나요?
Windows 머신의 경우 *TestCloudConnectivity* 도구를 사용하여 연결 문제를 식별할 수 있습니다. 이 도구는 기본적으로 *%SystemDrive%\Program Files\Microsoft Monitoring Agent\Agent* 폴더에 에이전트와 함께 설치됩니다. 관리자 권한 명령 프롬프트에서 도구를 실행합니다. 그러면 결과가 반환되고 테스트에 실패한 곳이 강조 표시됩니다. 

![TestCloudConnectivity 도구](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>추가 에이전트 문제 해결

Log Analytics 에이전트 관련 문제 해결에 대한 내용은 다음 문서를 참조하세요.

- [Windows용 Log Analytics 에이전트의 문제를 해결하는 방법](../agents/agent-windows-troubleshoot.md)
- [Linux용 Log Analytics 에이전트의 문제를 해결하는 방법](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>성능 보기에 데이터가 없음
에이전트가 올바르게 설치되어 있지만 성능 보기에 데이터가 표시되지 않으면 다음 섹션에서 가능한 원인을 확인하세요.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Log Analytics 작업 영역이 데이터 한도에 도달했나요?
[데이터 수집에 대한 용량 예약 및 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 확인하세요.

### <a name="is-your-virtual-machine-agent-connected-to-azure-monitor-logs"></a>가상 머신 에이전트가 Azure Monitor Logs에 연결되어 있나요?

Azure Portal의 Azure Monitor 메뉴에 있는 **로그** 에서 Log Analytics를 엽니다. 컴퓨터에 대한 다음 쿼리를 실행합니다.

```kuso
Heartbeat
| where Computer == "my-computer"
| sort by TimeGenerated desc 
```

데이터가 표시되지 않거나 컴퓨터가 최근에 하트비트를 보내지 않았다면 에이전트에 문제가 있을 수 있습니다. 에이전트 문제 해결 정보는 위의 섹션을 참조하세요.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>지도 보기에 가상 머신이 표시되지 않음

### <a name="is-the-dependency-agent-installed"></a>종속성 에이전트가 설치되어 있나요?
 위의 섹션에 나와 있는 정보를 사용하여 종속성 에이전트가 설치되어 있고 제대로 작동하는지 확인합니다.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Log Analytics 무료 계층을 사용하고 계신가요?
[Log Analytics 무료 계층](https://azure.microsoft.com/pricing/details/monitor/)은 최대 5개의 고유한 서비스 맵 머신을 허용하는 레거시 요금제입니다. 따라서 이전의 5개 서비스 맵 컴퓨터에서 데이터를 더 이상 보내지 않더라도 이후의 모든 컴퓨터는 서비스 맵에 표시되지 않습니다.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>가상 머신이 로그 및 성능 데이터를 Azure Monitor 로그로 전송하고 있나요?
[성능 보기에 데이터가 없음](#performance-view-has-no-data) 섹션에서 로그 쿼리를 사용하여 가상 머신에 대한 데이터가 수집되고 있는지 확인합니다. 데이터가 수집되고 있지 않는 경우 위에 나오는 TestCloudConnectivity 도구를 사용하여 연결 문제가 있는지 확인합니다.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>가상 머신이 지도 보기에 표시되지만 데이터가 누락됨
가상 머신이 지도 보기에 있는 경우 종속성 에이전트가 설치되어 실행 중이지만 커널 드라이버가 로드되지 않은 것입니다. 다음 위치에서 로그 파일을 확인합니다.

| 운영 체제 | 로그 | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

파일의 마지막 줄에 커널이 로드되지 않은 이유가 표시되어야 합니다. 예를 들어, 커널을 업데이트 한 경우 Linux에서 커널이 지원되지 않을 수 있습니다.
## <a name="next-steps"></a>다음 단계

- VM 인사이트 에이전트 온보딩에 대한 자세한 내용은 [VM 인사이트 사용 개요](vminsights-enable-overview.md)를 참조하세요.
