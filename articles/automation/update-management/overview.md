---
title: Azure Automation - 업데이트 관리 개요
description: 이 문서에서는 Windows 및 Linux 머신의 업데이트를 구현하는 업데이트 관리 기능의 개요를 살펴봅니다.
services: automation
ms.subservice: update-management
ms.date: 06/24/2021
ms.topic: conceptual
ms.openlocfilehash: 0190d5501b95c0c70606978586edf30ff3d39b79
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676611"
---
# <a name="update-management-overview"></a>업데이트 관리 개요

Azure Automation의 업데이트 관리를 사용하여 Azure, 물리적 또는 환경의 Windows 및 Linux 가상 머신이나 온-프레미스 환경 및 다른 클라우드 환경의 VM에 대한 운영 체제 업데이트를 관리할 수 있습니다. 사용 가능한 업데이트의 상태를 신속하게 평가하고 업데이트 관리에 보고하는 머신에 대해 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다. 

서비스 공급자는 여러 고객 테넌트를 [Azure Lighthouse](../../lighthouse/overview.md)에 온보딩했을 수 있습니다. 업데이트 관리를 사용하여 동일한 Azure AD(Azure Active Directory) 테넌트에서 또는 Azure Lighthouse를 사용하여 테넌트 간 여러 구독의 머신에 대해 업데이트 배포를 평가하고 예약할 수 있습니다.

Microsoft는 전체 업데이트 관리 전략의 일환으로 고려해야 하는 Azure VM 또는 Azure 가상 머신 확장 집합에 대한 업데이트를 관리하는 데 도움이 되는 다른 기능을 제공합니다. 

- Azure 가상 머신을 자동으로 평가하고 업데이트하여 매월 출시된 *중요* 및 *보안* 업데이트를 통해 보안 규격을 준수하게 하는 데 관심이 있는 경우 [자동 VM 게스트 패치](../../virtual-machines/automatic-vm-guest-patching.md)(미리 보기)를 검토합니다. 이것은 Azure Automation의 업데이트 관리에서 해당 VM에 대한 업데이트 배포를 관리하는 것과 다르게, Azure VM이 사용량이 적은 시간 동안 가용성 집합 내의 VM을 비롯한 VM을 자동으로 업데이트하기 위한 대체 업데이트 관리 솔루션입니다. 

