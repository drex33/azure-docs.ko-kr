---
title: Visual Studio Code를 사용하여 Azure Functions를 Azure Cosmos DB에 연결
description: 출력 바인딩을 Visual Studio Code 프로젝트에 추가하여 Azure Functions를 Azure Cosmos DB 계정에 연결하는 방법을 알아봅니다.
author: ThomasWeiss
ms.date: 08/17/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 9fbc67f14d6d67393b62129f2b4e9269fb6c6f52
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446269"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure Functions를 Azure Cosmos DB에 연결

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

이 문서에서는 Visual Studio Code를 사용하여 [Azure Cosmos DB](../cosmos-db/introduction.md)를 이전 빠른 시작 문서에서 만든 함수에 연결하는 방법을 보여 줍니다. 이 함수에 추가되는 출력 바인딩은 HTTP 요청의 데이터를 Azure Cosmos DB 컨테이너에 저장된 JSON 문서에 씁니다. 

::: zone pivot="programming-language-csharp"
시작하기 전에 [빠른 시작: Visual Studio Code를 사용하여 Azure에서 C# 함수 만들기](create-first-function-vs-code-csharp.md)를 완료해야 합니다. 해당 문서의 끝에서 리소스를 이미 정리한 경우, 해당 단계를 다시 수행하여 Azure에서 함수 앱 및 관련 리소스를 다시 만듭니다.
::: zone-end
::: zone pivot="programming-language-javascript"  
시작하기 전에 [빠른 시작: Visual Studio Code를 사용하여 Azure에서 JavaScript 함수 만들기](create-first-function-vs-code-node.md)를 완료해야 합니다. 해당 문서의 끝에서 리소스를 이미 정리한 경우, 해당 단계를 다시 수행하여 Azure에서 함수 앱 및 관련 리소스를 다시 만듭니다.  
::: zone-end

## <a name="configure-your-environment"></a>환경 구성

시작하기 전에 Visual Studio Code용 [Azure 데이터베이스 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)을 설치해야 합니다.

## <a name="create-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

> [!IMPORTANT]
> 이제 [Azure Cosmos DB 서버리스](../cosmos-db/serverless.md)가 일반 공급됩니다. 이 사용량 기반 모드에서는 Azure Cosmos DB를 서버리스 워크로드에 대한 강력한 옵션으로 만듭니다. Azure Cosmos DB를 서버리스 모드로 사용하려면 계정을 만들 때 **용량 모드** 로 **서버리스** 를 선택합니다.

1. Visual Studio Code에서, [이전 문서](./create-first-function-vs-code-csharp.md)에서 함수 앱을 만든 Azure 구독을 마우스 오른쪽 단추로 클릭하고 **서버 만들기...** 를 선택합니다.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-account.png" alt-text="Visual Studio Code에서 새 Azure Cosmos DB 계정 만들기" border="true":::

1. 프롬프트에서 다음 정보를 제공합니다.

    + **Azure 데이터베이스 서버 선택**: SQL 구문을 사용하여 쿼리할 수 있는 문서 데이터베이스를 만들려면 `Core (SQL)`을 선택합니다. [Azure Cosmos DB SQL API](../cosmos-db/introduction.md)에 대해 자세히 알아보세요.

    + **계정 이름**: Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다. 계정 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-31자여야 합니다.

    + **용량 모델 선택**: [서버리스](../cosmos-db/serverless.md) 모드로 계정을 만들려면 **서버리스** 를 선택합니다. **프로비저닝된 처리량** 을 선택하여 [프로비저닝된 처리량](../cosmos-db/set-throughput.md) 모드에서 계정을 만듭니다. Azure Cosmos DB를 시작하는 경우 **서버리스** 를 선택하는 것이 좋습니다.

    + **새 리소스에 대한 리소스 그룹 선택**: [이전 문서](./create-first-function-vs-code-csharp.md)에서 함수 앱을 만든 리소스 그룹을 선택합니다.

    + **새 리소스의 위치 선택**: Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빠르게 액세스하려면 사용자에게 가장 가까운 위치를 사용합니다.

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Azure Cosmos DB 데이터베이스 및 컨테이너 만들기

1. 새 Azure Cosmos DB 계정이 만들어지면 해당 이름을 마우스 오른쪽 단추로 클릭하고 **데이터베이스 만들기...** 를 선택합니다.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-database.png" alt-text="Visual Studio Code에서 새 Azure Cosmos DB 데이터베이스 만들기" border="true":::

1. 메시지가 표시되면 `my-database`를 **데이터베이스 이름** 으로 입력합니다.

1. 데이터베이스가 만들어지면 해당 이름을 마우스 오른쪽 단추로 클릭하고 **컬렉션 만들기...** 를 선택합니다.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Visual Studio Code에서 새 Azure Cosmos DB 컨테이너 만들기" border="true":::

1. 프롬프트에서 다음 정보를 제공합니다.

    + **컬렉션의 ID 입력**: `my-container`

    + **컬렉션의 [파티션 키](../cosmos-db/partitioning-overview.md) 입력**: `id`

## <a name="update-your-function-app-settings"></a>함수 앱 설정 업데이트

[이전 빠른 시작 문서](./create-first-function-vs-code-csharp.md)에서는 Azure에서 함수 앱을 만들었습니다. 이 문서에서는 JSON 문서를 위에서 만든 Azure Cosmos DB 컨테이너에 쓰도록 함수 앱을 업데이트합니다. Azure Cosmos DB 계정에 연결하려면 연결 문자열을 앱 설정에 추가해야 합니다. 그런 다음, 새 설정을 local.settings.json 파일에 다운로드하여 로컬로 실행할 때 Azure Cosmos DB 계정에 연결할 수 있습니다.

1. Visual Studio Code에서 Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭하고 **연결 문자열 복사** 를 선택합니다.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Azure Cosmos DB 연결 문자열 복사" border="true":::

1. <kbd>F1</kbd> 키를 눌러 명령 팔레트를 연 다음, `Azure Functions: Add New Setting...` 명령을 검색하여 실행합니다.

1. 이전 문서에서 만든 함수 앱을 선택합니다. 프롬프트에서 다음 정보를 제공합니다.

    + **새 앱 설정 이름 입력**: `CosmosDbConnectionString`을 입력합니다.

    + **"CosmosDbConnectionString" 값 입력**: 앞에서 복사한 Azure Cosmos DB 계정의 연결 문자열을 붙여넣습니다.

1. <kbd>F1</kbd> 키를 다시 눌러 명령 팔레트를 연 다음, `Azure Functions: Download Remote Settings...` 명령을 검색하여 실행합니다. 

1. 이전 문서에서 만든 함수 앱을 선택합니다. **모두 예** 를 선택하여 기존 로컬 설정을 덮어씁니다. 

## <a name="register-binding-extensions"></a>바인딩 확장 등록

Azure Cosmos DB 출력 바인딩을 사용하므로 프로젝트를 실행하기 전에 해당 바인딩 확장이 설치되어 있어야 합니다. 

::: zone pivot="programming-language-csharp"

HTTP 및 타이머 트리거를 제외하고 바인딩은 확장 패키지로 구현됩니다. 터미널 창에서 다음 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 실행하여 프로젝트에 스토리지 확장 패키지를 추가합니다.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

프로젝트가 미리 정의된 확장 패키지 세트를 자동으로 설치하는 [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하도록 구성되었습니다. 

확장 번들 사용은 프로젝트의 루트에 있는 host.json 파일에서 사용하도록 설정되며, 다음과 같이 표시됩니다.

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

이제 Azure Cosmos DB 출력 바인딩을 프로젝트에 추가할 수 있습니다.

## <a name="add-an-output-binding"></a>출력 바인딩 추가

Functions에서 각 바인딩 형식의 `direction`, `type` 및 고유한 `name`을 function.json 파일에 정의해야 합니다. 이러한 특성을 정의하는 방법은 함수 앱의 언어에 따라 달라집니다.

::: zone pivot="programming-language-csharp"

C# 클래스 라이브러리 프로젝트에서 바인딩은 함수 메서드의 바인딩 특성으로 정의됩니다. 그런 다음, 함수에 필요한 *function.json* 파일이 이러한 특성을 기반으로 자동으로 생성됩니다.

*HttpExample.cs* 프로젝트 파일을 열고 다음 매개 변수를 `Run` 메서드 정의에 추가합니다.

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut` 매개 변수는 `IAsyncCollector<T>` 형식이며, 함수가 완료되면 Azure Cosmos DB 컨테이너에 기록될 JSON 문서 컬렉션을 나타냅니다. 특정 특성은 컨테이너의 이름 및 해당 부모 데이터베이스의 이름을 지정합니다. Azure Cosmos DB 계정에 대한 연결 문자열은 `ConnectionStringSettingAttribute`에서 설정됩니다.

Run 메서드 정의는 이제 다음과 같이 표시됩니다.  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

바인딩 특성은 function.json 파일에서 직접 정의됩니다. 바인딩 형식에 따라 추가 속성이 필요할 수 있습니다. [Azure Cosmos DB 출력 구성](./functions-bindings-cosmosdb-v2-output.md#configuration)에서는 Azure Cosmos DB 출력 바인딩에 필요한 필드를 설명합니다. 확장을 통해 바인딩을 function.json 파일에 쉽게 추가할 수 있습니다. 

바인딩을 만들려면 HttpTrigger 폴더의 `function.json` 파일을 마우스 오른쪽 단추로 클릭하고(macOS에서 Ctrl+ 클릭) **바인딩 추가...** 를 선택합니다. 프롬프트에 따라 새 바인딩에 대해 다음 바인딩 속성을 정의합니다.

| prompt | 값 | 설명 |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **"out" 방향의 바인딩 선택** | `Azure Cosmos DB` | 바인딩은 Azure Cosmos DB 바인딩입니다. |
| **코드에서 이 바인딩을 식별하는 데 사용하는 이름** | `outputDocument` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **데이터를 쓸 Cosmos DB 데이터베이스** | `my-database` | 대상 컨테이너가 포함된 Azure Cosmos DB 데이터베이스의 이름입니다. |
| **데이터를 쓸 데이터베이스 컬렉션** | `my-container` | JSON 문서가 쓰이는 Azure Cosmos DB 컨테이너의 이름입니다. |
| **true이면 Cosmos DB 데이터베이스 및 컬렉션을 만듭니다.** | `false` | 대상 데이터베이스와 컨테이너가 이미 있습니다. |
| **"local.setting.json"에서 설정 선택** | `CosmosDbConnectionString` | Azure Cosmos DB 계정에 대한 연결 문자열이 포함된 애플리케이션 설정의 이름입니다. |
| **파티션 키(선택 사항)** | *비워 둠* | 출력 바인딩에서 컨테이너를 만드는 경우에만 필요합니다. |
| **컬렉션 처리량(선택 사항)** | *비워 둠* | 출력 바인딩에서 컨테이너를 만드는 경우에만 필요합니다. |

바인딩은 function.json의 `bindings` 배열에 추가되며, 다음과 같이 표시됩니다.

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

::: zone pivot="programming-language-csharp"  

`documentsOut` 출력 바인딩 개체를 사용하여 JSON 문서를 만드는 코드를 추가합니다. 메서드가 반환되기 전에 이 코드를 추가합니다.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

이 시점에서 함수는 다음과 같이 표시됩니다.

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

`context.bindings`에서 `outputDocument` 출력 바인딩 개체를 사용하여 JSON 문서를 만드는 코드를 추가합니다. `context.res` 문 앞에 이 코드를 추가합니다.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

이 시점에서 함수는 다음과 같이 표시됩니다.

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. 이전 문서에서와 같이 <kbd>F5</kbd>를 눌러 함수 앱 프로젝트와 Core Tools를 시작합니다. 

1. Core Tools가 실행 중인 상태에서 **Azure: Functions** 영역으로 이동합니다. **Functions** 에서 **로컬 프로젝트** > **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Mac에서는 Ctrl 클릭)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code에서 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  
 
1. 응답이 반환된 후 <kbd>Ctrl + C</kbd>를 눌러 Core Tools를 중지합니다.

### <a name="verify-that-a-json-document-has-been-created"></a>JSON 문서가 만들어졌는지 확인

1. Azure Portal에서 Azure Cosmos DB 계정으로 돌아가서 **데이터 탐색기** 를 선택합니다.

1. 데이터베이스와 컨테이너를 펼치고, **항목** 을 선택하여 컨테이너에 만든 문서를 나열합니다.

1. 출력 바인딩에서 새 JSON 문서가 만들어졌는지 확인합니다.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="새 문서가 Azure Cosmos DB 컨테이너에 만들어졌는지 확인" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>업데이트된 앱 다시 배포 및 확인

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Deploy to function app...`을 검색하여 선택합니다.

1. 처음 문서에서 만든 함수 앱을 선택합니다. 동일한 앱에 프로젝트를 다시 배포하므로 **배포** 를 선택하여 파일 덮어쓰기에 대한 경고를 해제합니다.

1. 배포가 완료되면 **지금 함수 실행...** 기능을 사용하여 Azure에서 함수를 트리거할 수 있습니다.

1. [Azure Cosmos DB 컨테이너에 만든 문서를 다시 확인](#verify-that-a-json-document-has-been-created)하여 출력 바인딩에서 새 JSON 문서를 생성하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 *리소스* 는 함수 앱, 함수, 스토리지 계정 등을 참조합니다. 리소스는 *리소스 그룹* 으로 그룹화되며 그룹을 삭제하면 그룹의 모든 항목을 삭제할 수 있습니다.

이러한 빠른 시작을 완료하기 위해 리소스를 만들었습니다. [계정 상태](https://azure.microsoft.com/account/) 및 [서비스 가격 책정](https://azure.microsoft.com/pricing/)에 따라 리소스에 대해 요금이 청구될 수 있습니다. 리소스가 더 이상 필요하지 않게 되면 다음과 같이 삭제합니다.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>다음 단계

JSON 문서를 Azure Cosmos DB 컨테이너에 쓰도록 HTTP 트리거 함수를 업데이트했습니다. 이제 Visual Studio Code를 사용하여 함수 개발에 대해 자세히 알아봅니다.

+ [Visual Studio Code를 사용하여 Azure Functions 개발](functions-develop-vs-code.md)

+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [C# 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=csharp)

+ [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript 개발자 가이드](functions-reference-node.md)  
::: zone-end  