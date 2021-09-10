---
title: Azure Monitor 배포
description: Azure 구독의 모든 리소스를 모니터링하기 위해 Azure Monitor를 완전히 구현하는 데 필요한 다양한 단계를 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 151caf0aadba66eafac464c55b073968ea4022e7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609488"
---
# <a name="deploy-azure-monitor"></a>Azure Monitor 배포
Azure Monitor에서 모든 Azure 리소스를 모니터링할 수 있도록 설정하는 것은 Azure Monitor 구성 요소를 구성하고 Azure Monitor가 수집할 모니터링 데이터를 생성하도록 Azure 리소스를 구성하는 것입니다. 이 문서에서는 Azure 구독의 모든 리소스를 모니터링하는 공통 구성을 사용하여 Azure Monitor를 완전히 구현하는 데 필요한 다양한 단계를 설명합니다. 각 단계에 대한 기본 설명은 자세한 구성 요구 사항에 대한 다른 문서의 링크와 함께 제공됩니다.

> [!IMPORTANT]
> Azure Monitor 기능과 해당 구성은 사용 가능한 기능의 비용과 균형을 이루는 비즈니스 요구 사항에 따라 달라집니다. 아래의 각 단계에서는 잠재적인 비용이 있는지 여부를 식별하며, 해당 단계를 진행하기 전에 이러한 비용을 평가해야 합니다. 전체 가격 책정 정보는 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="configuration-goals"></a>구성 목표
Azure Monitor의 전체 구현 목표는 모든 클라우드 리소스 및 애플리케이션에서 사용 가능한 모든 데이터를 수집하고 해당 데이터를 기반으로 Azure Monitor에서 가능한 한 많은 기능을 사용하도록 설정하는 것입니다.

Azure Monitor에서 수집한 데이터는 [Azure Monitor 메트릭](essentials/data-platform-metrics.md) 또는 [Azure Monitor 로그](logs/data-platform-logs.md)로 전송됩니다. 각각은 다른 종류의 데이터를 저장하고 다양한 종류의 분석 및 경고를 가능하게 합니다. 두 가지를 비교하려면 [Azure Monitor 메트릭 및 로그 비교](data-platform.md)를 참조하고 다양한 경고 유형에 대한 설명은 [Microsoft Azure의 경고 개요](alerts/alerts-overview.md)를 참조하세요. 

일부 데이터는 다른 기능을 사용하여 이를 활용하기 위해 메트릭과 로그 둘 다로 전송될 수 있습니다. 이러한 경우 각각을 개별적으로 구성해야 할 수 있습니다. 예를 들어 메트릭 데이터는 메트릭 탐색기 및 메트릭 경고를 지원하는 메트릭에 Azure 리소스에 의해 자동으로 전송됩니다. 로그에 동일한 메트릭 데이터를 전송하는 각 리소스에 대한 진단 설정을 만들어야 합니다. 이렇게 하면 Log Analytics를 사용하여 다른 로그 데이터를 통해 성능 추세를 분석할 수 있습니다. 아래 섹션에서는 데이터를 전송하는 위치를 식별하고 가능한 모든 위치에 데이터를 전송하는 데 필요한 각 단계를 포함합니다.

Azure 외부에서 리소스를 모니터링하고 Azure Monitor 외부로 데이터를 전송하는 등의 추가 요구 사항이 있을 수 있습니다. 이 문서에 설명된 기능의 추가 구성으로 이러한 요구 사항을 달성할 수 있습니다. 추가 구성 옵션을 보려면 각 단계의 설명서에 대한 링크를 따르세요.

## <a name="collect-data-from-azure-resources"></a>Azure 리소스에서 데이터 수집

> [!NOTE]
> Azure Monitor로 가상 머신을 모니터링하는 방법에 대한 전체 가이드는 [Azure Monitor로 Azure 리소스 모니터링](essentials/monitor-azure-resource.md)을 참조하세요.

