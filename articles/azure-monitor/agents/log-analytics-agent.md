---
title: Log Analytics 에이전트 개요
description: 이 항목에서는 Log Analytics를 사용하여 Azure, 온-프레미스 또는 여타 클라우드 환경에 호스트된 컴퓨터를 모니터링하고 데이터를 수집하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/02/2021
ms.openlocfilehash: 1fa0763c9d4ccdb4e0233a5c6159be7c0d745be6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470398"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 에이전트 개요

Azure Log Analytics 에이전트는 모든 클라우드, 온-프레미스 머신 및 [System Center Operations Manager](/system-center/scom/)에서 모니터링하는 Windows 및 Linux 가상 머신의 원격 분석을 수집하고 Azure Moniter의 Log Analytics 작업 영역으로 수집한 데이터를 보냅니다. Log Analytics 에이전트는 [VM 인사이트](../vm/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml), [Azure Automation](../../automation/automation-intro.md) 등의 Azure Monitor 내 기타 서비스와 인사이트도 지원합니다. 이 문서에서는 에이전트, 시스템 및 네트워크 요구 사항과 배포 모델을 상세히 살펴봅니다.

> [!NOTE]
> Log Analytics 에이전트를 Microsoft Monitoring Agent(MMA)라고도 합니다.

## <a name="comparison-to-azure-diagnostics-extension"></a>Azure 진단 확장과 비교

Azure Monitor의 [Azure 진단 확장](./diagnostics-extension-overview.md)을 사용하여 Azure 가상 머신의 게스트 운영 체제에서도 모니터링 데이터를 수집할 수 있습니다. 요구 사항에 따라 둘 중 하나 또는 모두를 사용하도록 선택할 수 있습니다. Azure Monitor 에이전트의 상세 비교는 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요. 

고려해야 할 주요 차이점은 다음과 같습니다.

