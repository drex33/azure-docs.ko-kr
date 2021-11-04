---
title: Azure Diagnostics 확장 개요
description: 클라우드 서비스, 가상 머신 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: d83b8080d275417f6ad58456ffa732f5118f0995
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012362"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure Diagnostics 확장 개요
Azure Diagnostics 확장은 가상 머신을 포함한 Azure 컴퓨팅 리소스에서 모니터링 데이터를 수집하는 [Azure Monitor의 에이전트](../agents/agents-overview.md)입니다. 이 문서에서는 지원하는 특정 기능과, 설치 및 구성 옵션을 포함한 Azure Diagnostics 확장의 개요를 제공합니다. 

> [!NOTE]
> Azure Diagnostics 확장은 컴퓨팅 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집하는 데 사용할 수 있는 에이전트 중 하나입니다. 다른 에이전트에 대한 설명과 요구 사항에 맞는 에이전트를 선택하기 위한 지침은 [Azure Monitor 에이전트의 개요](../agents/agents-overview.md)를 참조하세요.

## <a name="primary-scenarios"></a>기본 시나리오
Diagnostics 확장에서 처리하는 주요 시나리오는 다음과 같습니다.

- Azure Monitor 메트릭에 게스트 메트릭을 수집합니다.
- 보관을 위해 게스트 로그 및 메트릭을 Azure Storage로 보냅니다.
- Azure 외부로 보내기 위해 게스트 로그 및 메트릭을 Azure Event Hubs로 보냅니다.


## <a name="comparison-to-log-analytics-agent"></a>Log Analytics 에이전트 비교
Azure Monitor의 Log Analytics 에이전트를 사용하여 가상 머신의 게스트 운영 체제에서도 모니터링 데이터를 수집할 수 있습니다. 요구 사항에 따라 둘 중 하나 또는 모두를 사용하도록 선택할 수 있습니다. Azure Monitor 에이전트의 상세 비교는 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요. 

고려해야 할 주요 차이점은 다음과 같습니다.

- Azure Diagnostics 확장은 Azure Virtual Machines에만 사용할 수 있습니다. Log Analytics 에이전트는 Azure, 다른 클라우드 환경 및 온-프레미스의 가상 머신에 사용할 수 있습니다.
- Azure Diagnostics 확장은 Azure Storage, [Azure Monitor Metrics](../essentials/data-platform-metrics.md)(Windows만 해당) 및 Event Hubs로 데이터를 보냅니다. Log Analytics 에이전트는 데이터를 [Azure Monitor 로그](../logs/data-platform-logs.md)에 수집합니다.
- Log Analytics 에이전트는 [솔루션](../monitor-reference.md#insights-and-curated-visualizations), [VM 인사이트](../vm/vminsights-overview.md) 및 [Azure Security Center](../../security-center/index.yml) 등의 기타 서비스에 필요합니다.

## <a name="costs"></a>비용
Azure Diagnostic 확장에 대한 비용은 없지만 데이터 수집에 대한 요금이 발생할 수 있습니다. 데이터를 수집하는 대상에 대한 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)를 확인하세요.

## <a name="data-collected"></a>수집되는 데이터
다음 표에서는 Windows 및 Linux 진단 확장에서 수집할 수 있는 데이터를 나열합니다.

### <a name="windows-diagnostics-extension-wad"></a>WAD(Windows 진단 확장)

| 데이터 원본 | Description |
| --- | --- |
| Windows 이벤트 로그   | Windows 이벤트 로그의 이벤트 |
| 성능 카운터 | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값 |
| IIS 로그             | 게스트 운영 체제에서 실행되는 IIS 웹 사이트에 대한 사용 정보 |
| 애플리케이션 로그 전송 사용     | 애플리케이션에서 작성한 추적 메시지 |
| .NET EventSource 로그 |.NET [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) 클래스를 사용하여 이벤트를 작성하는 코드 |
| [매니페스트 기반 ETW 로그](/windows/desktop/etw/about-event-tracing) |모든 프로세스에서 생성된 Windows용 이벤트 추적 이벤트 |
| 크래시 덤프(로그)   | 애플리케이션 작동이 중단된 경우 프로세스 상태에 대한 정보 |
| 파일 기반 로그    | 애플리케이션 또는 서비스에서 생성된 로그입니다. |
| 에이전트 진단 로그 | Azure Diagnostics 자체에 대한 정보 |


### <a name="linux-diagnostics-extension-lad"></a>LAD(Linux 진단 확장)

