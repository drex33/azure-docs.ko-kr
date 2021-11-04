---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 633121c21bf09c608c0c40d6b87bf68edfbaf1f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483056"
---
## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure Portal을 사용하여 리소스 정리

[리소스 정리](../ingest-data/data-explorer-ingest-event-hub-portal.md#clean-up-resources) 단계에 따라 Azure Portal에서 리소스를 삭제합니다.

### <a name="clean-up-resources-using-powershell"></a>PowerShell을 사용하여 리소스 정리

Cloud Shell이 아직 열려 있으면 첫 번째 줄(Read-Host)을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
