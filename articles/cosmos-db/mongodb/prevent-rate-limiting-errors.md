---
title: Azure Cosmos DB API for MongoDB 작업에 대한 속도 제한 오류를 방지합니다.
description: SSR(서버 쪽 다시 시도) 기능을 사용하여 Azure Cosmos DB API for MongoDB 작업이 속도 제한 오류 발생을 방지하는 방법에 대해 알아봅니다.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9067a87887c47229b8bba687535e2730c2ed1b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030906"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Azure Cosmos DB API for MongoDB 작업에 대한 속도 제한 오류 방지
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB 작업은 컬렉션의 처리량 제한(RUs)을 초과하는 경우 속도 제한(16500/429) 오류가 발생하여 실패할 수 있습니다. 

SSR(서버 쪽 다시 시도) 기능을 사용하도록 설정하고 서버에서 이러한 작업을 자동으로 다시 시도하도록 할 수 있습니다. 계정의 모든 컬렉션에 대해 짧은 지연 후 요청이 다시 시도됩니다. 이 기능은 클라이언트 애플리케이션에서 속도 제한 오류를 처리하는 편리한 대안입니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos DB API for MongoDB 계정으로 이동합니다.

1. **설정** 섹션 아래의 **기능** 창으로 이동합니다.

1. **서버 쪽 다시 시도** 를 선택합니다.

1. 계정의 모든 컬렉션에 대해 이 기능을 사용하려면 **사용** 을 클릭합니다.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Azure Cosmos DB API for MongoDB에 대한 서버 쪽 다시 시도 기능의 스크린샷":::

## <a name="use-the-azure-cli"></a>Azure CLI 사용

1. 계정에 대해 SSR을 이미 사용하도록 설정되었는지 확인합니다.

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

1. 데이터베이스 계정의 모든 컬렉션에 대해 SSR을 **사용** 합니다. 이 변경 사항이 적용되려면 최대 15분이 소요될 수 있습니다.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
   ```

1. 다음 명령은 기능 목록에서 `DisableRateLimitingResponses`를 제거하여 데이터베이스 계정의 모든 컬렉션에 대해 서버 쪽 다시 시도를 **사용하지 않도록 설정** 합니다. 이 변경 사항이 적용되려면 최대 15분이 소요될 수 있습니다.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
   ```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-are-requests-retried"></a>요청을 다시 시도하는 방법

60초의 시간 제한에 도달할 때까지 요청은 계속해서 다시 시도됩니다. 제한 시간에 도달하면 클라이언트는 [ExceededTimeLimit 예외(50)](error-codes-solutions.md)를 수신합니다.

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>서버 쪽 다시 시도의 영향을 모니터링하려면 어떻게 해야 하나요?

Cosmos DB 메트릭 창에서 서버 쪽에서 다시 시도된 속도 제한 오류(429)를 볼 수 있습니다. 이러한 오류는 처리되고 서버 쪽에서 다시 시도되므로 SSR을 사용하는 경우 클라이언트로 이동하지 않습니다.

[Cosmos DB 리소스 로그](../cosmosdb-monitor-resource-logs.md)에서 *estimatedDelayFromRateLimitingInMilliseconds* 가 포함된 로그 항목을 검색할 수 있습니다.

### <a name="will-server-side-retry-affect-my-consistency-level"></a>서버 쪽 다시 시도가 일관성 수준에 영향을 주나요?

서버 쪽 다시 시도는 요청의 일관성에 영향을 주지 않습니다. 요청 수가 속도 제한(429 오류 포함)이면 서버 쪽에서 다시 시도됩니다.

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>서버 쪽 다시 시도는 클라이언트가 받을 수 있는 오류 형식에 영향을 주나요?

아니요, 서버 쪽 다시 시도는 서버 쪽에서 다시 시도하여 속도 제한 오류(429)에만 영향을 미칩니다. 이 기능을 통해 클라이언트 애플리케이션에서 속도 제한 오류를 처리할 필요가 없습니다. 모든 [기타 오류](error-codes-solutions.md)는 클라이언트로 이동합니다.

## <a name="next-steps"></a>다음 단계

일반적인 오류 문제 해결에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Mongo DB용 Azure Cosmos DB의 API에서 일반적으로 발생하는 문제 해결](error-codes-solutions.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
