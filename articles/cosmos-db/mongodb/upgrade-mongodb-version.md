---
title: Azure Cosmos DB의 API for MongoDB 계정의 Mongo 버전 업그레이드
description: 기존 Azure Cosmos DB의 API for MongoDB 계정의 MongoDB 쓰기 프로토콜 버전을 원활하게 업그레이드하는 방법
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: a90a5d7ad7696844f735a1bed2f618846758fff3
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035813"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Azure Cosmos DB의 API for MongoDB 계정의 API 버전 업그레이드
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB의 API for MongoDB 계정의 API 버전을 업그레이드하는 방법을 설명합니다. 업그레이드한 후 Azure Cosmos DB의 API for MongoDB에서 최신 기능을 사용할 수 있습니다. 업그레이드 프로세스는 계정의 가용성을 중단하지 않으며 언제든지 RU/s를 사용하거나 데이터베이스의 용량을 줄이지 않습니다. 기존 데이터나 인덱스는 이 프로세스의 영향을 받지 않습니다. 

새 API 버전으로 업그레이드하는 경우 프로덕션 워크로드를 업그레이드하기 전에 개발/테스트 워크로드로 시작합니다. Azure Cosmos DB API for MongoDB 계정을 업그레이드하기 전에 업그레이드하는 API 버전과 호환되는 버전으로 클라이언트를 업그레이드하는 것이 중요합니다.

>[!Note]
> 지금은 서버 버전 3.2를 사용하는 한정 계정만 버전 3.6 또는 4.0으로 업그레이드할 수 있습니다. 계정에 업그레이드 옵션이 표시되지 않으면 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하십시오.

## <a name="upgrading-to-40-or-36"></a>4\.0 또는 3.6으로 업그레이드

### <a name="benefits-of-upgrading-to-version-40"></a>버전 4.0으로 업그레이드 시 이점

다음은 버전 4.0에 포함된 새로운 기능입니다.
- Unsharded 컬렉션 내의 다중 문서 트랜잭션 지원
- 새 집계 연산자
- 향상된 검사 성능
- 더 빠르고 효율적인 스토리지

### <a name="benefits-of-upgrading-to-version-36"></a>버전 3.6으로 업그레이드 시 이점

다음은 버전 3.6에 포함된 새로운 기능입니다.
- 성능 및 안정성 향상
- 새 데이터베이스 명령에 대한 지원
- 집계 파이프라인에 대한 기본 지원 및 새 집계 단계 지원
- 변경 스트림 지원
- 복합 인덱스 지원
- update, delete, count 및 sort 작업에 대한 파티션 간 지원
- $count, $skip, $limit 및 $group 집계 작업의 성능이 향상되었습니다.
- 이제 와일드카드 인덱싱이 지원됩니다.

### <a name="changes-from-version-32"></a>버전 3.2 이후의 변경 내용

- 기본적으로 클라이언트 애플리케이션의 요청에서 16500 오류를 반환하지 않도록 [SSR(서버 쪽 다시 시도)](prevent-rate-limiting-errors.md) 기능을 사용하도록 설정되어 있습니다. 대신 요청이 완료되거나 60초 시간 초과될 때까지 다시 시작합니다.
- 요청당 시간 제한은 60초로 설정됩니다.
- 새 유선 프로토콜 버전에 만들어진 MongoDB 컬렉션에는 기본적으로 인덱싱된 `_id` 속성만 있습니다.

### <a name="action-required-when-upgrading-from-32"></a>3\.2에서 업그레이드하는 경우 필요한 작업

3\.2에서 업그레이드하는 경우 데이터베이스 계정 엔드포인트 접미사가 다음 형식으로 업데이트됩니다.

```
<your_database_account_name>.mongo.cosmos.azure.com
```

버전 3.2에서 업그레이드하는 경우 애플리케이션의 기존 엔드포인트와 이 데이터베이스 계정에 연결하는 드라이버를 바꿔야 합니다. **새 엔드포인트를 사용하는 연결에만 새 API 버전의 기능에 대한 액세스 권한이 있습니다**. 이전의 3.2 엔드포인트에는 `.documents.azure.com` 접미사가 있어야 합니다.