- Azure Diagnostics 확장은 Azure Virtual Machines에만 사용할 수 있습니다. Log Analytics 에이전트는 Azure, 다른 클라우드 환경 및 온-프레미스의 가상 머신에 사용할 수 있습니다.
- Azure Diagnostics 확장은 Azure Storage, [Azure Monitor Metrics](../essentials/data-platform-metrics.md)(Windows만 해당) 및 Event Hubs로 데이터를 보냅니다. Log Analytics 에이전트는 데이터를 [Azure Monitor 로그](../logs/data-platform-logs.md)에 보냅니다.
- Log Analytics 에이전트는 [솔루션](../monitor-reference.md#insights-and-curated-visualizations), [VM 인사이트](../vm/vminsights-overview.md) 및 [Azure Security Center](../../security-center/index.yml) 등의 기타 서비스에 필요합니다.

## <a name="costs"></a>비용

Log Analytics 에이전트에 대한 비용은 없지만 데이터 수집에 대한 요금이 발생할 수 있습니다. Log Analytics 작업 영역에서 수집된 데이터의 가격 책정에 대한 자세한 내용은 [Azure Monitor Logs로 사용량 및 비용 관리](../logs/manage-cost-storage.md)를 참조하세요.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

 Log Analytics 에이전트에서 지원하는 Windows 및 Linux 운영 체제 버전 목록은 [지원되는 운영 체제](../agents/agents-overview.md#supported-operating-systems)를 참조하세요. 

## <a name="data-collected"></a>수집되는 데이터

다음 표에는 연결된 모든 에이전트에서 수집하도록 Log Analytics 작업 영역을 구성할 수 있는 데이터 형식이 나열되어 있습니다. Log Analytics 에이전트를 사용하여 다른 종류의 데이터를 수집하는 인사이트, 솔루션 및 기타 솔루션 목록은 [Azure Monitor에서 모니터링하는 항목](../monitor-reference.md)을 참조하세요.

| 데이터 원본 | Description |
| --- | --- |
| [Windows 이벤트 로그](../agents/data-sources-windows-events.md) | Windows 이벤트 로깅 시스템으로 전송되는 정보입니다. |
| [Syslog](../agents/data-sources-syslog.md)                     | Linux 이벤트 로깅 시스템으로 전송되는 정보입니다. |
| [성능](../agents/data-sources-performance-counters.md)  | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값입니다. |
| [IIS 로그](../agents/data-sources-iis-logs.md)                 | 게스트 운영 체제에서 실행되는 IIS 웹 사이트에 대한 사용 정보입니다. |
| [사용자 지정 로그](../agents/data-sources-custom-logs.md)           | Windows와 Linux 컴퓨터의 텍스트 파일에서 수집하는 이벤트입니다. |

## <a name="data-destinations"></a>데이터 대상

Log Analytics 에이전트는 Azure Monitor의 Log Analytics 작업 영역으로 데이터를 보냅니다. Windows 에이전트를 멀티호밍하여 여러 작업 영역과 System Center Operations Manager 관리 그룹으로 데이터를 보낼 수 있습니다. Linux 에이전트는 작업 영역 또는 관리 그룹 중 하나만 단일 대상으로 보낼 수 있습니다.

## <a name="other-services"></a>기타 서비스

Linux 및 Windows용 에이전트는 Azure Monitor 연결에만 사용되지 않습니다. Azure Security Center 및 Azure Sentinel과 같은 기타 서비스도 에이전트 및 에이전트와 연결된 Log Analytics 작업 영역을 사용합니다. 에이전트는 [변경 내용 추적](../../automation/change-tracking/overview.md), [업데이트 관리](../../automation/update-management/overview.md), [Azure Security Center](../../security-center/security-center-introduction.md) 등의 Hybrid Runbook Worker 역할 및 기타 서비스를 호스트하도록 Azure Automation도 지원합니다. Hybrid Runbook Worker 역할에 대한 자세한 내용은 [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)를 참조하세요.  

## <a name="workspace-and-management-group-limitations"></a>작업 영역 및 관리 그룹 제한 사항

Operations Manager 관리 그룹에 에이전트를 연결하는 방법에 대한 자세한 내용은 [에이전트에서 Operations Manager 관리 그룹에 보고하도록 구성](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)을 참조하세요.

* Windows 에이전트는 System Center Operations Manager 관리 그룹에 연결되어 있는 경우에도 최대 4개의 작업 영역에 연결할 수 있습니다.
* Linux 에이전트는 멀티호밍을 지원하지 않으며 단일 작업 영역 또는 관리 그룹에만 연결할 수 있습니다.

## <a name="security-limitations"></a>보안 제한 사항

* Windows 및 Linux 에이전트는 [FIPS 140 표준](/windows/security/threat-protection/fips-140-validation)을 지원하지만 [다른 유형의 보안 강화는 지원하지 않을 수 있습니다](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>설치 옵션

요구 사항에 따라 Log Analytics 에이전트를 설치하고 머신을 Azure Monitor에 연결하는 방법에는 여러 가지가 있습니다. 다음 섹션에는 다양한 유형의 가상 머신에서 사용할 수 있는 방법이 나와 있습니다.

> [!NOTE]
> Log Analytics Agent가 이미 구성한 머신을 복제하는 것은 지원되지 않습니다. 에이전트가 작업 영역에 이미 연결되어 있는 경우에는 ‘최고 품질의 이미지’를 사용할 수 없습니다.

### <a name="azure-virtual-machine"></a>Azure 가상 머신

- [VM 인사이트](../vm/vminsights-enable-overview.md)는 에이전트를 대규모로 사용할 수 있는 다양한 방법을 제공합니다. 여기에는 Log Analytics 에이전트와 Dependency 에이전트의 설치가 포함됩니다. 
- 보안 취약점과 위협을 모니터링하도록 설정된 경우 Azure Security Center는 지원되는 모든 Azure VM과 새로 생성되는 VM에서 [Log Analytics 에이전트를 프로비저닝할 수 있습니다](../../security-center/security-center-enable-data-collection.md).
- [Windows](../../virtual-machines/extensions/oms-windows.md) 또는 [Linux](../../virtual-machines/extensions/oms-linux.md) 용 Log Analytics VM 확장은 Azure Portal, Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿과 함께 설치할 수 있습니다.
- [Azure Portal에서 수동으로](../vm/monitor-virtual-machine.md?toc=%2fazure%2fazure-monitor%2ftoc.json) 개별 Azure 가상 머신을 설치합니다.

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>온-프레미스 또는 다른 클라우드의 Windows 가상 머신

- [Azure Arc 사용 서버](../../azure-arc/servers/overview.md) 를 사용 하 여 Log Analytics VM 확장을 배포 하 고 관리 합니다. [배포 옵션](../../azure-arc/servers/concept-log-analytics-extension-deployment.md) 을 검토 하 여 Arc 사용 서버에 등록 된 컴퓨터에서 확장에 사용할 수 있는 다양 한 배포 방법을 파악 합니다.
- 명령줄에서 에이전트를 [수동으로 설치](../agents/agent-windows.md)합니다.
- [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation)를 사용하여 설치를 자동화합니다.
- [Azure Stack을 사용한 Resource Manager 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) 사용

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>온-프레미스 또는 다른 클라우드의 Linux 가상 머신

- [Azure Arc 사용 서버](../../azure-arc/servers/overview.md) 를 사용 하 여 Log Analytics VM 확장을 배포 하 고 관리 합니다. [배포 옵션](../../azure-arc/servers/concept-log-analytics-extension-deployment.md) 을 검토 하 여 Arc 사용 서버에 등록 된 컴퓨터에서 확장에 사용할 수 있는 다양 한 배포 방법을 파악 합니다.
- GitHub에 호스트된 래퍼 스크립트를 호출하는 에이전트를 [수동으로 설치](../vm/monitor-virtual-machine.md)합니다.
- Windows 컴퓨터 보고에서 수집된 데이터를 관리 그룹에 전달하도록 [System Center Operations Manager](./om-agents.md)와 Azure Monitor를 통합합니다.

## <a name="workspace-id-and-key"></a>작업 영역 ID 및 키

설치 방법과 관계없이, 에이전트를 연결할 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다. Azure Portal의 **Log Analytics 작업 영역** 메뉴에서 작업 영역을 선택합니다. 그런 다음 **설정** 섹션에서 **에이전트 관리** 를 선택합니다. 

[![작업 영역 세부 정보](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1.2 프로토콜

Azure Monitor 로그로 전송 중인 데이터를 보호하려면 적어도 TLS(전송 계층 보안) 1.2 이상을 사용하도록 에이전트를 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**.  자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../logs/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

## <a name="network-requirements"></a>네트워크 요구 사항

Linux 및 Windows용 에이전트는 TCP 포트 443을 통해 Azure Monitor 서비스로 아웃바운드 통신합니다. 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우 아래의 요구 사항을 검토하여 필요한 네트워크 구성을 파악하세요. IT 보안 정책에서 네트워크 컴퓨터의 인터넷 연결을 허용하지 않는 경우 [Log Analytics 게이트웨이](gateway.md)를 설정한 후 에이전트가 게이트웨이를 통해 Azure Monitor에 연결하도록 구성할 수 있습니다. 그러면 에이전트는 구성 정보를 수신하고 수집한 데이터를 보낼 수 있습니다.

![Log Analytics 에이전트 통신 다이어그램](./media/log-analytics-agent/log-analytics-agent-01.png)

다음 표에는 Linux 및 Windows 에이전트가 Azure Monitor 로그와 통신하는 데 필요한 프록시 및 방화벽 구성 정보가 나열되어 있습니다.

### <a name="firewall-requirements"></a>방화벽 요구 사항

|에이전트 리소스|포트 |Direction |HTTPS 검사 무시|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|*.oms.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|\*.blob.core.windows.net |포트 443 |아웃바운드|예 |
|\* .azure-automation.net |포트 443 |아웃바운드|예 |

Azure Government에 필요한 방화벽 정보는 [Azure Government 관리](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)를 참조하세요. 

Azure Automation Hybrid Runbook Worker를 사용하여 사용자 환경에서 Runbook이나 관리 기능을 사용하기 위해 Automation 서비스에 연결하고 등록하려면 [Hybrid Runbook Worker에 대한 네트워크 구성](../../automation/automation-hybrid-runbook-worker.md#network-planning)에 설명된 URL 및 포트 번호에 대한 액세스 권한이 있어야 합니다.

### <a name="proxy-configuration"></a>프록시 구성

Windows 및 Linux 에이전트는 HTTPS 프로토콜을 사용하여 프록시 서버 또는 Log Analytics 게이트웨이를 통해 Azure Monitor와 통신하도록 지원합니다.  익명 및 기본 인증(사용자 이름/암호)이 둘 다 지원됩니다.  서비스에 직접 연결된 Windows 에이전트의 경우, 프록시 구성은 설치 중이나 제어판 또는 PowerShell을 사용하여 [배포 후](../agents/agent-manage.md#update-proxy-settings)에 지정됩니다.  

Linux 에이전트의 경우, 설치 중에 또는 [설치 후에](../agents/agent-manage.md#update-proxy-settings) proxy.conf 구성 파일을 수정하여 프록시 서버를 지정할 수 있습니다. Linux 에이전트 프록시 구성 값은 다음 구문을 포함합니다.

`[protocol://][user:password@]proxyhost[:port]`

|속성| Description |
|--------|-------------|
|프로토콜 | https |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|password | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버/Log Analytics 게이트웨이의 주소 또는 FQDN |
|포트 | 프록시 서버/Log Analytics 게이트웨이 대한 선택적 포트 번호 |

예: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 암호에 "\@"과 같은 특수 문자를 사용하는 경우 값이 잘못 구문 분석되므로 프록시 연결 오류가 발생합니다.  이 문제를 해결하려면 [URLDecode](https://www.urldecoder.org/)와 같은 도구를 사용하여 URL에서 암호를 인코드합니다.  

## <a name="next-steps"></a>다음 단계

* [데이터 원본](../agents/agent-data-sources.md)을 검토하여 Windows 또는 Linux 시스템에서 데이터를 수집할 수 있는 데이터 원본을 이해하세요.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../logs/log-query-overview.md)에 대해 알아봅니다. 
* Azure Monitor에 기능을 추가하고 Log Analytics 작업 영역으로 데이터를 수집하는 [모니터링 솔루션](../insights/solutions.md)에 대해 알아봅니다.