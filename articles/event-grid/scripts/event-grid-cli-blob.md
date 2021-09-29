---
title: Azure CLI 스크립트 샘플 - Blob Storage 계정 구독 | Microsoft Docs
description: 이 문서에서는 Azure Blob Storage 계정의 이벤트를 구독하는 방법을 보여주는 샘플 Azure CLI 스크립트를 제공합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8c5baf79209414031073c8ef0505c0ca18252a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584621"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Azure CLI를 사용하여 Blob Storage 계정에 대한 이벤트 구독

이 스크립트는 Blob Storage 계정에 대한 이벤트에 대한 Event Grid 구독을 만듭니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 이벤트 구독을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Event Grid 구독을 만듭니다. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - 확장 버전 | Event Grid 구독을 만듭니다. |

## <a name="next-steps"></a>다음 단계

* 구독 쿼리에 대한 정보는 [쿼리 Event Grid 구독](../query-event-subscriptions.md)을 참조하세요.
* Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
