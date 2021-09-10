---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 08/13/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7bcfa8993cdfa0ab8efcbe35881921c5cf5a92cd
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182591"
---
## <a name="prerequisites"></a>사전 요구 사항
[Azure 구독](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기
다음 자습서의 지침을 따르세요. [빠른 시작: Azure Portal을 사용하여 Service Bus 항목 및 해당 항목에 대한 하나 이상의 구독 만들기](../service-bus-quickstart-topics-subscriptions-portal.md): 다음 작업을 수행합니다.

- **프리미엄** Service Bus 네임스페이스를 만듭니다. 
- 연결 문자열을 가져옵니다. 
- Service Bus 항목을 만듭니다.
- 항목에 대한 구독을 만듭니다. 이 자습서에서는 구독이 하나만 필요하므로 S2 및 S3 구독을 만들 필요가 없습니다. 

## <a name="send-messages-to-the-service-bus-topic"></a>Service Bus 항목으로 메시지 보내기
이 단계에서는 샘플 애플리케이션을 사용하여 이전 단계에서 만든 Service Bus 토픽으로 메시지를 보냅니다. 

1. [GitHub azure-service-bus 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.
2. Visual Studio에서 *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegrationV2* 폴더로 이동한 다음, *SBEventGridIntegration.sln* 파일을 엽니다.
3. 솔루션 탐색기 창에서 **MessageSender** 프로젝트를 확장하고 **Program.cs** 를 선택합니다.
4. `<SERVICE BUS NAMESPACE - CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꾸고 `<TOPIC NAME>`을 토픽 이름으로 바꿉니다. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. 프로그램을 빌드하고 실행하여 Service Bus 토픽에 5개의 테스트 메시지(`const int numberOfMessages = 5;`)를 보냅니다. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="콘솔 앱 출력":::
