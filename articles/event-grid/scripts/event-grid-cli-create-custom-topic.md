---
title: Azure CLI 스크립트 샘플 - 사용자 지정 항목 만들기 | Microsoft Docs
description: 이 문서에서는 Azure Event Grid 사용자 지정 항목을 만드는 방법을 보여주는 샘플 Azure CLI 스크립트를 제공합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/22/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54f56b1bce0c8d88bff3b39b6d4c8ff108106cb3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463796"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Azure CLI를 사용하여 Event Grid 사용자 지정 항목 만들기

이 스크립트는 Event Grid 사용자 지정 항목을 만듭니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 사용자 지정 항목을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az_eventgrid_topic_create) | Event Grid 사용자 지정 항목을 만듭니다. |


## <a name="next-steps"></a>다음 단계

* 구독 쿼리에 대한 정보는 [쿼리 Event Grid 구독](../query-event-subscriptions.md)을 참조하세요.
* Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
