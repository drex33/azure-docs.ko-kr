---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.openlocfilehash: 8542f3e39669135295073f4f937c6edfdd5127cf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566542"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor 에이전트 개요

가상 머신 및 기타 컴퓨팅 리소스에는 게스트 운영 체제 및 워크로드의 성능 및 가용성을 측정하는 데 필요한 모니터링 데이터를 수집하기 위한 에이전트가 필요 합니다. 이 문서에서는 Azure Monitor에서 사용하는 에이전트를 설명하고 특정 환경에 대한 요구 사항을 충족하는 데 필요한 사항을 결정하는 데 도움을 줍니다.

> [!NOTE]
> 최근 Azure Monitor는 게스트 운영 체제 모니터링 데이터를 수집하는 데 필요한 모든 기능을 제공하는 새 에이전트인 Azure Monitor 에이전트를 출시했습니다. Azure Monitor 및 Log Analytics의 통합으로 인해 존재하는 여러 레거시 에이전트가 있으며, 각각은 중복된 고유한 기능이 있으므로 기존 모든 에이전트의 기능을 통합하는 새 에이전트를 사용하고 추가 혜택을 제공하는 것이 좋습니다. [자세한 내용](./azure-monitor-agent-overview.md)

## <a name="summary-of-agents"></a>에이전트 요약

다음 표에서는 Windows용 Azure Monitor 에이전트와 Linux용 Azure Monitor 에이전트를 간단히 비교해서 보여 줍니다. 각각에 대한 자세한 내용은 아래 섹션에 나와 있습니다.

### <a name="windows-agents"></a>Windows 에이전트

| | Azure Monitor 에이전트 | 진단<br>확장(WAD) | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|
| **지원되는 환경** | Azure<br>기타 클라우드(Azure Arc)<br>온-프레미스(Azure Arc)  | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | 
| **에이전트 요구 사항**  | 없음 | 없음 | 없음 | Log Analytics 에이전트가 필요합니다. |
| **수집되는 데이터** | 이벤트 로그<br>성능 | 이벤트 로그<br>ETW 이벤트<br>성능<br>파일 기반 로그<br>IIS 로그<br>.NET 앱 로그<br>크래시 덤프<br>에이전트 진단 로그 | 이벤트 로그<br>성능<br>파일 기반 로그<br>IIS 로그<br>인사이트 및 솔루션<br>기타 서비스 | 프로세스 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Monitor 로그<br>Azure Monitor 메트릭<sup>1</sup> | Azure Storage<br>Azure Monitor 메트릭<br>이벤트 허브 | Azure Monitor 로그 | Azure Monitor 로그<br>(Log Analytics 에이전트를 통해) |
| **서비스 및**<br>**features**<br>**지원** | Log Analytics<br>메트릭 탐색기 | 메트릭 탐색기 | VM 인사이트<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM 인사이트<br>서비스 맵 |

### <a name="linux-agents"></a>Linux 에이전트

| | Azure Monitor 에이전트 | 진단<br>확장(LAD) | Telegraf<br>에이전트 | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|:---|
| **지원되는 환경** | Azure<br>기타 클라우드(Azure Arc)<br>온-프레미스(Azure Arc) | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 |
| **에이전트 요구 사항**  | 없음 | 없음 | 없음 | 없음 | Log Analytics 에이전트가 필요합니다. |
| **수집되는 데이터** | Syslog<br>성능 | Syslog<br>성능 | 성능 | Syslog<br>성능| 프로세스 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Monitor 로그<br>Azure Monitor 메트릭<sup>1</sup> | Azure Storage<br>이벤트 허브 | Azure Monitor 메트릭 | Azure Monitor 로그 | Azure Monitor 로그<br>(Log Analytics 에이전트를 통해) |
| **서비스 및**<br>**features**<br>**지원** | Log Analytics<br>메트릭 탐색기 | | 메트릭 탐색기 | VM 인사이트<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM 인사이트<br>서비스 맵 |

