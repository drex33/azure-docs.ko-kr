---
title: Azure Stack Edge Pro 디바이스에서 VM에 대한 사용자 지정 스크립트 확장 사용
description: 템플릿을 사용하여 Azure Stack Edge Pro 디바이스에서 실행되는 VM(가상 머신)에 사용자 지정 스크립트 확장을 설치하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 6ed084e26b12851985e2b3b906be1367b7b4a437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566805"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 디바이스에서 실행되는 VM에 사용자 지정 스크립트 확장 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

사용자 지정 스크립트 확장은 Azure Stack Edge Pro 디바이스에서 실행되는 가상 머신에서 스크립트 또는 명령을 다운로드하고 실행합니다. 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 사용자 지정 스크립트 확장을 설치하고 실행하는 방법을 자세히 설명합니다.

## <a name="about-custom-script-extension"></a>사용자 지정 스크립트 확장 정보

사용자 지정 스크립트 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. Azure Storage 또는 액세스 가능한 다른 인터넷 위치에서 스크립트를 다운로드하거나 확장 런타임에 스크립트 또는 명령을 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합됩니다. Azure CLI, PowerShell 또는 Azure Virtual Machines REST API를 사용하여 실행할 수도 있습니다.

## <a name="os-for-custom-script-extension"></a>사용자 지정 스크립트 확장을 위한 OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows에서 사용자 지정 스크립트 확장에 대해 지원되는 OS

Windows용 사용자 지정 스크립트 확장은 다음 OS에서 실행됩니다. 다른 버전은 작동할 수 있지만 Azure Stack Edge Pro 디바이스에서 실행되는 VM 내에서 테스트되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Windows Server 2019 | 핵심 |
| Windows Server 2016 | 핵심 |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Linux에서 사용자 지정 스크립트 확장에 대해 지원되는 OS

Linux용 사용자 지정 스크립트 확장은 다음 OS에서 실행됩니다. 다른 버전은 작동할 수 있지만 Azure Stack Edge Pro 디바이스에서 실행되는 VM 내에서 테스트되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>필수 구성 요소

1. 클라이언트 시스템에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates)합니다. 작업 디렉터리로 사용할 디렉터리에 다운로드의 압축을 풉니다.

1. VM을 만들고 디바이스에 배포해야 합니다. VM을 만들려면 [템플릿을 사용하여 Azure Stack Edge Pro에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)의 모든 단계를 따르세요.

    GitHub 또는 Azure Storage와 같은 스크립트를 외부에서 다운로드해야 하는 경우 컴퓨팅 네트워크를 구성하는 동안 컴퓨팅을 위해 인터넷에 연결된 포트를 사용하도록 설정합니다. 이렇게 하면 스크립트를 다운로드할 수 있습니다.

    다음 예에서 포트 2는 인터넷에 연결되어 컴퓨팅 네트워크를 사용하도록 설정하는 데 사용되었습니다. 이전 단계에서 Kubernetes가 필요하지 않음을 확인한 경우 Kubernetes 노드 IP 및 외부 서비스 IP 할당을 건너뛸 수 있습니다.

    ![Azure Stack Edge 디바이스에 대한 컴퓨팅 창의 스크린샷. 포트 2에 대한 컴퓨팅 설정이 강조 표시됩니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>사용자 지정 스크립트 확장 설치

VM의 운영 체제에 따라 Windows 또는 Linux용 사용자 지정 스크립트 확장을 설치할 수 있습니다.
 

### <a name="custom-script-extension-for-windows"></a>Windows용 사용자 지정 스크립트 확장

디바이스에서 실행되는 VM에 대한 Windows용 사용자 지정 스크립트 확장을 배포하려면 `addCSExtWindowsVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿`addCSextensiontoVM.json`을 배포합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

`addCSExtWindowsVM.parameters.json` 파일은 다음 매개 변수를 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 이름, 확장 이름 및 실행할 명령을 제공합니다.

다음은 이 문서에서 사용된 샘플 매개 변수 파일입니다.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>템플릿 배포

`addCSextensiontoVM.json` 템플릿을 배포합니다. 이 템플릿은 기존 VM에 확장을 배포합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> 확장 배포는 장기 실행 작업이며 완료하는 데 약 10분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>배포 추적

지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 배포가 완료되면 `ProvisioningState`가 `Succeeded`로 변경됩니다.

확장 출력은 대상 가상 머신의 다음 폴더 아래에 있는 파일에 기록됩니다. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

지정된 파일은 대상 가상 머신의 다음 폴더에 다운로드됩니다.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
여기서 *n* 은 확장의 실행 간에 변경될 수 있는 10진수 정수입니다. 1\.* 값은 확장의 실제 현재 `typeHandlerVersion` 값과 일치합니다. 예를 들어 이 인스턴스의 실제 디렉터리는 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0`입니다. 


이 인스턴스에서 사용자 지정 확장에 대해 실행할 명령은 `md C:\\Users\\Public\\Documents\\test`입니다. 확장이 성공적으로 설치되면 명령에서 지정된 경로의 VM에서 디렉터리가 만들어졌는지 확인할 수 있습니다. 


### <a name="custom-script-extension-for-linux"></a>Linux용 사용자 지정 스크립트 확장

디바이스에서 실행되는 VM에 대한 Windows용 사용자 지정 스크립트 확장을 배포하려면 `addCSExtLinuxVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿`addCSExtensiontoVM.json`을 배포합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

`addCSExtLinuxVM.parameters.json` 파일은 다음 매개 변수를 사용합니다.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 이름, 확장 이름 및 실행할 명령을 제공합니다.

다음은 이 문서에서 사용된 샘플 매개 변수 파일입니다.

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> 확장 배포는 장기 실행 작업이며 완료하는 데 약 10분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`는 `/home/Administrator` 디렉터리에 `file2.txt` 파일을 만들도록 설정되었으며 파일 내용은 `some text`입니다. 이 경우 지정된 경로에 파일이 만들어졌는지 확인할 수 있습니다.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>배포 상태 추적    
    
템플릿 배포는 장기 실행 작업입니다. 지정된 VM에 대한 확장의 배포 상태를 확인하려면 다른 PowerShell 세션을 엽니다(관리자 권한으로 실행). 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
샘플 출력은 다음과 같습니다. 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 배포가 완료되면 `ProvisioningState`가 `Succeeded`로 변경됩니다.

확장 실행 출력은 `/var/lib/waagent/custom-script/download/0/` 파일에 기록됩니다.


## <a name="remove-custom-script-extension"></a>사용자 지정 스크립트 확장 제거

사용자 지정 스크립트 확장을 제거하려면 다음 명령을 사용합니다.

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
