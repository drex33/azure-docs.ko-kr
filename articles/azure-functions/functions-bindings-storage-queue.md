---
title: Azure Functions의 Azure Queue 스토리지 트리거 및 바인딩 개요
description: Azure Queue Storage 트리거를 사용하고 Azure Functions에서 바인딩을 출력하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 252a3316ae14e17c2712547f088477d74da2c66e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658687"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Functions의 Azure Queue 스토리지 트리거 및 바인딩 개요

Azure Functions는 새 Azure Queue 스토리지 메시지가 만들어질 때 실행될 수 있고 함수 내에서 큐 메시지를 작성할 수 있습니다.

| 작업 | Type |
|---------|---------|
| 큐 스토리지 데이터 변경 시 함수 실행 | [트리거](./functions-bindings-storage-queue-trigger.md) |
| 큐 스토리지 메시지 쓰기 |[출력 바인딩](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

#### <a name="storage-extension-5x-and-higher"></a>스토리지 확장 5.x 이상

새 버전의 스토리지 바인딩 확장은 [미리 보기 NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3)로 제공됩니다. 이 미리 보기에서는 [비밀 대신 ID를 사용하여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입되었습니다. .NET 애플리케이션의 경우 `WindowsAzure.Storage` 및 `Microsoft.Azure.Storage`의 형식이 [Azure.Storage.Queues](/dotnet/api/azure.storage.queues)의 최신 형식으로 바뀌어 사용자가 바인딩할 수 있는 형식도 변경됩니다.

> [!NOTE]
> 미리 보기 패키지는 확장 번들에 포함되지 않으며 수동으로 설치해야 합니다. .NET 앱의 경우 패키지에 대한 참조를 추가합니다. 다른 모든 앱 유형에 대해서는 [확장 업데이트]를 참조하세요.

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------|
|maxPollingInterval|00:01:00|큐 폴링 사이의 최대 간격입니다. 최소 간격은 00:00:00.100(100밀리초)입니다. 간격이 `maxPollingInterval`까지 점진적으로 증가합니다. 기본값 `maxPollingInterval`은 00:01:00(1분)입니다. `maxPollingInterval`은(는) 00:00:00.100(100밀리초)보다 작지 않아야 합니다. Functions 2.x 이상에서 데이터 형식은 `TimeSpan`입니다. Functions 1.x에서는 밀리초 단위입니다.|
|visibilityTimeout|00:00:00|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다. |
|batchSize|16|함수 런타임이 동시에 검색하고 병렬로 처리하는 큐 메시지 수입니다. 처리되는 개수가 `newBatchThreshold`로 감소하면 런타임은 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 `batchSize` + `newBatchThreshold`입니다. 이 제한은 큐 트리거 함수에 개별적으로 적용됩니다. <br><br>하나의 큐에 수신된 메시지에 대해 병렬 실행을 방지하려면 `batchSize`을 1로 설정합니다. 그러나 이 설정은 함수 앱이 단일 VM(가상 머신)에서 실행되는 동안에만 동시성을 제거합니다. 함수 앱이 여러 VM에 확장되면 각 VM은 각 큐 트리거 함수의 인스턴스 하나를 실행할 수 있습니다.<br><br>최대 `batchSize`은 32입니다. |
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.|
|newBatchThreshold|N*batchSize/2|동시에 처리되는 메시지의 수가 이 숫자로 내려갈 때마다 런타임은 다른 일괄 처리를 검색합니다.<br><br>`N`은 App Service 또는 프리미엄 플랜에서 실행할 때 사용 가능한 vCPU 수를 나타냅니다. 사용 계획에 대한 값은 `1`입니다.|
|messageEncoding|base64| 이 설정은 [확장 버전 5.0.0 이상](#storage-extension-5x-and-higher)에서만 사용할 수 있습니다. 메시지의 인코딩 형식을 나타냅니다. 유효한 값은 `base64` 및 `none`입니다.|

## <a name="next-steps"></a>다음 단계

- [큐 스토리지 데이터 변경 시 함수 실행(트리거)](./functions-bindings-storage-queue-trigger.md)
- [큐 스토리지 메시지 쓰기(출력 바인딩)](./functions-bindings-storage-queue-output.md)