Azure 리소스의 일부 모니터링은 구성 없이 자동으로 사용할 수 있으며, 추가 모니터링 데이터를 수집하는 구성 단계를 수행해야 합니다. 다음 표에서는 Azure 리소스에서 사용 가능한 모든 데이터를 수집하는 데 필요한 구성 단계를 보여 줍니다. 여기에는 Azure Monitor 메트릭 및 Azure Monitor 로그로 전송되는 단계 데이터가 포함됩니다. 아래 섹션에서는 각 단계를 자세히 설명합니다.

[ ![Azure 리소스 모니터링 배포](media/deploy/deploy-azure-resources.png) ](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>구성 없음
Azure 구독을 만들 때 필요한 구성 없이 Azure Monitor의 다음 기능이 사용하도록 설정됩니다. 이 모니터링과 관련된 비용은 없습니다.

[Azure Active Directory 로그](../active-directory/reports-monitoring/overview-reports.md) - 로그인 활동의 테넌트 수준 기록 및 Azure Active Directory에서 변경된 사항에 대한 감사 추적을 제공합니다. Azure Active Directory 로그 및 Azure Portal에서 해당 로그를 보는 방법에 대한 자세한 내용은 [Azure Active Directory 포털의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 및 [Azure Active Directory 포털의 로그인 활동 보고서](../active-directory/reports-monitoring/concept-sign-ins.md)를 참조하세요.

[활동 로그](essentials/platform-logs-overview.md) - Azure에서 발생한 관리 그룹 및 구독 수준 이벤트에 대한 인사이트를 제공합니다. 이벤트는 새 Azure 리소스를 만들거나, 리소스를 수정하거나, 중요한 작업을 수행할 때 활동 로그에 자동으로 기록됩니다. 특정 이벤트가 생성되면 Azure Portal에서 이벤트를 보고 활동 로그 경고를 만들 수 있습니다. 활동 로그의 자세한 내용과 이를 Azure Portal에서 보는 방법은 [Azure 활동 로그](essentials/activity-log.md)를 참조하세요.

[플랫폼 메트릭](essentials/metrics-supported.md) - Azure 서비스에서 [Azure Monitor 메트릭](essentials/data-platform-metrics.md)으로 자동 수집됩니다. 이 데이터는 Azure Portal에서 종종 다양한 서비스에 대한 **개요** 페이지에 표시됩니다. Azure Portal에서 플랫폼 메트릭을 분석하는 방법에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](essentials/metrics-getting-started.md)을 참조하세요. 


### <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
[Azure Monitor 로그](logs/data-platform-logs.md)를 사용하도록 설정하려면 하나 이상의 Log Analytics 작업 영역이 필요합니다. 이는 Azure 리소스에서 로그와 같은 데이터를 수집하고, Azure 가상 머신의 게스트 운영 체제에서 데이터를 수집하고, 대부분의 Azure Monitor 인사이트에 필요합니다. Azure Sentinel 및 Azure Security Center와 같은 다른 서비스도 Log Analytics 작업 영역을 사용하며 Azure Monitor를 위해 사용하는 것과 동일한 작업 영역을 공유할 수 있습니다. 이 모니터링을 지원하기 위해 단일 작업 영역으로 시작할 수 있지만 여러 작업 영역을 사용해야 하는 경우에 대한 지침은 [Azure Monitor 로그 배포 설계](logs/design-logs-deployment.md)를 참조하세요.

Log Analytics 작업 영역을 만드는 데 드는 비용은 없지만 수집할 데이터를 구성하면 잠재적인 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)를 참조하세요.  

초기 Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](logs/quick-create-workspace.md)를 참조하세요. 액세스를 구성하려면 [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](logs/manage-access.md)를 참조하세요. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>테넌트 및 구독 로그를 수집하는 진단 설정 만들기
테넌트에 대한 [Azure Active Directory 로그](../active-directory/reports-monitoring/overview-reports.md)와 구독에 대한 [활동 로그](essentials/platform-logs-overview.md)가 자동으로 수집되지만 이를 Log Analytics 작업 영역으로 보내면 Log Analytics에서 로그 쿼리를 사용하여 다른 로그 데이터로 이러한 이벤트를 분석할 수 있습니다. 이를 통해 Azure Active Directory 로그에 대해 경고하고 활동 로그 경고보다 더 복잡한 논리를 제공하는 유일한 방법인 로그 쿼리 경고를 만들 수도 있습니다.

