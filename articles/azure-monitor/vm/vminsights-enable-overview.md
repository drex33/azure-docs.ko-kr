---
title: VM 인사이트 사용 개요
description: VM 인사이트를 배포 및 구성하는 방법에 대해 알아봅니다. 시스템 요구 사항을 확인합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: fa656b974a89924a2ce029a7c5e38b4d9f68f6b7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440092"
---
# <a name="enable-vm-insights-overview"></a>VM 인사이트 사용 개요

이 문서에서는 VM 인사이트를 사용하여 다음의 상태와 성능을 모니터링할 수 있는 옵션에 대한 개요를 제공합니다.

- Azure 가상 머신 
- Azure 가상 머신 확장 집합
- Azure Arc와 연결된 하이브리드 가상 머신
- 온-프레미스 가상 머신
- 다른 클라우드 환경에서 호스트되는 가상 머신  

VM 인사이트를 설정하려면:

* Azure Portal의 메뉴에서 직접 **인사이트** 를 선택하여 단일 Azure 가상 머신, Azure 가상 머신 확장 집합 또는 Azure Arc 머신을 사용하도록 설정합니다.
* Azure Policy를 사용하여 여러 Azure 가상 머신, Azure 가상 머신 또는 Azure Arc 머신을 사용하도록 설정합니다. 이 방법을 사용하면 기존 및 새 VM 및 확장 집합에서 필요한 종속성이 설치되고 올바르게 구성됩니다. 호환되지 않는 가상 머신 및 크기 집합을 보고하므로 이를 사용하도록 설정하고 재구성할지 여부를 결정할 수 있습니다.
* PowerShell을 사용하여 지정된 구독 또는 리소스 그룹에서 여러 Azure 가상 머신, Azure Arc 가상 머신, Azure 가상 머신 확장 집합 또는 Azure Arc 머신을 사용하도록 설정합니다.
* VM 인사이트를 사용하여 회사 네트워크 또는 다른 클라우드 환경에서 호스트되는 VM 또는 물리적 컴퓨터를 모니터링합니다.

## <a name="supported-machines"></a>지원되는 머신
VM 인사이트는 다음 머신을 지원합니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결된 하이브리드 가상 머신

> [!IMPORTANT]
> 가상 머신용 이더넷 디바이스에 9자 넘는 문자가 있는 경우 VM 인사이트를 통해 인식되지 않으며 데이터가 InsightsMetrics 테이블로 전송되지 않습니다. 에이전트는 [다른 원본](../agents/agent-data-sources.md)에서 데이터를 수집합니다.

## <a name="supported-azure-arc-machines"></a>지원되는 Azure Arc 머신
Azure Arc 지원 서버는 Arc 확장 서비스를 사용할 수 있는 지역에서 VM 인사이트를 사용할 수 있습니다. Arc Agent 버전 0.9 이상을 실행해야 합니다.

