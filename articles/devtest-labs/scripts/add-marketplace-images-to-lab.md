---
title: PowerShell - 랩에 마켓플레이스 이미지 추가
description: 이 PowerShell 스크립트는 Azure DevTest Labs의 랩에 마켓플레이스 이미지를 추가합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: ffb2baa4f82d3246f7193bfe0edad66122b5ed99
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637259"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>PowerShell을 사용하여 Azure DevTest Labs의 랩에 마켓플레이스 이미지 추가

이 샘플 PowerShell 스크립트는 Azure DevTest Labs의 랩에 마켓플레이스 이미지를 추가합니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>사전 요구 사항
* **랩**. 스크립트를 사용하려면 기존 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| Find-AzResource | 지정된 매개 변수를 기반으로 리소스를 검색합니다. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 리소스를 가져옵니다. |
| [집합 AzResource](/powershell/module/az.resources/set-azresource) | 리소스를 수정합니다. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 리소스를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
