---
title: Azure Monitor의 데이터 원본 | Microsoft Docs
description: Azure 리소스 및 리소스에서 실행되는 애플리케이션의 성능과 상태를 모니터링할 수 있는 데이터를 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: e213168b6ee1f547621b7c734e88f80fde42212b
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542290"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Monitor에 대 한 모니터링 데이터의 원본
Azure Monitor는 [로그](../logs/data-platform-logs.md) 및 [메트릭](../essentials/data-platform-metrics.md)을 포함하는 [일반적인 모니터링 데이터 플랫폼](../data-platform.md)을 기반으로 합니다. 이 플랫폼으로 데이터를 수집하면 Azure Monitor 공통 도구 세트를 사용하여 여러 리소스의 데이터를 함께 분석할 수 있습니다. 모니터링 데이터는 특정 시나리오를 지원하기 위해 다른 위치로 전송될 수도 있으며, 일부 리소스는 로그나 메트릭에 수집되기 전에 다른 위치에 기록될 수 있습니다.

이 문서에서는 Azure 리소스에서 만든 모니터링 데이터 외에도 Azure Monitor에 의해 수집 되는 다양 한 모니터링 데이터 원본을 설명 합니다. 이 데이터를 다른 위치로 수집 하는 데 필요한 구성에 대 한 자세한 정보를 제공 하는 링크가 제공 됩니다.

## <a name="application-tiers"></a>애플리케이션 계층

Azure 응용 프로그램에서 모니터링 데이터의 소스는 계층으로 구성 될 수 있으며, 가장 높은 계층은 응용 프로그램 자체와 Azure platform의 구성 요소가 되는 하위 계층으로 구성 될 수 있습니다. 각 계층의 데이터에 액세스하는 방법은 다양합니다. 애플리케이션 계층은 아래 표에 요약되어 있으며 각 계층의 모니터링 데이터 원본은 다음 섹션에 나옵니다. 각 데이터 위치 및 해당 데이터에 액세스하는 방법에 대한 설명은 [Azure에서 데이터 위치 모니터링](../monitor-reference.md)을 참조하세요.


![계층 모니터링](../media/overview/overview.png)


### <a name="azure"></a>Azure
다음 표에서는 Azure와 관련 된 응용 프로그램 계층을 간략하게 설명 합니다. 아래 섹션의 각 항목에 대 한 자세한 내용은 링크를 참조 하세요.

