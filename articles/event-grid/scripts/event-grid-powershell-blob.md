---
title: Azure PowerShell - Blob Storage 계정 구독
description: 이 문서에서는 Blob Storage 계정에 대한 Event Grid 이벤트를 구독하는 방법을 보여주는 샘플 Azure PowerShell 스크립트를 제공합니다.
ms.devlang: powershell
ms.topic: sample
ms.date: 09/15/2021
ms.openlocfilehash: 72899bf83d7610576d36a1670cd589d68291baf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602881"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>PowerShell을 사용하여 Blob Storage 계정에 대한 이벤트 구독

이 스크립트는 Blob Storage 계정에 대한 이벤트에 대한 Event Grid 구독을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>샘플 스크립트 - 안정적

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 이벤트 구독을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid 구독을 만듭니다. |

## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/get-started-azureps)를 참조하세요.
