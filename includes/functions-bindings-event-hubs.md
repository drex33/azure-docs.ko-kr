---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 70544909ae419cdf215bdad88c3ac19968f6e4f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520153"
---
## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[확장 번들]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[확장 업데이트]: ../articles/azure-functions/functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs 확장 5.x 이상

이제 새 버전의 Event Hubs 바인딩 확장을 사용할 수 있습니다. [비밀 대신 ID를 사용하여 연결하는](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)기능을 소개합니다. 관리 ID를 사용한 함수 앱 구성에 대한 자습서는 [ID 기반 연결을 사용한 함수 앱 만들기 자습서를 참조하세요.](../articles/azure-functions/functions-identity-based-connections-tutorial.md) .NET 애플리케이션의 경우 `Microsoft.Azure.EventHubs`의 형식이 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs)의 최신 형식으로 바뀌어 사용자가 바인딩할 수 있는 형식도 변경됩니다.

이 확장 버전은 [NuGet 패키지], 버전 5.x를 설치하여 사용할 수 있으며, 파일에 다음을 추가하여 확장 번들 v3에서 추가할 수 있습니다. `host.json`

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

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

## <a name="hostjson-settings"></a>host.json 설정
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 파일에는 Event Hubs 트리거에 대한 동작을 제어하는 설정이 포함되어 있습니다. 구성은 Azure Functions 버전에 따라 달라집니다.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]