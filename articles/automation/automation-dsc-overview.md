---
title: Azure Automation 상태 구성 개요
description: 이 문서에서는 Azure Automation 상태 구성에 대한 개요를 제공합니다.
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/17/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 898fc7b2574040ef6095178eae9cd8e7ee747ef9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352902"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation 상태 구성 개요

Azure Automation 상태 구성은 Azure 구성 관리 서비스로, 모든 클라우드 또는 온-프레미스 데이터 센터의 노드에 대한 PowerShell DSC(Desired State Configuratioin) [구성](/powershell/scripting/dsc/configurations/configurations)을 작성, 관리 및 컴파일합니다. 또한 이 서비스는 [DSC 리소스](/powershell/scripting/dsc/resources/resources)를 가져오고 모든 클라우드에서 대상 노드에 구성을 할당합니다. **Configuration Management** 에서 **DSC(State Configuration)** 를 선택하여 Azure Portal에서 Azure Automation 상태 구성에 액세스할 수 있습니다.

> [!NOTE]
> Automation State Configuration 사용하도록 설정하기 전에 최신 버전의 DSC를 미리 보기에서 사용할 수 있으며 [게스트 구성이라는](../governance/policy/concepts/guest-configuration.md)Azure Policy 기능으로 관리됩니다. 게스트 구성 서비스는 DSC 확장, Azure Automation State Configuration 기능 및 고객 피드백에서 가장 일반적으로 요청된 기능을 결합합니다. 게스트 구성에는 Arc 지원 서버를 통한 하이브리드 머신 지원도 [포함됩니다.](../azure-arc/servers/overview.md)

Azure Automation 상태 구성을 다양한 머신의 관리에 사용할 수 있습니다.

- Azure 가상 머신
- Azure 가상 머신(기본)
- 온-프레미스나 Azure(AWS EC2 인스턴스 포함) 이외의 클라우드에 있는 실제/가상 Windows 머신
- 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

클라우드에서 머신 구성을 관리할 수 없는 경우 Azure Automation 상태 구성을 보고서 전용 엔드포인트로 사용할 수 있습니다. 이 기능을 사용하면 DSC를 통해 (푸시) 구성을 설정하고 Azure Automation에서 보고 세부 정보를 볼 수 있습니다.

