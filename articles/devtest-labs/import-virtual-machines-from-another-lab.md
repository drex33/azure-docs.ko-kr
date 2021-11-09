---
title: 다른 랩에서 가상 머신 가져오기
description: Azure DevTest Labs 한 랩에서 다른 랩으로 가상 머신을 가져오는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 50f17183caa7da86ec6704af35129ed8bd707d5a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059850"
---
# <a name="import-virtual-machines-from-one-lab-to-another"></a>한 랩에서 다른 랩으로 가상 머신 가져오기

이 문서에서는 한 DevTest Labs 랩에서 다른 랩으로 VM(가상 머신)을 가져오는 방법을 설명하고 설명합니다.

## <a name="scenarios"></a>시나리오
한 랩에서 다른 랩으로 VM을 가져와야 하는 몇 가지 시나리오는 다음과 같습니다.

- 개인이 한 그룹에서 다른 그룹으로 이동하고 있으며 개발자 데스크톱을 새 팀의 랩으로 이동하려고 합니다.
- 그룹이 [구독 수준 할당량에](../azure-resource-manager/management/azure-subscription-service-limits.md) 도달했으며 팀을 두 개 이상의 Azure 구독으로 분할하려고 합니다.
- 회사는 Azure ExpressRoute 또는 다른 새로운 네트워킹 토폴로지로 이동하고 있으며, 팀은 새 인프라를 사용하도록 VM을 이동하려고 합니다.

## <a name="requirements-and-constraints"></a>요구 사항 및 제약 조건

가져오기 프로세스는 원본 랩에서 대상 랩으로 VM을 가져옵니다. 필요에 따라 프로세스에서 VM의 이름을 바꿀 수 있습니다. 가져오기 프로세스에는 디스크, 일정 및 네트워크 설정과 같은 모든 의존도가 포함됩니다.

이 프로세스는 이동 작업이 아닌 복사 작업이며 시간이 오래 걸릴 수 있습니다. 가져오기 시간은 부분적으로 다음 요인에 따라 달라집니다.

- 원본 컴퓨터에 연결된 디스크의 수 및 크기
- 원본 지역과 대상 지역 간 거리

가져오기가 완료되면 프로세스가 원본 VM을 종료하고 대상 랩에서 실행되는 새 VM을 시작합니다.

한 랩에서 다른 랩으로 VM을 가져오기 위한 몇 가지 요구 사항 및 제약 조건이 있습니다.

- 여러 구독 및 지역 간에 VM을 가져올 수 있지만 두 구독은 동일한 Azure Active Directory 테넌트와 연결되어야 합니다.
- VM은 원본 랩에서 클레임할 수 있는 상태가 될 수 없습니다.
- 원본 랩에서 VM의 소유자 및 대상 랩의 소유자여야 합니다.
- 현재 이 기능은 PowerShell 및 REST API 통해서만 지원됩니다.

## <a name="use-powershell-to-import-one-or-all-lab-vms"></a>PowerShell을 사용하여 하나 또는 모든 랩 VM 가져오기

[ImportVirtualMachines.ps1](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)다운로드하고 실행합니다. 스크립트를 사용하여 원본 랩에서 대상 랩으로 단일 VM 또는 모든 VM을 가져올 수 있습니다.

이 PowerShell 스크립트를 실행하려면 원본 및 대상 구독 및 랩과 원본 VM을 식별합니다. 필요에 따라 대상 VM에 대한 새 이름을 입력합니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>"`
                            -SourceDevTestLabName "<Name of the source lab>"`
                            -SourceVirtualMachineName "<Name of the VM to import from the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contains the destination lab>"`
                            -DestinationDevTestLabName "<Name of the destination lab>"`
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```
원본 VM을 지정하지 않으면 스크립트는 원본 랩의 모든 VM을 자동으로 가져옵니다.

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST를 사용하여 가상 머신 가져오기

REST 호출은 간단합니다. 원본 및 대상 리소스를 식별하는 정보를 제공합니다. 작업은 대상 랩 리소스에서 수행됩니다.

```http
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>다음 단계

- [랩에 대한 정책 설정](devtest-lab-set-lab-policy.md)
- [DevTest Labs 질문과 대답](devtest-lab-faq.yml)
