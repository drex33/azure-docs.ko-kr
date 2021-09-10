---
title: Azure Stack Edge Pro GPU 디바이스에서 GPU VM에 GPU 확장 설치
description: Azure Stack Edge Pro GPU 디바이스에서 GPU VM(가상 머신)에 GPU 확장을 설치하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 80337c4953182e903655eddb360fc8b49a88ca59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535942"
---
# <a name="install-gpu-extension-on-vms-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 VM에 GPU 확장 설치

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스에서 실행되는 GPU VM에 적절한 Nvidia 드라이버를 설치하기 위해 GPU 드라이버 확장을 설치하는 방법을 설명합니다. 이 문서에서는 Windows 및 Linux VM 둘 다에서 Azure Resource Manager 템플릿을 사용하여 GPU 확장을 설치하는 설치 단계에 대해 설명합니다.

> [!NOTE]
> Azure Portal에서 VM을 만드는 중이나 VM이 배포된 후 GPU 확장을 설치할 수 있습니다. 단계 및 요구 사항은 [GPU 가상 머신 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

디바이스에서 실행되는 GPU VM에 GPU 확장을 설치하기 전에 다음을 확인합니다.

1. 하나 이상의 GPU VM을 배포한 Azure Stack Edge 디바이스에 액세스할 수 있습니다. [디바이스에 GPU VM을 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)하는 방법을 참조하세요.

    - 디바이스의 컴퓨팅 네트워크에 대해 사용하도록 설정된 포트가 인터넷에 연결되어 있고 액세스 권한이 있는지 확인합니다. 인터넷 액세스를 통해 GPU 드라이버가 다운로드됩니다.

        다음은 포트 2가 인터넷에 연결되어 있고 컴퓨팅 네트워크를 사용하도록 설정하는 데 사용된 경우의 예제입니다. Kubernetes가 환경에 배포되지 않은 경우 Kubernetes 노드 IP 및 외부 서비스 IP 할당을 건너뛰어도 됩니다.

        ![Azure Stack Edge 디바이스의 컴퓨팅 창 스크린샷. 포트 2에 대한 컴퓨팅 설정이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension/enable-compute-network-1.png)
1. 클라이언트 머신에 [GPU 확장 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates)합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.
1. 디바이스에 액세스하는 데 사용하려는 클라이언트가 Azure PowerShell을 통해 Azure Resource Manager에 여전히 연결되어 있는지 확인합니다. Azure Resource Manager에 대한 연결은 1시간 30분마다 또는 Azure Stack Edge Pro 디바이스를 다시 시작하는 경우 만료됩니다. 이런 경우 실행 하는 모든 cmdlet은 Azure에 더 이상 연결되지 않는다는 오류 메시지를 반환합니다. 다시 로그인해야 합니다. 자세한 지침은 [Azure Stack Edge 디바이스에서 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.

## <a name="edit-parameters-file"></a>매개 변수 파일 편집

VM의 운영 체제에 따라 Windows 또는 Linux용 GPU 확장을 설치할 수 있습니다.


### <a name="windows"></a>[Windows](#tab/windows)

기존 VM에 대한 Nvidia GPU 드라이버를 배포하려면 `addGPUExtWindowsVM.parameters.json` 매개 변수 파일을 편집한 다음, 템플릿 `addGPUextensiontoVM.json`을 배포합니다.

`addGPUExtWindowsVM.parameters.json` 파일은 다음 매개 변수를 사용합니다.

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

### <a name="linux"></a>[Linux](#tab/linux)

기존 Linux VM에 대한 Nvidia GPU 드라이버를 배포하려면 매개 변수 파일을 편집한 다음, 템플릿 `addGPUextensiontoVM.json`을 배포합니다. 

Ubuntu 또는 RHEL(Red Hat Enterprise Linux)를 사용하는 경우 `addGPUExtLinuxVM.parameters.json` 파일은 다음 매개 변수를 사용합니다.

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    }
    }
    }
```

다음은 이 문서에서 사용된 샘플 Ubuntu 매개 변수 파일입니다.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
        }
    }
}
```

### <a name="gpu-vms-from-rhel-byos-images"></a>RHEL BYOS의 GPU VM 이미지

RHEL BYOS(Red Hat Enterprise Linux Bring Your Own Subscription) 이미지를 사용하여 VM을 만든 경우 다음을 확인합니다.

