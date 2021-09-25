---
title: 다른 랩에서 가상 머신 가져오기
description: 이 문서는 다른 랩의 가상 머신을 Azure DevTest Labs의 현재 랩으로 가져오는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 324f86b8192cdbc29261d6dfb60ce37cdebe5939
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593784"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 다른 랩의 가상 머신 가져오기
이 문서는 다른 랩에서 사용자의 랩으로 가상 머신을 가져오는 방법에 대한 정보를 제공합니다.

## <a name="scenarios"></a>시나리오
한 랩에서 다른 랩으로 가상 머신을 가져와야 하는 몇 가지 시나리오는 다음과 같습니다.

- 팀의 한 사람이 기업의 다른 그룹으로 이동하면서 개발자 데스크톱을 새로운 팀의 DevTest Labs로 가져가려고 합니다.
- 그룹이 [구독-수준 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)에 도달하여 팀을 몇 개의 구독으로 분할하려는 경우
- 회사가 Express Route(또는 기타 신규 네트워킹 토폴로지)로 이동하며 팀이 가상 머신을 이동하여 해당 신규 인프라를 사용하려는 경우

## <a name="solution-and-constraints"></a>해결 방법 및 제약 조건
이 기능을 사용하면 하나의 랩(원본)에서 다른 랩(대상)으로 가상 머신을 가져올 수 있습니다. 필요한 경우 프로세스에서 대상 가상 머신의 새 이름을 입력할 수 있습니다. 가져오기 프로세스에는 디스크, 일정, 네트워크 설정 등 모든 종속성이 포함됩니다.

이 프로세스에는 다소 시간이 소요되며 다음과 같은 요인의 영향을 받습니다.

- 원본 컴퓨터에 연결된 디스크의 수/크기(이동 작업이 아닌 복사 작업)
- 대상에 대한 거리(예: 미국 동부 지역~동남 아시아).

프로세스가 완료되면 원본 가상 머신은 종료 상태를 유지하며 새 가상 머신이 대상 랩에서 실행됩니다.

한 랩에서 다른 랩으로 가상 머신 가져오기를 계획할 때는 다음 두 가지 키 제약 조건을 고려해야 합니다.

- 구독 및 지역 간 가상 머신 가져오기가 지원되지만, 이 경우 구독은 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.
- 가상 머신은 원본 랩에서 클레임할 수 있는 상태가 아니어야 합니다.
- 원본 랩의 가상 머신 소유자이자 대상 랩의 랩 소유자입니다.
- 현재 이 기능은 Powershell 및 REST API을 통해서만 지원됩니다.

## <a name="use-powershell"></a>PowerShell 사용
[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)에서 ImportVirtualMachines.ps1 파일을 다운로드합니다. 스크립트를 사용하여 단일 가상 머신 또는 모든 가상 머신을 원본 랩에서 대상 랩으로 가져올 수 있습니다.

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell을 사용하여 단일 가상 머신 가져오기
이 powershell 스크립트를 실행하려면 원본 가상 머신과 대상 랩을 식별하고 대상 컴퓨터에 사용할 새 이름을 필요에 따라 제공해야 합니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell을 사용하여 원본 랩에서 모든 가상 머신 가져오기
원본 가상 머신을 지정하지 않은 경우 스크립트는 DevTest Labs의 모든 가상 머신을 자동으로 가져옵니다.  예를 들면 다음과 같습니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST를 사용하여 가상 머신 가져오기
REST 호출은 간단합니다. 원본 및 대상 리소스를 식별하기 위한 충분한 정보를 제공합니다. 작업은 대상 랩 리소스에서 수행됩니다.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩에 대한 정책 설정](devtest-lab-set-lab-policy.md)
- [자주 묻는 질문](devtest-lab-faq.yml)
