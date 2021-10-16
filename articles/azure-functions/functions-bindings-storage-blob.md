---
title: Azure Functions의 Azure Blob Storage 트리거 및 바인딩
description: Azure Functions에서 Azure Blob Storage 트리거 및 바인딩을 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 8f745a3569d8d90ad1ccd7daea6a70a4574e6ddf
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069523"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Functions의 Azure Blob Storage 바인딩 개요

Azure Functions는 [트리거 및 바인딩](./functions-triggers-bindings.md)을 통해 [Azure Storage](../storage/index.yml)와 통합됩니다. Blob Storage와 통합하면 Blob 데이터의 변경 내용 및 읽기/쓰기 값에 응답하는 함수를 작성할 수 있습니다.

| 작업 | Type |
|---------|---------|
| Blob Storage 데이터 변경 시 함수 실행 | [트리거](./functions-bindings-storage-blob-trigger.md) |
| 함수에서 Blob Storage 데이터 읽기 | [입력 바인딩](./functions-bindings-storage-blob-input.md) |
| 함수가 Blob Storage 데이터를 쓰도록 허용 |[출력 바인딩](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

#### <a name="storage-extension-5x-and-higher"></a>스토리지 확장 5.x 이상

새 버전의 Storage 바인딩 확장은 미리 보기에서 사용할 수 있습니다. [비밀 대신 id를 사용 하 여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. .NET 애플리케이션의 경우 `WindowsAzure.Storage` 및 `Microsoft.Azure.Storage`의 형식이 [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs)의 최신 형식으로 바뀌어 사용자가 바인딩할 수 있는 형식도 변경됩니다. [Azure.Storage.Blobs 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md)에서 이러한 새로운 유형의 차이점과 마이그레이션 방법에 대해 자세히 알아봅니다.

이 확장 버전은 [미리 보기 NuGet 패키지로] 사용할 수 있거나, 파일에 다음을 추가 하 여 미리 보기 확장 번들 v3에서 추가할 수 있습니다 `host.json` .

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[3.*, 4.0.0)"
  }
}
```

자세히 알아보려면 [확장 업데이트]를 참조 하세요.

[NuGet 패키지 미리 보기]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.5
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는이 바인딩이 사용 하는 함수에 사용할 수 있는 함수 앱 구성 설정에 대해 설명 합니다. 이러한 설정은 [확장 버전 5.0.0 이상을](#storage-extension-5x-and-higher)사용 하는 경우에만 적용 됩니다. 아래 예제 host.json 파일에는 이 바인딩에 대한 버전 2.x+ 설정만 포함되어 있습니다. 버전 2.x 이상 버전의 함수 앱 구성 설정에 대 한 자세한 내용은 [Azure Functions에 대 한 호스트 json 참조](functions-host-json.md)를 참조 하세요.

> [!NOTE]
> 이 섹션은 5.0.0 이전 확장 버전에는 적용 되지 않습니다. 이러한 이전 버전의 경우 blob에 대 한 함수 앱 전체 구성 설정이 없습니다.

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------|
|maxDegreeOfParallelism|8*(사용 가능한 코어 수)|각 Blob 트리거 함수에 대해 허용되는 동시 호출 수(정수)입니다. 허용되는 최솟값은 1입니다.|

## <a name="next-steps"></a>다음 단계

- [Blob Storage 데이터 변경 시 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수 실행 시 Blob Storage 데이터 읽기](./functions-bindings-storage-blob-input.md)
- [함수에서 Blob Storage 데이터 쓰기](./functions-bindings-storage-blob-output.md)
