---
title: Azure Monitor 모범 사례-데이터 컬렉션 구성
description: Azure Monitor에서 데이터 컬렉션을 구성 하기 위한 지침과 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b5522cb26f7f8fe486b4b690938a23c7e75c746a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315961"
---
# <a name="azure-monitor-best-practices---configure-data-collection"></a>Azure Monitor 모범 사례-데이터 컬렉션 구성
이 문서는 [Azure Monitor를 구성](best-practices.md)하는 권장 사항 시나리오의 일부입니다. Azure 및 하이브리드 응용 프로그램 및 리소스에 대해 Azure Monitor 기능을 사용 하도록 설정 하는 데 필요한 데이터 수집을 구성 하는 권장 단계에 대해 설명 합니다.

> [!IMPORTANT]
> Azure Monitor 기능과 해당 구성은 사용 가능한 기능의 비용과 균형을 이루는 비즈니스 요구 사항에 따라 달라집니다. 아래 각 단계에서 잠재적 비용이 있는지 여부를 확인 하 고 계속 하기 전에 이러한 비용을 평가 해야 합니다. 전체 가격 책정 정보는 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
[Azure Monitor 로그](logs/data-platform-logs.md)를 사용하도록 설정하려면 하나 이상의 Log Analytics 작업 영역이 필요합니다. 이는 Azure 리소스에서 로그와 같은 데이터를 수집하고, Azure 가상 머신의 게스트 운영 체제에서 데이터를 수집하고, 대부분의 Azure Monitor 인사이트에 필요합니다. Microsoft 센티널 및 Microsoft Defender for Cloud와 같은 기타 서비스는 Log Analytics 작업 영역을 사용 하 고 Azure Monitor에 사용 하는 것과 동일한 작업 영역을 공유할 수 있습니다. 이 모니터링을 지원하기 위해 단일 작업 영역으로 시작할 수 있지만 여러 작업 영역을 사용해야 하는 경우에 대한 지침은 [Azure Monitor 로그 배포 설계](logs/design-logs-deployment.md)를 참조하세요.

Log Analytics 작업 영역을 만드는 데 드는 비용은 없지만 수집할 데이터를 구성하면 잠재적인 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)를 참조하세요.  

초기 Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](logs/quick-create-workspace.md)를 참조하세요. 액세스를 구성하려면 [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](logs/manage-access.md)를 참조하세요. 리소스 관리자 템플릿과 같은 확장 가능한 메서드를 사용 하 여 작업 영역을 구성할 수 있습니다. 그러나 대부분의 환경에는 최소한의 숫자가 필요 하기 때문에 일반적으로 필요 하지 않습니다.

## <a name="collect-data-from-azure-resources"></a>Azure 리소스에서 데이터 수집
Azure 리소스의 일부 모니터링은 구성 없이 자동으로 사용할 수 있으며, 추가 모니터링 데이터를 수집하는 구성 단계를 수행해야 합니다. 다음 표에서는 Azure 리소스에서 사용 가능한 모든 데이터를 수집하는 데 필요한 구성 단계를 보여 줍니다. 여기에는 Azure Monitor 메트릭 및 Azure Monitor 로그로 전송되는 단계 데이터가 포함됩니다. 아래 섹션에서는 각 단계를 자세히 설명합니다.

