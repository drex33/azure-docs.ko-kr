---
title: Azure Functions의 Azure Event Grid 바인딩
description: Azure Functions에서 Event Grid 이벤트를 처리하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: f0f42b37c40a3a3a2ecda4115ebc7729fc686671
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473968"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Functions의 Azure Event Grid 바인딩

이 참조에서는 Azure Functions에서 [Event Grid](../event-grid/overview.md) 이벤트를 처리하는 방법을 설명합니다. HTTP 엔드포인트에서 Event Grid 메시지를 처리하는 방법에 대한 자세한 내용은 [HTTP 엔드포인트에서 이벤트 수신](../event-grid/receive-events.md)을 참조하세요.

Event Grid는 *게시자* 에서 발생하는 이벤트를 알리기 위해 HTTP 요청을 전송하는 Azure 서비스입니다. 게시자는 이벤트를 시작하는 서비스 또는 리소스입니다. 예를 들어, Azure Blob Storage 계정은 게시자이고 [Blob 업로드 또는 삭제는 이벤트](../storage/blobs/storage-blob-event-overview.md)입니다. 일부 [Azure 서비스에는 Event Grid에 이벤트를 게시하는 방식을 기본적으로 지원합니다](../event-grid/overview.md#event-sources).

이벤트 *처리기* 는 이벤트를 수신하고 처리합니다. Azure Functions는 [Event Grid 이벤트를 처리를 기본적으로 지원하는 Azure 서비스](../event-grid/overview.md#event-handlers) 중 하나입니다. 이 참조에서는 Event Grid에서 이벤트를 받을 때 Event Grid 트리거를 사용하여 함수를 호출하고 출력 바인딩을 사용하여 [Event Grid 사용자 지정 토픽](../event-grid/post-to-custom-topic.md)에 이벤트를 보내는 방법을 알아봅니다.

필요한 경우 HTTP 트리거를 사용하여 Event Grid 이벤트를 처리할 수 있습니다. [HTTP 엔드포인트에서 이벤트 수신](../event-grid/receive-events.md)을 참조하세요. 현재는, 이벤트가 [CloudEvents 스키마](../event-grid/cloudevents-schema.md#azure-functions)를 통해 전달되는 경우, Azure Functions 앱에 대해 Event Grid 트리거를 사용할 수 없습니다. 대신, HTTP 트리거를 사용합니다.

| 작업 | 유형 |
|---------|---------|
| Event Grid 이벤트가 디스패치되면 함수를 실행합니다. | [트리거](./functions-bindings-event-grid-trigger.md) |
| Event Grid 이벤트를 보냅니다. |[출력 바인딩](./functions-bindings-event-grid-output.md) |

이 참조의 코드는 Functions 버전 2.x 이상에서 사용되는 .NET Core 구문을 기본값으로 사용합니다. 1.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어 | 추가 방법... | 설명 |
|---|---|---|
| C# | [NuGet 패키지] 버전 2.x 설치 | |
| C# 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록 | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만) | 바인딩 추가 | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="event-grid-extension-3x-and-higher"></a>Event Grid 확장 3.x 이상

이제 새 버전의 Event Grid 바인딩 확장을 사용할 수 있습니다. .NET 애플리케이션의 경우 바인딩할 수 있는 형식을 변경하여 의 형식을 `Microsoft.Azure.EventGrid.Models` [Azure.Messaging.EventGrid의](/dotnet/api/azure.messaging.eventgrid)최신 형식으로 바꿉니다. [클라우드 이벤트](/dotnet/api/azure.messaging.cloudevent)는 새로운 Event Grid 확장 프로그램에서도 지원됩니다.

이 확장 버전은 [NuGet 패키지], 버전 3.x를 설치하여 사용할 수 있으며, 파일에 다음을 추가하여 확장 번들 v3에서 추가할 수 있습니다. `host.json`

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

자세한 내용은 [확장 업데이트를 참조하세요.]

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

## <a name="next-steps"></a>다음 단계
* [Event Grid 이벤트가 디스패치되면 함수 실행](./functions-bindings-event-grid-trigger.md)
* [Event Grid 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)
