---
title: Azure Monitor의 와이어 데이터 솔루션 | Microsoft Docs
description: Wire Data는 Log Analytics 에이전트를 사용한 컴퓨터의 통합 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 로그 데이터와 결합됩니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 74d3544fd7e858a685b669ad826bc17e0eb6e64f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312923"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Azure Monitor(사용 중지됨)의 Wire Data 2.0(미리 보기) 솔루션

![Wire Data 기호](media/wire-data/wire-data2-symbol.png)

> [!NOTE]
> Wire Data 솔루션은 [VM 인사이트](../vm/vminsights-overview.md) 및 [서비스 맵 솔루션](../vm/service-map.md)으로 대체되었습니다.  둘 다 Log Analytics 에이전트와 Dependency Agent를 사용하여 네트워크 연결 데이터를 Azure Monitor로 수집합니다.
>
> Wire Data 솔루션에 대한 지원이 **2022년 3월 31일** 에 종료됩니다.  사용 중지 날짜가 될 때까지 Wire Data 2.0(미리 보기) 솔루션을 사용하는 기존 고객은 이를 계속 사용할 수 있습니다.
>
> 신규 및 기존 고객은 [VM 인사이트](../vm/vminsights-enable-overview.md) 또는 [서비스 맵 솔루션](../vm/service-map.md)을 설치해야 합니다.  수집하는 맵 데이터 세트는 Wire Data 2.0(미리 보기) 데이터 세트와 비교할 수 있습니다.  VM 인사이트에는 분석을 위한 추가 성능 데이터 및 기능과 함께 서비스 맵 데이터 세트가 포함되어 있습니다. 두 제품 모두 [Microsoft 센티널에 연결](../../sentinel/connect-data-sources.md#map-data-types-with-microsoft-sentinel-connection-options)되어 있습니다.

Wire Data는 사용자 환경의 Operations Manager에서 모니터링되는 데이터를 포함하여 Log Analytics 에이전트를 통해 Windows 연결 및 Linux 연결 컴퓨터에서 수집되는 통합 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 다른 로그 데이터와 결합됩니다.

Log Analytics 에이전트 외에 Wire Data 솔루션은 IT 인프라에서 컴퓨터에 설치하는 Microsoft 종속성 에이전트를 사용합니다. 종속성 에이전트는 사용된 다양한 프로토콜 및 포트를 포함하여 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)에서 네트워크 수준 2-3에 해당하는 컴퓨터와 주고 받는 네트워크 데이터를 모니터링합니다. 그런 다음 에이전트를 사용하여 Azure Monitor에 데이터를 보냅니다.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Azure Monitor VM 인사이트 또는 서비스 맵으로 마이그레이션