- [RHEL BYOS 이미지 사용](azure-stack-edge-gpu-create-virtual-machine-image.md)의 단계를 따라 작업했습니다. 
- GPU VM을 만든 후 Red Hat 고객 포털을 사용하여 VM을 등록하고 구독합니다. VM이 제대로 등록되지 않은 경우 VM에 권한이 부여되지 않으므로 설치는 진행되지 않습니다. [Red Hat Subscription Manager를 사용하여 등록 및 자동 등록을 한 단계로 진행](https://access.redhat.com/solutions/253273)을 참조하세요. 이 단계에서는 설치 스크립트를 통해 GPU 드라이버에 대한 관련 패키지를 다운로드할 수 있습니다.
- `vulkan-filesystem` 패키지를 수동으로 설치하거나 yum 리포지토리 목록에 CentOS7 리포지토리를 추가합니다. GPU 확장을 설치하면 설치 스크립트가 CentOS7 리포지토리(RHEL7)에 있는 `vulkan-filesystem` 패키지를 찾습니다. 

---

## <a name="deploy-template"></a>템플릿 배포 

### <a name="windows"></a>[Windows](#tab/windows)

`addGPUextensiontoVM.json` 템플릿을 배포합니다. 이 템플릿은 기존 VM에 확장을 배포합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 확장 배포는 장기 실행 작업이며 완료하는 데 약 10분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

### <a name="linux"></a>[Linux](#tab/linux)

`addGPUextensiontoVM.json` 템플릿을 배포합니다. 이 템플릿은 기존 VM에 확장을 배포합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 확장 배포는 장기 실행 작업이며 완료하는 데 약 10분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
---

## <a name="track-deployment"></a>배포 추적

### <a name="windows"></a>[Windows](#tab/windows)

지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

확장 실행 출력은 다음 파일에 기록됩니다. 설치 상태를 추적하려면 이 파일 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`을 참조하세요. 


설치가 성공하면 `message`가 `Enable Extension`으로, `status`가 `success`로 표시됩니다.

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

### <a name="linux"></a>[Linux](#tab/linux)

템플릿 배포는 장기 실행 작업입니다. 지정된 VM에 대한 확장의 배포 상태를 확인하려면 다른 PowerShell 세션을 엽니다(관리자 권한으로 실행). 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
샘플 출력은 다음과 같습니다. 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

확장 실행 출력은 `/var/log/azure/nvidia-vmext-status` 파일에 기록됩니다.

---

## <a name="verify-driver-installation"></a>드라이버 설치 확인

### <a name="windows"></a>[Windows](#tab/windows)

VM에 로그인하고 드라이버와 함께 설치된 nvidia-smi 명령줄 유틸리티를 실행합니다. `nvidia-smi.exe`는 `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe`에 있습니다. 이 파일이 표시되지 않아도 드라이버 설치는 백그라운드에서 계속 실행될 수 있습니다. 10분 동안 기다린 후 다시 확인합니다.

드라이버가 설치된 경우 다음 샘플과 유사한 출력이 표시됩니다. 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

자세한 내용은 [Windows용 NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-windows.md)을 참조하세요.

> [!NOTE]
> GPU 드라이버와 GPU 확장을 설치한 후에는 더 이상 컴퓨팅을 위해 인터넷에 액세스할 수 있는 포트를 사용할 필요가 없습니다.

### <a name="linux"></a>[Linux](#tab/linux)

다음 단계에 따라 드라이버 설치를 확인합니다.

1. GPU VM에 연결합니다. [LINUX VM에 연결](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)에 제공되는 지침을 따릅니다. 

    샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. 드라이버와 함께 설치된 nvidia-smi 명령줄 유틸리티를 실행합니다. 드라이버가 성공적으로 설치되면 유틸리티를 실행하고 다음 출력을 볼 수 있습니다.

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

자세한 내용은 [Linux용 NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-linux.md)을 참조하세요.

> [!NOTE]
> GPU 드라이버와 GPU 확장을 설치한 후에는 더 이상 컴퓨팅을 위해 인터넷에 액세스할 수 있는 포트를 사용할 필요가 없습니다.


---


## <a name="remove-gpu-extension"></a>GPU 확장 제거

GPU 확장을 제거하려면 다음 명령을 사용합니다.

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

샘플 출력은 다음과 같습니다.

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```


## <a name="next-steps"></a>다음 단계

방법 배우기:

- [GPU 확장 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [디바이스에서 VM 작업 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM 디스크 관리](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md).
- [VM 네트워크 인터페이스 관리](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)
- [VM 크기 관리](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md)