활동 로그를 작업 영역에 전송하는 데 드는 비용은 없지만 Azure Active Directory 로그에 대한 데이터 수집 및 보존 요금은 있습니다. 

Log Analytics 작업 영역에 로그 항목을 보내도록 테넌트 및 구독에 대한 진단 설정을 만들려면 [Azure AD 로그를 Azure Monitor 로그와 통합](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 및 [플랫폼 로그 및 메트릭을 다른 대상으로 보내기 위한 진단 설정 만들기](essentials/diagnostic-settings.md)를 참조하세요. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>리소스 로그 및 플랫폼 메트릭을 수집하기 위한 진단 설정 만들기
Azure의 리소스는 리소스 내에서 수행되는 작업의 세부 정보를 제공하는 [리소스 로그](essentials/platform-logs-overview.md)를 자동으로 생성합니다. 그러나 플랫폼 메트릭과 달리 리소스 로그를 수집하도록 구성해야 합니다. Log Analytics 작업 영역에 전송하여 Azure Monitor 로그에 사용되는 다른 데이터와 결합하는 진단 설정을 만듭니다. 동일한 진단 설정을 사용하여 대부분의 리소스에 대한 플랫폼 메트릭을 동일한 작업 영역에 보낼 수도 있습니다. 이를 통해 수집된 다른 데이터와 함께 로그 쿼리를 사용하여 메트릭 데이터를 분석할 수 있습니다.

이 컬렉션에 대한 비용이 있으므로 많은 리소스에서 구현하기 전에 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요. 또한 로그 수집 비용 최적화에 대한 자세한 내용은 [Azure Monitor 로그를 사용한 사용량 및 비용 관리](logs/manage-cost-storage.md)를 참조하세요.

Azure 리소스에 대한 진단 설정을 만들려면 [Azure에서 리소스 로그 및 메트릭을 수집하는 진단 설정 만들기](essentials/diagnostic-settings.md#create-in-azure-portal)를 참조하세요. 각 Azure 리소스에 대해 진단 설정을 만들어야 하므로 Azure 리소스가 만들어질 때마다 설정이 자동으로 생성되도록 [Azure Policy](../governance/policy/overview.md)를 사용하는 방법에 대한 자세한 내용은 [대규모로 Azure Monitor 배포](deploy-scale.md)를 참조하세요.

### <a name="enable-insights-and-solutions"></a>인사이트 및 솔루션 사용
인사이트 및 솔루션은 특정 서비스 또는 솔루션에 대한 특수한 모니터링을 제공합니다. 인사이트는 통합 문서와 같은 Azure Monitor의 최신 기능을 사용하므로 서비스에 사용할 수 있는 경우 인사이트를 사용해야 합니다. 모든 Azure 구독에서 자동으로 사용할 수 있지만, 전체 기능을 사용하려면 몇 가지 구성이 필요할 수 있습니다. 일반적으로 이전에 구성한 플랫폼 메트릭 및 리소스 로그를 사용하고 추가 데이터를 수집할 수 있습니다.

솔루션은 각 구독에 추가되어야 하고 Azure Monitor 로그의 데이터로만 작동해야 하며, 추가 로그 데이터를 수집할 수 있습니다.

솔루션 또는 인사이트에 대한 비용은 없지만 수집하는 모든 데이터에 대해 요금이 부과될 수 있습니다.

Azure Monitor에서 사용 가능한 인사이트 및 솔루션 목록은 [Azure Monitor에서 모니터링하는 항목](monitor-reference.md)을 참조하세요. 고유한 구성 또는 가격 책정 정보는 각각에 대한 설명서를 참조하세요. 

## <a name="collect-data-from-virtual-machines"></a>가상 머신에서 데이터 수집

> [!NOTE]
> Azure Monitor로 가상 머신을 모니터링하는 방법에 대한 전체 가이드는 [Azure Monitor로 Azure 가상 머신 모니터링](vm/monitor-vm-azure.md)을 참조하세요. 

가상 머신은 다른 Azure 리소스와 비슷한 데이터를 생성하지만 게스트 운영 체제에서 데이터를 수집하려면 에이전트가 필요합니다. Azure Monitor에서 사용하는 에이전트를 비교하려면 [Azure Monitor 에이전트 개요](agents/agents-overview.md)를 참조하세요. 

[VM 인사이트](vm/vminsights-overview.md)는 Log Analytics 에이전트 및 종속성 에이전트를 사용하여 가상 머신의 게스트 운영 체제에서 데이터를 수집하므로 이 인사이트 구현의 일부로 이러한 에이전트를 배포할 수 있습니다. 이렇게 하면 Log Analytics 에이전트를 사용하는 다른 서비스(예: Azure Security Center)에 사용할 수 있습니다.


[ ![Azure VM 배포](media/deploy/deploy-azure-vm.png) ](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-vm-insights"></a>VM 인사이트를 위한 작업 영역 구성
VM 인사이트를 사용하려면 일반적으로 다른 Azure 리소스에서 데이터를 수집하기 위해 만든 작업 영역과 동일한 Log Analytics 작업 영역이 필요합니다. 가상 머신을 사용하도록 설정하기 전에 VM 인사이트에 필요한 솔루션을 작업 영역에 추가해야 합니다.

VM 인사이트를 위한 Log Analytics 작업 영역 구성에 대한 자세한 내용은 [VM 인사이트를 위한 Log Analytics 작업 영역 구성](vm/vminsights-configure-workspace.md)을 참조하세요.

### <a name="enable-vm-insights-on-each-virtual-machine"></a>각 가상 머신에서 VM 인사이트 사용
작업 영역이 구성되면 Log Analytics 에이전트 및 종속성 에이전트를 설치하여 각 가상 머신을 사용하도록 설정할 수 있습니다. 각 가상 머신을 만들 때 자동으로 구성할 수 있는 Azure Policy를 포함하여 이러한 에이전트를 설치하는 방법에는 여러 가지가 있습니다. VM 인사이트에서 수집한 성능 데이터 및 프로세스 세부 정보는 Azure Monitor 로그에 저장됩니다.

에이전트를 가상 머신에 배포하고 모니터링할 수 있도록 설정하는 옵션은 [VM 인사이트 사용 개요](vm/vminsights-enable-overview.md)를 참조하세요.

### <a name="configure-workspace-to-collect-events"></a>작업 영역을 구성하여 이벤트 수집
VM 인사이트는 각 가상 컴퓨터의 게스트 운영 체제에서 성능 데이터와 프로세스의 세부 정보 및 종속성을 수집합니다. Log Analytics 에이전트는 Windows의 이벤트 로그 및 Linux의 syslog를 포함하여 게스트에서 로그를 수집할 수도 있습니다. 연결된 Log Analytics 작업 영역에서 이러한 로그에 대한 구성을 검색합니다. 작업 영역을 한 번만 구성하면 에이전트가 연결될 때마다 구성 변경 내용이 다운로드됩니다. 

에이전트 가상 머신에서 추가 데이터를 수집하도록 Log Analytics 작업 영역을 구성하는 방법에 대한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](agents/agent-data-sources.md)을 참조하세요.

> [!NOTE]
> 성능 카운터를 수집하도록 작업 영역을 구성할 수도 있지만 이는 VM 인사이트로 수집된 성능 데이터와 중복될 가능성이 높습니다. 작업 영역에서 수집한 성능 데이터는 *Perf* 테이블에 저장되고 VM 인사이트로 수집된 성능 데이터는 *InsightsMetrics* 테이블에 저장됩니다. VM 인사이트에서 아직 수집하지 않은 카운터가 필요한 경우에만 작업 영역에 성능 수집을 구성합니다.

### <a name="diagnostic-extension-and-telegraf-agent"></a>진단 확장 및 Telegraf 에이전트
VM 인사이트는 성능 데이터를 Log Analytics 작업 영역으로 보내는 Log Analytics 에이전트를 사용하지만 Azure Monitor 메트릭에는 보내지 않습니다. 이 데이터를 메트릭에 전송하면 메트릭 탐색기를 사용하여 분석하고 메트릭 경고와 함께 사용할 수 있습니다. 이렇게 하려면 Windows의 진단 확장과 Linux의 Telegraf 에이전트가 필요합니다.

이러한 에이전트 설치 및 구성에 대한 자세한 내용은 [WAD(Windows Azure Diagnostics 확장) 설치 및 구성](agents/diagnostics-extension-windows-install.md) 및 [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](essentials/collect-custom-metrics-linux-telegraf.md)을 참조하세요.


## <a name="monitor-applications"></a>애플리케이션 모니터링
Azure Monitor는 모니터링하려는 각 애플리케이션을 위해 구성해야 하는 [Application Insights](app/app-insights-overview.md)를 사용하여 사용자 지정 애플리케이션을 모니터링합니다. 구성 프로세스는 모니터링되는 애플리케이션의 유형과 수행하려는 모니터링 유형에 따라 달라집니다. Application Insights에서 수집한 데이터는 기능에 따라 Azure Monitor 메트릭, Azure Monitor 로그 및 Azure Blob Storage에 저장됩니다. 성능 데이터는 추가 구성 없이 Azure Monitor 메트릭과 Azure Monitor 로그에 모두 저장됩니다.

### <a name="create-an-application-resource"></a>애플리케이션 리소스 만들기
모니터링하려는 각 애플리케이션에 대해 Application Insights 리소스를 만들어야 합니다. Application Insights에서 수집한 로그 데이터는 작업 영역 기반 애플리케이션의 Azure Monitor 로그에 저장됩니다. 클래식 애플리케이션의 로그 데이터는 [데이터 구조](logs/data-platform-logs.md#data-structure)에 설명된 대로 Log Analytics 작업 영역과 별도로 저장됩니다.

 클래식 애플리케이션을 만들지 또는 작업 영역 기반 애플리케이션을 만들지 선택해야 합니다. 클래식 애플리케이션을 만들려면 [Application Insights 리소스 만들기](app/create-new-resource.md)를 참조하세요. 작업 영역 기반 애플리케이션을 만들려면 [작업 영역 기반 Application Insights 리소스(미리 보기)](app/create-workspace-resource.md)를 참조하세요.

### <a name="configure-codeless-or-code-based-monitoring"></a>코드 없는 모니터링 또는 코드 기반 모니터링 구성
애플리케이션에 대한 모니터링을 사용하도록 설정하려면 코드 없는 모니터링을 사용할지 또는 코드 기반 모니터링을 사용할지 결정해야 합니다. 구성 프로세스는 이 결정 및 모니터링하려는 애플리케이션의 유형에 따라 달라집니다.

**코드 없는 모니터링** 은 구현하기 가장 쉽고 코드 개발 후에 구성할 수 있습니다. 코드를 업데이트할 필요가 없습니다. 애플리케이션에 따라 모니터링을 사용하도록 설정하는 자세한 내용은 다음 리소스를 참조하세요.

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


## <a name="visualize-data"></a>데이터 시각화
인사이트 및 솔루션에는 자신의 데이터를 분석하기 위한 자체 통합 문서와 보기가 포함되어 있습니다. 이 외에도 Azure Monitor 데이터 및 대시보드용 통합 문서를 포함하여 고유한 [시각화](visualizations.md)를 만들어 Azure Monitor 데이터를 Azure의 다른 서비스의 데이터와 결합할 수 있습니다.


### <a name="create-workbooks"></a>통합 문서 만들기
Azure Monitor의 [통합 문서](visualize/workbooks-overview.md)를 사용하면 Azure Portal에서 풍부한 시각적 보고서를 만들 수 있습니다. Azure Monitor 메트릭과 Azure Monitor 로그에서 다양한 데이터 집합을 결합하여 통합된 대화형 환경을 만들 수 있습니다. Azure Monitor 메뉴의 **통합 문서** 탭에서 통합 문서 갤러리에 액세스할 수 있습니다. 

사용자 지정 통합 문서 만들기에 대한 자세한 내용은 [Azure Monitor 통합 문서](visualize/workbooks-overview.md)를 참조하세요.

### <a name="create-dashboards"></a>대시보드 만들기
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)는 Azure의 기본 대시보드 기술이며 Azure Monitor 데이터를 다른 서비스의 데이터와 결합하여 Azure 인프라에 대한 단일 창을 제공할 수 있습니다. Azure Monitor 로그의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Log Analytics 데이터의 대시보드 만들기 및 공유](visualize/tutorial-logs-dashboards.md)를 참조하세요. 

Application Insights의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드 만들기](app/tutorial-app-dashboards.md)를 참조하세요. 

