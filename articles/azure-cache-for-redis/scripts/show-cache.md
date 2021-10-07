---
title: Azure Cache for Redis에 대한 세부 정보 가져오기 - Azure CLI
description: 이 Azure CLI 코드 샘플에서는 프로비전 상태를 포함하여 Azure Cache for Redis 인스턴스에 대한 세부 정보를 검색하는 방법을 보여줍니다.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e41b432fe7259fac9769a70a89bb6357c3d550e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535357"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 세부 정보 가져오기

이 시나리오에서는 프로비전 상태를 포함하여 Azure Cache for Redis 인스턴스에 대한 세부 정보를 검색하는 방법을 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Azure Cache for Redis 인스턴스에 대한 세부 정보를 검색합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az redis show](/cli/azure/redis) | Azure Redis Cache 인스턴스에 대한 세부 정보를 검색합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Cache for Redis CLI 스크립트 샘플은 [Azure Cache for Redis 설명서](../cli-samples.md)에서 찾을 수 있습니다.