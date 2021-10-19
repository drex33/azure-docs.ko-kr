---
title: Azure Lab Services용 PowerShell 모듈 | Microsoft Docs
description: 이 문서에서는 Azure Lab Services에서 아티팩트를 관리하는 데 도움이 되는 PowerShell 모듈에 관한 정보를 제공합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 81ed7d689105aa459151d698466b98f71f7b352e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177691"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 모듈(미리 보기)
Az.LabServices는 Azure Lab Services의 관리를 간소화하는 PowerShell 모듈입니다. 랩 계정, 랩, VM, 이미지 등을 만들고, 쿼리, 업데이트, 삭제하는 구성 가능한 함수를 제공합니다. 이 모듈에 관한 자세한 내용은 [GitHub의 Az.LabServices 홈페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조하세요.

> [!NOTE]
> 이 모듈은 **미리 보기** 상태입니다. 

## <a name="example-command"></a>예제 명령
PowerShell 명령을 사용하여 모든 랩에서 실행 중인 모든 VM 중지하는 예제는 다음과 같습니다.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>시작
1. 머신에 없는 경우 [Azure PowerShell](/powershell/azure/)을 설치합니다. 
2. [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1)을 머신에 다운로드합니다.
3. 모듈을 가져옵니다.

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 다음 명령을 실행하여 구독의 모든 랩을 나열합니다.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>다음 단계
[GitHub의 Az.LabServices 홈페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조하세요.