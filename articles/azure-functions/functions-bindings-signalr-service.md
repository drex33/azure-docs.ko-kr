---
title: Azure Functions SignalR Service 바인딩
description: Azure Functions에서 SignalR Service 바인딩을 사용하는 방법을 배웁니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 34460fb0076c4cb666242d996add913cc2b74d22
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108642"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

이 일련의 문서에서는 Azure Functions에서 SignalR Service 바인딩을 사용하여 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/)에 연결된 클라이언트에 실시간 메시지를 인증하고 전송하는 방법을 설명합니다. Azure Functions는 SignalR Service에 대한 입력 및 출력 바인딩을 지원합니다.

| 작업 | 유형 |
|---------|---------|
| SignalR Service 메시지 처리 | [트리거 바인딩](./functions-bindings-signalr-service-trigger.md) |
| 서비스 엔드포인트 URL 및 액세스 토큰 반환 | [입력 바인딩](./functions-bindings-signalr-service-input.md) |
| SignalR Service 메시지 보내기 |[출력 바인딩](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

SignalR 서비스와 Azure Functions를 함께 구성하고 사용하는 방법에 대한 자세한 내용은 [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](../azure-signalr/signalr-concept-serverless-development-config.md)을 참조하세요.

### <a name="annotations-library-java-only"></a>주석 라이브러리(Java 전용)

Java 함수에서 SignalR Service 주석을 사용하려면 *pom.xml* 파일의 *azure-functions-java-library-signalr* 아티팩트(버전 1.0 이상)에 종속성을 추가해야 합니다.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="connection-string-settings"></a>연결 문자열 설정

연결 문자열을 사용하여 애플리케이션 설정을 가리키는 _host.json_ 파일에 `AzureSignalRConnectionString` 키를 추가합니다. 로컬 개발의 경우 이 값은 _local.settings.json_ 파일에 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [SignalR Service 메시지 처리(트리거 바인딩)](./functions-bindings-signalr-service-trigger.md)
- [서비스 엔드포인트 URL 및 액세스 토큰 반환(입력 바인딩)](./functions-bindings-signalr-service-input.md)
- [SignalR Service 메시지 보내기(출력 바인딩)](./functions-bindings-signalr-service-output.md)