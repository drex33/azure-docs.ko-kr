---
title: InfiniBand 드라이버 확장 - Azure Windows VM
description: Windows를 실행하는 H 시리즈 및 N 시리즈 컴퓨팅 VM에 InfiniBand 드라이버를 설치하기 위한 Microsoft Azure 확장입니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/14/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f2e6d04542df5795bd3c30763f0197295a21f78
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038349"
---
# <a name="infiniband-driver-extension-for-windows"></a>Windows용 InfiniBand 드라이버 확장

이 확장은 Windows를 실행하는 [H 시리즈](../sizes-hpc.md) 및 [N 시리즈](../sizes-gpu.md) VM에 InfiniBand ND 드라이버(SR-IOV를 사용하지 않는 경우) 및 OFED 드라이버(SR-IOV를 사용하는 경우)(‘r’ 크기)를 설치합니다. VM 제품군에 따라 확장은 Connect-X NIC에 적절한 드라이버를 설치합니다.

[Linux VM](hpc-compute-infiniband-linux.md)용 InfiniBand 드라이버를 설치하기 위한 확장도 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

이 확장은 특정 OS 버전의 드라이버 지원에 따라 다음 OS 배포판을 지원합니다. 원하는 H 시리즈 및 N 시리즈 VM 크기에 적합한 InfiniBand NIC를 확인합니다.

| 배포 | InfiniBand NIC 드라이버 |
|---|---|
| Windows 10 | CX3-Pro, CX5, CX6 |
| Windows Server 2019 | CX3-Pro, CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

### <a name="internet-connectivity"></a>인터넷 연결

InfiniBand 드라이버용 Microsoft Azure 확장을 사용하려면 대상 VM이 인터넷에 연결되어 있고 액세스 권한이 있어야 합니다.

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.5",
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
| type | InfiniBandDriverWindows | 문자열 |
| typeHandlerVersion | 1.5 | int |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.5",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.5 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.5 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 확장 추가

다음 예제에서는 *Myresourcegroup* 이라는 리소스 그룹에 배포 된 *myvmss* 라는 기존 가상 머신 확장 집합의 모든 RDMA 지원 vm에 최신 버전 1.5 InfiniBandDriverWindows 확장을 설치 합니다.

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.5"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
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

확장 실행 출력은 다음 파일에 기록됩니다. 설치 상태를 추적하고 오류를 해결하려면 이 파일을 참조하세요.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>종료 코드

다음 표에서는 확장 설치 프로세스의 종료 코드에 따른 의미와 권장 사항에 대해 설명합니다.

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 0 | 작업이 성공했습니다. |
| 3010 | 작업이 성공했습니다. 다시 부팅해야 합니다. |
| 100 | 작업이 지원되지 않거나 완료할 수 없습니다. | 가능한 원인: PowerShell 버전이 지원되지 않습니다. VM 크기가 InfiniBand 사용 VM이 아니며, 데이터를 다운로드하지 못했습니다. 로그 파일을 확인하여 오류의 원인을 파악합니다. |
| 240, 840 | 작업 시간이 초과되었습니다. | 작업을 다시 시도합니다. |
| -1 | 예외가 발생했습니다. | 로그 파일을 확인하여 예외의 원인을 파악합니다. |

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)를 통해 지원 인시던트를 제출할 수 있습니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
InfiniBand 사용(‘r’ 크기)에 대한 자세한 내용은 [H 시리즈](../sizes-hpc.md) 및 [N 시리즈](../sizes-gpu.md) VM을 참조하세요.

> [!div class="nextstepaction"]
> [Linux VM 확장 및 기능에 대한 자세한 정보](features-linux.md)
