---
title: 클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기 - Azure CLI
description: 이 Azure CLI 코드 샘플에서는 활성화된 클러스터링 및 두 개의 분할이 있는 6GB 프리미엄 계층 Azure Cache for Redis를 만드는 방법을 보여줍니다.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9a7dd571e0d2fc27238293ff5776bda6bdc64185
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583221"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기

이 시나리오에서는 사용하도록 설정된 클러스터링 및 두 개의 분할이 있는 6GB 프리미엄 계층 Azure Cache for Redis를 만드는 방법을 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹 및 사용하도록 설정된 클러스터링이 있는 프리미엄 계층 Azure Cache for Redis를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az redis create](/cli/azure/redis) | Azure Cache for Redis 인스턴스를 만듭니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Cache for Redis CLI 스크립트 샘플은 [Azure Cache for Redis 설명서](../cli-samples.md)에서 찾을 수 있습니다.