| 계층 | Description | Collection 메서드 |
|:---|:---|:---|
| [Azure 테 넌 트](#azure-tenant) | Azure Active Directory와 같은 테넌트 수준 Azure 서비스의 작업에 대한 데이터입니다. | 포털에서 AAD 데이터를 보거나 테 넌 트 진단 설정을 사용 하 여 Azure Monitor 컬렉션을 구성 합니다. |
| [Azure 구독](#azure-subscription) | 리소스 관리자 및 Service Health와 같은 Azure 구독에서 리소스 간 서비스의 상태 및 관리와 관련 된 데이터입니다. | 포털에서 보거나 로그 프로필을 사용 하 여 Azure Monitor 컬렉션을 구성 합니다. |
| [Azure 리소스](#azure-resources) |  각 Azure 리소스의 작업 및 성능에 대 한 데이터입니다. | 자동으로 수집 된 메트릭, 메트릭 탐색기에서 보기<br>Azure Monitor에서 로그를 수집 하도록 진단 설정을 구성 합니다.<br>특정 리소스 종류에 대 한 자세한 모니터링을 위해 사용 가능한 모니터링 솔루션 및 Insights |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, 기타 클라우드 또는 온-프레미스 
다음 표에서는 Azure, 다른 클라우드 또는 온-프레미스에 있을 수 있는 응용 프로그램 계층을 간략하게 설명 합니다. 아래 섹션의 각 항목에 대 한 자세한 내용은 링크를 참조 하세요.

| 계층 | Description | Collection 메서드 |
|:---|:---|:---|
| [운영 체제(게스트)](#operating-system-guest) | 계산 리소스의 운영 체제에 대 한 데이터입니다. | Log Analytics 에이전트를 설치 하 여 클라이언트 데이터 원본을 Azure Monitor 및 종속성 에이전트로 수집 하 여 VM 정보를 지 원하는 종속성을 수집 합니다.<br>Azure virtual machines의 경우 Azure 진단 확장을 설치 하 여 Azure Monitor로 로그 및 메트릭을 수집 합니다. |
| [응용 프로그램 코드](#application-code) | 성능 추적, 응용 프로그램 로그 및 사용자 원격 분석을 포함 하 여 실제 응용 프로그램 및 코드의 성능 및 기능에 대 한 데이터입니다. | 코드를 계측 하 여 데이터를 Application Insights으로 수집 합니다. |
| [사용자 지정 원본](#custom-sources) | 외부 서비스 또는 기타 구성 요소 또는 장치의 데이터 | 모든 REST 클라이언트에서 Azure Monitor 로그 또는 메트릭 데이터를 수집 합니다. |

## <a name="azure-tenant"></a>Azure 테넌트
Azure 테넌트와 관련된 원격 분석은 Azure Active Directory와 같은 테넌트 전체 서비스에서 수집됩니다.

![Azure 테넌트 컬렉션](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그
[Azure Active Directory 보고](../../active-directory/reports-monitoring/overview-reports.md)는 로그인 활동 및 특정 테넌트 내의 변경 감사 내역에 대한 기록을 포함합니다. 

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 다른 모니터링 데이터로 분석하도록 Azure Monitor에서 수집할 Azure AD 로그를 구성합니다. | [Azure Monitor 로그(미리 보기)와 Azure AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 보관을 위해 Azure AD 로그를 Azure Storage로 내보냅니다. | [자습서: Azure Storage 계정에 Azure AD 로그 보관(미리 보기)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 이벤트 허브 | Event Hubs를 사용 하 여 Azure AD 로그를 다른 위치로 스트리밍합니다. | [자습서: Azure Active Directory 로그를 Azure 이벤트 허브로 스트리밍(미리 보기)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="azure-subscription"></a>Azure 구독
Azure 구독의 상태 및 작업과 관련 된 원격 분석입니다.

![Azure 구독](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 활동 로그 
Azure [활동 로그](../essentials/platform-logs-overview.md) 에는 azure 구독의 리소스에 대 한 구성 변경에 대 한 기록과 함께 서비스 상태 레코드가 포함 됩니다. 활동 로그는 모든 Azure 리소스에 대해 사용할 수 있으며 해당 _외부_ 보기를 나타냅니다.

| 대상 | Description | 참조 |
|:---|:---|
| 활동 로그 | 활동 로그는 Azure Monitor 메뉴에서 보거나 활동 로그 경고를 만드는 데 사용할 수 있는 자체 데이터 저장소로 수집 됩니다. | [Azure Portal에서 활동 로그를 쿼리 합니다.](../essentials/activity-log.md#view-the-activity-log) |
| Azure Monitor 로그 | 활동 로그를 수집 하 여 다른 모니터링 데이터를 사용 하 여 분석 하도록 Azure Monitor 로그를 구성 합니다. | [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 합니다.](../essentials/activity-log.md) |
| Azure Storage | 작업 로그를 보관을 위해 Azure Storage로 내보냅니다. | [활동 로그 보관](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치로 활동 로그 스트림 | [활동 로그를 이벤트 허브로 스트림](../essentials/resource-logs.md#send-to-azure-event-hubs)합니다. |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md)는 애플리케이션 및 리소스가 의존하는 구독에서 Azure 서비스의 상태에 대한 정보를 제공합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| 활동 로그<br>Azure Monitor 로그 | Service Health 레코드는 Azure 활동 로그에 저장 되므로 Azure Portal에서 보거나 활동 로그로 수행할 수 있는 다른 활동을 수행할 수 있습니다. | [Azure Portal을 사용하여 서비스 상태 알림 보기](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Azure 리소스
메트릭 및 리소스 로그는 Azure 리소스의 _내부_ 작업에 대 한 정보를 제공 합니다. 이러한 기능은 대부분의 Azure 서비스에 사용할 수 있으며, 모니터링 솔루션과 통찰력은 특정 서비스에 대 한 추가 데이터를 수집 합니다.

![Azure 리소스 컬렉션](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>플랫폼 메트릭 
대부분의 Azure 서비스는 성능 및 작업을 메트릭 데이터베이스에 직접 반영 하는 [플랫폼 메트릭을](../essentials/data-platform-metrics.md) 전송 합니다. 특정 [메트릭은 리소스의 각 형식마다 다릅니다](../essentials/metrics-supported.md). 

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 메트릭 | 플랫폼 메트릭은 구성 없이 Azure Monitor 메트릭 데이터베이스에 기록합니다. 메트릭 탐색기 플랫폼 메트릭에 액세스합니다.  | [Azure 메트릭 탐색기 시작](../essentials/metrics-getting-started.md)<br>[Azure Monitor에서 지원되는 메트릭](../essentials/metrics-supported.md) |
| Azure Monitor 로그 | Log Analytics를 사용하여 추세 및 기타 분석을 위해 플랫폼 메트릭을 로그에 복사합니다. | [Azure 진단에서 Log Analytics로 직접 연결](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Event Hubs 사용하여 메트릭을 다른 위치로 스트리밍합니다. |[Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>리소스 로그
[리소스 로그는](../essentials/platform-logs-overview.md) Azure 리소스의 _내부_ 작업에 대한 인사이트를 제공합니다.  리소스 로그는 자동으로 생성되지만 각 리소스에 대해 수집할 대상을 지정하는 진단 설정을 만들어야 합니다.

리소스 로그의 구성 요구 사항 및 콘텐츠는 리소스 종류에 따라 다르며, 모든 서비스에서 아직 만들지는 않습니다. 각 서비스에 대한 자세한 내용과 자세한 구성 절차에 대한 링크는 [Azure 리소스 로그에 대해 지원되는 서비스, 스키마](../essentials/resource-logs-schema.md) 및 범주를 참조하세요. 서비스가 이 문서에 나열되지 않은 경우 해당 서비스는 현재 리소스 로그를 만들지 않습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 수집된 다른 로그 데이터와 함께 분석을 위해 Azure Monitor 로그에 리소스 로그를 보냅니다. | [Azure Monitor Log Analytics 작업 영역에서 Azure 리소스 로그 수집](../essentials/resource-logs.md#send-to-azure-storage) |
| 스토리지 | 보관을 위해 리소스 로그를 Azure Storage 보냅니다. | [Azure 리소스 로그 보관](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Event Hubs 사용하여 리소스 로그를 다른 위치로 스트리밍합니다. |[이벤트 허브로 Azure 리소스 로그 스트리밍](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>운영 체제(게스트)
Azure, 다른 클라우드 및 온-프레미스의 컴퓨팅 리소스에는 모니터링할 게스트 운영 체제가 있습니다. 하나 이상의 에이전트를 설치하면 게스트에서 Azure Monitor로 원격 분석을 수집해 Azure 서비스와 동일한 모니터링 도구를 사용하여 분석할 수 있습니다.

![Azure 컴퓨팅 리소스 컬렉션](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 진단 확장
Azure Virtual Machines에 Azure Diagnostics 확장을 사용하도록 설정하면 Azure Cloud Service(클래식) 웹 및 작업자 역할, Virtual Machines, 가상 머신 확장 집합 및 Service Fabric 포함하여 Azure 컴퓨팅 리소스의 게스트 운영 체제에서 로그 및 메트릭을 수집할 수 있습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| 스토리지 | Azure 진단 확장은 항상 Azure Storage 계정에 기록합니다. | [WAD(Windows Azure Diagnostics) 확장 설치 및 구성](./diagnostics-extension-windows-install.md)<br>[Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor 메트릭 | 성능 카운터를 수집하도록 진단 확장을 구성하면 Azure Monitor 메트릭 데이터베이스에 기록됩니다. | [Windows 가상 머신에 대한 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Event Hubs 사용하여 다른 위치로 데이터를 스트리밍하도록 진단 확장을 구성합니다.  | [Event Hubs 사용하여 데이터 스트리밍 Azure Diagnostics](./diagnostics-extension-stream-event-hubs.md)<br>[Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md) |
| 애플리케이션 Insights 로그 | 다른 애플리케이션 데이터로 분석할 애플리케이션을 지원하는 컴퓨팅 리소스에서 로그 및 성능 카운터를 수집합니다. | [Application Insights에 클라우드 서비스, Virtual Machine 또는 Service Fabric 데이터 보내기](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics 에이전트 
Windows 또는 Linux 가상 머신의 포괄적인 모니터링 및 관리를 위해 Log Analytics 에이전트를 설치합니다. 가상 머신은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | Log Analytics 에이전트는 직접 또는 System Center Operations Manager 통해 Azure Monitor 연결하며, 가상 머신에서 실행되는 애플리케이션에 대한 추가 인사이트를 제공하는 모니터링 솔루션에서 구성한 데이터 원본에서 데이터를 수집할 수 있습니다. | [Azure Monitor의 에이전트 데이터 원본](../agents/agent-data-sources.md)<br>[Azure Monitor에 Operations Manager 연결](./om-agents.md) |
| VM Storage | VM 인사이트는 Log Analytics 에이전트를 사용하여 사용자 지정 위치에 상태 정보를 저장합니다. 자세한 내용은 다음 섹션을 참조하세요.  |


### <a name="vm-insights"></a>VM 인사이트 
[VM 인사이트](../vm/vminsights-overview.md)는 Azure Monitor 핵심 기능 이상의 기능을 제공하는 가상 머신에 대한 사용자 지정 모니터링 환경을 제공합니다. 가상 머신 및 외부 프로세스 종속성에서 실행되는 프로세스에 대한 검색된 데이터를 수집하려면 Log Analytics 에이전트와 통합되는 Windows 및 Linux 가상 머신에 Dependency Agent가 필요합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 프로세스 및 종속성에 대한 데이터를 에이전트에 저장합니다. | [VM 인사이트 맵을 사용하여 애플리케이션 구성 요소 이해](../vm/vminsights-maps.md) |



## <a name="application-code"></a>응용 프로그램 코드
Azure Monitor 자세한 애플리케이션 모니터링은 다양한 플랫폼에서 실행되는 애플리케이션에서 데이터를 수집하는 [Application Insights](/azure/application-insights/) 수행됩니다. 애플리케이션은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

![애플리케이션 데이터 수집](media/data-sources/applications.png)


### <a name="application-data"></a>애플리케이션 데이터
계측 패키지를 설치하여 애플리케이션에 대한 Application Insights를 사용하도록 설정하는 경우 애플리케이션의 성능 및 작업과 관련된 메트릭 및 로그를 수집합니다. 애플리케이션 Insights 수집한 데이터를 다른 데이터 원본에서 사용하는 동일한 Azure Monitor 데이터 플랫폼에 저장합니다. 여기에는 이 데이터를 분석하기 위한 광범위한 도구가 포함되어 있지만 메트릭 탐색기 및 Log Analytics와 같은 도구를 사용하여 다른 원본의 데이터로 분석할 수도 있습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 페이지 보기, 애플리케이션 요청, 예외 및 추적을 포함한 애플리케이션에 대한 작업 데이터입니다. | [Azure Monitor의 로그 데이터 분석](../logs/log-query-overview.md) |
|                    | 애플리케이션 맵 및 원격 분석 상관 관계를 지원하기 위한 애플리케이션 구성 요소 간의 종속성 정보입니다. | [Application Insights의 원격 분석 상관 관계](../app/correlation.md) <br> [애플리케이션 맵](../app/app-map.md) |
|            | 공용 인터넷의 여러 위치에서 애플리케이션의 가용성 및 응답성을 테스트하는 가용성 테스트의 결과입니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
| Azure Monitor 메트릭 | 애플리케이션 Insights 애플리케이션에서 정의하는 사용자 지정 메트릭 외에도 애플리케이션의 성능 및 작업을 설명하는 메트릭을 Azure Monitor 메트릭 데이터베이스에 수집합니다. | [Azure Application Insights의 로그 기반 및 사전 집계 메트릭](../app/pre-aggregated-metrics-log-metrics.md)<br>[사용자 지정 이벤트 및 메트릭용 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure Storage | 보관을 위해 애플리케이션 데이터를 Azure Storage 보냅니다. | [Application Insights에서 원격 분석 내보내기](../app/export-telemetry.md) |
|            | 가용성 테스트의 세부 정보는 Azure Storage 저장됩니다. 로컬 분석을 위해 다운로드하려면 Azure Portal 애플리케이션 Insights 사용합니다. 가용성 테스트의 결과는 Azure Monitor 로그에 저장됩니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
|            | 프로파일러 추적 데이터는 Azure Storage 저장됩니다. 로컬 분석을 위해 다운로드하려면 Azure Portal 애플리케이션 Insights 사용합니다.  | [Application Insights를 사용하여 Azure에서 프로덕션 애플리케이션 프로파일링](../app/profiler-overview.md) 
|            | 예외의 하위 집합에 대해 캡처된 디버그 스냅샷 데이터는 Azure Storage 저장됩니다. 로컬 분석을 위해 다운로드하려면 Azure Portal 애플리케이션 Insights 사용합니다.  | [스냅샷 작동 방식](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>모니터링 솔루션 및 Insights
[모니터링 솔루션 및](../insights/solutions.md) [Insights](../monitor-reference.md) 데이터를 수집하여 특정 서비스 또는 애플리케이션의 작업에 대한 추가 인사이트를 제공합니다. 서로 다른 애플리케이션 계층 및 여러 계층의 리소스를 처리할 수 있습니다.

### <a name="monitoring-solutions"></a>모니터링 솔루션

| 대상 | Description | 참조
|:---|:---|:---|
| Azure Monitor 로그 | 모니터링 솔루션은 일반적으로 솔루션에 포함된 쿼리 언어 또는 [뷰를](../visualize/view-designer.md) 사용하여 분석할 수 있는 Azure Monitor 로그에 데이터를 수집합니다. | [Azure의 모니터링 솔루션에 대한 데이터 수집 세부 정보](../monitor-reference.md) |


### <a name="container-insights"></a>컨테이너 인사이트
[컨테이너 인사이트는](../containers/container-insights-overview.md) [AKS(Azure Kubernetes Service)에](../../aks/index.yml)대한 사용자 지정된 모니터링 환경을 제공합니다. 다음 표에 설명된 이러한 리소스에 대한 추가 데이터를 수집합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 인벤토리, 로그 및 이벤트를 포함하여 AKS에 대한 모니터링 데이터를 저장합니다. 또한 메트릭 데이터는 포털에서 분석 기능을 활용하기 위해 로그에 저장됩니다. | [컨테이너 인사이트를 통해 AKS 클러스터 성능 이해](../containers/container-insights-analyze.md) |
| Azure Monitor 메트릭 | 메트릭 데이터는 시각화 및 경고를 유도하기 위해 메트릭 데이터베이스에 저장됩니다. | [메트릭 탐색기에서 컨테이너 메트릭 보기](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 포털에서 AKS(Azure Kubernetes Service) 컨테이너 로그(stdout/stderror), 이벤트 및 Pod 메트릭에 대한 직접 액세스를 제공합니다. | [Kubernetes 로그, 이벤트 및 Pod 메트릭을 실시간으로 보는 방법](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>VM 인사이트
[VM 인사이트는](../vm/vminsights-overview.md) 가상 머신을 모니터링하기 위한 사용자 지정 환경을 제공합니다. VM 인사이트에서 수집한 데이터에 대한 설명은 위의 [운영 체제(게스트)](#operating-system-guest) 섹션에 포함되어 있습니다.

## <a name="custom-sources"></a>사용자 지정 원본
애플리케이션의 표준 계층 외에 다른 데이터 원본과 함께 수집할 수 없는 원격 분석이 있는 다른 리소스를 모니터링해야 할 수도 있습니다. 이러한 리소스의 경우 Azure Monitor API를 사용하여 메트릭 또는 로그에 이 데이터를 씁니다.

![사용자 지정 컬렉션](media/data-sources/custom.png)

| 대상 | 메서드 | Description | 참조 |
|:---|:---|:---|:---|
| Azure Monitor 로그 | 데이터 수집기 API | REST 클라이언트에서 로그 데이터를 수집하고 Log Analytics 작업 영역에 저장합니다. | [HTTP 데이터 수집기 API로 Azure Monitor에 로그 데이터 전송(공개 미리 보기)](../logs/data-collector-api.md) |
| Azure Monitor 메트릭 | 사용자 지정 메트릭 API | REST 클라이언트에서 메트릭 데이터를 수집하고 Azure Monitor 메트릭 데이터베이스에 저장합니다. | [REST API를 사용하여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내기](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>기타 서비스
Azure의 다른 서비스는 Azure Monitor 데이터 플랫폼에 데이터를 씁니다. 이렇게 하면 이러한 서비스에서 수집한 데이터를 Azure Monitor 수집된 데이터로 분석하고 동일한 분석 및 시각화 도구를 활용할 수 있습니다.

| 서비스 | 대상 | Description | 참조 |
|:---|:---|:---|:---|
| [Microsoft Defender for Cloud](../../security-center/index.yml) | Azure Monitor 로그 | Microsoft Defender for Cloud는 수집한 보안 데이터를 Log Analytics 작업 영역에 저장하여 Azure Monitor 수집한 다른 로그 데이터로 분석할 수 있도록 합니다.  | [Microsoft Defender for Cloud의 데이터 수집](../../security-center/security-center-enable-data-collection.md) |
| [Microsoft Sentinel](../../sentinel/index.yml) | Azure Monitor 로그 | Microsoft Sentinel은 다른 데이터 원본에서 수집한 데이터를 Log Analytics 작업 영역에 저장하여 Azure Monitor 수집한 다른 로그 데이터로 분석할 수 있습니다.  | [데이터 원본 연결](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 수집한 모니터링 데이터의 유형](../data-platform.md)과 이 데이터를 보고 분석하는 방법에 대해 자세히 알아보세요.
- Azure [리소스가 데이터를 저장하는 다양한 위치와](../monitor-reference.md) 액세스하는 방법을 나열합니다.
