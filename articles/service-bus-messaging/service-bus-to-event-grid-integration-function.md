---
title: Azure Functions를 사용하여 Event Grid를 통해 Service Bus 이벤트 처리
description: 이 문서에서는 Azure Functions를 사용하여 Event Grid를 통해 Service Bus 이벤트를 처리하는 단계를 설명합니다.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 46dc7a9ac1b985c116162f60c58afb0e8dff9c3a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273164"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>자습서: Azure Functions를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답
이 자습서에서는 Azure Functions 및 Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답하는 방법을 알아봅니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Service Bus 네임스페이스 만들기
> * 메시지를 전송하도록 예제 애플리케이션 준비
> * Service Bus 항목으로 메시지 보내기
> * Logic Apps를 사용하여 메시지 받기
> * Azure에서 테스트 함수 설정
> * Event Grid를 통해 함수와 네임스페이스 연결
> * Azure Functions를 사용하여 메시지 받기

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>추가 필수 조건
[Visual Studio 2019](https://www.visualstudio.com/vs)를 설치하고 **Azure 개발** 워크로드를 포함시킵니다. 이 워크로드에는 Visual Studio에서 Azure Functions 프로젝트를 만들고 빌드하고 배포하는 데 필요한 **Azure 함수 도구** 가 들어 있습니다. 

## <a name="deploy-the-function-app"></a>함수 앱 배포 

>[!NOTE]
> Azure Functions 앱을 만들고 배포하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md)을 참조하세요.

