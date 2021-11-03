---
title: Azure CLI를 사용하여 VM 확장 사용
description: 이 문서에서는 Azure CLI를 사용하여 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7294285cd6e4fa7c2bbfac859c3fec8dfa3a474
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440415"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure VM 확장 사용

이 문서에서는 Azure CLI를 사용 하 여 Azure Arc 사용 서버에서 지 원하는 VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포, 업그레이드, 업데이트 및 제거 하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포와 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

ConnectedMachine 명령은 Azure CLI의 일부로 제공되지 않습니다. Azure CLI를 사용 하 여 Azure에 연결 하 고 Azure Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하기 전에 ConnectedMachine 확장을 로드 해야 합니다. 이러한 관리 작업은 워크스테이션에서 수행할 수 있으며, Azure Arc 사용 서버에서 실행할 필요가 없습니다.

다음 명령을 실행하여 가져옵니다.

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>확장 사용

Azure Arc 사용 서버에서 VM 확장을 사용 하도록 설정 하려면,, [](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) `--machine-name` ,, `--extension-name` `--location` `--type` `settings` 및 `--publisher` 매개 변수와 함께 az connectedmachine extension create를 사용 합니다.

다음 예제에서는 Azure Arc 사용 서버에서 Log Analytics VM 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "regionName" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

다음 예제에서는 Azure Arc 사용 서버에서 사용자 지정 스크립트 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "regionName" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

다음 예제에서는 Azure Arc 사용 서버에서 Key Vault VM 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

다음 예에서는 Azure Arc 사용 Windows 서버에서 Microsoft Antimalware 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.Security" --type "IaaSAntimalware" --name "IaaSAntimalware" --settings '{"AntimalwareEnabled": true}'
```

## <a name="list-extensions-installed"></a>설치된 확장 나열

Azure Arc 사용 서버에서 VM 확장 목록을 가져오려면 및 매개 변수와 함께 [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) 를 사용 `--machine-name` `--resource-group` 합니다.

예제:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

기본적으로 Azure CLI 명령의 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 예를 들어 기본 출력을 목록이나 테이블로 변경하려면 [az config set core.output=table](/cli/azure/reference-index)을 사용합니다. 출력 형식을 1번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다.

다음 예제에서는 `az connectedmachine extension -list` 명령에서의 부분 JSON 출력을 표시합니다.

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "regionName",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="update-extension-configuration"></a>확장 구성 업데이트

일부 VM 확장은 사용자 지정 스크립트 확장 및 Log Analytics 에이전트 VM 확장과 같이 Arc 사용 서버에 설치 하기 위해 구성 설정이 필요 합니다. 확장의 구성을 업그레이드 하려면 [az connectedmachine extension update](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_update)를 사용 합니다.

다음 예제에서는 사용자 지정 스크립트 확장을 구성 하는 방법을 보여 줍니다.

```azurecli
az connectedmachine extension update --name "CustomScriptExtension" --type "CustomScriptExtension" --publisher "Microsoft.HybridCompute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -lt 100 }\\\"\"}" --type-handler-version "1.10" --machine-name "myMachine" --resource-group "myResourceGroup"
```

## <a name="upgrade-extensions"></a>확장 업그레이드

지원 되는 VM 확장의 새 버전이 릴리스되면 최신 릴리스로 업그레이드할 수 있습니다. VM 확장을 업그레이드 하려면, 및 매개 변수와 함께 [az connectedmachine upgrade-extension](/cli/azure/connectedmachine) 을 사용 `--machine-name` `--resource-group` `--extension-targets` 합니다.

`--extension-targets`매개 변수의 경우 확장 및 사용 가능한 최신 버전을 지정 해야 합니다. 사용 가능한 최신 버전을 확인 하려면 Azure Portal에서 선택한 Arc 사용 서버에 대 한 **확장** 페이지에서 또는 [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list)를 실행 하 여이 정보를 가져올 수 있습니다.

최신 버전을 사용할 수 있는 Windows에 대 한 Log Analytics 에이전트 확장을 업그레이드 하려면 다음 명령을 실행 합니다.

```azurecli
az connectedmachine upgrade-extension --machine-name "myMachineName" --resource-group "myResourceGroup --extension-targets  --extension-targets "{\"MicrosoftMonitoringAgent\":{\"targetVersion\":\"1.0.18053.0\"}}"" 
```

명령 [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list)를 실행 하 여 언제 든 지 설치 된 VM 확장의 버전을 검토할 수 있습니다. `typeHandlerVersion`속성 값은 확장의 버전을 나타냅니다.

## <a name="remove-extensions"></a>확장 제거

Azure Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면, 및 매개 변수와 함께 [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) 를 사용 `--extension-name` `--machine-name` `--resource-group` 합니다.

예를 들어, Linux용 Log Analytics VM 확장을 제거하려면 다음 명령을 실행합니다.

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal](manage-vm-extensions-portal.md) 또는 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)에서 [Azure PowerShell](manage-vm-extensions-powershell.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.

- 명령에 대한 자세한 내용은 Azure CLI VM 확장 [개요](/cli/azure/connectedmachine/extension) 문서를 참조하세요.
