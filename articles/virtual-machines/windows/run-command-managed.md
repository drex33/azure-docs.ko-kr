---
title: 관리되는 실행 명령을 사용하여 Azure의 Windows VM에서 스크립트 실행(미리 보기)
description: 이 항목에서는 업데이트된 실행 명령 기능을 사용하여 Azure Windows 가상 머신 내에서 스크립트를 실행하는 방법에 대해 설명합니다.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fca5afb1b633d5c35279e046c88cf64c9a7a4ab8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482928"
---
# <a name="preview-run-scripts-in-your-windows-vm-by-using-managed-run-commands"></a>미리 보기: 관리되는 실행 명령을 사용하여 Windows VM에서 스크립트 실행

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

> [!IMPORTANT]
> **관리되는 실행 명령** 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

실행 명령 기능은 VM(가상 머신) 에이전트를 사용하여 Azure Windows VM 내에서 스크립트를 작성합니다. 이러한 스크립트는 일반 머신 또는 애플리케이션 관리에 사용할 수 있습니다. VM 액세스 및 네트워크 문제를 신속하게 진단 및 수정하고 VM을 다시 양호한 상태로 되돌릴 수 있습니다.

*업데이트된* 관리되는 실행 명령 동일한 VM 에이전트 채널을 사용하여 스크립트를 실행하고 [원래 작업 지향 실행 명령](run-command.md)대해 다음과 같은 향상된 기능을 제공합니다. 
- ARM 배포 템플릿을 통해 업데이트된 실행 명령 지원 
- 여러 스크립트의 병렬 실행 
- 스크립트의 순차적 실행 
- RunCommand 스크립트를 취소할 수 있습니다.  
- 사용자가 지정한 스크립트 시간 제한 
- 장기 실행(시간/일) 스크립트 지원 
- 안전한 방식으로 비밀(매개 변수, 암호) 전달


## <a name="azure-cli"></a>Azure CLI 

다음 예제에서는 [az vm run-command를](/cli/azure/vm/run-command) 사용하여 Azure Windows VM에서 셸 스크립트를 실행합니다.

### <a name="execute-a-script-with-the-vm"></a>VM을 사용하여 스크립트 실행
이 명령은 VM에 스크립트를 전달하고, 실행하고, 캡처된 출력을 반환합니다.

```azurecli-interactive
az vm run-command set --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>VM에 배포된 모든 RunCommand 리소스 나열 
이 명령은 이전에 배포된 실행 명령의 전체 목록과 해당 속성을 반환합니다. 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>실행 상태 및 결과 얻기 
이 명령은 최신 출력, 시작/종료 시간, 종료 코드 및 실행의 터미널 상태를 포함하여 현재 실행 진행 상황을 검색합니다.

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>VM에서 RunCommand 리소스 삭제
이전에 VM에 배포된 RunCommand 리소스를 제거합니다. 스크립트 실행이 아직 진행 중이면 실행이 종료됩니다. 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>VM을 사용하여 스크립트 실행
이 명령은 VM에 스크립트를 전달하고, 실행하고, 캡처된 출력을 반환합니다.

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "myRunCommand" – Script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>VM에 배포된 모든 RunCommand 리소스 나열 
이 명령은 이전에 배포된 실행 명령의 전체 목록과 해당 속성을 반환합니다.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>실행 상태 및 결과 얻기 
이 명령은 최신 출력, 시작/종료 시간, 종료 코드 및 실행의 터미널 상태를 포함하여 현재 실행 진행 상황을 검색합니다.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "myRunCommand" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>VM에서 RunCommand 리소스 삭제
이전에 VM에 배포된 RunCommand 리소스를 제거합니다. 스크립트 실행이 아직 진행 중이면 실행이 종료됩니다. 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "myRunCommand"
```
 

## <a name="rest-api"></a>REST API 

새 실행 명령 배포하려면 VM에서 PUT을 직접 실행하고 실행 명령 인스턴스의 고유한 이름을 지정합니다. 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "Write-Host Hello World!", 
        "scriptUri": "<URI>",  
        "commandId": "<Id>"  
        }, 
    "parameters": [ 
        { 
            "name": "param1",
            "value": "value1" 
            }, 
        { 
            "name": "param2", 
            "value": "value2" 
            } 
        ], 
    "protectedParameters": [ 
        { 
            "name": "secret1", 
            "value": "value1" 
            }, 
        { 
            "name": "secret2", 
            "value": "value2" 
            } 
        ], 
    "runAsUser": "userName",
    "runAsPassword": "userPassword", 
    "timeoutInSeconds": 3600, 
    "outputBlobUri": "<URI>", 
    "errorBlobUri": "<URI>"  
    }
}
```

### <a name="notes"></a>참고
 
- 인라인 스크립트, 스크립트 URI 또는 기본 제공 스크립트 [명령 ID를](run-command.md#available-commands) 입력 원본으로 제공할 수 있습니다.
- 하나의 명령 실행에 대해 하나의 원본 입력 유형만 지원됩니다. 
- 실행 명령 큰 스크립트 출력을 저장하는 데 사용할 수 있는 Storage Blob에 대한 출력을 지원합니다.
- 실행 명령 Storage Blob에 대한 오류 출력을 지원합니다. 


### <a name="list-running-instances-of-run-command-on-a-vm"></a>VM에서 실행 중인 실행 명령 인스턴스 나열 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>특정 실행 명령 배포에 대한 출력 세부 정보 얻기 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>특정 실행 명령 배포 취소 

실행 중인 배포를 취소하려면 실행 명령 실행 중인 인스턴스에서 PUT 또는 PATCH를 실행하고 요청 본문에 빈 스크립트를 지정할 수 있습니다. 그러면 진행 중인 실행이 취소됩니다. 

실행 명령 인스턴스를 삭제할 수도 있습니다.  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>순서가 정한 시퀀스로 스크립트 배포 

스크립트를 순차적으로 배포하려면 배포 템플릿을 사용하여 `dependsOn` 순차적 스크립트 간의 관계를 지정합니다. 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "Write-Host Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>여러 실행 명령을 순차적으로 실행 

기본적으로 배포 템플릿을 사용하여 여러 RunCommand 리소스를 배포하는 경우 VM에서 동시에 실행됩니다. 스크립트에 대한 종속성과 기본 실행 순서가 있는 경우 속성을 사용하여 `dependsOn` 순차적으로 실행할 수 있습니다. 

이 예제에서 **secondRunCommand는** **firstRunCommand** 이후에 실행됩니다. 

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "resources":[
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/firstRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
         ],
         "properties":{
            "source":{
               "script":"Write-Host First: Hello World!"
            },
            "parameters":[
               {
                  "name":"param1",
                  "value":"value1"
               },
               {
                  "name":"param2",
                  "value":"value2"
               }
            ],
            "timeoutInSeconds":20
         }
      },
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/secondRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'),'runcommands/firstRunCommand')]"
         ],
         "properties":{
            "source":{
               "scriptUrl":"http://github.com/myscript.ps1"
            },
            "timeoutInSeconds":60
         }
      }
   ]
}
```


## <a name="next-steps"></a>다음 단계

VM에서 스크립트 및 명령을 원격으로 실행하는 다른 방법에 대해 알아보려면 [Windows VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조하세요.
