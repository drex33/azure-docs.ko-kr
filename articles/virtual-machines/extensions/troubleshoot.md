---
title: Windows VM 확장 오류 문제 해결
description: Azure Windows VM 확장 오류 문제 해결에 대해 자세히 알아봅니다.
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292798"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 확장 오류 문제 해결
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>확장 상태 보기
Azure Powershell에서 Azure Resource Manager 템플릿을 실행할 수 있습니다. 템플릿을 실행하면 Azure Resource Explorer 또는 명령줄 도구에서 확장 상태를 볼 수 있습니다.

다음은 예제입니다.

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

샘플 출력은 다음과 같습니다.

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>확장 오류 문제 해결

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>VM 에이전트가 실행 중이고 준비 상태인지 확인
확장을 관리, 설치, 실행하려면 VM 에이전트가 필요합니다. VM 에이전트가 실행되고 있지 않거나 Azure 플랫폼에 준비 상태를 보고하지 못하는 경우 확장이 제대로 작동하지 않습니다.

VM 에이전트 문제를 해결하려면 다음 페이지를 참조하세요.
- Windows VM에 대한 [Microsoft Azure 게스트 에이전트 문제 해결](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)
- Linux VM에 대한 [Azure Linux 에이전트 문제 해결](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent)

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>특정 확장 문제 해결 가이드 확인
일부 확장에는 문제 해결 방법을 설명하는 특정 페이지가 있습니다. [확장 문제 해결](./overview.md#troubleshoot-extensions)에서 이 확장 및 페이지 목록을 찾을 수 있습니다.

### <a name="view-the-extensions-status"></a>확장 상태 보기
위에서 설명한 대로 확장 상태를 찾으려면 다음 PowerShell cmdlet을 실행하거나,
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

다음 CLI 명령을 실행하거나,
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

Azure Portal에서 VM 블레이드/설정/확장으로 이동합니다. 그런 다음, 확장을 클릭하고 해당 상태와 메시지를 확인할 수 있습니다.


### <a name="rerun-the-extension-on-the-vm"></a>VM에서 확장 다시 실행
사용자 지정 스크립트 확장을 사용하여 VM에서 스크립트를 실행하는 경우 때때로 VM이 성공적으로 만들어졌지만 스크립트가 실패하는 오류가 발생할 수 있습니다. 이러한 조건에서 이 오류를 복구하는 권장 방법은 확장을 제거하고 템플릿을 다시 실행하는 것입니다.
참고: 이후에는 확장을 제거할 필요가 없도록 이 기능이 향상될 예정입니다.

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell에서 확장 제거
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

확장이 제거되면 템플릿을 다시 실행하여 VM에서 스크립트를 실행할 수 있습니다.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>VM에 새 GoalState 트리거
"Windows Azure CRP 인증서 생성기"가 누락되어 확장이 실행되지 않았거나 실행에 실패하는 것을 알 수 있습니다(해당 인증서는 확장의 보호된 설정 전송을 보호하는 데 사용됨).
해당 인증서는 Virtual Machine 내에서 Windows 게스트 에이전트를 다시 시작하여 자동으로 다시 생성됩니다.
- 태스크 관리자 열기
- 세부 정보 탭으로 이동
- WindowsAzureGuestAgent.exe 프로세스 찾기
- 마우스 오른쪽 단추로 클릭하고 "작업 종료"를 선택합니다. 프로세스는 자동으로 다시 시작됩니다.


"VM 다시 적용"을 실행하여 새 GoalState를 VM에 트리거할 수도 있습니다. VM [다시 적용](/rest/api/compute/virtualmachines/reapply)은 VM 상태를 다시 적용하기 위해 2020년에 도입된 API입니다. 짧은 VM 가동 중지 시간을 허용하려면 이 작업을 한 번에 수행하는 것이 좋습니다. 다시 적용 자체는 VM 재부팅을 발생시키지 않으며 Reapply를 호출하는 대부분의 경우 VM을 다시 부팅하지 않지만, 다시 적용이 새 목표 상태를 트리거할 때 VM 모델에 보류 중인 다른 업데이트가 적용되고 다른 변경에 다시 시작이 필요할 수 있는 위험이 매우 적습니다. 

Azure Portal:

포털에서 VM을 선택하고 왼쪽 창의 **지원 + 문제 해결** 아래에서 **다시 배포 + 다시 적용** 을 선택한 다음, **다시 적용** 을 선택합니다.


Azure PowerShell *(RG 이름 및 VM 이름을 사용자의 값으로 대체)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(RG 이름 및 VM 이름을 사용자의 값으로 대체)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

"VM 다시 적용"이 작동하지 않는 경우 Azure 관리 포털의 VM에 비어 있는 새 데이터 디스크를 추가한 다음, 나중에 인증서가 다시 추가되면 제거할 수 있습니다.


### <a name="look-at-the-extension-logs-inside-the-vm"></a>VM 내에서 확장 로그 확인

이전 단계가 작동하지 않은 경우와 확장이 여전히 실패 상태인 경우 다음 단계는 가상 머신 내에서 해당 로그를 확인하는 것입니다.

**Windows** VM에서 확장 로그는 일반적으로 다음 위치에 있습니다. 
```
C:\WindowsAzure\Logs\Plugins
```
또한 확장 설정과 상태 파일은 다음 위치에 있습니다. 
```
C:\Packages\Plugins
```
<br/>

**Linux** VM에서 확장 로그는 일반적으로 다음 위치에 있습니다. 
```
/var/log/azure/
```
또한 확장 설정과 상태 파일은 다음 위치에 있습니다. 
```
/var/lib/waagent/
```


각 확장은 서로 다르지만 일반적으로 유사한 원칙을 따릅니다.

확장 패키지와 이진 파일은 VM에 다운로드됩니다(예: _"/var/lib/waagent/custom-script/download/1"_ (Linux) 또는 _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0"_ (Windows)). 

해당 구성과 설정은 VM 에이전트를 통해 Azure 플랫폼에서 확장 처리기에 전달됩니다(예: _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config"_ (Linux) 또는 _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings"_ (Windows)).

VM 내의 확장 처리기는 상태 파일(예: _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status"_ (Linux) 또는 _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status"_ (Windows))에 기록된 후 Azure 플랫폼에 보고됩니다. 이 상태는 PowerShell, CLI 또는 Azure Portal의 VM 확장 블레이드를 통해 보고됩니다.

또한 실행에 대한 자세한 로그를 작성합니다(예: _"/var/log/azure/custom-script/handler.log"_ (Linux) 또는 _"C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log"_ (Windows)).


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>VM이 기존 VM에서 다시 생성되는 경우

다른 Azure VM에서 들어오는 특수화된 디스크를 기반으로 Azure VM을 만드는 경우가 있을 수 있습니다. 이 경우 이전 VM에 확장이 포함되었고 이에 따라 이진 파일, 로그, 상태 파일 등이 남아 있을 수 있습니다. 새 VM 모델은 이전 VM의 확장 상태를 알 수 없으며 해당 확장에 대해 잘못된 상태를 보고할 수 있습니다. 새 VM을 만들기 전에 이전 VM에서 확장을 제거한 다음, 새 VM이 만들어지면 해당 확장을 다시 설치하는 것이 좋습니다.
기존 Azure VM에서 일반화된 이미지를 만드는 경우에도 마찬가지입니다. 확장에서 일관되지 않은 상태를 방지하기 위해 확장을 제거하는 것이 좋습니다.