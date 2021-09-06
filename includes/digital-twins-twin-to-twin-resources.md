---
author: baanders
description: Event Grid 엔드포인트 및 경로를 설정하는 방법을 설명하는 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748572"
---
### <a name="create-the-event-grid-topic"></a>이벤트 그리드 토픽 만들기

[Event Grid](../articles/event-grid/overview.md)는 Azure 서비스에서 Azure 내의 다른 위치로 이벤트를 라우팅하고 전송하는 데 도움이 되는 Azure 서비스입니다. [이벤트 그리드 토픽](../articles/event-grid/concepts.md)을 만들어 원본에서 특정 이벤트를 수집할 수 있습니다. 그러면 구독자가 토픽에서 수신 대기하여 이벤트가 올 때 해당 이벤트를 받을 수 있습니다.

Azure Cloud Shell에서 다음 명령을 실행하여 이벤트 그리드 토픽을 만듭니다.

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

이 명령을 실행하면 사용자가 만든 이벤트 그리드 토픽에 대한 정보가 출력됩니다. 나중에 사용되게 되므로 이벤트 그리드 토픽에 지정한 **이름** 을 저장합니다.

### <a name="create-the-endpoint"></a>엔드포인트 만들기

다음으로, 인스턴스를 이벤트 그리드 토픽에 연결하는 Azure Digital Twins에 Event Grid 엔드포인트를 만듭니다. 아래 명령을 사용하여 필요에 따라 Event Grid 토픽 및 다른 자리 표시자 필드의 이름을 입력합니다.

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

이 명령을 실행하면 만든 엔드포인트에 대한 정보가 출력됩니다.

출력에서 `provisioningState` 필드를 찾아 해당 값이 "성공"인지 확인합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="Azure Portal의 Cloud Shell에서 수행된 엔드포인트 쿼리 결과에서 provisioningState가 Succeeded인 엔드포인트를 보여 주는 스크린샷":::

"프로비저닝"을 나타낼 수도 있습니다. 즉, 엔드포인트가 아직 생성되고 있음을 의미합니다. 이 경우 몇 초 정도 기다렸다가 다음 명령을 실행하여 엔드포인트의 상태를 확인합니다. `provisioningState`에 "성공이" 표시될 때까지 반복합니다.

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

나중에 사용하므로 엔드포인트의 **이름** 을 저장합니다.

### <a name="create-the-route"></a>경로 만들기

그런 다음, 방금 만든 Event Grid 엔드포인트에 이벤트를 보내는 Azure Digital Twins 경로를 만듭니다. 

이 작업은 다음 CLI 명령을 통해 수행할 수 있습니다(필요에 따라 엔드포인트 및 다른 자리 표시자 필드의 이름 입력). 이 명령은 트윈 그래프에서 발생하는 모든 이벤트를 전달합니다. [필터](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events)를 사용하여 원하는 경우 이벤트를 특정 이벤트로만 제한할 수 있습니다.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

이 명령을 실행하면 만든 경로에 대한 일부 정보가 출력됩니다.

>[!NOTE]
>엔드포인트(이전 단계에서)는 프로비저닝을 완료해야 해당 엔드포인트를 사용하는 이벤트 경로를 설정할 수 있습니다. 엔드포인트가 준비되지 않았기 때문에 경로를 만들지 못한 경우 몇 분 정도 기다린 다음, 다시 시도하세요.