| 연결된 원본 | 지원됨 | Description |
|:--|:--|:--|
| Windows 에이전트 | 예 | [Windows용 Log Analytics 에이전트](../agents/log-analytics-agent.md)와 함께 Windows 에이전트에는 Dependency 에이전트가 필요합니다. 자세한 내용은 [지원되는 운영 체제](../agents/agents-overview.md#supported-operating-systems)를 참조하세요. |
| Linux 에이전트 | 예 | [Linux용 Log Analytics 에이전트](../agents/log-analytics-agent.md)와 함께 Linux 에이전트에는 Dependency 에이전트가 필요합니다. 자세한 내용은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| System Center Operations Manager 관리 그룹 | 예 | |

## <a name="supported-operating-systems"></a>지원되는 운영 체제

VM 인사이트는 Log Analytics 에이전트 및 Dependency 에이전트를 지원하는 모든 운영 체제를 지원합니다. 전체 목록은 [Azure Monitor 에이전트 개요](../agents/agents-overview.md#supported-operating-systems)를 참조하세요.

> [!IMPORTANT]
> VM 인사이트 게스트 상태 기능은 공개 미리 보기로 제공되는 동안 보다 제한된 운영 체제를 지원합니다. 자세한 목록은 [VM 인사이트 게스트 상태 사용(미리 보기)](../vm/vminsights-health-enable.md)을 참조하세요.

### <a name="linux-considerations"></a>Linux 고려 사항
VM 인사이트를 지원하는 Dependency 에이전트의 Linux 지원에 대한 다음 고려 사항 목록을 참조하세요.

- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen* 의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.
- 9\.4 버전 이외의 Debian 배포판에서는 맵 기능이 지원되지 않으며 성능 기능은 Azure Monitor 메뉴에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 사용할 수 없습니다.
- CentOSPlus 커널은 지원됩니다.

Spectre 및 Meltdown 취약성에 대해 Linux 커널의 패치를 적용해야 합니다. 자세한 내용은 Linux 배포 공급 업체에 문의하세요. Spectre/Meltdown이 완화된 경우 다음 명령을 실행하여 사용 가능 여부를 확인합니다.

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

이 명령에 대한 출력은 다음과 유사하게 표시되며 머신에 문제가 있는지 여부를 지정합니다. 이러한 파일이 없는 경우 머신에 패치가 적용되지 않습니다.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
VM 인사이트에 Log Analytics 작업 영역이 필요합니다. 이 작업 영역의 세부 정보 및 요구 사항은 [VM 인사이트에 대한 Log Analytics 작업 영역 구성](vminsights-configure-workspace.md)을 참조하세요.
## <a name="agents"></a>에이전트
VM 인사이트를 사용하려면 모니터링할 가상 머신 또는 가상 머신 확장 집합에 다음 두 개의 에이전트를 설치해야 합니다. 리소스를 등록하려면 이러한 에이전트를 설치하고 작업 영역에 연결합니다.  이러한 에이전트의 네트워크 요구 사항은 [네트워크 요구 사항](../agents/log-analytics-agent.md#network-requirements)을 참조하세요.

- [Log Analytics 에이전트](../agents/log-analytics-agent.md) 가상 머신 또는 가상 머신 확장 집합에서 이벤트 및 성능 데이터를 수집하여 Log Analytics 작업 영역으로 전달합니다. Azure 리소스의 Log Analytics 에이전트에 대한 배포 방법에서는 [Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md)용 VM 확장을 사용합니다.
- Dependency 에이전트 [VM 인사이트의 Map 기능](../vm/vminsights-maps.md)에서 사용되는 가상 머신 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대한 검색된 데이터를 수집합니다. Dependency 에이전트는 Log Analytics 에이전트를 사용하여 데이터를 Azure Monitor로 배달합니다. Azure 리소스에 대한 Dependency 에이전트 배포 방법에서는 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)용 VM 확장을 사용합니다.

> [!NOTE]
> Log Analytics 에이전트는 System Center Operations Manager에서 사용하는 것과 동일한 에이전트입니다. VM 인사이트는 직접 연결된 경우에도 Operations Manager에서 모니터링되는 에이전트를 모니터링하고, Dependency 에이전트를 설치할 수 있습니다. [관리 그룹 연결](../tform/../agents/om-agents.md)을 통해 Azure Monitor에 연결된 에이전트는 VM 인사이트에서 모니터링할 수 없습니다.

다음은 이러한 에이전트를 배포하는 여러 가지 방법입니다. 

| 메서드 | Description |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | 단일 가상 머신, 가상 머신 확장 집합 또는 Azure Arc와 연결된 하이브리드 가상 머신에 두 에이전트를 모두 설치합니다. |
| [Resource Manager 템플릿](../vm/vminsights-enable-resource-manager.md) | 지원되는 방법 중 하나를 사용하여 CLI 및 PowerShell을 비롯한 Resource Manager 템플릿을 배포하는 두 에이전트를 모두 설치합니다. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | 가상 머신 또는 가상 머신 확장 집합을 만들 때 에이전트를 자동으로 설치하도록 Azure Policy 이니셔티브를 할당합니다. |
| [수동 설치](../vm/vminsights-enable-hybrid.md) | 데이터 센터 또는 다른 클라우드 환경을 포함하여 Azure 외부에 호스트된 컴퓨터의 게스트 운영 체제에 에이전트를 설치합니다. |


## <a name="network-requirements"></a>네트워크 요구 사항

- Log Analytics 에이전트의 네트워크 요구 사항은 [네트워크 요구 사항](../agents/log-analytics-agent.md#network-requirements)을 참조하세요.
- 종속성 에이전트를 사용하려면 가상 머신에서 169.254.169.254 주소로 연결해야 합니다. Azure 메타데이터 서비스 엔드포인트입니다. 방화벽 설정에서 이 엔드포인트에 대한 연결을 허용하는지 확인합니다.


## <a name="management-packs"></a>관리 팩
VM 인사이트에 대한 Log Analytics 작업 영역이 구성된 경우 해당 작업 영역에 연결된 모든 Windows 컴퓨터에 두 개의 관리 팩이 전달됩니다. 관리 팩은 *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* 및 *Microsoft.IntelligencePacks.VMInsights* 로 이름이 지정되며 *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs* 에 기록됩니다. 

*ApplicationDependencyMonitor* 관리 팩에 사용된 데이터 원본은 * *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* 입니다. *VMInsights* 관리 팩에 사용된 데이터 원본은 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll* 입니다.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스의 품질, 보안 및 무결성을 개선합니다. 

정확하고 효율적인 문제 해결 기능을 제공하기 위해 Map 기능은 소프트웨어 구성에 대한 데이터를 포함합니다. 데이터는 운영 체제, 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 정보를 제공합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

성능 모니터링 기능을 사용하는 방법에 대한 자세한 내용은 [VM 인사이트 성능 보기](../vm/vminsights-performance.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM 인사이트 Map 보기](../vm/vminsights-maps.md)를 참조하세요.
