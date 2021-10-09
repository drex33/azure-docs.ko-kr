---
title: Azure Automation 업데이트 관리 지원 클라이언트
description: 이 문서에서는 Azure Automation 업데이트 관리를 통해 지원되는 Windows 및 Linux 운영 체제에 대해 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: a470a3c8d4ab9ac4f390f7b18133f65f44fed096
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711635"
---
# <a name="operating-systems-supported-by-update-management"></a>업데이트 관리에서 지원하는 운영 체제

이 문서에서는 지원되는 Windows 및 Linux 운영 체제와 업데이트 관리에서 관리하는 컴퓨터 또는 서버의 시스템 요구 사항에 대해 자세히 설명합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에는 업데이트 평가 및 패치를 지원하는 운영 체제가 나와 있습니다. 패치를 적용하려면 시스템 Hybrid Runbook Worker가 필요하며, 이는 업데이트 관리에서 관리를 위해 가상 머신 또는 서버를 사용하도록 설정할 때 자동으로 설치됩니다. Hybrid Runbook Worker 시스템 요구 사항에 대한 자세한 내용은 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md#prerequisites) 및 [Linux Hybrid Runbook Worker 배포](../automation-linux-hrw-install.md#prerequisites)를 참조하세요.

모든 운영 체제는 x64로 간주됩니다. x86은 어떤 운영 체제에서도 지원되지 않습니다.

