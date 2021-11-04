---
title: Azure Functions 개발 지침
description: 프로그래밍 언어 및 바인딩에 관계 없이 Azure에서 함수를 개발하는 데 필요한 Azure Functions 개념 및 기술에 대해 알아봅니다.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: db0fc469d7f4429d8a99c5869940dfc50b63e845
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477063"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions 개발자 가이드
Azure Functions에서 특정 함수는 사용하는 언어나 바인딩에 관계없이 몇 가지 핵심적 기술 개념과 구성 요소를 공유합니다. 특정 언어나 바인딩에 해당하는 세부 정보를 학습하기 전에, 모든 항목에 해당하는 이 개요를 꼼꼼히 읽어 보시기 바랍니다.

이 문서에서는 [Azure Functions 개요](functions-overview.md)를 이미 읽었다고 가정합니다.

## <a name="function-code"></a>함수 코드
*함수* 는 Azure Functions의 기본 개념입니다. 함수에는 다양한 언어로 작성할 수 있는 코드와 일부 구성인 function.json 파일의 두 가지 중요한 부분이 포함되어 있습니다. 컴파일된 언어의 경우, 이 구성 파일은 코드의 주석에서 자동으로 생성됩니다. 스크립팅 언어의 경우 구성 파일을 직접 제공해야 합니다.

function.json 파일은 함수의 트리거, 바인딩 및 기타 구성 설정을 정의합니다. 모든 함수에 하나의 트리거만 있습니다. 런타임은 이 구성 파일을 사용하여 모니터링할 이벤트와 함수 실행에 데이터를 전달하고 반환하는 방법을 확인합니다. 다음은 function.json 파일의 예제입니다.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

`bindings` 속성은 트리거와 바인딩을 모두 구성하는 곳에 위치합니다. 각 바인딩은 몇 가지 공통적인 설정과 특정한 바인딩 형식에 해당하는 일부 설정을 공유합니다. 모든 바인딩에는 다음 설정이 필요합니다.

| 속성    | 값 | 유형 | 의견|
|---|---|---|---|
| type  | 바인딩의 이름입니다.<br><br>예들 들어 `queueTrigger`입니다. | 문자열 | |
| direction | `in`, `out`  | 문자열 | 함수 안으로 데이터를 수신할 바인딩인지 또는 함수의 데이터를 전송할 바인딩인지를 나타냅니다. |
| name | 함수 식별자입니다.<br><br>예들 들어 `myQueue`입니다. | 문자열 | 함수에서 바인딩 데이터에 사용되는 이름입니다. C#의 경우 인수 이름이며, JavaScript의 경우 키/값 목록의 키입니다. |

## <a name="function-app"></a>함수 앱
함수 앱은 함수가 실행되는 Azure의 실행 컨텍스트를 제공합니다. 함수에 대한 배포 및 관리 단위입니다. 함수 앱은 함께 관리, 배포 및 크기 조정되는 하나 이상의 개별 함수로 구성됩니다. 함수 앱의 모든 함수는 동일한 가격 책정 계획, 배포 방법 및 런타임 버전을 공유합니다. 함수 앱을 함수를 구성하고 전체적으로 관리하는 방법으로 생각합니다. 자세한 내용은 [함수 앱을 관리하는 방법](functions-how-to-use-azure-function-app-settings.md)을 참조하세요. 

> [!NOTE]
> 함수 앱의 모든 함수는 동일한 언어로 작성되어야 합니다. [이전 버전](functions-versions.md)의 Azure Functions 런타임에서는 이러한 요구 사항이 필요하지 않았습니다.

## <a name="folder-structure"></a>폴더 구조
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

위 그림은 함수 앱의 기본(및 권장) 폴더 구조입니다. 함수 코드의 파일 위치를 변경하려면 _function.json_ 파일의 `scriptFile` 섹션을 수정합니다. 또한 [패키지 배포](deployment-zip-push.md)를 사용하여 Azure의 함수 앱에 프로젝트를 배포하는 것이 좋습니다. [연속 통합 및 지속적인 배포](functions-continuous-deployment.md), Azure DevOps 등의 기존 도구를 사용할 수도 있습니다.