고객이 Wire Data 2.0(미리 보기)과 [VM 인사이트](../vm/vminsights-overview.md) 또는 [서비스 맵 솔루션](../vm/service-map.md)을 동일한 VM에서 이미 사용하도록 설정한 경우가 많습니다.  즉, VM에서 대체 제품을 사용할 수 있습니다.  간단히 [Log Analytics 작업 영역에서 Wire Data 2.0(미리 보기) 솔루션을 제거](./solutions.md?tabs=portal#remove-a-monitoring-solution)할 수 있습니다.

Wire Data 2.0(미리 보기)만 사용하도록 설정된 VM이 있는 경우 VM을 [VM 인사이트](../vm/vminsights-enable-overview.md) 또는 [서비스 맵 솔루션](../vm/service-map.md)에 온보딩한 다음 [Log Analytics 작업 영역에서 Wire Data 2.0(미리 보기) 솔루션을 제거할 수 있습니다](./solutions.md?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Azure Monitor VM 인사이트에서 VMConnection 테이블로 쿼리 마이그레이션

### <a name="agents-providing-data"></a>데이터를 제공하는 에이전트

#### <a name="wire-data-20-query"></a>Wire Data 2.0 쿼리

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM 인사이트 및 서비스 맵 쿼리

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>데이터를 제공하는 에이전트의 IP 주소

#### <a name="wire-data-20-query"></a>Wire Data 2.0 쿼리

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM 인사이트 및 서비스 맵 쿼리

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>원격 IP 주소별 모든 아웃바운드 통신

#### <a name="wire-data-20-query"></a>Wire Data 2.0 쿼리

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM 인사이트 및 서비스 맵 쿼리

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>프로토콜 이름별 받은 바이트

#### <a name="wire-data-20-query"></a>Wire Data 2.0 쿼리

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM 인사이트 및 서비스 맵 쿼리

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>프로세스별 네트워크 트래픽 용량(바이트 단위)

#### <a name="wire-data-20-query"></a>Wire Data 2.0 쿼리

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM 인사이트 및 서비스 맵 쿼리

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>더 많은 쿼리 예

추가 쿼리 예는 [VM 인사이트 로그 검색 문서](../vm/vminsights-log-search.md) 및 [VM 인사이트 경고 문서](../vm/monitor-virtual-machine-alerts.md)를 참조하세요.

## <a name="uninstall-wire-data-20-solution"></a>Wire Data 2.0 솔루션 제거

Wire Data 2.0을 제거하려면 Log Analytics 작업 영역에서 솔루션을 제거하기만 하면 됩니다.  결과는 다음과 같습니다.

- 작업 영역에 연결 된 Vm에서 네트워크 데이터 관리 팩이 제거 되 고 있습니다.
- 실시간 데이터 데이터 형식이 작업 영역에 더 이상 나타나지 않습니다.

Wire Data 솔루션을 제거하려면 [이 지침](./solutions.md?tabs=portal#remove-a-monitoring-solution)을 따르세요.

>[!NOTE]
>작업 영역에 서비스 맵 또는 VM 인사이트 솔루션이 있는 경우 이 솔루션 또한 관리 팩을 사용하므로 관리 팩은 제거되지 않습니다.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 관리 팩

Log Analytics 작업 영역에서 Wire Data가 활성화되면 해당 작업 영역의 모든 Windows 서버에 300KB 관리 팩이 전송됩니다. [연결된 관리 그룹](../agents/om-agents.md)에서 System Center Operations Manager 에이전트를 사용하는 경우 Dependency Monitor 관리 팩은 System Center Operations Manager에서 배포됩니다. 에이전트가 직접 연결되어 있으면 Azure Monitor가 관리 팩을 제공합니다.

관리 팩 이름은 Microsoft.IntelligencePacks.ApplicationDependencyMonitor입니다. 이것은 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs에 기록됩니다. 관리 팩에 사용된 데이터 원본은 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll입니다.

## <a name="uninstall-the-dependency-agent"></a>Dependency Agent 제거

>[!NOTE]
>Wire Data를 서비스 맵 또는 VM 인사이트로 바꾸려는 경우 Dependency Agent를 제거하면 안 됩니다.

다음 섹션을 사용하여 Dependency Agent를 제거할 수 있도록 돕습니다.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows에서 Dependency Agent 제거

관리자는 제어판을 통해 Windows용 Dependency Agent를 제거할 수 있습니다.

관리자는 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe를 실행하여 Dependency Agent를 제거할 수도 있습니다.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux의 Dependency Agent 제거

Linux에서 Dependency Agent를 완전히 제거하려면 에이전트 자체와 에이전트와 함께 자동으로 설치된 커넥터를 제거해야 합니다. 다음 단일 명령을 사용하여 둘 다 제거할 수 있습니다.

```bash
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 솔루션 사용

Azure Portal의 사용자 Log Analytics 작업 영역에 대한 **개요** 페이지에서 **Wire Data 2.0** 타일을 클릭하여 Wire Data 대시보드를 엽니다. 대시보드에는 다음 테이블의 블레이드가 포함되어 있습니다. 각 블레이드에는 지정된 범위 및 시간 범위에 대한 해당 블레이드의 기준과 일치하는 항목이 최대 10개까지 나열됩니다. 블레이드 맨 아래에서 **모두 보기** 를 클릭하거나 블레이드 헤더를 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

| **블레이드** | **설명** |
| --- | --- |
| 네트워크 트래픽을 캡처하는 에이전트 | 네트워크 트래픽을 캡처하는 에이전트의 수를 표시하고 트래픽을 캡처하는 상위 10개의 컴퓨터를 나열합니다. <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 목록에서 컴퓨터를 클릭하여 캡처된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |
| 로컬 서브넷 | 에이전트가 검색한 로컬 서브넷의 수를 보여 줍니다.  각각을 통해 전송된 바이트의 수와 함께 모든 서브넷을 나열하는 <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 목록에서 서브넷을 클릭하여 서브넷을 통해 전송된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |
| 애플리케이션 수준 프로토콜 | 에이전트에서 검색된 것으로 사용 중인 애플리케이션 수준 프로토콜의 수를 보여 줍니다. <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 프로토콜을 클릭하여 프로토콜을 사용하여 전송된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |

![Wire Data 대시보드](./media/wire-data/wire-data-dash.png)

**네트워크 트래픽을 캡처하는 에이전트** 블레이드를 사용하여 컴퓨터에서 사용되는 네트워크 대역폭 양을 확인할 수 있습니다. 이 블레이드는 사용자 환경에서 _chattiest_ 컴퓨터를 쉽게 찾을 수 있도록 도와 줍니다. 이러한 컴퓨터는 오버로드되거나 비정상적으로 작동하거나 보통 때보다 더 많은 네트워크 리소스를 사용할 수 있습니다.

![각 컴퓨터에서 사용하는 네트워크 대역폭을 보여 주는 Wire Data 2.0 대시보드의 네트워크 트래픽 블레이드를 캡처한 에어전트 스크린샷](./media/wire-data/log-search-example01.png)

마찬가지로 **로컬 서브넷** 블레이드를 사용하여 서브넷을 통해 이동하는 네트워크 트래픽의 양을 확인할 수 있습니다. 사용자는 종종 애플리케이션의 중요한 영역 주위 서브넷을 정의합니다. 이 블레이드는 해당 영역에 대한 뷰를 제공합니다.

![각 LocalSubnet 사용 되는 네트워크 대역폭을 보여 주는 Wire Data 2.0 대시보드의 로컬 서브넷 블레이드의 스크린샷](./media/wire-data/log-search-example02.png)

**애플리케이션 수준 프로토콜** 블레이드는 사용 중인 프로토콜을 아는 데 도움이 되므로 유용합니다. 예를 들어 네트워크 환경에서 SSH가 사용되지 않는 것을 예상할 수 있습니다. 블레이드에서 사용할 수 있는 정보 보기는 사용자의 예상을 신속하게 확인하거나 예상이 틀렸음을 증명할 수 있습니다.

![각 프로토콜에서 사용하는 네트워크 대역폭을 보여 주는 Wire Data 2.0 대시보드의 애플리케이션 수준 프로토콜 블레이드의 스크린샷](./media/wire-data/log-search-example03.png)

프로토콜 트래픽이 시간에 따라 증가하는지 감소하는지 여부를 아는 데 유용합니다. 예를 들어 애플리케이션에 의해 전송되고 있는 데이터 양이 증가하는 경우 이를 알고 있어야 하거나 주목할 만한 것일 수 있습니다.

## <a name="input-data"></a>입력 데이터

실시간 데이터 기능은 설정한 에이전트를 사용하여 네트워크 트래픽에 대한 메타데이터를 수집합니다. 각 에이전트는 약 15초마다 데이터를 보냅니다.

## <a name="output-data"></a>출력 데이터

각 형식의 입력 데이터에 대해 종류가 _WireData_ 인 레코드가 만들어집니다. WireData 레코드는 다음 테이블에 표시된 속성을 가집니다.

| 속성 | Description |
|---|---|
| Computer | 데이터가 수집된 컴퓨터 이름 |
| TimeGenerated | 레코드 시간 |
| LocalIP | 로컬 컴퓨터의 IP 주소 |
| SessionState | 연결 또는 연결 끊김 |
| ReceivedBytes | 받은 바이트의 양 |
| ProtocolName | 사용되는 네트워크 프로토콜의 이름 |
| IPVersion | IP 버전 |
| 방향 | 인바운드 또는 아웃바운드 |
| MaliciousIP | 알려진 악의적인 원본의 IP 주소 |
| 심각도 | 의심되는 맬웨어 심각도 |
| RemoteIPCountry | 원격 IP 주소의 국가/지역 |
| ManagementGroupName | Operations Manager 관리 그룹의 이름 |
| SourceSystem | 데이터가 수집된 원본 |
| SessionStartTime | 세션의 시작 시간 |
| SessionEndTime | 세션의 종료 시간 |
| LocalSubnet | 데이터가 수집된 서브넷 |
| LocalPortNumber | 로컬 포트 번호 |
| RemoteIP | 원격 컴퓨터에서 사용하는 원격 IP 주소 |
| RemotePortNumber | 원격 IP 주소에서 사용하는 포트 번호 |
| SessionID | 두 개의 IP 주소 간의 통신 세션을 식별하는 고유 값 |
| SentBytes | 보낸 바이트 수 |
| TotalBytes | 세션 중에 보낸 총 바이트 수 |
| ApplicationProtocol | 사용되는 네트워크 프로토콜의 유형   |
| ProcessID | Windows 프로세스 ID |
| ProcessName | 프로세스의 경로 및 파일 이름 |
| RemoteIPLongitude | IP 경도 값 |
| RemoteIPLatitude | IP 위도 값 |

## <a name="next-steps"></a>다음 단계

- 가상 머신에 대한 모니터링을 사용하도록 설정하기 위한 요구 사항 및 방법은 [VM 인사이트 배포](../vm/vminsights-enable-overview.md)를 참조하세요.
- [로그를 검색](../logs/log-query-overview.md)하여 자세한 실시간 데이터 검색 레코드를 볼 수 있습니다.