## <a name="alerts"></a>경고
Azure Monitor의 경고는 모니터링 데이터에서 식별된 중요한 데이터 또는 패턴을 사전에 알려줍니다. 일부 인사이트는 구성 없이 경고를 생성합니다. 다른 시나리오의 경우 분석할 데이터와 경보 생성 시기에 대한 기준, 경보가 생성될 때 취할 작업을 정의하는 작업 그룹을 포함하는 [경고 규칙](alerts/alerts-overview.md)을 작성해야 합니다. 


### <a name="create-action-groups"></a>작업 그룹 만들기
[작업 그룹](alerts/action-groups.md)은 경고가 트리거될 때 수행할 작업을 결정하기 위해 경고 규칙에서 사용하는 알림 기본 설정 컬렉션입니다. 작업의 예에는 메일 또는 문자 보내기, 웹후크 호출 또는 ITSM 도구로 데이터 보내기가 포함됩니다. 각 경고 규칙에는 하나 이상의 작업 그룹이 필요하며 여러 경고 규칙에서 단일 작업 그룹을 사용할 수 있습니다.

작업 그룹 만들기 및 포함할 수 있는 다양한 작업 설명은 [Azure Portal에서 작업 그룹 만들기 및 관리](alerts/action-groups.md)를 참조하세요.


