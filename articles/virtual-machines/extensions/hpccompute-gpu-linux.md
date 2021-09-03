---
title: NVIDIA GPU 드라이버 확장 - Azure Linux VM
description: Linux를 실행하는 N 시리즈 컴퓨팅 VM에서 NVIDIA GPU 드라이버를 설치하기 위한 Microsoft Azure 확장입니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98ac0d72ee3bcb7e99030c0d6748cb359c148924
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446054"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Linux용 NVIDIA GPU 드라이버 확장

## <a name="overview"></a>개요

이 확장은 Linux N 시리즈 VM에서 NVIDIA GPU 드라이버를 설치합니다. 확장은 VM 제품군에 따라 CUDA 또는 GRID 드라이버를 설치합니다. 이 확장을 사용하여 NVIDIA 드라이버를 설치하면 [NVIDIA 최종 사용자 사용권 계약](https://go.microsoft.com/fwlink/?linkid=874330)을 수락하고 이에 동의하게 됩니다. 설치 프로세스 중에 드라이버 설치를 완료하기 위해 VM이 다시 부팅될 수 있습니다.

드라이버의 수동 설치와 현재 지원되는 버전에 대한 지침을 확인할 수 있습니다. 자세한 내용은 [Linux용 Azure N 시리즈 GPU 드라이버 설치](../linux/n-series-driver-setup.md)를 참조하세요.
확장은 [Windows N 시리즈 VM](hpccompute-gpu-windows.md)에서 NVIDIA GPU 드라이버를 설치하는 데 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="operating-system"></a>운영 체제

이 확장은 특정 OS 버전의 드라이버 지원에 따라 다음 OS 배포판을 지원합니다.

| 배포 | 버전 |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |

### <a name="internet-connectivity"></a>인터넷 연결

NVIDIA GPU 드라이버용 Microsoft Azure 확장을 사용하려면 대상 VM이 인터넷에 연결되어 있고 액세스 권한이 있어야 합니다.

## <a name="extension-schema"></a>확장 스키마

확장에 대한 스키마를 보여 주는 JSON은 다음과 같습니다.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>속성

| 속성 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | 문자열 |
| type | NvidiaGpuDriverLinux | 문자열 |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>설정

모든 설정은 선택 사항입니다. 기본 동작은 드라이버 설치에 필요하지 않은 경우 커널을 업데이트하지 않고, 지원되는 최신 드라이버 및 CUDA 도구 키트(해당하는 경우)를 설치하는 것입니다.

| 속성 | 설명 | 기본값 | 유효한 값 | 데이터 형식 |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | 드라이버 설치에 필요하지 않은 경우에도 커널을 업데이트합니다. | false | true, false | boolean |
| driverVersion | NV: GRID 드라이버 버전<br> NC/ND: CUDA 도구 키트 버전. 선택한 CUDA에 대한 최신 드라이버가 자동으로 설치됩니다. | 최신 | 지원되는 드라이버 버전 [목록](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) | 문자열 |
| installCUDA | CUDA 도구 키트를 설치합니다. NC/ND 시리즈 VM에만 관련됩니다. | true | true, false | boolean |


## <a name="deployment"></a>배포


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿 

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후 구성이 필요한 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다.

가상 머신 확장에 대한 JSON 구성은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON 구성의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요. 

다음 예제에서는 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

다음 예는 위의 Azure Resource Manager와 PowerShell 예들을 미러링합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

또한 다음 예에서는 사용자 지정 설정을 기본이 아닌 드라이버 설치의 예가 추가됩니다. 특히, OS 커널을 최신으로 업데이트하고 특정 CUDA 도구 키트 버전 드라이버를 설치합니다. 다시 말해서 '--Settings'는 선택 사항이며 기본값입니다. 커널을 업데이트하면 확장 설치 시간이 늘어날 수 있습니다. 또한 특정 (이전) VERDA tolkit 버전을 선택하면 최신 커널과 항상 호환되지 않을 수도 있습니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure Portal에서 Azure PowerShell 및 Azure CLI를 사용하여 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다. (모든 장기 실행) 설치 상태를 추적하고 오류를 해결하려면 이 파일을 참조하세요.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>종료 코드

| 종료 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 0 | 작업이 성공했습니다. |
| 1 | 확장의 사용이 잘못되었습니다. | 실행 출력 로그를 확인합니다. |
| 10 | Hyper-V 및 Azure에 대한 Linux Integration Services를 사용하거나 설치할 수 없습니다. | lspci의 출력을 확인합니다. |
| 11 | NVIDIA GPU는 이 VM 크기에서 찾을 수 없습니다. | [지원되는 VM 크기 및 OS](../linux/n-series-driver-setup.md)를 사용합니다. |
| 12 | 지원되지 않는 이미지 제품 |
| 13 | 지원되지 않는 VM 크기 | 배포하려면 N 시리즈 VM 사용 |
| 14 | 작업 실패 | 실행 출력 로그를 확인합니다. |


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](features-linux.md)을 참조하세요.

N 시리즈 VM에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](../sizes-gpu.md)를 참조하세요.