| 데이터 원본 | Description |
| --- | --- |
| syslog | Linux 이벤트 로깅 시스템으로 전송되는 이벤트   |
| 성능 카운터  | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값 |
| 로그 파일 | 파일 기반 로그에 전송된 항목  |

## <a name="data-destinations"></a>데이터 대상
Windows 및 Linux용 Azure 진단 확장은 항상 데이터를 Azure Storage 계정에 수집합니다. 이 데이터가 수집되는 특정 테이블 및 Blob 목록은 [WAD(Windows Azure Diagnostics) 확장](diagnostics-extension-windows-install.md) 및 [Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md)을 참조하세요.

하나 이상의 *데이터 싱크* 를 구성하여 다른 추가 대상에 데이터를 보냅니다. 다음 섹션에서는 Windows 및 Linux 진단 확장에 사용할 수 있는 싱크를 나열합니다.

### <a name="windows-diagnostics-extension-wad"></a>WAD(Windows 진단 확장)

| 대상 | Description |
|:---|:---|
| Azure Monitor 메트릭 | 성능 데이터를 Azure Monitor 메트릭에 수집합니다. [Azure Monitor 메트릭 데이터베이스에 게스트 OS 메트릭 보내기](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)를 참조하세요.  |
| 이벤트 허브(영문) | Azure Event Hubs를 사용하여 Azure 외부로 데이터를 보냅니다. [Event Hubs에 Azure Diagnostics 데이터 스트리밍](diagnostics-extension-stream-event-hubs.md) 참조 |
| Azure Storage Blob | 테이블 외에도 Azure Storage의 Blob에 데이터를 씁니다. |
| Application Insights | VM에서 실행되는 애플리케이션의 데이터를 Application Insights에 수집하여 다른 애플리케이션 모니터링과 통합합니다. [Application Insights에 진단 데이터 보내기](diagnostics-extension-to-application-insights.md)를 참조하세요. |

보통은 Log Analytics 에이전트를 사용하지만, Log Analytics 작업 영역에 스토리지의 WAD 데이터를 수집하여 Azure Monitor Logs를 통해 분석할 수도 있습니다. 데이터를 Log Analytics 작업 영역으로 직접 보낼 수 있고 추가 기능을 제공하는 솔루션과 인사이트를 지원합니다.  [Azure Storage에서 Azure 진단 로그 수집](../agents/diagnostics-extension-logs.md)을 참조하세요. 


### <a name="linux-diagnostics-extension-lad"></a>LAD(Linux 진단 확장)
LAD는 Azure Storage의 테이블에 데이터를 씁니다. 다음 표의 싱크를 지원합니다.

| 대상 | Description |
|:---|:---|
| 이벤트 허브(영문) | Azure Event Hubs를 사용하여 Azure 외부로 데이터를 보냅니다. |
| Azure Storage Blob | 테이블 외에도 Azure Storage의 Blob에 데이터를 씁니다. |
| Azure Monitor 메트릭 | LAD와 함께 Telegraf 에이전트를 설치합니다. [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](../essentials/collect-custom-metrics-linux-telegraf.md)을 참조하세요.


## <a name="installation-and-configuration"></a>설치 및 구성
진단 확장은 Azure에서 [가상 머신 확장](../../virtual-machines/extensions/overview.md)으로 구현되므로 Resource Manager 템플릿, PowerShell 및 CLI를 사용한 동일한 설치 옵션을 지원합니다. 가상 머신 확장 설치 및 유지 관리에 대한 일반 세부 사항은 [Windows용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-windows.md) 및 [Linux용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-linux.md)을 참조하세요.

Azure Portal의 가상 머신 메뉴에 있는 **모니터링** 섹션의 **진단 설정** 에서 Windows 및 Linux 진단 확장을 설치 및 구성할 수도 있습니다.

Windows 및 Linux용 진단 확장을 설치 및 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [WAD(Windows Azure Diagnostics) 확장 설치 및 구성](diagnostics-extension-windows-install.md)
- [Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>기타 문서

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service(클래식) 웹 및 작업자 역할
- [클라우드 서비스 모니터링 소개](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Cloud Services에서 Azure Diagnostics 사용](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure Cloud Services용 Application Insights](../app/cloudservices.md)<br>[Azure Diagnostics로 Cloud Services 애플리케이션의 흐름 추적](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [로컬 머신 개발 설정에서의 모니터링 및 진단 서비스](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>다음 단계


* [Azure Diagnostics에서 성능 카운터 사용](../../cloud-services/diagnostics-performance-counters.md)에 대해 알아봅니다.
* 진단을 시작하거나 Azure 스토리지 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure Diagnostics 문제 해결](diagnostics-extension-troubleshooting.md)을 참조하세요.