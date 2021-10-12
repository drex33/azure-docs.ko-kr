---
title: Azure Logic Apps를 사용하여 Event Grid를 통해 Service Bus 이벤트 처리
description: 이 문서에서는 Azure Logic Apps를 사용하여 Event Grid를 통해 Service Bus 이벤트를 처리하는 단계를 제공합니다.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e5089985b56ad271f3de41fc3c68f091beddffb
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457212"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>자습서: Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답
이 자습서에서는 Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답하는 방법을 알아봅니다. 

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps를 사용하여 메시지 받기
이 단계에서는 Azure Event Grid를 통해 Service Bus 이벤트를 수신하는 Azure 논리 앱을 만듭니다. 

1. Azure Portal에서 논리 앱을 만듭니다.
    1. **+ 리소스 만들기** 를 선택하고 **통합** 을 선택한 후 **논리 앱** 을 선택합니다. 
    2. Azure **구독** 을 선택합니다. 
    3. **리소스 그룹** 에 대해 **기존 항목 사용** 을 선택하고 다른 리소스(예: Azure 함수, Service Bus 네임스페이스)에 사용한 리소스 그룹을 선택합니다. 
    1. **유형** 으로 **소비** 를 선택합니다. 
    1. 논리 앱의 **이름** 을 입력합니다.
    1. 논리 앱의 **지역** 을 선택합니다. 
    1. **검토 + 만들기** 를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 **만들기** 를 선택하여 논리 앱을 만듭니다. 
    1. **배포 완료** 페이지에서 **리소스로 이동** 을 선택합니다. 
1. **Logic Apps 디자이너** 페이지에서 **템플릿** 아래에 있는 **비어 있는 논리 앱** 을 선택합니다. 

