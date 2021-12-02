---
title: AMD GPU 드라이버 확장 - Azure Windows VM
description: Windows를 실행하는 NVv4 시리즈 VM에 AMD GPU 드라이버를 설치하기 위한 Microsoft Azure 확장입니다.
services: virtual-machines-windows
author: vikancha-MSFT
manager: jkabat
ms.topic: article
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/14/2021
ms.author: vikancha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37218cfb9cfb3086a4f4033c2e6cc954b45e416d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133479985"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Windows용 AMD GPU 드라이버 확장

이 문서에서는 Windows [NVv4 시리즈](../nvv4-series.md) VM에 AMD GPU 드라이버를 배포하기 위한 VM 확장에 대한 개요를 제공합니다. 이 확장을 사용하여 AMD 드라이버를 설치하면 [AMD 최종 사용자 사용권 계약](https://amd.com/radeonsoftwarems)을 수락하고 이에 동의하게 됩니다. 설치 프로세스 중에 드라이버 설치를 완료하기 위해 VM이 다시 부팅될 수 있습니다.

드라이버의 수동 설치 및 현재 지원되는 버전에 대한 지침을 확인할 수 있습니다. 자세한 내용은 [Windows용 Azure N 시리즈 AMD GPU 드라이버 설정](../windows/n-series-amd-driver-setup.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

확장에서 지원하는 OS는 다음과 같습니다.

| 배포 | 버전 |
|---|---|
| Windows 10 EMS | 빌드 1909 |
| Windows 10 | 빌드 1909 |
| Windows Server 2016 | 핵심 |
| Windows Server 2019 | 핵심 |

### <a name="internet-connectivity"></a>인터넷 연결

AMD GPU 드라이버용 Microsoft Azure 확장을 사용하려면 대상 VM이 인터넷에 연결되어 있고 액세스 권한이 있어야 합니다.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.1",
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
| type | AmdGpuDriverWindows | 문자열 |
| typeHandlerVersion | 1.1 | int |


## <a name="deployment"></a>배포
### <a name="azure-portal"></a>Azure Portal

Azure Portal에서 Azure AMD VM 확장을 배포할 수 있습니다.

1. 브라우저에서 [Azure 포털](https://portal.azure.com)로 이동합니다.

2. 드라이버를 설치 하려는 가상 머신으로 이동 합니다.

3. 왼쪽 메뉴에서 **확장** 을 선택 합니다.

    :::image type="content" source="./media/amd-ext-portal/extensions-menu.png" alt-text="Azure Portal 메뉴에서 확장을 선택 하는 것을 보여 주는 스크린샷":::

4. **추가** 를 선택합니다.

    :::image type="content" source="./media/amd-ext-portal/add-extension.png" alt-text="선택한 V M에 대 한 V M 확장을 추가 하는 것을 보여 주는 스크린샷":::

5. 스크롤하여 **AMD GPU 드라이버 확장** 을 찾아 선택 하 고 **다음** 을 선택 합니다.

    :::image type="content" source="./media/amd-ext-portal/select-amd-extension.png" alt-text="AMD G P U 드라이버를 선택 하는 것을 보여 주는 스크린샷":::

6. **검토 + 만들기** 를 선택 하 고 **만들기** 를 클릭 한 다음 드라이버가 배포 될 때까지 몇 분 정도 기다립니다.

    :::image type="content" source="./media/amd-ext-portal/create-amd-extension.png" alt-text="검토 및 만들기 단추를 선택 하는 것을 보여 주는 스크린샷":::
  
7. 확장이 설치 된 확장 목록에 추가 되었는지 확인 합니다.

    :::image type="content" source="./media/amd-ext-portal/verify-extension.png" alt-text="V M에 대 한 확장 목록에서 새 확장을 보여 주는 스크린샷":::

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.1 `
  --settings '{ `
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

확장 실행 출력은 다음 디렉터리에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.AmdGpuDriverMicrosoft\
```

### <a name="error-codes"></a>오류 코드

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 0 | 작업이 성공했습니다. |
| 1 | 작업이 성공했습니다. 다시 부팅해야 합니다. |
| 100 | 작업이 지원되지 않거나 완료할 수 없습니다. | 가능한 원인: PowerShell 버전이 지원되지 않습니다. VM 크기가 N 시리즈 VM이 아니며, 데이터를 다운로드하지 못했습니다. 로그 파일을 확인하여 오류의 원인을 파악합니다. |
| 240, 840 | 작업 시간이 초과되었습니다. | 작업을 다시 시도합니다. |
| -1 | 예외가 발생했습니다. | 로그 파일을 확인하여 예외의 원인을 파악합니다. |
| -5x | 보류 중인 재부팅으로 인해 작업이 중단되었습니다. | VM을 다시 부팅합니다. 다시 부팅한 후에 설치가 계속됩니다. 제거는 수동으로 호출해야 합니다. |


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
확장에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](features-windows.md)을 참조하세요.

N 시리즈 VM에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](../sizes-gpu.md)를 참조하세요.