<sup>1</sup> Azure Monitor 메트릭을 사용 하는 다른 제한 사항을 검토 하려면 [여기를 클릭](../essentials/metrics-custom-overview.md#quotas-and-limits) 하세요. Linux에서 Azure Monitor 메트릭은 유일한 대상이 1.10.9.0 이상에서 지원 됩니다. 

## <a name="azure-monitor-agent"></a>Azure Monitor 에이전트

[Azure Monitor 에이전트](azure-monitor-agent-overview.md)는 Windows 및 Linux 머신 모두에 대한 Log Analytics 에이전트, Azure 진단 확장 및 Telegraf 에이전트를 대체합니다. Azure Monitor 로그와 Azure Monitor 메트릭 모두에 데이터를 보낼 수 있으며, 각 에이전트에 대한 데이터 수집 및 대상을 구성하는 보다 스케일링 가능한 방법을 제공하는 [DCR(데이터 수집 규칙)](data-collection-rule-overview.md)을 사용합니다.

다음 작업을 수행해야 하는 경우 Azure Monitor 에이전트를 사용합니다.

- Azure의 모든 머신, 다른 클라우드 또는 온-프레미스에서 게스트 로그 및 메트릭을 수집합니다. (Azure 외부 머신에 대해 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)가 필요합니다.) 
- [데이터 수집 규칙](./data-collection-rule-overview.md)을 사용하여 중앙에서 데이터 수집 구성을 관리하고 전체 관리를 위해 ARM(Azure Resource Manager) 템플릿 또는 정책을 사용합니다.
- Azure Monitor를 사용 하 여 분석을 위해 Azure Monitor 로그 및 Azure Monitor 메트릭 (미리 보기)에 데이터를 보냅니다. 
- Windows 및 Linux에서 로그에 대한 Windows 이벤트 필터링 또는 멀티 호밍 활용
<!--- Send data to Azure Storage for archiving.
- Send data to third-party tools using [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Manage the security of your machines using [Azure Security Center](../../security-center/security-center-introduction.md)  or [Azure Sentinel](../../sentinel/overview.md). (Available in private preview.)
- Use [VM insights](../vm/vminsights-overview.md) which allows you to monitor your machines at scale and monitors their processes and dependencies on other resources and external processes..  
- Manage the security of your machines using [Azure Security Center](../../security-center/security-center-introduction.md)  or [Azure Sentinel](../../sentinel/overview.md).
- Use different [solutions](../monitor-reference.md#insights-and-core-solutions) to monitor a particular service or application. */
-->
Azure Monitor 에이전트의 제한 사항은 다음과 같습니다.
- 프로덕션에서 Log Analytics 솔루션을 사용할 수 없습니다(미리 보기에서만 사용 가능, [지원되는 기능 보기](./azure-monitor-agent-overview.md#supported-services-and-features)).
- 프라이빗 링크와 관련된 네트워킹 시나리오는 아직 지원되지 않습니다. 
- 사용자 지정 로그(파일) 또는 IIS 로그 파일 수집은 아직 지원되지 않습니다. 
- 대상으로 Event Hubs 및 Storage 계정을 지원하지 않습니다.
- Hybrid Runbook 작업자를 지원하지 않습니다.


## <a name="log-analytics-agent"></a>Log Analytics 에이전트

[Log Analytics 에이전트](./log-analytics-agent.md)는 게스트 운영 체제에서 모니터링 데이터를 수집하고, Azure, 기타 클라우드 공급자 및 온-프레미스 머신에서 가상 머신의 워크로드를 수집합니다. Log Analytics 작업 영역에 데이터를 보냅니다. Log Analytics 에이전트는 System Center Operations Manager에서 사용하는 것과 동일한 에이전트이며, 에이전트 컴퓨터를 멀티홈으로 지정하여 관리 그룹 및 Azure Monitor와 동시에 통신할 수 있습니다. 이 에이전트는 Azure Monitor 및 Azure의 기타 서비스에서 사용되는 특정 인사이트에도 필요합니다.

> [!NOTE]
> Windows용 Log Analytics 에이전트를 종종 MMA(Microsoft Monitoring Agent)라고 합니다. Linux용 Log Analytics 에이전트를 OMS 에이전트라고도 합니다.

다음 작업을 수행해야 하는 경우 Log Analytics 에이전트를 사용합니다.

* Azure 가상 머신 또는 Azure 외부에 호스트된 하이브리드 머신에서 로그 및 성능 데이터를 수집합니다.
* Log Analytics 작업 영역으로 데이터를 전송하여 [로그 쿼리](../logs/log-query-overview.md) 같은 [Azure Monitor 로그](../logs/data-platform-logs.md)에서 지원되는 기능을 활용합니다.
* 대규모로 머신을 모니터링할 수 있도록 하며 다른 리소스 및 외부 프로세스에 대한 프로세스 및 종속성을 모니터링하는 [VM 인사이트](../vm/vminsights-overview.md)를 사용합니다.  
* [Azure Security Center](../../security-center/security-center-introduction.md) 또는 [Azure Sentinel](../../sentinel/overview.md)을 사용하여 머신의 보안을 관리합니다.
* [Azure Automation 업데이트 관리](../../automation/update-management/overview.md), [Azure Automation 상태 구성](../../automation/automation-dsc-overview.md) 또는 [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md)를 사용하여 Azure 및 비 Azure 머신에 대한 포괄적인 관리를 제공합니다.
* 다른 [솔루션](../monitor-reference.md#insights-and-core-solutions)을 사용하여 특정 서비스 또는 애플리케이션을 모니터링합니다.

Log Analytics 에이전트의 제한 사항은 다음과 같습니다.

- Azure Monitor 메트릭, Azure Storage 또는 Azure Event Hubs로 데이터를 보낼 수 없습니다.
- 개별 에이전트에 대해 고유한 모니터링 정의를 구성하기 어렵습니다.
- 각 가상 머신에는 고유한 구성이 있으므로 대규모로 관리하기가 어렵습니다.

## <a name="azure-diagnostics-extension"></a>Azure 진단 확장

[Azure Diagnostics 확장](./diagnostics-extension-overview.md)은 게스트 운영 체제에서 모니터링 데이터를 수집하고 Azure 가상 머신 및 기타 컴퓨팅 리소스의 워크로드를 수집합니다. 주로 데이터를 Azure Storage로 수집할 뿐만 아니라 Azure Monitor 메트릭 및 Azure Event Hubs 등의 다른 대상으로도 데이터를 보내도록 데이터 싱크를 정의할 수 있습니다.

다음 작업을 수행해야 하는 경우 Azure 진단 확장을 사용합니다.

- 보관을 위해 Azure Storage로 데이터를 보내거나 [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같은 도구를 사용하여 분석합니다.
- [Azure Monitor 메트릭](../essentials/data-platform-metrics.md)으로 데이터를 전송하여 [메트릭 탐색기](../essentials/metrics-getting-started.md)로 분석하고, 거의 실시간 [메트릭 경고](../alerts/alerts-metric-overview.md) 및 [자동 스케일링](../autoscale/autoscale-overview.md)(Windows에만 해당) 같은 기능을 활용합니다.
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md)를 사용하여 타사 도구로 데이터를 보냅니다.
- [부팅 진단](/troubleshoot/azure/virtual-machines/boot-diagnostics)을 수집하여 VM 부팅 문제를 조사합니다.

Azure 진단 확장의 제한 사항은 다음과 같습니다.

- Azure 리소스에서만 사용할 수 있습니다.
- Azure Monitor 로그로 데이터를 전송하도록 기능이 제한됩니다.

## <a name="telegraf-agent"></a>Telegraf 에이전트

[InfluxData Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md)는 Linux 머신의 성능 데이터를 Azure Monitor 메트릭으로 수집하는 데 사용됩니다.

다음 작업을 수행해야 하는 경우 Telegraf 에이전트를 사용합니다.

* [Azure Monitor 메트릭](../essentials/data-platform-metrics.md)으로 데이터를 전송하여 [메트릭 탐색기](../essentials/metrics-getting-started.md)로 분석하고, 거의 실시간 [메트릭 경고](../alerts/alerts-metric-overview.md) 및 [자동 스케일링](../autoscale/autoscale-overview.md)(Linux에만 해당) 같은 기능을 활용합니다.

## <a name="dependency-agent"></a>종속성 에이전트

종속성 에이전트는 머신 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대한 검색된 데이터를 수집합니다. 

다음 작업을 수행해야 하는 경우 종속성 에이전트를 사용합니다.

* 맵 기능 [VM 인사이트](../vm/vminsights-overview.md) 또는 [서비스 맵](../vm/service-map.md) 솔루션을 사용합니다.

종속성 에이전트를 사용하는 경우 다음 사항을 고려하세요.

- 종속성 에이전트를 사용하려면 Log Analytics 에이전트를 동일한 머신에 설치해야 합니다.
- Linux 머신에서는 Azure 진단 확장 전에 Log Analytics 에이전트를 설치해야 합니다.

## <a name="virtual-machine-extensions"></a>가상 머신 확장

[Azure Monitor 에이전트](./azure-monitor-agent-install.md#virtual-machine-extension-details)는 가상 머신 확장으로만 사용할 수 있습니다. [Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md)용 Log Analytics 확장은 Azure 가상 머신에 Log Analytics 에이전트를 설치합니다. [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)용 Azure Monitor 종속성 확장은 Azure 가상 머신에 종속성 에이전트를 설치합니다. 이러한 에이전트는 위에서 설명한 것과 동일한 에이전트이지만 [가상 머신 확장](../../virtual-machines/extensions/overview.md)을 통해 관리할 수 있습니다. 가능하면 언제든지 확장을 사용하여 에이전트를 설치하고 관리하는 것이 좋습니다.

하이브리드 머신에서 [Azure Arc 지원 서버](../../azure-arc/servers/manage-vm-extensions.md)를 사용하여 Azure Monitor 에이전트, Log Analytics 및 Azure Monitor 종속성 VM 확장을 배포합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에서는 Azure Monitor 에이전트에서 지원되는 운영 체제를 나열합니다. 각 에이전트에 대한 설명서에서 고유한 고려 사항과 설치 프로세스를 참조하세요. 지원되는 운영 체제에 대해서는 Telegraf 설명서를 참조하세요. 모든 운영 체제는 x64로 간주됩니다. x86은 어떤 운영 체제에서도 지원되지 않습니다.

### <a name="windows"></a>Windows

| 운영 체제 | Azure Monitor 에이전트 | Log Analytics 에이전트 | 종속성 에이전트 | 진단 확장 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2022                                      | X |   |   |   |
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2019 Core                                 | X |   |   |   |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 | X |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   |   | X | X |
| Windows Server 2008 SP2                                   |   | X |  |  |
| Windows 10 Enterprise<br>(다중 세션 포함) 및 Pro<br>(서버 시나리오만<sup>1</sup>)  | X | X | X | X |
| Windows 8 Enterprise 및 Pro<br>(서버 시나리오만<sup>1</sup>)  |   | X | X |   |
| Windows 7 SP1<br>(서버 시나리오만<sup>1</sup>)                 |   | X | X |   |

<sup>1</sup> 서버 하드웨어에서 OS를 실행 합니다. 즉, 항상 연결 되어 있으며 항상 켜져 있고 다른 작업 (PC, office, 브라우저 등)을 실행 하지 않는 컴퓨터를 실행 합니다.

### <a name="linux"></a>Linux

| 운영 체제 | Azure Monitor 에이전트 <sup>1</sup> | Log Analytics 에이전트 <sup>1</sup> | 종속성 에이전트 | 진단 확장 <sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                        |   | X |   |   |
| CentOS Linux 8                                              | X <sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5+                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1 이상                                              |   |   |   | X |
| Oracle Linux 8                                              | X <sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4 이상                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8.2, 8.3, 8.4               | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 8                           | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7+                        |   | X | X | X |
| SUSE Linux Enterprise Server 15.2                           | X <sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15.1                           | X <sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15 SP1                         | X | X | X |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12 SP5                         | X | X | X | X |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20.04 LTS                                            | X | X | X |   |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X |   | X |

<sup>1</sup> 머신에 Python(2 또는 3)을 설치해야 합니다.

<sup>3</sup> 1.9.0 이전 버전의 Syslog 이벤트 수집에 관한 알려진 문제
#### <a name="dependency-agent-linux-kernel-support"></a>종속성 에이전트 Linux 커널 지원

종속성 에이전트는 커널 수준에서 작동하므로 지원은 커널 버전에도 좌우됩니다. 다음 표에는 종속성 에이전트에 대한 주 및 부 Linux OS 릴리스와 지원되는 커널 버전이 나와 있습니다.

| 배포 | OS 버전 | 커널 버전 |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20.04   | 5.4\* |
|                    | 18.04   | 5.3.0-1020<br>5.0(Azure 조정 커널 포함)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14-150\*
|                                 | 12 SP4 | 4.12.*(Azure 조정 커널 포함) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 

## <a name="next-steps"></a>다음 단계

각 에이전트에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Monitor 에이전트 개요](./azure-monitor-agent-overview.md)
- [Log Analytics 에이전트 개요](./log-analytics-agent.md)
- [Azure Diagnostics 확장 개요](./diagnostics-extension-overview.md)
- [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](../essentials/collect-custom-metrics-linux-telegraf.md)