### <a name="create-alert-rules"></a>경고 규칙 만들기
사용하는 데이터 형식에 따라 여러 유형의 경고 규칙이 정의되어 있습니다. 각 규칙에는 다양한 기능과 다양한 비용이 있습니다. 수행해야 하는 기본 전략은 필요한 논리를 제공하는 비용이 가장 낮은 경고 규칙 유형을 사용하는 것입니다.

- [활동 로그 규칙](alerts/activity-log-alerts.md). 지정된 조건과 일치하는 새 활동 로그 이벤트에 대한 응답으로 경고를 만듭니다. 이러한 경고에 대한 비용은 없으므로 첫 번째 선택이어야 합니다. 활동 로그 경고 만들기에 대한 자세한 내용은 [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리](alerts/alerts-activity-log.md)를 참조하세요.
- [메트릭 경고 규칙](alerts/alerts-metric-overview.md). 임계값을 초과하는 하나 이상의 메트릭 값에 대한 응답으로 경고를 만듭니다. 메트릭 경고는 값이 임계값 아래로 떨어지면 경고가 자동으로 닫히는 상태 저장 의미이며 상태가 변경될 때만 알림을 보냅니다. 메트릭 경고에 대한 비용이 발생하지만 로그 경고보다 훨씬 적습니다. 메트릭 경고 만들기에 대한 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](alerts/alerts-metric.md)를 참조하세요.
- [로그 경고 규칙](alerts/alerts-unified-log.md). 일정 쿼리 결과가 지정된 조건과 일치하는 경우 경고를 만듭니다. 이러한 규칙은 경고 규칙 중 가장 비용이 많이 들지만 가장 복잡한 기준을 허용합니다. 로그 쿼리 경고 만들기에 대한 자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](alerts/alerts-log.md)를 참조하세요.
- [애플리케이션 경고](app/monitor-web-app-availability.md)를 사용하면 웹 애플리케이션의 사전 성능 및 가용성 테스트를 수행할 수 있습니다. 간단한 ping 테스트를 무료로 수행할 수 있지만 더 복잡한 테스트를 위한 비용이 듭니다. 다양한 테스트에 대한 설명과 테스트 생성에 대한 자세한 내용은 [웹 사이트의 가용성 모니터링](app/monitor-web-app-availability.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Policy를 사용하여 대규모로 Azure Monitor 배포](deploy-scale.md)를 참조하세요.