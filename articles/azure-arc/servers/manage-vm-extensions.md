---
title: Azure Arc 사용 서버로 VM 확장 관리
description: Azure Arc 사용 서버에서는 Azure가 아닌 VM을 사용하여 배포 후 구성 및 자동화 작업을 제공하는 가상 머신 확장 배포 기능을 관리할 수 있습니다.
ms.date: 08/24/2021
ms.topic: conceptual
ms.openlocfilehash: bc19255d7e234b3ca4ada1bc2f9356dc52b3e4dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807258"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Azure Arc 사용 서버로 가상 머신 확장 관리

Azure VM(가상 머신) 확장은 Azure VM에 배포 후 구성 및 자동화 작업을 제공하는 작은 애플리케이션입니다. 예를 들어 가상 머신에서 소프트웨어 설치, 바이러스 백신 보호 또는 내부 스크립트 실행을 요구하는 경우 VM 확장을 사용할 수 있습니다.

Azure Arc 사용 서버를 사용하면 Azure VM 확장을 비 Azure Windows 및 Linux VM에 배포, 제거 및 업데이트하여 해당 수명 주기 동안 하이브리드 머신의 관리 작업을 간소화할 수 있습니다. VM 확장은 하이브리드 머신 또는 Arc 사용 서버에 의해 관리되는 서버에서 다음 방법을 사용하여 관리할 수 있습니다.

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager 템플릿](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc 사용 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VM에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

> [!NOTE]
> 현재 Azure Portal에서만 확장을 업데이트할 수 있습니다. 현재 Azure CLI, Azure PowerShell에서 이 작업을 수행하거나 Azure Resource Manager 템플릿을 사용하는 것은 지원되지 않습니다.

## <a name="key-benefits"></a>주요 이점

Azure Arc 사용 서버 VM 확장 지원에서 제공하는 주요 이점은 다음과 같습니다.

- Log Analytics 에이전트 VM 확장을 사용하도록 설정하여 [Azure Monitor의 로그](../../azure-monitor/logs/data-platform-logs.md)로 분석할 로그 데이터를 수집합니다. Log Analytics를 사용하면 다양한 종류의 원본에서 로그 데이터에 대해 복잡한 분석을 수행하는 경우에 유용합니다.

- [VM 인사이트](../../azure-monitor/vm/vminsights-overview.md)를 사용하여 Windows 및 Linux VM의 성능을 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. 이는 Log Analytics 에이전트와 Dependency Agent VM 확장을 모두 사용 하도록 설정하여 수행됩니다.

- 사용자 지정 스크립트 확장을 이용해 하이브리드 연결 컴퓨터에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성 또는 관리 작업에 유용합니다.

- [Azure Key Vault](../../key-vault/general/overview.md)에 저장된 인증서를 자동으로 새로 고칩니다.

## <a name="availability"></a>가용성

VM 확장 기능은 [지원되는 지역](overview.md#supported-regions) 목록에 한해 사용할 수 있습니다. 이러한 지역 중 하나에서 컴퓨터를 온보딩해야 확인합니다.

## <a name="extensions"></a>확장

이 릴리스의 경우 Windows 및 Linux 컴퓨터에서 다음과 같은 VM 확장을 지원합니다.

Azure Connected Machine 에이전트 패키지 및 확장 에이전트 구성 요소에 대 한 자세한 내용을 알아보려면 [에이전트 개요](agent-overview.md#agent-component-details)를 참조 하세요.

> [!NOTE]
> 최근에 Azure Arc 지원 서버에 대한 DSC VM 확장 지원이 제거되었습니다. 그 대신에 사용자 지정 스크립트 확장을 사용하여 서버 또는 컴퓨터의 배포 후 구성을 관리하는 것이 좋습니다.

Arc 지원 서버는 구성에 영향을 주지 않고 리소스 그룹 또는 다른 Azure 구독 간에 하나 이상의 VM 확장이 설치된 머신을 이동할 수 있도록 지원합니다. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../../active-directory/develop/quickstart-create-new-tenant.md) 내에 있어야 합니다. 이 지원은 Connected Machine 에이전트 버전 **1.8.21197.005** 부터 사용할 수 있습니다. 계속하기 전에 리소스 이동 및 고려 사항에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

### <a name="windows-extensions"></a>Windows 확장

|내선 번호 |Publisher |유형 |추가 정보 |
|----------|----------|-----|-----------------------|
|Azure Defender 통합 취약성 스캐너 |Qualys |WindowsAgent.AzureSecurityCenter |[Azure 및 하이브리드 컴퓨터용 Azure Defender의 통합 취약성 평가 솔루션](../../security-center/deploy-vulnerability-assessment-vm.md)|
|사용자 지정 스크립트 확장 |Microsoft.Compute | CustomScriptExtension |[Windows 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics 에이전트 |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Windows용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-windows.md)|
|VM용 Azure Monitor(인사이트) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Windows용 종속성 에이전트 가상 머신 확장](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 인증서 동기화 | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Windows용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor 에이전트 |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Azure Monitor 에이전트 설치(미리 보기)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux 확장

|내선 번호 |Publisher |유형 |추가 정보 |
|----------|----------|-----|-----------------------|
|Azure Defender 통합 취약성 스캐너 |Qualys |LinuxAgent.AzureSecurityCenter |[Azure 및 하이브리드 컴퓨터용 Azure Defender의 통합 취약성 평가 솔루션](../../security-center/deploy-vulnerability-assessment-vm.md)|
|사용자 지정 스크립트 확장 |Microsoft.Azure.Extensions |CustomScript |[Linux 사용자 지정 스크립트 확장 버전 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics 에이전트 |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Linux용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-linux.md) |
|VM용 Azure Monitor(인사이트) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Linux용 Dependency Agent 가상 머신 확장](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 인증서 동기화 | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Linux용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor 에이전트 |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Azure Monitor 에이전트 설치(미리 보기)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>사전 요구 사항

이 기능은 구독의 다음 Azure 리소스 공급자에 따라 달라집니다.

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

아직 등록하지 않은 경우 [Azure 리소스 공급자 등록](agent-overview.md#register-azure-resource-providers)의 단계를 따릅니다.

네트워크 또는 시스템 요구 사항이 있는지 이해하려면 앞에 나온 표에서 참조된 각 VM 확장 설명서를 검토하십시오. 이렇게 하면 해당 VM 확장을 사용하는 Azure 서비스 또는 기능의 연결 문제를 방지할 수 있습니다.

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 확장

Linux용 Log Analytics 에이전트 VM 확장을 사용하려면 대상 컴퓨터에 Python 2.x가 설치되어 있어야 합니다.

### <a name="azure-key-vault-vm-extension"></a>Azure Key Vault VM 확장 

Key Vault VM 확장은 다음 Linux 운영 체제를 지원하지 않습니다.

- CentOS Linux 7(x64)
- RHEL(Red Hat Enterprise Linux) 7(x64)
- Amazon Linux 2(x64)

Key Vault VM 확장 배포 작업은 다음을 통해서만 지원됩니다.

- Azure CLI
- Azure PowerShell
- Azure Resource Manager 템플릿

확장을 배포하기 전에 다음을 완료해야 합니다.

1. [자격 증명 모음 및 인증서를 만듭니다](../../key-vault/certificates/quick-create-portal.md)(자체 서명 또는 가져오기).

2. 인증서 비밀에 대한 액세스 권한을 Azure Arc 사용 서버에 부여합니다. [RBAC 미리 보기](../../key-vault/general/rbac-guide.md)를 사용하는 경우 Azure Arc 리소스의 이름을 검색하고 **Key Vault 암호 사용자(미리 보기)** 역할에 할당합니다. [Key Vault 액세스 정책](../../key-vault/general/assign-access-policy-portal.md)을 사용하는 경우 Azure Arc 리소스의 시스템 할당 ID에 암호 **Get** 권한을 할당합니다.

### <a name="connected-machine-agent"></a>Connected Machine 에이전트

컴퓨터가 Azure Connected Machine 에이전트에 [지원되는 버전](agent-overview.md#supported-operating-systems)의 Windows 및 Linux 운영 체제와 일치하는지 확인합니다.

Windows 및 Linux에서 이 기능을 통해 지원되는 Connected Machine 에이전트의 최소 버전은 1.0 릴리스입니다.

필요한 에이전트 버전으로 컴퓨터를 업그레이드하려면 [에이전트 업그레이드](manage-agent.md#upgrading-agent)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Portal](manage-vm-extensions-portal.md)또는 [Azure Resource Manager 템플릿에서](manage-vm-extensions-template.md) [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md)을 사용하여 VM 확장을 배포, 관리하고 제거할 수 있습니다.