1. **SBEventGridIntegration.sln** 솔루션의 **FunctionApp1** 프로젝트에서 **ReceiveMessagesOnEvent.cs** 파일을 엽니다. 
1. `<SERCICE BUS NAMESPACE - CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다. 동일한 솔루션에서 **MessageSender** 프로젝트의 **Program.cs** 파일에서 사용한 것과 동일한 연결 문자열입니다. 
1. **FunctionApp1** 을 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. 
1. **게시** 페이지에서 **시작** 을 선택합니다. 이러한 단계가 개발자의 화면에 표시되는 내용과 다를 수 있지만 게시 프로세스는 비슷합니다. 
1. **게시** 마법사의 **대상** 페이지에서 **Azure** 를 **대상** 으로 선택합니다. 
1. **특정 대상** 페이지에서 **Azure 함수 앱(Windows)** 을 선택합니다. 
1. **Functions 인스턴스** 페이지에서 **새 Azure 함수 만들기** 를 선택합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/add-function-button.png" alt-text="Visual Studio - 게시 대화 상자의 함수 추가 단추를 보여주는 이미지.":::
1. **함수 앱(Windows)** 페이지에서 다음 단계를 수행합니다.
    1. 함수 앱의 **이름** 을 입력합니다.
    1. Azure **구독** 을 선택합니다.
    1. 기존 **리소스 그룹** 을 선택하거나 새 리소스 그룹을 만듭니다. 이 자습서에서는 Service Bus 네임스페이스가 있는 리소스 그룹을 선택합니다. 
    1. App Service의 **플랜 유형** 을 선택합니다.
    1. **위치** 를 선택합니다. Service Bus 네임스페이스와 동일한 위치를 선택합니다. 
    1. 기존 **Azure Storage** 를 선택하거나 **새로 만들기** 를 선택하여 Functions 앱에서 사용할 새 Storage 계정을 만듭니다. 
    1. **만들기** 를 선택하여 Functions 앱을 만듭니다. 
1. **게시** 마법사의 **Functions 인스턴스** 페이지로 돌아가서 **마침** 을 선택합니다. 
1. Visual Studio의 **게시** 페이지에서 **게시** 를 선택하여 Functions 앱을 Azure에 게시합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Functions 앱 게시":::    
1. **출력** 창에서 빌드 및 게시의 메시지를 살펴보고, 둘 다 성공했는지 확인합니다. 
1. 이제 **게시** 페이지에서 **Azure Portal에서 관리** 를 선택합니다. 
1. Azure Portal의 **함수 앱** 페이지에서 왼쪽 메뉴에 있는 **함수** 를 선택하고, 다음 두 함수가 표시되는지 확인합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Event Grid 트리거 및 HTTP 트리거를 처리하는 두 함수":::

    > [!NOTE]
    > `EventGridTriggerFunction`은 [Event Grid 트리거](../azure-functions/functions-bindings-event-grid-trigger.md)를 사용하고 `HTTPTriggerFunction`은 [HTTP 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md)를 사용합니다.
1. 목록에서 **EventGridTriggerFunction** 을 선택합니다. HTTP 트리거에 비해 몇 가지 이점이 있는 Event Grid 트리거를 Azure Functions에서 사용하는 것이 좋습니다. 자세한 내용은 [Event Grid 이벤트에 대한 이벤트 처리기로서의 Azure 함수](../event-grid/handler-functions.md)를 참조하세요.
1. **EventGridTriggerFunction** 의 **함수** 페이지에서 왼쪽 메뉴의 **모니터** 를 선택합니다. 
1. **구성** 을 선택하여 호출 로그를 캡처하도록 Application Insights를 구성합니다. 이 페이지를 사용하여 Event Grid에서 Service Bus 이벤트를 받을 때 함수 실행을 모니터링할 수 있습니다. 
1. **Application Insights** 페이지에서 리소스 이름을 입력하고 리소스 **위치** 를 선택한 다음, **확인** 을 선택합니다. 
1. 맨 위(이동 경로 메뉴)에서 **EventGridTriggerFunction** 함수를 선택하여 **함수** 페이지로 돌아갑니다. 
1. 현재 **모니터** 페이지에 있는지 확인합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="함수를 호출하기 전의 함수 모니터 페이지":::
    
    웹 브라우저 탭에서 이 페이지를 열어 둡니다. 나중에 이 함수에 대한 호출을 확인하기 위해 이 페이지를 새로 고칠 것입니다.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid를 통해 함수와 네임스페이스 연결
이 섹션에서는 Azure Portal을 사용하여 함수와 Service Bus 네임스페이스를 연결합니다. 

Azure Event Grid 구독을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 네임스페이스로 이동한 다음, 왼쪽 창에서 **이벤트** 를 선택합니다. 오른쪽 창에 두 개의 Event Grid 구독이 표시되며 네임스페이스 창이 열립니다. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus - 이벤트 페이지":::
2. 도구 모음에서 **+ 이벤트 구독** 을 선택합니다. 
3. **이벤트 구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 구독의 **이름** 을 입력합니다. 
    2. **시스템 항목** 의 **이름** 을 입력합니다. 시스템 항목은 Azure Storage 계정 및 Azure Service Bus와 같은 Azure 리소스용으로 만들어진 항목입니다. 시스템 항목에 대해 자세히 알아보려면 [시스템 항목 개요](../event-grid/system-topics.md)를 참조하세요.
    2. **엔드포인트 형식** 으로 **Azure 함수** 를 선택하고 **엔드포인트 선택** 을 클릭합니다. 

        ![Service Bus - Event Grid 구독](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **Azure 함수 선택** 페이지에서 구독, 리소스 그룹, 함수 앱, 슬롯 및 함수를 선택한 다음, **선택 항목 확인** 을 선택합니다. 

        ![함수 - 엔드포인트 선택](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. **이벤트 구독 만들기** 페이지에서 **필터** 탭으로 전환하고, 다음 작업을 수행합니다.
        1. **제목 필터링 사용** 을 선택합니다.
        2. 이전에 만든 Service Bus 토픽(**S1**)의 구독 이름을 입력합니다.
        3. **만들기** 단추를 선택합니다. 

            ![이벤트 구독 필터](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. **이벤트** 페이지의 **이벤트 구독** 탭으로 전환하고 목록에 이벤트 구독이 표시되는지 확인합니다.

    ![목록의 이벤트 구독](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Functions 앱 모니터링
이전에 Service Bus 토픽으로 보낸 메시지는 구독(S1)에 전달됩니다. Event Grid는 구독의 메시지를 Azure 함수에 전달합니다. 자습서의 이 단계에서는 함수가 호출되었는지 확인하고 기록된 정보 메시지를 살펴봅니다. 

1. [모니터] 탭이 아직 활성화되지 않은 경우 Azure 함수 앱에 대한 페이지에서 **모니터** 탭으로 전환합니다. Service Bus 항목에 게시된 각 메시지에 대한 항목이 표시됩니다. 표시되지 않는 않으면 몇 분 정도 기다린 후 페이지를 새로 고칩니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="호출한 이후의 함수 모니터 페이지":::
2. 세부 정보를 보려면 목록에서 호출을 선택합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="함수 호출 세부 정보" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    **모니터** 페이지의 **로그** 탭을 사용하여 메시지가 전송될 때 로깅 정보를 볼 수도 있습니다. 약간의 지연이 있을 수 있으므로 기록된 메시지를 확인하는 데 몇 분이 걸립니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="함수 로그" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>문제 해결
잠시 기다렸다가 새로 고친 후에도 호출이 표시되지 않으면 다음 단계를 수행합니다. 

1. 메시지가 Service Bus 토픽에 도달했는지 확인합니다. **Service Bus 토픽** 페이지에서 **들어오는 메시지** 카운터를 참조하세요. 이 경우 **MessageSender** 애플리케이션을 두 번 실행했으므로 10개의 메시지(각 실행당 5개의 메시지)가 표시됩니다.

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Service Bus 토픽 페이지 - 들어오는 메시지":::    
1. Service Bus 구독에 **활성 메시지가 없는** 지 확인합니다. 
    이 페이지에 이벤트가 표시되지 않으면 **Service Bus 구독** 페이지에 **활성 메시지 수** 가 표시되지 않는지 확인합니다. 이 카운터의 수가 0보다 크면 특정 이유로 인해 구독의 메시지가 처리기 함수(이벤트 구독 처리기)로 전달되지 않습니다. 이벤트 구독을 제대로 설정했는지 확인합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Service Bus 구독에 있는 활성 메시지 수":::    
1. Service Bus 네임스페이스의 **이벤트** 페이지에도 **전달된 이벤트** 가 표시됩니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="이벤트 페이지 - 전달된 이벤트" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. **이벤트 구독** 페이지에서 이벤트가 전달된 것을 확인할 수도 있습니다. **이벤트** 페이지에서 이벤트 구독을 선택하여 이 페이지로 이동할 수 있습니다. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="이벤트 구독 페이지 - 전달된 이벤트":::
    


## <a name="next-steps"></a>다음 단계

* [Azure Event Grid](../event-grid/index.yml)에 대해 자세히 알아봅니다.
* [Azure Functions](../azure-functions/index.yml)에 대해 자세히 알아봅니다.
* [Azure App Service의 Logic Apps 기능](../logic-apps/index.yml)에 대해 자세히 알아봅니다.
* [Azure Service Bus](/azure/service-bus/)에 대해 자세히 알아보세요.


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png