> [!NOTE]
> 패키지를 수동으로 배포하는 경우 _host.json_ 파일과 함수 폴더를 `wwwroot` 폴더에 직접 배포해야 합니다. 배포에 `wwwroot` 폴더를 포함하지 마세요. 그렇지 않으면 `wwwroot\wwwroot` 폴더가 만들어집니다.

#### <a name="use-local-tools-and-publishing"></a>로컬 도구 및 게시 사용
함수 앱은 [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) 및 [Azure Functions Core Tools](./functions-develop-local.md)를 비롯한 다양한 도구를 사용하여 작성하고 게시할 수 있습니다. 자세한 내용은 [Azure Functions를 로컬에서 코딩 및 테스트](./functions-develop-local.md)를 참조하세요.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Azure Portal에서 함수를 편집하는 방법
Azure Portal에 기본 제공된 함수 편집기를 사용하면 코드와 *function.json* 파일을 인라인에서 직접 업데이트할 수 있습니다. 이 작업은 사소한 변경이나 개념 증명에만 권장되며, VS Code와 같은 로컬 개발 도구를 사용하는 것이 좋습니다.

## <a name="parallel-execution"></a>병렬 실행
복수의 트리거 이벤트가 단일 스레드 함수 런타임이 해당 이벤트를 처리할 수 있는 속도보다 빨리 발생하면 런타임은 병렬 모드로 함수를 여러 번 호출할 수 있습니다.  함수 앱이 [소비 호스팅 계획](event-driven-scaling.md)을 사용하는 경우 함수 앱은 자동으로 확장할 수 있습니다.  앱이 소비 호스팅 계획 또는 일반 [App Service 계획](../app-service/overview-hosting-plans.md)에서 실행되는지 여부에 관계없이 함수 앱의 각 인스턴스는 여러 스레드를 사용하여 동시 함수 호출을 병렬로 처리할 수 있습니다.  각 함수 앱 인스턴스의 최대 동시 함수 호출 수는 함수 앱 내의 다른 함수에서 사용하는 리소스뿐만 아니라 사용 중인 트리거 유형에 따라 달라집니다.

## <a name="functions-runtime-versioning"></a>Functions 런타임 버전 관리

`FUNCTIONS_EXTENSION_VERSION` 앱 설정을 사용하여 Functions 런타임의 버전을 구성할 수 있습니다. 예를 들어 ‘~3’ 값은 함수 앱이 주 버전으로 3.x를 사용한다는 의미입니다. 함수 앱은 부 버전이 새로 릴리스될 때마다 업그레이드됩니다. 정확한 함수 앱 버전을 확인하는 방법을 비롯한 자세한 내용을 보려면 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

