---
title: 트윈 간 이벤트 처리 설정
titleSuffix: Azure Digital Twins
description: 트윈 그래프를 통해 이벤트를 전파하기 위해 Azure에서 함수를 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538315"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>Azure Functions를 사용하여 트윈 간 이벤트 처리 설정

완전히 연결된 Azure Digital Twins 그래프는 이벤트 전파를 통해 구동됩니다. 데이터는 IoT 허브 같은 외부 원본에서 Azure Digital Twins에 도착한 다음, Azure Digital Twins 그래프를 통해 전파되어 관련 트윈을 적절하게 업데이트합니다.

예를 들어 각 층에 여러 공간이 있는 건물의 층과 공간을 나타내는 그래프를 고려해 보겠습니다. 공간 트윈의 온도 속성이 업데이트될 때마다 같은 층의 모든 공간에 대해 평균 온도가 새로 계산되고 층 트윈의 온도 속성을 업데이트하여 포함된 모든 공간(업데이트된 공간 포함)의 새로운 평균 온도를 반영하도록 트윈 간 데이터 흐름을 설정할 수 있습니다. 

이 문서에서는 그래프에 있는 다른 트윈의 속성 변경이나 기타 데이터에 대한 응답으로 트윈을 업데이트할 수 있도록 트윈 간 이벤트를 보내는 방법을 살펴보겠습니다. 현재 트윈 간 업데이트는 그래프의 다른 영역에 영향을 주는 트윈 수명 주기 이벤트를 감시하고 그에 따라 다른 트윈을 변경하는 [Azure 함수](../azure-functions/functions-overview.md)를 설정하여 처리됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 **Visual Studio** 를 사용합니다. [Visual Studio 다운로드](https://visualstudio.microsoft.com/downloads/)에서 최신 버전을 다운로드할 수 있습니다.

트윈 간 처리를 설정하려면 작업할 **Azure Digital Twins 인스턴스** 가 필요합니다. 인스턴스를 만드는 방법에 대한 지침은 [Azure Digital Twins 인스턴스 및 인증 설정](./how-to-set-up-instance-portal.md)을 참조하세요. 인스턴스에는 데이터를 보내려는 **트윈이 최소 두 개** 이상 있어야 합니다.

필요에 따라 트윈에 대해서도 [IoT 허브를 통해 자동 원격 분석 수집](how-to-ingest-iot-hub-data.md)을 설정할 수도 있습니다. 이는 트윈 간에 데이터를 보내는 데는 필요하지 않지만 트윈 그래프가 라이브 원격 분석을 통해 구동되는 완전한 솔루션의 중요한 부분입니다.

## <a name="set-up-endpoint-and-route"></a>엔드포인트 및 경로 설정

트윈 간 이벤트 처리를 설정하려면 먼저 Azure Digital Twins에서 **엔드포인트** 를 만들고 해당 엔드포인트에 대한 **경로** 를 만듭니다. 업데이트를 진행 중인 트윈은 경로를 사용하여 해당 업데이트 이벤트에 대한 정보를 엔드포인트로 보냅니다. 여기서 Event Grid는 나중에 해당 이벤트를 선택하고 처리를 위해 Azure 함수에 전달할 수 있습니다.

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>Azure 함수 만들기

다음으로, 엔드포인트에서 수신 대기하다가 경로를 통해 전송되는 트윈 이벤트를 수신하는 Azure 함수를 만듭니다. 

1. 먼저, 머신의 Visual Studio에 Azure Functions 프로젝트를 만듭니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)을 참조하세요.

2. 프로젝트에 다음 패키지를 추가합니다. 명령줄 도구에서 Visual Studio NuGet 패키지 관리자 또는 `dotnet` 명령을 사용할 수 있습니다.

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. 함수의 논리를 입력합니다. 시작하는 데 도움이 되도록 [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) 리포지토리에서 여러 시나리오에 대한 샘플 함수 코드를 볼 수 있습니다.

5. 함수 앱을 Azure에 게시합니다. 함수 앱을 게시하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>함수 앱 구성

함수가 Azure Digital Twins 액세스하려면 인스턴스 및 액세스 권한에 대한 일부 정보가 필요합니다. 이 섹션에서는 인스턴스를 찾아 액세스할 수 있도록 함수에 대한 **액세스 역할을 할당** 하고 **애플리케이션 설정을 구성** 합니다.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>Event Grid에 함수 연결

다음으로, 앞에서 만든 Event Grid 항목에 Azure 함수를 구독합니다. 이렇게 하면 Event Grid 항목을 통해 업데이트된 트윈에서 함수로 데이터가 흐를 수 있습니다.

이렇게 하려면 앞에서 만든 Event Grid 항목에서 Azure 함수로 데이터를 전송하는 **Event Grid 구독** 을 만듭니다.

다음 CLI 명령을 사용하여 구독 ID, 리소스 그룹, 함수 앱 및 함수 이름에 대한 자리 표시자를 입력합니다.

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

이제 함수는 Event Grid 항목을 통해 이벤트를 수신할 수 있습니다. 데이터 흐름 설정이 완료되었습니다.

## <a name="test-and-verify-results"></a>결과 테스트 및 확인

마지막 단계는 트윈을 업데이트하고 Azure 함수의 논리에 따라 관련 트윈이 업데이트되는지 확인하여 흐름이 작동하는지 확인하는 것입니다.

프로세스를 시작하려면 이벤트 흐름의 원본인 트윈을 업데이트합니다. [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update), [Azure Digital Twins SDK](how-to-manage-twin.md#update-a-digital-twin) 또는 [Azure Digital Twins REST API](how-to-use-postman.md?tabs=data-plane)를 사용하여 업데이트할 수 있습니다.

다음으로, Azure Digital Twins 인스턴스에서 관련 트윈을 쿼리합니다. [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 또는 [Azure Digital Twins REST API 및 SDK](how-to-query-graph.md#run-queries-with-the-api)를 사용할 수 있습니다. 트윈이 데이터를 수신하고 예상대로 업데이트했는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins에서 트윈 간 이벤트 처리를 설정합니다. 다음으로, [IoT 허브에서 원격 분석 수집](how-to-ingest-iot-hub-data.md)에 따라 IoT 허브 디바이스에서 들어오는 원격 분석을 기반으로 이 흐름을 자동으로 트리거하도록 Azure 함수를 설정합니다.
