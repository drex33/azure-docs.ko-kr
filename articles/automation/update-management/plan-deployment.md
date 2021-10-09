---
title: Azure Automation 업데이트 관리 배포 계획
description: 이 문서에서는 Azure Automation 업데이트 관리 배포를 준비하기 위해 내려야 할 고려 사항 및 결정에 대해 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 09/28/2021
ms.topic: conceptual
ms.openlocfilehash: fcf7235ae071dece392a11bacbf9f750569380ae
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715301"
---
# <a name="plan-your-update-management-deployment"></a>업데이트 관리 배포 계획

## <a name="step-1---automation-account"></a>1단계 - Automation 계정

업데이트 관리는 Azure Automation 기능이므로 Automation 계정이 필요합니다. 구독에서 기존 Automation 계정을 사용하거나 다른 Automation 기능 없이 업데이트 관리 전용으로 새 계정을 만들 수 있습니다.

## <a name="step-2---azure-monitor-logs"></a>2단계 - Azure Monitor 로그

업데이트 관리는 관리형 머신에서 수집된 평가 및 업데이트 상태 로그 데이터를 저장하기 위해 Azure Monitor의 Log Analytics 작업 영역에 따라 달라집니다. Log Analytics와 통합하면 Azure Monitor에서 자세한 분석 및 경고를 수행할 수도 있습니다. 구독에서 기존 작업 영역을 사용하거나 업데이트 관리 전용으로 새 작업 영역을 만들 수 있습니다.

Azure Monitor 로그 및 Log Analytics 작업 영역을 처음 사용하는 경우 [Log Analytics 작업 영역 디자인](../../azure-monitor/logs/design-logs-deployment.md) 배포 가이드를 검토해야 합니다. 

## <a name="step-3---supported-operating-systems"></a>3단계 - 지원되는 운영 체제

업데이트 관리는 특정 버전의 Windows Server 및 Linux 운영 체제를 지원합니다. 업데이트 관리를 사용하도록 설정하기 전에 대상 머신이 [운영 체제 요구 사항](operating-system-requirements.md)을 충족하는지 확인합니다. 

## <a name="step-4---log-analytics-agent"></a>4단계 - Log Analytics 에이전트

Windows 및 Linux용 [Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)는 업데이트 관리를 지원해야 합니다. 에이전트는 데이터 수집 및 Automation 시스템 Hybrid Runbook Worker 역할 모두에 사용되어 머신에서 평가를 관리하고 배포를 업데이트하는 데 사용되는 업데이트 관리 Runbook을 지원합니다. 

Azure VM에서 Log Analytics 에이전트가 아직 설치되지 않은 경우 VM에 업데이트 관리를 사용하도록 설정하면 [Windows](../../virtual-machines/extensions/oms-windows.md) 또는 [Linux](../../virtual-machines/extensions/oms-linux.md)에 대해 Log Analytics VM 확장을 사용하여 자동으로 설치됩니다. 에이전트는 Automation 계정 업데이트 관리에 연결된 Log Analytics 작업 영역에 대한 보고가 활성화되도록 구성됩니다.

