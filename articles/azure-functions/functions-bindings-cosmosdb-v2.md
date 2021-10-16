---
title: 함수 2.x 이상에 대 한 Azure Cosmos DB 바인딩
description: Azure Functions에서 Azure Cosmos DB 트리거 및 바인딩을 사용하는 방법을 파악합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: d0b97d65a9c543c6afd4ae8afa9e75bf37b9f7a3
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071210"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Functions 2.x 이상의 Azure Cosmos DB 트리거 및 바인딩 개요

> [!div class="op_single_selector" title1="사용할 Azure Functions 런타임 버전을 선택합니다. "]
> * [버전 1](functions-bindings-cosmosdb.md)
> * [버전 2 이상](functions-bindings-cosmosdb-v2.md)

이 문서 세트에서는 Azure Functions 2.x 이상에서 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Azure Cosmos DB에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

| 작업 | 형식 |
|---------|---------|
| Azure Cosmos DB 문서를 만들거나 수정할 때 함수 실행 | [트리거](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB 문서 읽기 | [입력 바인딩](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB 문서에 변경 내용 저장  |[출력 바인딩](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 이 참조는 [Azure Functions 버전 2.x 이상](functions-versions.md)에 대한 것입니다.  Functions 1.x에서 이러한 바인딩을 사용하는 방법에 대한 내용은 [Azure Functions 1.x에 대한 Azure Cosmos DB 바인딩](functions-bindings-cosmosdb.md)을 참조하세요.
>
> 이 바인딩의 원래 이름은 DocumentDB입니다. Functions 버전 2.x 이상에서는 트리거, 바인딩 및 패키지의 이름이 모두 Cosmos DB로 지정됩니다.

## <a name="supported-apis"></a>지원되는 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Cosmos DB 확장 4.x 이상

새 버전의 Cosmos DB 바인딩 확장은 미리 보기에서 사용할 수 있습니다. [비밀 대신 id를 사용 하 여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. .NET 응용 프로그램의 경우에는 바인딩할 수 있는 형식도 변경 하 여 v2 sdk의 형식을 `Microsoft.Azure.DocumentDB` V3 sdk의 최신 형식으로 바꿉니다. [Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md)을 참조 하세요. 이러한 새 형식에 대 한 자세한 내용과 [SDK 마이그레이션 가이드](../cosmos-db/sql/migrate-dotnet-v3.md), [트리거](./functions-bindings-cosmosdb-v2-trigger.md), [입력 바인딩](./functions-bindings-cosmosdb-v2-input.md)및 [출력 바인딩](./functions-bindings-cosmosdb-v2-output.md) 예제에서 이러한 형식의 마이그레이션 방법에 대해 자세히 알아보세요.

이 확장 버전은 [미리 보기 NuGet 패키지로]제공 됩니다. 자세히 알아보려면 [확장 업데이트]를 참조 하세요.

[NuGet 패키지 미리 보기]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview2

> [!NOTE]
> 현재, 4.x preview 확장을 사용 하는 암호 대신 id를 사용 하는 인증은 탄력적 Premium 계획에만 사용할 수 있습니다. 

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참고 |
|---|---|
| CosmosDB | [CosmosDB 오류 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|속성  |기본값 |Description |
|----------|--------|------------|
|GatewayMode|게이트웨이|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 모드입니다. 옵션은 `Direct` 및 `Gateway`입니다.|
|프로토콜|Https|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 프로토콜입니다. [두 모드에 대 한 설명을 보려면 여기를](../cosmos-db/performance-tips.md#networking)참조 하세요. <br><br> 이 설정은 [확장의 버전](#cosmos-db-extension-4x-and-higher)4.x에서 사용할 수 없습니다. |
|leasePrefix|해당 없음|앱의 모든 함수에서 사용할 접두사를 임대합니다. <br><br> 이 설정은 [확장의 버전](#cosmos-db-extension-4x-and-higher)4.x에서 사용할 수 없습니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 문서를 만들거나 수정할 때 함수 실행(트리거)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB 문서 읽기(입력 바인딩)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB 문서에 변경 내용 저장(출력 바인딩)](./functions-bindings-cosmosdb-v2-output.md)