> [!NOTE]
> Linux 머신의 업데이트 평가는 Automation 계정 및 Log Analytics 작업 영역 [매핑 테이블](../how-to/region-mappings.md#supported-mappings)에 안내된 대로 특정 지역에서만 지원됩니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2019(Server Core를 포함하는 데이터 센터/표준)<br><br>Windows Server 2016(Server Core를 제외한 데이터 센터/표준)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2(RTM 및 SP1 Standard)| 업데이트 관리는 해당 운영 체제에 대한 평가 및 패치를 지원합니다. Windows Server 2008 R2에서는 [Hybrid Runbook Worker](../automation-windows-hrw-install.md)가 지원됩니다. |
|CentOS 6, 7, 8       | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다. 분류 기반 패치를 사용하려면 `yum`에서 CentOS가 RTM 릴리스에 없는 보안 데이터를 반환해야 합니다. CentOS의 분류 기반 패치에 대한 자세한 내용은 [Linux의 업데이트 분류](view-update-assessments.md#linux)를 참조하세요.          |
|Oracle Linux 6.x, 7.x, 8x | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.        |
|Red Hat Enterprise 6, 7, 8      | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.        |
|SUSE Linux Enterprise Server 12, 15, 15.1, 15.2      | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.     |
|Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS, 20.04 LTS       |Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.         |

> [!NOTE]
> 업데이트 관리는 Azure 가상 머신 확장 집합의 모든 인스턴스에서 업데이트 관리를 안전하게 자동화하는 기능은 지원하지 않습니다. [자동 OS 이미지 업그레이드](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)는 확장 집합에서 OS 이미지 업그레이드를 관리하는 데 권장되는 방법입니다.

## <a name="unsupported-operating-systems"></a>지원되지 않는 운영 체제

다음 표에서는 업데이트 관리에서 지원하지 않는 운영 체제를 보여 줍니다.

|운영 체제  |메모  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.<br> Azure WVD(Windows Virtual Desktop)의 경우<br> 업데이트를 관리하려면 Windows 10 클라이언트 머신 패치 관리를 위한 [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md)가 권장되는 방법입니다. |
|Windows Server 2016 Nano Server     | 지원되지 않습니다.       |
|Azure Kubernetes Service 노드 | 지원되지 않습니다. [AKS(Azure Kubernetes Service)에서 Linux 노드에 보안 및 커널 업데이트 적용](../../aks/node-updates-kured.md)에서 설명하는 패치 프로세스를 사용합니다.|

## <a name="system-requirements"></a>시스템 요구 사항

아래에서는 운영 체제별 요구 사항에 대해 설명합니다. 추가 지침이 필요하면 [네트워크 계획](plan-deployment.md#ports)을 참조하세요. TLS 1.2에 대한 요구 사항을 이해하려면 [Azure Automation용 TLS 1.2](../automation-managing-data.md#tls-12-for-azure-automation)를 참조하세요.

### <a name="windows"></a>Windows

소프트웨어 요구 사항:

- .NET Framework 4.6 이상이 필요합니다. ([.NET Framework을 다운로드하세요](/dotnet/framework/install/guide-for-developers).
- Windows PowerShell 5.1이 필요합니다([Windows Management Framework 5.1을 다운로드하세요](https://www.microsoft.com/download/details.aspx?id=54616)).
- 업데이트 관리 기능은 시스템 Hybrid Runbook Worker 역할에 따라 다르며 해당 [시스템 요구 사항](../automation-windows-hrw-install.md#prerequisites)을 확인해야 합니다.

Windows 업데이트 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성해야 하며 그렇지 않으면 Microsoft 업데이트에 액세스해야 합니다. 하이브리드 머신의 경우 먼저 머신을 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 연결하여 Windows용 Log Analytics 에이전트를 설치한 다음 Azure Policy를 사용하여 [Windows Azure Arc 머신에 Log Analytics 에이전트 배포](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 할당하는 것이 좋습니다. 또는 VM 인사이트로 컴퓨터를 모니터링하려는 경우 [VM 인사이트 사용 ](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 대신 사용합니다.

업데이트 관리를 Microsoft Endpoint Configuration Manager와 함께 사용할 수 있습니다. 통합 시나리오에 대한 자세한 내용은 To learn more about integration scenarios, see [Windows Endpoint Configuration Manager와 업데이트 관리 통합](mecmintegration.md)을 참조하세요. Configuration Manager 환경에서 사이트에 의해 관리되는 Windows 서버에는 [Windows용 Log Analytics 에이전트](../../azure-monitor/agents/agent-windows.md)가 필요합니다.

기본적으로 Azure Marketplace에서 배포된 VM은 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다. 이 동작은 작업 영역에 Windows VM을 추가해도 달라지지 않습니다. 업데이트 관리를 사용하여 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

> [!NOTE]
> 머신 재부팅이 시스템이 아니라 사용자에 의해서만 수행될 수 있도록 그룹 정책을 수정할 수 있습니다. 관리형 머신은 업데이트 관리에 사용자의 수동 개입 없이 머신을 재부팅할 권한이 없는 경우 멈출 수 있습니다. 자세한 내용은 [자동 업데이트를 위한 그룹 정책 설정 구성](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)을 참조하세요.

### <a name="linux"></a>Linux

소프트웨어 요구 사항:

- 머신에 업데이트 리포지토리에 대한 프라이빗 또는 퍼블릭 액세스 권한이 필요합니다.
- 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다.
- 업데이트 관리 기능은 시스템 Hybrid Runbook Worker 역할에 따라 다르며 해당 [시스템 요구 사항](../automation-linux-hrw-install.md#prerequisites)을 확인해야 합니다. 업데이트 관리는 Automation Runbook을 사용하여 컴퓨터 평가 및 업데이트를 시작하므로 지원되는 Linux 배포판에 [필요한 Python 버전](../automation-linux-hrw-install.md#supported-runbook-types)을 검토하세요.

> [!NOTE]
> Linux 머신의 업데이트 평가는 특정 지역에서만 지원됩니다. Automation 계정 및 Log Analytics 작업 영역 [매핑 테이블](../how-to/region-mappings.md#supported-mappings)을 참조하세요.

하이브리드 머신의 경우 먼저 머신을 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 연결하여 Linux용 Log Analytics 에이전트를 설치한 다음 Azure Policy를 사용하여 [ Azure Arc 머신에 Log Analytics 에이전트 배포](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 할당하는 것이 좋습니다. VM용 Azure Monitor를 사용하여 머신을 모니터링하려는 경우에는 대신 [VM용 Azure Monitor 사용](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 사용합니다.

## <a name="next-steps"></a>다음 단계

업데이트 관리를 사용하도록 설정하고 사용하기 전에 먼저 [업데이트 관리 배포 계획](plan-deployment.md)을 검토하세요.