[![Azure 리소스 모니터링 배포](media/best-practices-data-collection/best-practices-azure-resources.png)](media/best-practices-data-collection/best-practices-azure-resources.png#lightbox)

### <a name="collect-tenant-and-subscription-logs"></a>테 넌 트 및 구독 로그 수집
테넌트에 대한 [Azure Active Directory 로그](../active-directory/reports-monitoring/overview-reports.md)와 구독에 대한 [활동 로그](essentials/platform-logs-overview.md)가 자동으로 수집되지만 이를 Log Analytics 작업 영역으로 보내면 Log Analytics에서 로그 쿼리를 사용하여 다른 로그 데이터로 이러한 이벤트를 분석할 수 있습니다. 이를 통해 Azure Active Directory 로그에 대해 경고하고 활동 로그 경고보다 더 복잡한 논리를 제공하는 유일한 방법인 로그 쿼리 경고를 만들 수도 있습니다.

활동 로그를 작업 영역에 전송하는 데 드는 비용은 없지만 Azure Active Directory 로그에 대한 데이터 수집 및 보존 요금은 있습니다. 

Log Analytics 작업 영역에 로그 항목을 보내도록 테넌트 및 구독에 대한 진단 설정을 만들려면 [Azure AD 로그를 Azure Monitor 로그와 통합](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 및 [플랫폼 로그 및 메트릭을 다른 대상으로 보내기 위한 진단 설정 만들기](essentials/diagnostic-settings.md)를 참조하세요. 




### <a name="collect-resource-logs-and-platform-metrics"></a>리소스 로그 및 플랫폼 메트릭 수집
Azure의 리소스는 리소스 내에서 수행되는 작업의 세부 정보를 제공하는 [리소스 로그](essentials/platform-logs-overview.md)를 자동으로 생성합니다. 그러나 플랫폼 메트릭과 달리 리소스 로그를 수집하도록 구성해야 합니다. Log Analytics 작업 영역으로 보내고 Azure Monitor 로그에 사용 되는 다른 데이터와 결합 하는 진단 설정을 만듭니다. 동일한 진단 설정을 사용하여 대부분의 리소스에 대한 플랫폼 메트릭을 동일한 작업 영역에 보낼 수도 있습니다. 이를 통해 수집된 다른 데이터와 함께 로그 쿼리를 사용하여 메트릭 데이터를 분석할 수 있습니다.

Log Analytics 작업 영역에서 리소스 로그를 수집 하는 데 드는 비용이 있으므로 중요 한 데이터가 포함 된 로그 범주만 선택 하면 됩니다. 모든 범주를 수집 하면 값이 거의 없는 데이터를 수집 하는 비용이 발생 합니다. 수집할 범주 및 권장 사항에 대 한 설명은 각 Azure 서비스에 대 한 모니터링 설명서를 참조 하세요. 또한 로그 수집 비용 최적화에 대한 자세한 내용은 [Azure Monitor 로그를 사용한 사용량 및 비용 관리](logs/manage-cost-storage.md)를 참조하세요.

Azure 리소스에 대한 진단 설정을 만들려면 [Azure에서 리소스 로그 및 메트릭을 수집하는 진단 설정 만들기](essentials/diagnostic-settings.md#create-in-azure-portal)를 참조하세요. 

각 Azure 리소스에 대 한 진단 설정을 만들어야 하므로 각 리소스가 생성 될 때마다 Azure Policy를 사용 하 여 진단 설정을 자동으로 만듭니다. 각 Azure 리소스 종류에는 진단 설정에 나열해야 하는 고유한 범주 세트가 있습니다. 이 때문에 각 리소스 종류에는 별도의 정책 정의가 필요합니다. 일부 리소스 종류에는 수정 없이 할당할 수 있는 기본 제공 정책 정의가 있습니다. 다른 리소스 종류의 경우 사용자 지정 정의를 만들어야 합니다.

특정 Azure 서비스에 대 한 정책 정의를 만드는 프로세스의 경우 [Azure Policy를 사용 하 여 크기 조정에서 만들기](essentials/diagnostic-settings.md#create-at-scale-using-azure-policy) 및 진단 설정 만들기에 대 한 자세한 내용은을 참조 하세요.

### <a name="enable-insights"></a>인사이트 사용
Insights 특정 서비스에 대 한 특수 모니터링 환경을 제공 합니다. 플랫폼 메트릭 및 리소스 로그와 같이 이미 수집 되는 동일한 데이터를 사용 하지만 가장 중요 한 데이터를 식별 하 고 분석 하는 데 도움이 되는 사용자 지정 통합 문서를 제공 합니다. 대부분의 정보는 해당 서비스에 대 한 리소스 로그를 수집 하는 것 외에는 구성이 필요 없는 Azure Portal에서 사용할 수 있습니다. 각 Azure 서비스에 대 한 모니터링 설명서를 참조 하 여 정보 및 구성이 필요한 지 여부를 확인 합니다.

정보에 대 한 비용은 없지만 수집 하는 모든 데이터에 대해 요금이 부과 될 수 있습니다.

Azure Monitor에서 사용 가능한 인사이트 및 솔루션 목록은 [Azure Monitor에서 모니터링하는 항목](monitor-reference.md)을 참조하세요. 고유한 구성 또는 가격 책정 정보는 각각에 대한 설명서를 참조하세요.

> [!IMPORTANT]
> 다음 정보는 다른 것 보다 훨씬 더 복잡 하 고 구성에 대 한 추가 지침을 제공 합니다.
> 
> - [VM 인사이트](#monitor-virtual-machines)
> - [컨테이너 인사이트](#monitor-containers)
> - [애플리케이션 모니터링](#monitor-applications)


## <a name="monitor-virtual-machines"></a>가상 머신 모니터링
가상 머신은 다른 Azure 리소스와 유사한 데이터를 생성 하지만, 게스트 운영 체제에서 데이터를 수집 하려면 에이전트가 필요 합니다. 가상 컴퓨터에서 실행 되는 다른 작업으로 인해 고유한 모니터링 요구 사항도 있습니다. Azure Monitor를 사용 하 여 가상 머신 모니터링에 대 한 전용 시나리오는 [Azure Monitor으로 Azure virtual Machines 모니터링](vm/monitor-vm-azure.md) 을 참조 하세요.

## <a name="monitor-containers"></a>모니터 컨테이너
가상 머신은 다른 Azure 리소스와 유사한 데이터를 생성 하지만 필요한 데이터를 수집 하려면 컨테이너 화 된 버전의 Log Analytics 에이전트가 필요 합니다. 컨테이너 통찰력을 통해 컨테이너 화 된 환경을 모니터링 하 고 타사 도구와 함께 사용 하 여 AKS 및 지원 되는 워크플로를 포괄적으로 모니터링할 수 있습니다. Azure Monitor를 사용한 모니터링 AKS의 전용 시나리오에 대 한 [Azure Monitor를 사용 하 여 AKS (Azure Kubernetes Service) 모니터링](../aks/monitor-aks.md?toc=/azure/azure-monitor/toc.json) 을 참조 하세요.

## <a name="monitor-applications"></a>애플리케이션 모니터링
Azure Monitor는 모니터링하려는 각 애플리케이션을 위해 구성해야 하는 [Application Insights](app/app-insights-overview.md)를 사용하여 사용자 지정 애플리케이션을 모니터링합니다. 구성 프로세스는 모니터링되는 애플리케이션의 유형과 수행하려는 모니터링 유형에 따라 달라집니다. Application Insights에서 수집한 데이터는 기능에 따라 Azure Monitor 메트릭, Azure Monitor 로그 및 Azure Blob Storage에 저장됩니다. 성능 데이터는 추가 구성 없이 Azure Monitor 메트릭과 Azure Monitor 로그에 모두 저장됩니다.

### <a name="create-an-application-resource"></a>애플리케이션 리소스 만들기
Application Insights은 클라우드 네이티브 및 하이브리드 응용 프로그램을 모니터링 하기 위한 Azure Monitor의 기능입니다.

모니터링하려는 각 애플리케이션에 대해 Application Insights 리소스를 만들어야 합니다. Application Insights에서 수집한 로그 데이터는 작업 영역 기반 애플리케이션의 Azure Monitor 로그에 저장됩니다. 클래식 애플리케이션의 로그 데이터는 [데이터 구조](logs/data-platform-logs.md#data-structure)에 설명된 대로 Log Analytics 작업 영역과 별도로 저장됩니다.

 클래식 애플리케이션을 만들지 또는 작업 영역 기반 애플리케이션을 만들지 선택해야 합니다. 클래식 애플리케이션을 만들려면 [Application Insights 리소스 만들기](app/create-new-resource.md)를 참조하세요. 작업 영역 기반 애플리케이션을 만들려면 [작업 영역 기반 Application Insights 리소스(미리 보기)](app/create-workspace-resource.md)를 참조하세요.


 기본적인 디자인 결정은 여러 응용 프로그램에 대해 별도의 응용 프로그램 리소스를 사용할지 아니면 단일 응용 프로그램 리소스를 사용할지를 결정 합니다. 별도의 리소스를 통해 비용을 절감 하 고 다양 한 응용 프로그램의 데이터를 혼합할 수 있지만 단일 리소스를 통해 모든 관련 원격 분석을 함께 유지 하 여 모니터링을 간소화할 수 있습니다. 이러한 디자인 결정에 대 한 자세한 기준은 [몇 Application Insights 리소스를 배포 해야](app/separate-resources.md) 하는지 확인 하세요.



### <a name="configure-codeless-or-code-based-monitoring"></a>코드 없는 모니터링 또는 코드 기반 모니터링 구성
애플리케이션에 대한 모니터링을 사용하도록 설정하려면 코드 없는 모니터링을 사용할지 또는 코드 기반 모니터링을 사용할지 결정해야 합니다. 구성 프로세스는 이 결정 및 모니터링하려는 애플리케이션의 유형에 따라 달라집니다.

**코드 없는 모니터링** 은 구현하기 가장 쉽고 코드 개발 후에 구성할 수 있습니다. 코드를 업데이트할 필요가 없습니다. 애플리케이션에 따라 모니터링을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Web Apps에서 호스팅되는 애플리케이션](app/azure-web-apps.md)
- [Java 애플리케이션](app/java-in-process-agent.md)
- [Azure VM 또는 Azure Virtual Machine Scale Set의 IIS에 호스트된 ASP.NET 애플리케이션](app/azure-vm-vmss-apps.md)
- [IIS 온-프레미스에 호스트된 ASP.NET 애플리케이션](app/status-monitor-v2-overview.md)


**코드 기반 모니터링** 은 다양한 사용자 지정이 가능하고 추가 원격 분석을 수집하지만 Application Insights SDK NuGet 패키지의 코드에 종속성을 추가해야 합니다. 애플리케이션에 따라 모니터링을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [ASP.NET 애플리케이션](app/asp-net.md)
- [ASP.NET Core 애플리케이션](app/asp-net-core.md)
- [.NET 콘솔 애플리케이션](app/console.md)
- [Java](app/java-in-process-agent.md)
- [Node.JS](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [기타 플랫폼](app/platforms.md)

### <a name="configure-availability-testing"></a>가용성 테스트 구성
Application Insights의 가용성 테스트는 전 세계 지점에서 정기적으로 애플리케이션의 가용성 및 응답성을 모니터링하는 반복 테스트입니다. 간단한 ping 테스트를 무료로 만들거나 웹 요청 시퀀스를 만들어 관련 비용이 있는 사용자 트랜잭션을 시뮬레이션할 수 있습니다. 

다양한 종류의 테스트에 대한 요약과 테스트 생성에 대한 자세한 내용은 [웹 사이트의 가용성 모니터링](app/monitor-web-app-availability.md)을 참조하세요.

### <a name="configure-profiler"></a>프로파일러 구성
Azure Application Insights의 Profiler는 .NET 애플리케이션의 성능 추적을 제공합니다. 특정 웹 요청을 처리할 때 가장 오래 걸리는 "핫" 코드 경로를 식별하는 데 도움이 됩니다. 프로파일러를 구성하는 프로세스는 애플리케이션의 유형에 따라 달라집니다. 

프로파일러 구성에 대한 자세한 내용은 [Application Insights를 사용하여 Azure에서 프로덕션 애플리케이션 프로파일링](app/profiler-overview.md)을 참조하세요.

### <a name="configure-snapshot-debugger"></a>스냅샷 디버거 구성
Application Insights의 스냅샷 디버거는 .NET 애플리케이션에서 예외 원격 분석을 모니터링하고, 프로덕션에서 문제를 진단하는 데 필요한 정보를 포함하도록 최상위 throw 예외에 대한 스냅샷을 수집합니다. 스냅샷 디버거를 구성하는 프로세스는 애플리케이션의 유형에 따라 달라집니다. 

스냅샷 디버거 구성에 대한 자세한 내용은 [.NET 앱의 예외에 대한 디버그 스냅샷](app/snapshot-debugger.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 모든 Azure 리소스에 대해 구성된 데이터 수집을 사용하여 [데이터 분석 및 시각화를](best-practices-analysis.md) 참조하여 이 데이터를 분석하는 옵션을 확인합니다. 