- Azure 가상 머신 확장 집합을 관리하는 경우 [자동 OS 이미지 업그레이드](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 수행하여 확장 집합의 모든 인스턴스에 대한 OS 디스크를 안전하게 자동으로 업그레이드하는 방법을 검토합니다. 

업데이트 관리를 배포하고 관리를 위해 머신을 사용하도록 설정하기 전에 다음 섹션의 정보를 이해하고 있어야 합니다.

## <a name="about-update-management"></a>업데이트 관리에 대해 자세히 알아보기

다음 다이어그램에서는 업데이트 관리에서 보안 업데이트를 평가하고 모든 연결된 Windows Server 및 Linux 서버에 이를 적용하는 방법을 보여 줍니다.

![업데이트 관리 워크플로](./media/overview/update-mgmt-updateworkflow.png)

업데이트 관리는 할당된 Azure 및 비 Azure 머신에서 업데이트 평가 및 업데이트 배포 결과를 로그 데이터로 저장하기 위해 Azure Monitor 로그와 통합됩니다. 이 데이터를 수집하기 위해 Automation 계정 및 Log Analytics 작업 영역이 함께 연결되며, Windows 및 Linux용 Log Analytics 에이전트가 머신에 있어야 하고 이 작업 영역에 보고하도록 구성해야 합니다. 업데이트 관리 기능은 작업 영역에 연결된 System Center Operations Manager 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집하도록 지원합니다. 머신 하나를 둘 이상의 Log Analytics 작업 영역에서 업데이트 관리에 등록(멀티 호밍)하는 것은 지원되지 않습니다.

다음 표에서는 업데이트 관리에 대해 지원되는 연결된 원본을 요약해서 보여 줍니다.

| 연결된 원본 | 지원됨 | Description |
| --- | --- | --- |
| Windows |예 |업데이트 관리 기능은 Log Analytics 에이전트 및 필요한 업데이트 설치를 통해 Windows 머신에서 시스템 업데이트에 대한 정보를 수집합니다. |
| Linux |예 |업데이트 관리 기능은 Log Analytics 에이전트 및 지원되는 배포판의 필수 업데이트 설치를 통해 Linux 머신에서 시스템 업데이트에 대한 정보를 수집합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리 기능은 연결된 관리 그룹의 에이전트로부터 소프트웨어 업데이트에 대한 정보를 수집합니다.<br/><br/>Operations Manager 에이전트에서 Azure Monitor 로그로의 직접 연결은 필요하지 않습니다. 로그 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

업데이트 관리에 할당된 머신은 동기화하도록 구성된 원본을 기준으로 얼마나 최신 상태인지를 보고합니다. Windows 머신은 Windows Server Update Services 또는 Microsoft 업데이트에 보고하도록 구성할 수 있으며, Linux 머신은 로컬 또는 퍼블릭 리포지토리에 보고하도록 구성할 수 있습니다. 또한 Microsoft Endpoint Configuration Manager에서 업데이트 관리 기능을 사용할 수도 있습니다. 자세한 내용은 [Windows Endpoint Configuration Manager와 업데이트 관리 통합](mecmintegration.md)을 참조하세요. 

Windows 머신의 WUA(Windows 업데이트 에이전트)를 WSUS에 보고하도록 구성한 경우 WSUS가 Microsoft 업데이트와 마지막으로 동기화된 시점에 따라 결과가 Microsoft 업데이트가 표시하는 내용과 다를 수 있습니다. 이 동작은 퍼블릭 리포지토리 대신 로컬 리포지토리에 보고하도록 구성된 Linux 머신에서도 동일하게 나타납니다. Windows 머신에서는 기본적으로 12시간마다 준수 검사가 실행됩니다. Linux 머신에서는 기본적으로 1시간마다 준수 검사가 이루어집니다. Log Analytics 에이전트가 다시 시작되면 15분 이내에 준수 검사가 시작됩니다. 머신에서 업데이트 준수 검사를 완료하면 에이전트가 Azure Monitor 로그에 정보를 대량으로 전달합니다. 

예약 배포를 만들어서 업데이트가 필요한 머신에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. 선택 사항으로 분류된 업데이트는 Windows 머신의 배포 범위에 포함되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약 배포는 머신을 명시적으로 지정하거나 특정 머신 집합의 로그 검색을 기반으로 하는(또는 지정된 기준에 따라 Azure VM을 동적으로 선택하는 [Azure 쿼리](query-logs.md)를 기준으로 하는) [컴퓨터 그룹](../../azure-monitor/logs/computer-groups.md)을 선택하여 해당 업데이트를 받을 대상 머신을 정의합니다. 이 그룹은 업데이트 관리를 사용하도록 설정하기 위해 구성을 수신할 머신의 대상을 제어하는 데 사용되는 [범위 구성](../../azure-monitor/insights/solution-targeting.md)과는 다릅니다. 이 그룹은 업데이트 준수를 수행 및 보고하는 것을 방지하며, 승인된 필요한 업데이트를 설치합니다.

배포를 정의할 때는 승인할 일정을 지정하고 업데이트가 설치될 수 있는 기간을 설정합니다. 이 기간을 유지 관리 기간이라고 합니다. 유지 관리 기간 중 20분은 재부팅이 필요하며 사용자가 적절한 재부팅 옵션을 선택했다고 가정한 상태로 재부팅을 위해 예약됩니다. 패치에 예상보다 시간이 오래 걸리고 유지 관리 기간이 20분 미만이라면 재부팅이 이루어지지 않습니다.

업데이트 패키지 배포 일정을 예약한 후에 평가를 위해 Linux 머신의 업데이트가 나타날 때까지 2~3시간이 걸립니다. Windows 머신의 경우 릴리스된 후 평가를 위해 업데이트가 나타날 때까지 12~15시간이 걸립니다. 업데이트 설치 전후에 업데이트 준수 검사가 수행되고 로그 데이터 결과가 작업 영역으로 전달됩니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 Runbook은 볼 수 없고 Runbook에는 구성이 필요하지 않습니다. 업데이트 배포가 생성되면 업데이트 배포는 포함된 머신에 대해 지정된 시간에 마스터 업데이트 Runbook을 시작하는 일정을 만듭니다. 마스터 Runbook은 Windows에서 Windows 업데이트 에이전트 또는 지원되는 Linux 배포판에서 해당하는 명령을 사용하여 필요한 업데이트의 설치를 시작하는 자식 Runbook을 각 에이전트에서 시작합니다.

업데이트 배포에 지정된 날짜 및 시간에 대상 머신은 배포를 병렬로 실행합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. WSUS 클라이언트 머신의 경우 업데이트가 WSUS에서 승인되지 않았으면 업데이트 배포가 실패합니다.

## <a name="limits"></a>제한

업데이트 관리에 적용되는 제한에 대해서는 [Azure Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)을 참조하세요.

## <a name="permissions"></a>사용 권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 내용은 [역할 기반 액세스 - 업데이트 관리](../automation-role-based-access-control.md#update-management-permissions)를 참조하세요.

## <a name="update-management-components"></a>업데이트 관리 구성 요소

업데이트 관리는 섹션에서 설명하는 리소스를 사용합니다. 리소스는 업데이트 관리를 사용하도록 설정하면 Automation 계정에 자동으로 추가됩니다.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker 그룹

업데이트 관리를 사용하도록 설정하면 Log Analytics 작업 영역에 직접 연결된 모든 Windows 머신이 자동으로 시스템 Hybrid Runbook Worker로 구성되어 업데이트 관리를 지원하는 Runbook을 지원합니다.

업데이트 관리에서 관리되는 각 Windows 머신은 경우 해당 Automation 계정의 Hybrid Worker 그룹 창에 시스템 Hybrid Worker 그룹으로 표시됩니다. 그룹은 `Hostname FQDN_GUID` 명명 규칙을 사용합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 대상으로 지정하려고 시도해도 실패하게 됩니다. 그룹은 업데이트 관리를 지원하는 용도로만 사용할 수 있습니다. Hybrid Runbook Worker로 구성된 Windows 머신의 목록을 확인하는 방법에 대한 자세한 내용은 [Hybrid Runbook Worker 보기](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)를 참조하세요.

업데이트 관리와 사용자 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 경우, Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 머신을 추가하여 Automation Runbook을 지원할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

업데이트 관리 기능으로 관리되는 머신에는 다음 관리 팩이 설치됩니다. Operations Manager 관리 그룹이 [Log Analytics 작업 영역에 연결되면](../../azure-monitor/agents/om-agents.md) 관리 팩이 Operations Manager 관리 그룹에 설치됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 로그 데이터를 수집하도록 관리 그룹에 에이전트가 구성된 Log Analytics 작업 영역에 연결된 Operations Manager 1807 또는 2019 관리 그룹이 있는 경우, **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 규칙에서 `IsAutoRegistrationEnabled` 매개 변수를 재정의하고 `True`로 설정해야 합니다.

관리 팩의 업데이트에 대한 자세한 내용은 [Azure Monitor 로그에 Operations Manager 연결](../../azure-monitor/agents/om-agents.md)을 참조하세요.

> [!NOTE]
> 업데이트 관리가 Log Analytics 에이전트를 사용하여 머신을 완전히 관리할 수 있으려면 Windows용 Log Analytics 에이전트 또는 Linux용 Log Analytics 에이전트로 업데이트해야 합니다. 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용하는 환경에서는 System Center Operations Manager 2012 R2 UR 14 이상을 실행해야 합니다.

## <a name="data-collection-frequency"></a>데이터 수집 빈도

업데이트 관리는 다음 규칙을 사용하여 관리형 머신에서 데이터를 검사합니다. 관리형 머신의 업데이트된 데이터가 대시보드에 표시되기까지 30분에서 6시간이 걸릴 수 있습니다.

* 각 Windows 머신 - 업데이트 관리가 각 머신에 대해 하루에 두 번 검사를 수행합니다.

* 각 Linux 머신 - 업데이트 관리가 한 시간에 한 번씩 검사를 수행합니다.

업데이트 관리를 사용하는 머신에서 Azure Monitor 로그가 평균적으로 사용하는 데이터는 대략적으로 월 25MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다. Azure Monitor 로그 데이터 사용량을 분석하는 방법에 대한 자세한 내용은 [사용량 및 비용 관리](../../azure-monitor/logs/manage-cost-storage.md)를 참조하세요.

## <a name="update-classifications"></a>업데이트 분류

다음 표에서는 업데이트 관리가 Windows 업데이트에 대해 지원하는 분류를 정의합니다.

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

다음 표에서는 Linux 업데이트에 대해 지원되는 분류를 정의합니다.

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 그 외의 모든 업데이트입니다.        |

> [!NOTE]
> Linux 머신에 대한 업데이트 분류는 지원되는 Azure 퍼블릭 클라우드 지역에서 사용되는 경우에만 사용 가능합니다. 다음 국가별 클라우드 지역에서 업데이트 관리를 사용하는 경우 Linux 업데이트 분류는 없습니다.
>
>* Azure 미국 정부
>* 중국 Vianet 21
>
> 업데이트는 분류되는 대신 **다른 업데이트** 범주에 보고됩니다.
>
> 업데이트 관리는 지원되는 배포, 특히 릴리스된 [OVAL](https://oval.mitre.org/)(Open Vulnerability and Assessment Language) 파일에서 게시한 데이터를 사용합니다. 이러한 국가별 클라우드에서는 인터넷 액세스가 제한되므로 업데이트 관리 파일에 액세스할 수 없습니다.

Linux의 경우 클라우드의 데이터 보강으로 인해 평가 데이터가 표시되면서 업데이트 관리가 클라우드의 분류 **보안** 및 **기타** 에서 중요 업데이트와 보안 업데이트를 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS에서는 RTM 버전에서 이 정보를 사용할 수 없습니다. CentOS 머신이 다음 명령에 대해 보안 데이터를 반환하도록 구성된 경우 업데이트 관리에서는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재는 CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드가 없습니다. 따라서 해당 기능을 직접 사용하도록 설정했을 수 있는 고객에게만 제한된 지원이 제공됩니다.

Red Hat Enterprise 버전 6의 업데이트를 분류하려면 yum-보안 플러그인을 설치해야 합니다. Red Hat Enterprise Linux 7에서는 플러그인이 이미 yum 자체에 포함되어 있으므로 아무것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 항목](https://access.redhat.com/solutions/10021)을 참조하세요.

Linux 머신에서 실행할 업데이트를 예약하는 경우, 예를 들어 **보안** 분류와 일치하는 업데이트만 설치하도록 구성되었다면 설치된 업데이트는 해당 분류와 일치하는 업데이트의 하위 집합이거나 이와 다를 수 있습니다. Linux 머신에 대해 보류 중인 OS 업데이트의 평가를 수행하는 경우 업데이트 관리에서 분류를 위해 Linux 배포판 공급 업체에서 제공하는 [OVAL](https://oval.mitre.org/)(Open Vulnerability and Assessment Language) 파일을 사용합니다.

분류는 보안 문제나 취약성을 해결하는 업데이트를 포함하는 OVAL 파일을 기반으로 Linux 업데이트의 경우 **보안** 또는 **기타** 방식으로 수행됩니다. 하지만 업데이트 일정을 실행하면 해당 패키지 관리자(예: YUM, APT 또는 ZYPPER)를 사용하여 설치하기 위해 Linux 머신에서 실행됩니다. Linux 배포판 패키지 관리자는 업데이트를 분류하는 다른 메커니즘을 사용할 수 있습니다. 이 경우 업데이트 관리를 사용하여 OVAL 파일에서 가져온 것과 다를 수 있습니다. 머신을 수동으로 확인하고 패키지 관리자의 보안 관련 업데이트가 무엇인지 이해하려면 [Linux 업데이트 배포 문제 해결](../troubleshoot/update-management.md#updates-linux-installed-different)을 참조하세요.

>[!NOTE]
> 업데이트 분류별로 업데이트를 배포하면 업데이트 관리에서 지원하는 Linux 배포판에서 제대로 작동하지 않을 수 있습니다. 이는 OVAL 파일의 명명 스키마로 식별된 문제의 결과이며, 이로 인해 업데이트 관리가 필터링 규칙에 따라 분류를 적절하게 일치시킬 수 없습니다. 보안 업데이트 평가에 사용되는 논리가 다르기 때문에 배포 중에 적용되는 보안 업데이트와 결과가 다를 수 있습니다. 분류가 **중요** 및 **보안** 으로 설정된 경우 업데이트 배포가 예상대로 작동합니다. 평가 중 *업데이트 분류* 만 영향을 받습니다.
>
> Windows Server 컴퓨터용 업데이트 관리 영향을 받지 않으며 업데이트 분류 및 배포는 변경되지 않습니다.

## <a name="integrate-update-management-with-configuration-manager"></a>Configuration Manager와 업데이트 관리 통합

PC, 서버 및 모바일 디바이스를 관리하기 위해 Microsoft Endpoint Configuration Manager에 투자한 고객은 Configuration Manager의 이점과 성숙도에 의존하여 소프트웨어 업데이트를 관리합니다. Configuration Manager에 업데이트 관리를 통합하는 방법을 알아보려면 [Windows Endpoint Configuration Manager와 업데이트 관리 통합](mecmintegration.md)을 참조하세요.

## <a name="third-party-updates-on-windows"></a>Windows의 타사 업데이트

업데이트 관리는 로컬에 구성된 업데이트 리포지토리를 사용하여 지원되는 Windows 시스템(WSUS 또는 Windows 업데이트)을 업데이트합니다. [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher)와 같은 도구를 사용하면 WSUS를 통해 사용자 지정 업데이트를 가져오고 게시할 수 있습니다. 이 시나리오에서는 업데이트 관리에서 타사 소프트웨어를 통해 Configuration Manager를 업데이트 리포지토리로 사용하는 머신을 업데이트할 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](/configmgr/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 업데이트 관리를 사용하도록 설정하고 사용하기 전에 먼저 [업데이트 관리 배포 계획](plan-deployment.md)을 검토하세요.

* [Azure Automation 자주 묻는 질문](../automation-faq.md)에서 업데이트 관리에 대한 일반적인 질문을 살펴보세요.