### <a name="add-a-step-receive-messages-from-service-bus-via-event-grid"></a>Event Grid를 통해 Service Bus에서 메시지를 수신하는 단계 추가
1. 디자이너에서 다음 단계를 수행합니다.
    1. **Event Grid** 를 검색합니다. 
    2. **리소스 이벤트가 발생하는 경우 - Azure Event Grid** 를 선택합니다. 

        ![Logic Apps 디자이너 - Event Grid 트리거 선택](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **로그인** 을 선택하고 Azure 자격 증명을 입력한 후 **액세스 허용** 을 선택합니다. 
5. **리소스 이벤트가 발생하는 경우** 페이지에서 다음 단계를 수행합니다.
    1. Azure 구독을 선택합니다. 
    2. **리소스 종류** 로 **Microsoft.ServiceBus.Namespaces** 를 선택합니다. 
    3. **리소스 이름** 에 대해 Service Bus 네임스페이스를 선택합니다. 
    4. **새 매개 변수 추가** 를 선택하고, **접미사 필터** 를 선택한 다음, 포커스를 드롭다운 목록 밖으로 이동합니다.
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/add-new-parameter-suffix-filter.png" alt-text="접미사 필터 추가를 보여 주는 이미지":::
    1. **접미사 필터** 에 대해 Service Bus 항목 구독의 이름을 입력합니다. 
        ![Logic Apps 디자이너 - 이벤트 구성](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
1. 디자이너에서 **+ 새 단계** 를 선택하고 다음 단계를 수행합니다.
    1. **Service Bus** 를 검색합니다.
    2. 목록에서 **Service Bus** 를 선택합니다. 
    3. **작업** 목록에서 **메시지 가져오기** 를 선택합니다. 
    4. **항목 구독에서 메시지 가져오기(보기 잠금)** 를 선택합니다. 

        ![Logic Apps 디자이너 - 메시지 가져오기 작업](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **연결의 이름** 을 입력합니다. 예를 들면 다음과 같습니다. **항목 구독에서 메시지를 가져오고**, Service Bus 네임스페이스를 선택합니다. 

        ![Logic Apps 디자이너 - Service Bus 네임스페이스 선택](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey** 를 선택한 다음, **만들기** 를 선택합니다.

        ![Logic Apps 디자이너 - 공유 액세스 키 선택](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. **항목** 및 **구독** 을 선택합니다. 
    
        ![항목 및 구독을 선택할 수 있는 위치를 보여주는 스크린샷.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)

### <a name="add-a-step-to-process-and-complete-received-messages"></a>수신된 메시지를 처리하고 완료하는 단계를 추가
이 단계에서는 수신된 메시지를 이메일로 보낸 다음, 메시지를 완료하는 단계를 추가합니다. 실제 시나리오에서는 메시지를 완료하기 전에 논리 앱에서 메시지를 처리합니다.

#### <a name="add-a-foreach-loop"></a>foreach 루프 추가
1. **+ 새 단계** 를 선택합니다.
1. **컨트롤** 을 검색한 다음 선택합니다.  

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-control.png" alt-text="컨트롤 범주의 선택 항목을 보여주는 이미지":::
1. **작업** 목록에서 **For each** 를 선택합니다.

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-for-each.png" alt-text="For each 컨트롤의 선택 항목을 보여 주는 이미지":::    
1. **이전 단계에서 출력 선택**(필요하다면 텍스트 상자 내부 클릭) 시 **주제 구독에서 메시지 가져오기(보기-잠금)** 에서 **본문** 을 선택합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-input-for-each.png" alt-text="For each에 대한 입력 선택 항목을 보여주는 이미지":::    

#### <a name="add-a-step-inside-the-foreach-loop-to-send-an-email-with-the-message-body"></a>foreach 루프 내에 단계를 추가하여 메시지 본문과 함께 이메일 보내기

1. **For each** 루프 내에서 **작업 추가** 를 선택합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-add-action.png" alt-text="For each 루프 내에서 작업 추가 단추 선택을 보여주는 이미지":::        
1. **커넥터 및 작업 검색** 텍스트 상자에 **Office 365** 를 입력합니다. 
1. 검색 결과에서 **Office 365 Outlook** 을 선택합니다. 
1. 작업 목록에서 **이메일 보내기(V2)** 를 선택합니다. 
1. **메일 보내기(V2)** 창에서 다음 단계를 수행합니다. 
1. **본문** 의 텍스트 상자 내부를 선택하고 다음 단계를 수행합니다.
    1. **대상** 에 이메일 주소를 입력합니다. 
    1. **제목** 에 **Service Bus 토픽 구독에서 받은 메시지** 를 입력합니다.  
    1. **식** 으로 전환합니다.
    1. 다음 식을 입력합니다.
    
        ```
        base64ToString(items('For_each')?['ContentData'])
        ``` 
    1. **확인** 을 선택합니다. 
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/specify-expression-email.png" alt-text="메일 보내기 작업의 본문에 대한 식을 보여 주는 이미지":::

#### <a name="add-another-action-in-the-foreach-loop-to-complete-the-message"></a>foreach 루프 내 다른 작업을 추가하여 메시지를 완료         
1. **For each** 루프 내에서 **작업 추가** 를 선택합니다. 
    1. **최근** 목록에서 **Service Bus** 를 선택합니다.
    2. 작업 목록에서 **항목 구독의 메시지 완료** 를 선택합니다. 
    3. Service Bus **항목** 을 선택합니다.
    4. 항목에 대한 **구독** 을 선택합니다.
    5. **메시지의 잠금 토큰** 에 대해 **동적 콘텐츠** 의 **잠금 토큰** 을 선택합니다. 

        ![Logic Apps 디자이너 - 메시지 완료](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Logic Apps 디자이너 도구 모음에서 **저장** 을 선택하여 논리 앱을 저장합니다. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="논리 앱 저장":::

## <a name="test-the-app"></a>앱 테스트
1. 토픽에 테스트 메시지를 아직 보내지 않은 경우 [Service Bus 토픽으로 메시지 보내기](#send-messages-to-the-service-bus-topic) 섹션의 지침에 따라 토픽에 메시지를 보냅니다. 
1. 논리 앱의 **개요** 페이지로 전환한 다음, 아래쪽 창에서 **실행 기록** 탭으로 전환합니다. 논리 앱이 토픽에 전송된 메시지를 실행하는 것을 볼 수 있습니다. 논리 앱이 실행되기까지 몇 분 정도 걸릴 수 있습니다. 도구 모음에서 **새로 고침** 을 선택하여 페이지를 새로 고칩니다. 

    ![Logic Apps 디자이너 - 논리 앱 실행](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. 세부 정보를 보려면 실행 논리 앱을 선택합니다. for 루프에서 5개의 메시지를 처리했습니다. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="논리 앱 실행 정보"::: 
2. 논리 앱에서 수신한 각 메시지에 대해 이메일을 수신해야 합니다.    

## <a name="troubleshoot"></a>문제 해결
잠시 기다렸다가 새로 고친 후에 호출이 표시되지 않으면 다음 단계를 수행합니다. 

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