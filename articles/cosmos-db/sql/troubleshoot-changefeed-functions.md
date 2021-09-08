---
title: Cosmos DB용 Azure Functions 트리거를 사용할 때의 문제 해결
description: Cosmos DB용 Azure Functions 트리거를 사용할 때 일반적인 문제, 해결, 진단 단계를 설명합니다
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b293a0208ad3f520a6cb72010601aa8a019c28ec
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117069"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB용 Azure Functions 트리거를 사용할 때 문제를 진단하고 해결하는 방법
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 [Cosmos DB용 Azure Functions 트리거](change-feed-functions.md)를 사용할 때 일반적인 문제, 해결 및 진단 단계를 설명합니다.

## <a name="dependencies"></a>종속성

Cosmos DB용 Azure Functions 트리거와 바인딩은 기본 Azure Functions 런타임에 대한 확장 패키지에 종속됩니다. 발생할 수 있는 잠재적인 문제를 해결할 수 있는 수정 사항 및 새로운 기능을 포함할 수 있으므로 항상 이러한 패키지를 업데이트된 상태로 유지합니다.

* Azure Functions v2의 경우 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)를 참조하세요.
* Azure Functions V1의 경우 [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)를 참조하세요.

이 문서는 명시적으로 지정되지 않은 경우, 런타임이 언급될 때마다 항상 Azure Functions V2를 참조합니다.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>독립적으로 Azure Cosmos DB SDK 사용

확장 패키지의 핵심 기능은 Cosmos DB용 Azure Functions 트리거와 바인딩을 지원하는 것입니다. 여기에는 트리거와 바인딩을 사용하지 않고 프로그래밍 방식으로 Azure Cosmos DB와 상호작용하려는 경우에 유용한 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)도 포함되어 있습니다.

Azure Cosmos DB SDK를 사용하려면 다른 NuGet 패키지 참조를 프로젝트에 추가하지 않아야 합니다. 대신 **Azure Functions의 확장 패키지를 통해 SDK 참조를 확인하도록 합니다**. 트리거 및 바인딩과 별도로 Azure Cosmos DB SDK 사용