> [!NOTE]
> Azure Automation 상태 구성을 사용한 Azure VM 관리는 설치된 VM 필요한 상태 구성 확장 버전이 2.70보다 높은 경우 추가 비용 없이 포함됩니다. 자세한 내용은 [**Automation 가격 책정 페이지**](https://azure.microsoft.com/pricing/details/automation/)를 참조하세요.

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 사용하는 이유

Azure Automation 상태 구성은 Azure 외부에서 DSC를 사용하는 것에 비해 몇 가지 장점이 있습니다. 이 서비스를 이용하면 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 사용하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정하는 필요 상태로 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

Azure Automation runbook이 PowerShell 스크립팅에 대한 것이라면 Azure Automation 상태 구성 서비스는 DSC에 대한 것입니다. 즉, Azure Automation에서 PowerShell 스크립트 관리를 지원하는 동일한 방법으로 DSC 구성 관리를 지원합니다.

### <a name="built-in-pull-server"></a>기본 제공 끌어오기 서버

Azure Automation 상태 구성은 [Windows 기능 DSC 서비스](/powershell/scripting/dsc/pull-server/pullserver)와 유사한 DSC 끌어오기 서버를 제공합니다. 대상 노드는 구성을 자동으로 수신하고 원하는 상태에 따르며 규정 준수를 보고할 수 있습니다. Azure Automation에 있는 기본 제공 끌어오기 서버는 자체 끌어오기 서버를 설정하고 유지 관리할 필요가 없습니다. Azure Automation은 클라우드 또는 온-프레미스에서 가상 또는 실제 Windows 또는 Linux 컴퓨터를 대상으로 할 수 있습니다.

### <a name="management-of-all-your-dsc-artifacts"></a>모든 DSC 아티팩트 관리

Azure Automation 상태 구성은 PowerShell 스크립팅에 제공하는 것처럼 동일한 관리 계층을 [PowerShell DSC(Desired State Configuration)](/powershell/scripting/dsc/overview/overview)에 제공합니다. Azure Portal 또는 PowerShell에서 DSC 구성, 리소스 및 대상 노드를 모두 관리할 수 있습니다.

![Azure Automation 페이지의 스크린샷](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>보고 데이터를 Azure Monitor 로그로 가져오기

Azure Automation 상태 구성으로 관리되는 노드는 상세한 보고 상태 데이터를 기본 제공 끌어오기 서버에 보냅니다. 이 데이터를 Log Analytics 작업 영역으로 보내려면 Azure Automation 상태 구성을 구성할 수 있습니다. [Azure Monitor 로그에 데이터를 보고하는 Azure Automation 상태 구성 전달](automation-dsc-diagnostics.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Automation 상태 구성을 사용하는 경우 이 섹션의 요구 사항을 고려합니다.

### <a name="operating-system-requirements"></a>운영 체제 요구 사항

Windows를 실행하는 노드의 경우 지원되는 버전은 다음과 같습니다.

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- 윈도우 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) 독립 실행형 제품 SKU에는 DSC 구현이 포함되어 있지 않습니다. 따라서 PowerShell DSC 또는 Azure Automation 상태 구성으로 관리할 수 없습니다.

Linux를 실행하는 노드의 경우 DSC Linux 확장은 [PowerShell DSC 문서](/powershell/scripting/dsc/getting-started/lnxgettingstarted)에 나열된 모든 Linux 배포판을 지원합니다.

### <a name="dsc-requirements"></a>DSC 요구 사항

Azure에서 실행되는 모든 Windows 노드의 경우 컴퓨터를 사용하도록 설정하면 [WMF 5.1](/powershell/scripting/wmf/setup/install-configure)이 설치됩니다. Windows Server 2012 및 Windows 7을 실행하는 노드의 경우 [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)이 사용됩니다.

Azure에서 실행되는 모든 Linux 노드의 경우 컴퓨터를 사용하도록 설정하면 [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)가 설치됩니다.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>개인 네트워크 구성

개인 네트워크의 노드에 필요한 포트, URL 및 기타 네트워크 세부 정보에 대한 자세한 내용은 [Azure Automation 네트워크 구성](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration)을 확인하세요.

#### <a name="proxy-support"></a>프록시 지원

DSC 에이전트에 대한 프록시 지원은 Windows 버전 1809 이상에서 사용할 수 있습니다. 이 옵션은 노드를 등록하는 데 사용된 [메타 구성 스크립트](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)의 `ProxyURL` 및 `ProxyCredential` 속성값을 설정하여 사용할 수 있습니다.

>[!NOTE]
>Azure Automation 상태 구성은 이전 버전의 Windows에 대한 DSC 프록시 지원을 제공하지 않습니다.

Linux 노드의 경우 DSC 에이전트는 프록시를 지원하고 `http_proxy` 변수를 사용하여 URL을 결정합니다. 프록시 지원에 대한 자세한 내용은 [DSC 메타 구성 생성](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)을 참조하세요.

#### <a name="dns-records-per-region"></a>지역별 DNS 레코드

예외를 정의할 때 [지역별 DNS 레코드](how-to/automation-region-dns-records.md) 표에 나열된 주소를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation State Configuration 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- 노드를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성 사용](automation-dsc-onboarding.md)을 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation State Configuration에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation State Configuration을 사용하는 예제는 [Chocolatey를 사용한 지속적인 배포 설정](automation-dsc-cd-chocolatey.md)을 참조하세요.
- 가격 책정 정보는 [Azure Automation State Configuration 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