비 Azure VM 또는 서버에는 Windows 또는 Linux용 Log Analytics 에이전트가 설치되어 있고, 연결된 작업 영역에 대한 보고가 있어야 합니다. 먼저 컴퓨터를 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 연결하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치한 다음, Azure Policy를 사용하여 [Linux 또는 Windows Azure Arc 컴퓨터에 Log Analytics 에이전트 배포](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 할당하는 것이 좋습니다. 또는 [VM 인사이트](../../azure-monitor/vm/vminsights-overview.md)를 사용하여 머신을 모니터링하려는 경우 [VM용 Azure Monitor 사용](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 대신 사용합니다.

현재 Operations Manager에서 관리하는 컴퓨터를 사용하도록 설정하는 경우 새 에이전트가 필요하지 않습니다. 관리 그룹을 Log Analytics 작업 영역에 연결하면 작업 영역 정보가 에이전트 구성에 추가됩니다.

머신 하나를 둘 이상의 Log Analytics 작업 영역에서 업데이트 관리에 등록(멀티 호밍)하는 것은 지원되지 않습니다.

## <a name="step-5---network-planning"></a><a name="ports"></a> 5단계 - 네트워크 계획

업데이트 관리를 지원하도록 네트워크를 준비하려면 일부 인프라 구성 요소를 구성해야 할 수 있습니다. 예를 들어 방화벽 포트를 열어 업데이트 관리 및 Azure Monitor에 사용되는 통신을 전달합니다.

포트, URL 및 Hybrid Runbook Worker 역할을 포함한 업데이트 관리에 필요한 기타 네트워킹 세부 정보에 대한 자세한 내용은 [Azure Automation 네트워크 구성](../automation-network-configuration.md)을 검토합니다. Azure VM에서 Automation 서비스에 안전하게 비공개로 연결하려면 [Azure Private Link 사용](../how-to/private-link-security.md)을 검토하세요. 

Windows 머신의 경우 Windows 업데이트 에이전트에 필요한 모든 엔드포인트로도 트래픽을 허용해야 합니다. 필요한 엔드포인트의 업데이트된 목록은 [HTTP/프록시 관련 문제](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에서 확인할 수 있습니다. 로컬 [WSUS(Windows Server Update Services)](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) 배포가 있는 경우 [WSUS 키](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)에 지정된 서버로도 트래픽을 허용해야 합니다.

Red Hat Linux 머신의 경우 [RHUI 콘텐츠 배달 서버의 IP](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)에서 필요한 엔드포인트를 확인하세요. 기타 Linux 배포판의 경우 공급자의 설명서를 확인하세요.

IT 보안 정책에서 네트워크에 있는 머신의 인터넷 연결을 허용하지 않는 경우 [Log Analytics 게이트웨이](../../azure-monitor/agents/gateway.md)를 설정한 다음 게이트웨이를 통해 Azure Automation 및 Azure Monitor에 연결하도록 머신을 구성할 수 있습니다.

## <a name="step-6---permissions"></a>6단계 - 권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 내용은 [역할 기반 액세스 - 업데이트 관리](../automation-role-based-access-control.md#update-management-permissions)를 참조하세요.

## <a name="step-7---windows-update-agent"></a>7단계 - 업데이트 에이전트 Windows

Azure Automation 업데이트 관리 Windows 업데이트 에이전트를 사용하여 Windows 업데이트를 다운로드하고 설치합니다. 머신의 WUA(Windows 업데이트 에이전트)에서 WSUS(Windows Server Update Services) 또는 Microsoft 업데이트에 연결하는 데 사용하는 특정 그룹 정책 설정이 있습니다. 이러한 그룹 정책 설정은 소프트웨어 업데이트 호환성을 성공적으로 검사하고 소프트웨어 업데이트를 자동으로 업데이트하는 데도 사용됩니다. 권장 사항을 검토하려면 [업데이트 관리에 대한 Windows 업데이트 설정 구성](configure-wuagent.md)을 참조하세요.

## <a name="step-8---linux-repository"></a>8단계 - Linux 리포지토리

Azure Marketplace에서 제공되는 주문형 RHEL(Red Hat Enterprise Linux) 이미지에서 만든 VM은 Azure에 배포된 RHUI(Red Hat Update Infrastructure)에 액세스하도록 등록됩니다. 다른 모든 Linux 배포판은 해당 배포판에서 지원되는 방법을 사용하여 배포판의 온라인 파일 리포지토리에서 업데이트되어야 합니다.

Red Hat Enterprise 버전 6의 업데이트를 분류하려면 yum-보안 플러그인을 설치해야 합니다. Red Hat Enterprise Linux 7에서는 플러그인이 이미 yum 자체에 포함되어 있으므로 아무것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 항목](https://access.redhat.com/solutions/10021)을 참조하세요.

## <a name="step-9---plan-deployment-targets"></a>9단계 - 배포 대상 계획

업데이트 관리를 사용하면 Azure 또는 비 Azure 머신을 나타내는 동적 그룹에 대한 업데이트를 대상으로 지정할 수 있으므로 특정 머신이 항상 가장 편리한 시간에 올바른 업데이트를 받을 수 있습니다. 동적 그룹은 배포 시 확인되며 다음 조건을 기반으로 합니다.

* Subscription
* 리소스 그룹
* 위치
* 태그들 

Azure가 아닌 머신에 대한 동적 그룹은 [컴퓨터 그룹](../../azure-monitor/logs/computer-groups.md)이라고도 하는 저장된 검색을 사용합니다. 머신 그룹으로 범위가 지정되는 업데이트 배포는 특정 Azure VM이 아닌 업데이트 관리 **배포 일정** 옵션의 Automation 계정에서만 볼 수 있습니다.

또는 선택한 Azure VM에 대해서만 업데이트를 관리할 수 있습니다. 특정 머신으로 범위가 지정되는 업데이트 배포는 업데이트 관리 **배포 일정** 옵션의 머신과 Automation 계정 모두에서 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

업데이트 관리를 사용하도록 설정하고 다음 방법 중 하나를 사용하여 관리될 머신을 선택합니다.

- 신규 또는 기존 Automation 계정과 구독의 Azure Monitor Log Analytics 작업 영역으로 업데이트 관리를 배포하기 위해 Azure [Resource Manager 템플릿](enable-from-template.md)을 사용합니다. 템플릿은 관리해야 하는 머신의 범위를 구성하지는 않습니다. 이 작업은 템플릿을 사용한 후 별도의 단계로 수행됩니다.

- Azure Arc 지원 서버를 포함하여 하나 이상의 Azure 및 비 Azure 머신에 대한 [Automation 계정에서.](enable-from-automation-account.md)

- **Enable-AutomationSolution** [Runbook](enable-from-runbook.md)을 사용하여 Azure VM 온보딩을 자동화합니다.

- Azure Portal의 **가상 머신** 페이지에서 [선택한 Azure VM](enable-from-vm.md)의 경우. 이 시나리오는 Linux VM과 Windows VM에서 지원됩니다.

- [여러 Azure VM](enable-from-portal.md)의 경우 Azure Portal의 **가상 머신** 페이지에서 해당 VM을 선택합니다.