또한 [AZURE COSMOS DB SDK 클라이언트](./sql-api-sdk-dotnet-core.md)의 고유한 인스턴스를 수동으로 생성하는 경우, [싱글톤 패턴 접근 방법을 사용하여](../../azure-functions/manage-connections.md?tabs=csharp#azure-cosmos-db-clients)클라이언트의 인스턴스를 하나만 포함하는 패턴을 따라야 합니다. 이 프로세스를 수행하면 작업에서 발생할 수 있는 소켓 문제가 발생하지 않습니다.

## <a name="common-scenarios-and-workarounds"></a>일반적인 시나리오 및 해결 방법

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>오류 메시지 컬렉션이 존재하지 않아 Azure Function이 실패함

Azure Function이 실패하고 "원본 컬렉션 'collection-name'(데이터베이스 ' database-name') 또는 임대 컬렉션 'collection2-name'(데이터베이스 'database2-name')이 없습니다. 두 컬렉션 모두 수신기가 시작되기 전에 존재해야 합니다. 임대 컬렉션을 자동으로 생성하려면 'CreateLeaseCollectionIfNotExists'를 'true 로 설정합니다.”라는 메시지가 표시됩니다

즉, 트리거가 작동하는 데 필요한 Azure Cosmos 컨테이너 중 하나 또는 모두 다 존재하지 않거나 Azure Function에 연결할 수 없다는 것을 의미합니다. **오류는 구성에 따라 찾고 있는 트리거인 Azure Cosmos 데이터베이스 및 컨테이너를 알려줍니다**.

1. `ConnectionStringSetting` 특성을 확인하고 **Azure Function App에 있는 설정을 참조합니다**. 이 특성의 값은 연결 문자열 자체가 아니라 구성 설정의 이름이어야 합니다.
2. `databaseName` 및 `collectionName`가 Azure Cosmos 계정에 있는지 확인합니다. 자동 값 바꾸기(`%settingName%` 패턴 사용)를 사용하는 경우, 설정의 이름이 Azure Function App에 있는지 확인합니다.
3. `LeaseCollectionName/leaseCollectionName`을 지정하지 않으면 기본값은 "임대"입니다. 그러한 컨테이너가 있는지 확인합니다. 필요에 따라 트리거의 `CreateLeaseCollectionIfNotExists` 특성을 `true`로 설정하여 자동으로 생성할 수 있습니다.
4. Azure [Cosmos 계정의 방화벽 구성](../how-to-configure-firewall.md)을 확인하여 Azure Function을 차단하지 않는지 확인합니다.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function이 "공유 처리량 컬렉션에 파티션 키가 있어야 합니다"로 인해 시작하지 못함

이전 버전의 Azure Cosmos DB 확장은 [공유 처리량 데이터베이스](../set-throughput.md#set-throughput-on-a-database)내에 생성된 임대 컨테이너 사용을 지원하지 않습니다. 이 문제를 해결하려면 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 확장을 업데이트하여 최신 버전을 가져옵니다.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure Function이 "이 작업에 PartitionKey를 제공해야 합니다."로 인해 시작하지 못합니다.

이 오류는 현재 이전 [확장 종속성](#dependencies)이 있는 분할된 임대 컬렉션을 사용하고 있음을 의미합니다. 사용 가능한 최신 버전으로 업그레이드합니다. Azure Functions V1에서 실행하는 경우, Azure Functions V2로 업그레이드해야 합니다.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Function이 "임대 컬렉션(분할된 경우)은 ID와 동일한 파티션 키를 가져야 합니다."로 인해 시작하지 못합니다.

이 오류는 현재 임대 컨테이너가 분할되었지만 파티션 키 경로가 `/id`이 아니라는 것을 의미합니다. 이 문제를 해결하려면 `/id`을 파티션 키로 사용하여 임대 컨테이너를 다시 생성해야 합니다.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>트리거를 실행하려고 할 때 Azure Functions 로그에서 "값은 null일 수 없습니다. 트리거에서 실행을 시도할 때 Azure Functions 로그에 매개 변수 이름: o"가 표시되는 경우

이 문제는 Azure Portal를 사용하고 있으며 트리거를 사용하는 Azure Function을 검사하려고 화면에서 **실행** 단추를 선택하려고 할 때 나타납니다. 트리거를 시작하기 위해 실행을 선택할 필요는 없으며, Azure Function을 배포할 때 자동으로 시작됩니다. Azure Portal에서 Azure Function의 로그 스트림을 확인하려면 모니터링된 컨테이너로 이동하여 일부 새 항목을 삽입하면 트리거가 자동으로 실행되는 것을 볼 수 있습니다.

### <a name="my-changes-take-too-long-to-be-received"></a>변경 내용을 수신하는 데 너무 오래 걸립니다

이 시나리오에는 여러 가지원인이 있을 수 있으며 이를 모두 확인해야 합니다.

1. Azure 함수가 Azure Cosmos 계정과 동일한 지역에 배포되나요? 네트워크 대기 시간을 최적화하려면 Azure 함수와 Azure Cosmos 계정이 동일한 Azure 지역에 공동 배치되어야 합니다.
2. Azure Cosmos 컨테이너가 연속적으로 변경되나요? 간헐적으로 변경되나요?
후자인 경우 저장되는 변경 내용과 변경 내용을 선택하는 Azure 함수 사이에 약간의 지연이 있을 수 있습니다. 이는 내부적으로 트리거가 Azure Cosmos 컨테이너에서 변경 내용을 확인하여 읽기 보류 중인 항목이 없으면 새 변경 내용을 확인하기 전에 높은 RU 사용을 방지하기 위해 구성 가능한 시간(기본값: 5초) 동안 일시 중지하기 때문입니다. 트리거의 [구성](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).
3. Azure Cosmos 컨테이너는 [속도가 제한](../request-units.md)될 수 있습니다.
4. 트리거의 `PreferredLocations` 특성을 사용하여 사용자 지정 기본 연결 순서를 정의하기 위해 쉼표로 구분된 Azure 지역 목록을 지정할 수 있습니다.

### <a name="some-changes-are-repeated-in-my-trigger"></a>일부 변경 내용은 내 트리거에서 반복됩니다

"변경"의 개념은 문서에 대한 작업입니다. 동일한 문서에 대한 이벤트를 수신하는 가장 일반적인 시나리오는 다음과 같습니다.
* 계정이 최종 일관성을 사용하고 있습니다. 최종 일관성 수준에서 변경 피드를 사용하는 동안 후속 변경 피드 읽기 작업 간에 중복된 이벤트가 있을 수 있습니다(하나의 읽기 작업의 마지막 이벤트는 다음 중 첫 번째 작업으로 표시됨).
* 문서가 업데이트되고 있습니다. 변경 피드에는 동일한 문서에 대한 여러 작업이 포함될 수 있습니다. 해당 문서가 업데이트를 수신하는 경우 여러 이벤트(업데이트 마다 하나씩)를 선택할 수 있습니다. 같은 문서에 대해 서로 다른 작업을 구분하는 한 가지 쉬운 방법은 `_lsn` [각 변경에 대한 속성](../change-feed.md#change-feed-and-_etag-_lsn-or-_ts)을 추적하는 것입니다. 일치하지 않는 경우, 이는 동일한 문서에 대한 서로 다른 변경입니다.
* `id`을 사용하여 문서를 식별하는 경우 문서에 대한 고유 식별자는 `id` 및 해당 파티션 키(`id`이 동일하지만 다른 파티션 키를 사용하는 두 개의 문서가 있을 수 있음)임에 주의해야 합니다.

### <a name="some-changes-are-missing-in-my-trigger"></a>일부 변경 내용이 내 트리거에서 누락됨

Azure Cosmos 컨테이너에서 발생한 일부 변경 내용이 Azure Function에 의해 선택되지 않거나 대상에 일부 변경 내용이 없는 경우 아래 단계를 수행하세요.

Azure Function은 변경 내용을 받으면 자주 처리하고, 선택적으로 결과를 다른 대상으로 보낼 수 있습니다. 누락된 변경 내용을 조사하는 경우, **대상이 아니라 수집 지점에서 수신되는 변경 내용**(Azure Function이 시작될 때)을 측정해야 합니다.

대상에서 일부 변경 내용이 누락된 경우, 변경 내용이 수신된 후 Azure Function 실행 중에 일부 오류가 발생한다는 의미일 수 있습니다.

이 시나리오에서 가장 좋은 작업은 코드 및 변경 내용을 처리할 수 있는 루프 내부에 `try/catch` 블록을 추가하여 항목의 특정 하위 집합에 대한 오류를 감지하고 그에 따라 처리하는 것입니다(추가 분석 또는 재시도를 위해 다른 스토리지로 보내기).

> [!NOTE]
> 코드를 실행하는 동안 처리되지 않은 예외가 발생하면 기본적으로 Cosmos DB에 대한 Azure Functions 트리거가 변경 내용 일괄 처리를 다시 시도하지 않습니다. 즉, 변경 내용이 대상에 도착하지 않은 이유는 처리에 실패했기 때문입니다.

대상이 다른 Cosmos 컨테이너이고 Upsert 작업을 수행하여 항목을 복사하는 경우, **모니터링되는 컨테이너와 대상 컨테이너의 파티션 키 정의가 동일한지 확인합니다**. Upsert 작업은 이러한 구성 차이로 인해 여러 원본 항목을 대상에 하나로 저장할 수 있습니다.

트리거에서 일부 변경 내용을 전혀 받지 못한 경우, 가장 일반적인 시나리오는 **실행 중인 다른 Azure Function이** 있는 것입니다. **정확히 동일한 구성**(동일한 모니터링 및 임대 컨테이너)을 가진 개발자 컴퓨터에서 로컬로 실행되는 Azure Function 또는 Azure에 배포된 다른 Azure Function이 있으며, 이 Azure Function이 의도한 Azure Function에서 처리할 것으로 예상되는 변경 내용의 하위 집합을 도용하고 있을 수 있습니다.

또한 실행 중인 Azure Function App 인스턴스의 개수를 알고 있는 경우, 시나리오의 유효성을 검사할 수 있습니다. 임대 컨테이너를 검사하고 내부의 임대 항목의 수를 센 경우, `Owner` 속성의 고유 값은 함수 앱 인스턴스의 수와 같아야 합니다. 알려진 Azure Function App 인스턴스보다 소유자가 더 많을 경우 추가 소유자는 변경 내용을 "도용하는" 소유자인 것을 의미합니다.

이 상황을 쉽게 해결할 수 있는 한 가지 방법은 새 값/다른 값을 사용하여 함수에 `LeaseCollectionPrefix/leaseCollectionPrefix`을 적용하거나 새 임대 컨테이너를 사용하여 테스트하는 것입니다.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>처음부터 컨테이너의 모든 항목을 다시 시작하고 다시 처리해야 합니다 
컨테이너의 모든 항목을 처음부터 다시 처리하려면 다음을 수행합니다.
1. 현재 실행 중인 Azure Function을 중지합니다. 
1. 임대 컬렉션의 문서를 삭제합니다(또는 삭제하고 다시 만들어 임대 컬렉션을 삭제 한 후 다시 생성합니다)
1. 함수에서 [StartFromBeginning](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger 특성을 true로 설정합니다. 
1. Azure Function을 다시 시작합니다. 이제는 처음부터 모든 변경 내용을 읽고 처리합니다. 

[StartFromBeginning](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)을 true로 설정하면 Azure Function은 현재 시간 대신 컬렉션의 기록 시작 부분에서 변경 내용 읽기를 시작하도록 지시합니다. 이는 이미 생성된 임대(즉, 임대 컬렉션에 있는 문서)가 없는 경우에만 작동합니다. 이미 생성된 임대가 있는 경우 이 속성을 true로 설정 해도 아무런 효과가 없습니다. 이 시나리오에서는 함수가 중지되었다가 다시 시작될 때 임대 컬렉션에 정의된 대로 마지막 검사점에서 읽기를 시작합니다. 처음부터 다시 처리하려면 위의 1-4 단계를 수행합니다.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>바인딩은 IReadOnlyList\<Document> 또는 JArray로만 수행할 수 있습니다

이 오류는 Azure Functions 프로젝트 또는 참조된 프로젝트에 [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies)에서 제공하는 버전과 다른 버전의 Azure Cosmos DB SDK에 대한 수동 NuGet 참조가 포함된 경우에 발생합니다.

이 상황을 해결하려면 추가된 수동 NuGet 참조를 제거하고 Azure Functions Cosmos DB 확장 패키지를 통해 Azure Cosmos DB SDK 참조를 확인하도록 합니다.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>변경 내용 검색에 대한 Azure Function의 폴링 간격 변경

위 [내용을 수신하는 데 너무 오래 걸립니다](#my-changes-take-too-long-to-be-received)에서 설명한대로, Azure function은 새 변경 내용을 확인(높은 RU 소모를 피하기 위해)하기 전에 구성 가능한 시간(기본적으로 5 초) 동안 대기합니다. 트리거의 [구성](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).

## <a name="next-steps"></a>다음 단계

* [Azure Functions에 대한 모니터링 사용](../../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 문제해결](./troubleshoot-dot-net-sdk.md)
