---
title: Azure CLI를 사용하여 VM 확장 사용
description: 이 문서에서는 Azure CLI를 사용하여 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 10/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41924f20679de16205af3c7cb962d6005e757e2d
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069637"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure VM 확장 사용

이 문서에서는 Azure CLI를 사용하여 Azure Arc 지원 서버에서 지원하는 VM 확장을 Linux 또는 Windows 하이브리드 머신에 배포하고 제거하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

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

## <a name="remove-an-installed-extension"></a>설치된 확장 제거

Azure Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면, 및 매개 변수와 함께 [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) 를 사용 `--extension-name` `--machine-name` `--resource-group` 합니다.

예를 들어, Linux용 Log Analytics VM 확장을 제거하려면 다음 명령을 실행합니다.

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal](manage-vm-extensions-portal.md) 또는 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)에서 [Azure PowerShell](manage-vm-extensions-powershell.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.

- 명령에 대한 자세한 내용은 Azure CLI VM 확장 [개요](/cli/azure/connectedmachine/extension) 문서를 참조하세요.