>[!Note]
> 엔드포인트는 계정이 소버린, 정부 또는 제한된 Azure Cloud에 있는 경우 약간 다를 수 있습니다.

## <a name="how-to-upgrade"></a>업그레이드하는 방법

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos DB API for MongoDB 계정으로 이동합니다. **개요** 창을 열고 현재 **서버 버전** 이 3.2 또는 3.6인지 확인합니다.

    :::image type="content" source="./media/upgrade-mongodb-version/check-current-version.png" alt-text="Azure Portal에서 MongoDB 계정의 현재 버전을 확인합니다." border="true":::

1. 왼쪽 메뉴에서 `Features` 창을 엽니다. 이 창에는 데이터베이스 계정에 사용할 수 있는 계정 수준의 기능이 표시됩니다.

1. `Upgrade MongoDB server version` 행을 선택합니다. 이 옵션이 표시되지 않으면 계정이 업그레이드에 적합하지 않은 것일 수 있습니다. 해당하는 경우 [지원 티켓](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출합니다.

    :::image type="content" source="./media/upgrade-mongodb-version/upgrade-server-version.png" alt-text="기능 블레이드를 열고 계정을 업그레이드합니다." border="true":::

1. 업그레이드에 대해 표시된 정보를 검토합니다. `Set server version to 4.0`(또는 현재 버전에 따라 3.6)를 선택합니다.

    :::image type="content" source="./media/upgrade-mongodb-version/select-upgrade.png" alt-text="업그레이드 지침을 검토하고 업그레이드를 선택합니다." border="true":::

1. 업그레이드를 시작하면 **기능** 메뉴가 회색으로 표시되고 상태가 *보류 중* 으로 설정됩니다. 업그레이드를 완료하는 데는 약 15분이 걸립니다. 이 프로세스는 데이터베이스 계정의 기존 기능이나 작업에는 영향을 주지 않습니다. 완료되면 **MongoDB 서버 버전 업데이트** 상태에 업그레이드된 버전이 표시됩니다. 요청을 처리하는 동안 문제가 발생한 경우 [고객 지원에 문의하세요](https://azure.microsoft.com/support/create-ticket/).

1. 계정을 업그레이드한 후에는 다음과 같은 몇 가지 사항을 고려해야 합니다.

    1. 3\.2에서 업그레이드한 경우 **개요** 창으로 돌아가서 애플리케이션에서 사용할 새 연결 문자열을 복사합니다. 3\.2를 실행하는 이전 연결 문자열은 중단되지 않습니다. 일관된 환경을 보장하기 위해 모든 애플리케이션은 새 엔드포인트를 사용해야 합니다.

    1. 3\.6에서 업그레이드한 경우 기존 연결 문자열은 지정된 버전으로 업그레이드되며 계속 사용되어야 합니다.

## <a name="how-to-downgrade"></a>다운그레이드 방법

‘업그레이드 방법’ 섹션의 동일한 단계를 통해 계정을 4.0에서 3.6로 다운그레이드할 수도 있습니다.

3\.2에서 4.0 또는 3.6으로 업그레이드한 상태에서 다시 3.2로 다운그레이드하려는 경우 이전 3.2 연결 문자열을 호스트 `accountname.documents.azure.com`으로 사용하기만 하면 다시 전환됩니다. 이렇게 하면 활성 업그레이드 후 버전 3.2이 계속 실행됩니다.

## <a name="next-steps"></a>다음 단계

- [MongoDB 버전 4.0](feature-support-40.md)의 지원되는 기능 및 지원되지 않는 기능에 대해 알아봅니다.
- [MongoDB 버전 3.6](feature-support-36.md)의 지원되는 기능 및 지원되지 않는 기능에 대해 알아봅니다.
- 자세한 내용은 [Mongo 3.6 버전 기능](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)을 확인하세요.
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
