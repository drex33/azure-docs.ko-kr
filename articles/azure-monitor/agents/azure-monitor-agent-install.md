---
title: Azure Monitor 에이전트 설치
description: Azure 가상 머신과 Azure Arc 사용 서버에서 AMA(Azure Monitor 에이전트)를 설치하는 옵션.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/19/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a5390b2bd4888c0fe43fb0b0d94b07563d1a68
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434901"
---
# <a name="install-the-azure-monitor-agent"></a>Azure Monitor 에이전트 설치
이 문서에서는 Azure 가상 머신과 Azure Arc 사용 서버 모두에 [Azure Monitor 에이전트](azure-monitor-agent-overview.md)를 설치하는 데 사용할 수 있는 다양한 옵션을 제공하며, 에이전트가 수집해야 하는 데이터를 정의하는 [데이터 수집 규칙을 사용하여 연결](data-collection-rule-azure-monitor-agent.md)을 만드는 옵션도 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure Monitor 에이전트를 설치하기 전에 다음 필수 구성 요소가 필요합니다.

- Azure 가상 머신에서 [관리형 시스템 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)를 사용하도록 설정해야 합니다. Azure Arc 사용 서버에는 필요하지 않습니다. [Azure Portal을 사용하여 데이터 수집 규칙을 만들고 할당](#install-with-azure-portal)하는 프로세스의 일부로 에이전트가 설치된 경우 시스템 ID를 사용하도록 자동으로 설정됩니다.
- 가상 머신의 가상 네트워크에서 [AzureResourceManager 서비스 태그](../../virtual-network/service-tags-overview.md)를 사용으로 설정해야 합니다.
- 가상 머신에는 다음 HTTPS 엔드포인트에 대한 액세스 권한이 있어야 합니다.
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> Azure Monitor 에이전트는 현재 프라이빗 링크를 지원하지 않습니다.

## <a name="virtual-machine-extension-details"></a>가상 머신 확장 세부 정보
Azure Monitor 에이전트는 다음 테이블의 세부 정보를 사용하여 [Azure VM 확장](../../virtual-machines/extensions/overview.md)으로 구현됩니다. 이 문서에 설명된 방법을 포함하여 가상 머신 확장을 설치하는 방법 중 하나를 사용하여 설치할 수 있습니다.

| 속성 | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| 형식      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>확장 버전
미리 보기 버전을 사용하는 대신 GA+ 버전으로 업데이트하는 것을 적극 권장합니다.

| 출시 날짜 | 릴리스 정보 | Windows | Linux |
|:---|:---|:---|:---|:---|
| 2021년 6월 | 일반 공급 지원을 발표했습니다. <ul><li>이제 메트릭 대상을 제외한 모든 기능 공급됨</li><li>프로덕션 품질, 보안 및 규정 준수</li><li>모든 공용 지역에서 사용 가능</li><li>더 높은 EPS의 성능 및 확장성 향상</li></ul> [자세한 정보](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| 2021년 7월 | <ul><li>직접 프록시 지원</li><li>Log Analytics 게이트웨이 지원</li></ul> [자세한 정보](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| 2021년 8월 | Azure Monitor 메트릭을 유일한 대상으로 허용하는 문제 해결 | 1.1.2.0 | 1.10.9.0(1.10.7.0 사용 금지) |


## <a name="install-with-azure-portal"></a>Azure Portal을 사용하여 설치
Azure Portal을 사용하여 Azure Monitor 에이전트를 설치하려면 Azure Portal에서 [데이터 수집 규칙 만들기](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) 프로세스를 따릅니다. 그러면 데이터 수집 규칙을 하나 이상의 Azure 가상 머신이나 Azure Arc 사용 서버와 연결할 수 있습니다. 에이전트는 아직 설치된 에이전트가 없는 가상 머신에 설치됩니다.


## <a name="install-with-resource-manager-template"></a>Resource Manager 템플릿으로 설치
Resource Manager 템플릿을 사용하여 Azure 가상 머신 및 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치하고 데이터 수집 규칙과의 연결을 만들 수 있습니다. 연결을 만들기 전에 데이터 수집 규칙을 만들어야 합니다.

다음에서 에이전트를 설치하고 연결을 만들기 위한 샘플 템플릿을 가져옵니다. 

- [Azure Monitor 에이전트(Azure 및 Azure Arc)를 설치하기 위한 템플릿](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [데이터 수집 규칙과의 연결을 만들기 위한 템플릿](./resource-manager-data-collection-rules.md)

다음 명령과 같이 [Resource Manager 템플릿의 배포 방법](../../azure-resource-manager/templates/deploy-powershell.md)을 사용하여 템플릿을 설치합니다.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>PowerShell을 사용하여 설치
가상 머신 확장을 추가하기 위한 PowerShell 명령을 사용하여 Azure 가상 머신과 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치할 수 있습니다. 

### <a name="azure-virtual-machines"></a>Azure 가상 머신
다음 PowerShell 명령을 사용하여 Azure 가상 머신에 Azure Monitor 에이전트를 설치합니다.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버
다음 PowerShell 명령을 사용하여 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치합니다.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="azure-cli"></a>Azure CLI
가상 머신 확장을 추가하기 위한 Azure CLI 명령을 사용하여 Azure 가상 머신과 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치할 수 있습니다. 

### <a name="azure-virtual-machines"></a>Azure 가상 머신
다음 CLI 명령을 사용하여 Azure 가상 머신에 Azure Monitor 에이전트를 설치합니다.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버
다음 CLI 명령을 사용하여 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치합니다.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="next-steps"></a>다음 단계

- 에이전트에서 데이터를 수집하고 Azure Monitor로 보내기 위한 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md).