## <a name="repositories"></a>리포지토리
Azure Functions에 대한 코드는 공개 소스이며 GitHub 리포지토리에 저장됩니다.

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 호스트](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 포털](https://github.com/azure/azure-functions-ux)
* [Azure Functions 템플릿](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>바인딩
지원되는 모든 바인딩을 포함하는 테이블입니다.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

바인딩에서 들어오는 오류와 함께 문제가 있습니까? [Azure Functions 바인딩 오류 코드](functions-bindings-error-pages.md) 설명서를 참조하세요.


## <a name="connections"></a>Connections

함수 프로젝트는 구성 공급자의 이름별로 연결 정보를 참조합니다. 연결 세부 정보를 직접 허용하지 않으므로 환경 간에 변경될 수 있습니다. 예를 들어 트리거 정의에는 `connection` 속성이 포함될 수 있습니다. 이는 연결 문자열을 참조할 수 있지만 `function.json`에서 직접 연결 문자열을 설정할 수 없습니다. 대신 `connection`을 연결 문자열을 포함하는 환경 변수의 이름으로 설정합니다.

기본 구성 공급자는 환경 변수를 사용합니다. Azure Functions 서비스에서 실행 중일 때 [애플리케이션 설정](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings)에서, 또는 로컬에서 개발할 때 [로컬 설정 파일](functions-develop-local.md#local-settings-file)에서 설정할 수 있습니다.

### <a name="connection-values"></a>연결 값

연결 이름이 정확한 단일 값으로 확인되면 런타임은 일반적으로 비밀을 포함하는 _연결 문자열_ 로 값을 식별합니다. 연결 문자열의 세부 정보는 연결하려는 서비스에 의해 정의됩니다.

그러나 연결 이름은 ID 기반 연결을 구성하는 데 유용한 여러 구성 항목의 컬렉션을 참조할 수도 [있습니다.](#configure-an-identity-based-connection) 이중 밑줄(`__`)로 끝나는 공유 접두사를 사용하여 환경 변수를 컬렉션으로 처리할 수 있습니다. 그런 다음 연결 이름을 이 접두사로 설정하여 그룹을 참조할 수 있습니다.

예를 들어 `connection` Azure Blob 트리거 정의의 속성은 "Storage1"일 수 있습니다. "Storage1"이라는 환경 변수에 의해 구성된 단일 문자열 값이 없는 한 라는 환경 `Storage1__blobServiceUri` 변수를 사용하여 연결의 속성을 알릴 수 `blobServiceUri` 있습니다. 연결 속성은 각 서비스마다 다릅니다. 연결을 사용하는 구성 요소에 대한 설명서를 참조하세요.

### <a name="configure-an-identity-based-connection"></a>ID 기반 연결 구성

Azure Functions의 일부 연결은 비밀 대신 id를 사용 하도록 구성할 수 있습니다. 지원은 연결을 사용하는 확장에 따라 다릅니다. 경우에 따라 연결하는 서비스에서 ID 기반 연결을 지원하는 경우에도 Functions에서 연결 문자열이 필요할 수 있습니다. 관리 id를 사용 하 여 함수 앱을 구성 하는 방법에 대 한 자습서는 [id 기반 연결을 사용 하 여 함수 앱 만들기 자습서](./functions-identity-based-connections-tutorial.md)를 참조 하세요.

Id 기반 연결은 다음 구성 요소에서 지원 됩니다.

| 연결 원본                                       | 계획 지원 | 자세한 정보                                                                                                         |
|---------------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| Azure Blob 트리거 및 바인딩               | 모두             | [확장 버전 5.0.0 이상](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)     |
| Azure 큐 트리거 및 바인딩            | 모두             | [확장 버전 5.0.0 이상](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)    |
| Azure Event Hubs 트리거 및 바인딩     | 모두             | [확장 버전 5.0.0 이상](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)    |
| Azure Service Bus 트리거 및 바인딩       | 모두             | [확장 버전 5.0.0 이상](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)  |
| 트리거 및 바인딩 Azure Cosmos DB-미리 보기         | 탄력적 Premium | [확장 버전 4.0.0-tooling 이상](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) |
| 호스트-필요한 저장소 ("AzureWebJobsStorage")-미리 보기 | 모두             | [ID로 호스트 저장소에 연결](#connecting-to-host-storage-with-an-identity-preview)                        |

> [!NOTE]
> ID 기반 연결은 Durable Functions에서 지원되지 않습니다.

[!INCLUDE [functions-identity-based-connections-configuration](../../includes/functions-identity-based-connections-configuration.md)]

아래 탭을 선택 하 여 각 구성 요소에 대 한 사용 권한에 대해 알아보세요.

# <a name="azure-blobs-extension"></a>[Azure Blob 확장](#tab/blob)

[!INCLUDE [functions-blob-permissions](../../includes/functions-blob-permissions.md)]

# <a name="azure-queues-extension"></a>[Azure 큐 확장](#tab/queue)

[!INCLUDE [functions-queue-permissions](../../includes/functions-queue-permissions.md)]

# <a name="event-hubs-extension"></a>[Event Hubs 확장](#tab/eventhubs)

[!INCLUDE [functions-event-hubs-permissions](../../includes/functions-event-hubs-permissions.md)]

# <a name="service-bus-extension"></a>[Service Bus 확장](#tab/servicebus)

[!INCLUDE [functions-service-bus-permissions](../../includes/functions-service-bus-permissions.md)]

# <a name="azure-cosmos-db-extension-preview"></a>[Azure Cosmos DB 확장 (미리 보기)](#tab/cosmos)

[!INCLUDE [functions-cosmos-permissions](../../includes/functions-cosmos-permissions.md)]

# <a name="functions-host-storage-preview"></a>[함수 호스트 저장소 (미리 보기)](#tab/azurewebjobsstorage)

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

---

#### <a name="common-properties-for-identity-based-connections"></a>Id 기반 연결에 대 한 공용 속성

Azure 서비스에 대 한 id 기반 연결에는 다음과 같은 공통 속성이 적용 `<CONNECTION_NAME_PREFIX>` 됩니다. 여기서은 `connection` 트리거 또는 바인딩 정의의 속성 값입니다.

| 속성    |  환경 변수 템플릿 | Description |
|---|---|---|---|
| 토큰 자격 증명 |  `<CONNECTION_NAME_PREFIX>__credential` | 연결을 위해 토큰을 가져오는 방법을 정의합니다. "managedidentity"로 설정해야 하는 사용자가 할당한 ID를 지정할 때만 권장됩니다. 이는 Azure Functions 서비스에서 호스팅되는 경우에만 유효합니다. |
| 클라이언트 ID | `<CONNECTION_NAME_PREFIX>__clientId` | `credential`이 "managedidentity"로 설정된 경우 이 속성은 토큰을 가져올 때 사용할 사용자가 할당한 ID를 지정합니다. 속성은 애플리케이션에 할당된 사용자가 할당한 ID에 해당하는 클라이언트 ID를 허용합니다. 지정하지 않으면 시스템 할당 ID가 사용됩니다. 이 속성은 [이 설정되지 않아야 하는 ](#local-development-with-identity-based-connections)로컬 개발 시나리오`credential`에서 다르게 사용됩니다. |

지정된 연결 유형에 대해 추가 옵션이 지원될 수 있습니다. 연결을 구성하는 구성 요소에 대한 설명서를 참조하세요.

##### <a name="local-development-with-identity-based-connections"></a>ID 기반 연결을 사용하여 로컬 개발

> [!NOTE]
> Id 기반 연결을 사용 하는 로컬 개발에는 업데이트 된 버전의 [Azure Functions Core Tools](./functions-run-local.md)가 필요 합니다. 를 실행 하 여 현재 설치 된 버전을 확인할 수 있습니다 `func -v` . 함수 v3의 경우 버전 이상을 사용 `3.0.3904` 합니다. 함수 v4의 경우 버전 이상을 사용 `4.0.3904` 합니다. 

로컬로 실행하는 경우 위의 구성은 런타임에 로컬 개발자 ID를 사용하도록 지시합니다. 연결은 다음 위치에서 토큰을 순서대로 가져오려고 시도합니다.

- Microsoft 애플리케이션 간에 공유되는 로컬 캐시
- Visual Studio의 현재 사용자 컨텍스트
- Visual Studio Code의 현재 사용자 컨텍스트
- Azure CLI의 현재 사용자 컨텍스트

성공적인 옵션이 없는 경우 오류가 발생합니다.

이는 개발자 id를 사용 하기 때문에 개발 리소스에 대 한 일부 역할이 이미 있지만 데이터 액세스를 제공 하지 않을 수 있습니다. [Owner](../role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 각 구성 요소에 대 한 연결에 필요한 사용 권한을 다시 확인 하 고 자신에 게 자신을 할당 했는지 확인 합니다.

경우에 따라 다른 ID를 사용하도록 지정할 수 있습니다. Azure Active Directory 서비스 사용자의 클라이언트 ID 및 클라이언트 암호를 기반으로 대체 id를 가리키는 연결에 대 한 구성 속성을 추가할 수 있습니다. **이 구성 옵션은 Azure Functions 서비스에서 호스팅될 때 지원 되지 않습니다.** 로컬 컴퓨터에서 ID와 암호를 사용 하려면 다음과 같은 추가 속성을 사용 하 여 연결을 정의 합니다.

| 속성    | 환경 변수 템플릿 | Description |
|---|---|---|
| 테넌트 ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Azure Active Directory 테넌트(디렉터리) ID. |
| 클라이언트 ID | `<CONNECTION_NAME_PREFIX>__clientId` |  테넌트에 있는 앱 등록의 클라이언트(애플리케이션) ID. |
| 클라이언트 암호 | `<CONNECTION_NAME_PREFIX>__clientSecret` | 앱 등록을 위해 생성된 클라이언트 암호. |

`local.settings.json`Azure blob에 대 한 id 기반 연결에 필요한 속성의 예는 다음과 같습니다. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__blobServiceUri": "<blobServiceUri>",
    "<CONNECTION_NAME_PREFIX>__queueServiceUri": "<queueServiceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity-preview"></a>Id를 사용 하 여 호스트 저장소에 연결 (미리 보기)

기본적으로 Azure Functions는 타이머 트리거의 단일 실행 및 기본 앱 키 저장소를 조정 하는 등의 핵심 동작에 "AzureWebJobsStorage" 연결을 사용 합니다. Id도 활용 하도록 구성할 수 있습니다.

> [!CAUTION]
> 함수의 다른 구성 요소는 기본 동작에 대해 "AzureWebJobsStorage"를 사용 합니다. 이러한 유형의 연결을 지원 하지 않는 이전 버전의 확장을 사용 하는 경우 (Azure Blob 및 Event Hubs에 대 한 트리거 및 바인딩 포함) id 기반 연결로 이동 하면 안 됩니다. 마찬가지로 `AzureWebJobsStorage` 는 Linux 사용에서 서버 쪽 빌드를 사용 하는 경우 배포 아티팩트에 사용 되며,이를 사용 하도록 설정 하는 경우 [외부 배포 패키지](/run-functions-from-deployment-package)를 통해 배포 해야 합니다.
>
> 또한 일부 앱은 트리거, 바인딩 및/또는 함수 코드의 다른 저장소 연결에 대해 "AzureWebJobsStorage"를 다시 사용 합니다. 연결 문자열에서이 연결을 변경 하기 전에 "AzureWebJobsStorage"를 사용 하 여 id 기반 연결 형식을 사용할 수 있는지 확인 합니다.

"AzureWebJobsStorage"에 대 한 id 기반 연결을 사용 하려면 다음 앱 설정을 구성 합니다.

| 설정                       | 설명                                | 예제 값                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__blobServiceUri`| 저장소 계정의 blob 서비스에 대 한 데이터 평면 URI입니다. | <storage_account_name>. blob.core.windows.net |
| `AzureWebJobsStorage__queueServiceUri` | 저장소 계정의 큐 서비스에 대 한 데이터 평면 URI입니다. | <storage_account_name>. queue.core.windows.net |

[Id 기반 연결에 대 한 공용 속성도](#common-properties-for-identity-based-connections) 설정할 수도 있습니다.

전역 Azure에 대 한 기본 DNS 접미사 및 서비스 이름을 사용 하는 저장소 계정을 사용 하는 경우 형식에 따라 `https://<accountName>.blob/queue/file/table.core.windows.net` 대신을 `AzureWebJobsStorage__accountName` 저장소 계정의 이름으로 설정할 수 있습니다. 이 계정에 대해 blob 및 큐 끝점이 유추 됩니다. 저장소 계정이 소 버린 클라우드에 있거나 사용자 지정 DNS를 포함 하는 경우에는이 작업이 수행 되지 않습니다.

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

## <a name="reporting-issues"></a>문제 보고
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](./functions-develop-local.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)
* [Azure Functions Node.js 개발자 참조](functions-reference-node.md)
