---
title: Azure Time Series Insights와 통합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 Azure Time Series Insights로 이벤트 경로를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3dc79c2307365a81c6994a1e692f86ec1faa0511
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566211"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Twins를 Azure Time Series Insights와 통합

이 문서에서는 Azure Digital Twins를 [Azure TSI(Time Series Insights)](../time-series-insights/overview-what-is-tsi.md)와 통합하는 방법을 알아봅니다.

이 문서에서 설명하는 솔루션을 통해 IoT 솔루션에 대한 기록 데이터를 수집하고 분석할 수 있습니다. Azure Digital Twins는 여러 데이터 스트림의 상관 관계를 발견하고 정보를 Time Series Insights로 전송하기 전에 표준화할 수 있으므로 Time Series Insights에 데이터를 공급하는 좋은 방법이 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Time Series Insights와의 관계를 설정하려면 먼저 다음 리소스를 설정해야 합니다.
* **Azure Digital Twins 인스턴스**. 자세한 지침은 [Azure Digital Twins 인스턴스 및 인증 설정](./how-to-set-up-instance-portal.md)을 참조하세요.
* **Azure Digital Twins 인스턴스의 모델 및 트윈**. Time Series Insights에서 추적된 데이터를 보려면 트윈의 정보를 몇 번 업데이트해야 합니다. 자세한 지침은’IoT Hub에서 원격 분석 수집’ 문서에서 [모델 및 트윈 추가](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) 섹션을 참조하세요.

> [!TIP]
> 이 문서에서는 Time Series Insights에 표시되는 변화하는 디지털 트윈 값을 간단하게 수동으로 업데이트합니다. 그러나 라이브 시뮬레이션 데이터로 이 문서를 완료하려는 경우 시뮬레이션된 디바이스에서 IoT 원격 분석 이벤트를 기반으로 디지털 트윈을 업데이트하는 Azure 함수를 설정할 수 있습니다. 지침은 디바이스 시뮬레이터를 실행하고 데이터 흐름이 작동하는지 확인하는 마지막 단계를 포함하여 [IoT Hub 데이터 수집](how-to-ingest-iot-hub-data.md)을 참조하세요.
>
> 나중에 디바이스 시뮬레이터 실행을 시작하는 위치를 보여주고 Azure 함수가 수동 디지털 트윈 업데이트 명령을 보내는 대신 트윈을 자동으로 업데이트하도록 하는 다른 TIP를 찾으세요.


## <a name="solution-architecture"></a>솔루션 아키텍처

아래 경로를 통해 Azure Digital Twins에 Time Series Insights를 연결합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Time Series Insights를 강조하는 엔드투엔드 시나리오의 Azure 서비스 다이어그램" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>이벤트 허브 네임스페이스 만들기

이벤트 허브를 만들기 전에 먼저 Azure Digital Twins 인스턴스에서 이벤트를 수신할 이벤트 허브 네임스페이스를 만듭니다. 아래 Azure CLI 명령을 사용하거나 [Azure Portal을 사용하여 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)에 따라 Azure Portal을 사용할 수 있습니다. 이벤트 허브를 지원하는 지역을 확인하려면 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)을 참조하세요.

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-Event-Hubs-namespace> --resource-group <your-resource-group> --location <region>
```

> [!TIP]
> `BadRequest: The specified service namespace is invalid.`라는 오류가 발생하면 네임스페이스에 대해 선택한 이름이 이 참조 문서 [네임스페이스 만들기](/rest/api/servicebus/create-namespace)에 설명된 이름 지정 요구 사항을 충족하는지 확인합니다.

이 이벤트 허브 네임스페이스를 사용하여 이 문서에 필요한 두 개의 이벤트 허브를 보유하게 됩니다.

  1. **트윈 허브** - 트윈 변경 이벤트를 수신하는 이벤트 허브
  2. **시계열 허브** - Time Series Insights로 이벤트를 스트리밍하는 이벤트 허브

다음 섹션에서는 이벤트 허브 네임스페이스 내에서 이러한 허브를 만들고 구성하는 과정을 안내합니다.

## <a name="create-twins-hub"></a>트윈 허브 만들기

이 문서에서 만들 첫 번째 이벤트 허브는 **트윈 허브** 입니다. 이 이벤트 허브는 Azure Digital Twins에서 트윈 변경 이벤트를 수신합니다.
트윈 허브를 설정하려면 이 섹션에서 다음 단계를 완료합니다.

1. 트윈 허브 만들기
2. 허브에 대한 권한을 제어하기 위한 권한 부여 규칙 만들기
3. 권한 부여 규칙을 사용하여 허브에 액세스하는 Azure Digital Twins에서 엔드포인트 만들기
4. 트윈 업데이트 이벤트를 엔드포인트 및 연결된 트윈 허브로 보내는 Azure Digital Twins에서 경로 만들기
5. 트윈 허브 연결 문자열 가져오기

다음 CLI 명령을 사용하여 **트윈 허브** 를 만듭니다. 트윈 허브에 대한 이름을 지정합니다.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>트윈 허브 권한 부여 규칙 만들기

보내기 및 받기 권한을 사용하여 [권한 부여 규칙](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create)을 만듭니다. 규칙의 이름을 지정합니다.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>트윈 허브 엔드포인트 만들기

Azure Digital Twins 인스턴스에 이벤트 허브를 연결하는 Azure Digital Twins [엔드포인트](concepts-route-events.md#create-an-endpoint)를 만듭니다. 트윈 허브 엔드포인트에 대한 이름을 지정합니다.

```azurecli-interactive
az dt endpoint create eventhub --dt-name <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-Event-Hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>트윈 허브 이벤트 경로 만들기

Azure Digital Twins 인스턴스는 트윈의 상태가 업데이트될 때마다 [트윈 업데이트 이벤트](./concepts-event-notifications.md)를 내보낼 수 있습니다. 이 섹션에서는 추가 처리를 위해 이러한 업데이트 이벤트를 트윈 허브로 보내는 Azure Digital Twins **이벤트 경로** 를 만듭니다.

Azure Digital Twins에서 [경로](concepts-route-events.md#create-an-event-route)를 만들어 위에서 엔드포인트로 트윈 업데이트 이벤트를 보냅니다. 이 경로의 필터는 트윈 업데이트 메시지만 엔드포인트에 전달되도록 허용합니다. 트윈 허브 이벤트 경로에 대한 이름을 지정합니다.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>트윈 허브 연결 문자열 가져오기

트윈 허브에 대해 위에서 만든 권한 부여 규칙을 사용하여 [트윈 이벤트 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
결과의 **primaryConnectionString** 값을 참고하여 이 문서 뒷부분의 트윈 허브 앱 설정을 구성합니다.

## <a name="create-time-series-hub"></a>시계열 허브 만들기

이 문서에서 생성하는 두 번째 이벤트 허브는 **시계열 허브** 입니다. 이것은 Azure Digital Twins 이벤트를 Time Series Insights로 스트리밍할 이벤트 허브입니다.
시계열 허브를 설정하려면 다음 단계를 완료합니다.

1. 시계열 허브 만들기
2. 허브에 대한 권한을 제어하기 위한 권한 부여 규칙 만들기
3. 시계열 허브 연결 문자열 가져오기

나중에 Time Series Insights 인스턴스를 생성할 때 이 시계열 허브를 Time Series Insights 인스턴스의 이벤트 소스로 연결합니다.

다음 명령을 사용하여 **시계열 허브** 를 생성합니다. 시계열 허브의 이름을 지정합니다.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>시계열 허브 권한 부여 규칙 만들기

보내기 및 받기 권한을 사용하여 [권한 부여 규칙](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create)을 만듭니다. 시계열 허브 인증 규칙의 이름을 지정합니다.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>시계열 허브 연결 문자열 가져오기

시계열 허브에 대해 위에서 만든 권한 부여 규칙을 사용하여 [시계열 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
결과의 **primaryConnectionString** 값을 참고하여 이 문서 뒷부분의 시계열 허브 앱 설정을 구성합니다.

또한 나중에 Time Series Insights 인스턴스를 만드는 데 사용할 수 있도록 다음 값을 기록해 두세요.
* 이벤트 허브 네임스페이스
* 시계열 허브 이름
* 시계열 허브 인증 규칙

## <a name="create-a-function"></a>함수 만들기

이 섹션에서 트윈 업데이트 이벤트를 원래 형식에서 JSON 패치 문서로 변환하는 Azure 함수를 만들고 트윈에서 업데이트되고 추가된 값만 포함하는 JSON 개체로 변환합니다.

1. 먼저 Visual Studio에서 새 함수 앱 프로젝트를 만듭니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)을 참조하세요.

2. *ProcessDTUpdatetoTSI.cs* 라는 새 Azure 함수를 만들어 디바이스 원격 분석 이벤트를 Time Series Insights로 업데이트합니다. 함수 유형은 **이벤트 허브 트리거** 입니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="이벤트 허브 트리거 유형의 새 Azure 함수를 만드는 Visual Studio의 스크린샷.":::

3. 프로젝트에 다음 패키지를 추가합니다.
    * [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

4. *ProcessDTUpdatetoTSI.cs* 파일의 코드를 다음 코드로 바꿉니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

    함수 코드를 저장합니다.

5. *ProcessDTUpdatetoTSI.cs* 함수가 있는 프로젝트를 Azure의 함수 앱에 게시합니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

나중에 두 이벤트 허브에 대한 앱 설정을 구성하는 데 사용할 함수 앱 이름을 저장합니다.

### <a name="configure-the-function-app"></a>함수 앱 구성

다음으로, 함수에 대한 **액세스 역할을 할당** 하고 **애플리케이션 설정을 구성** 하여 리소스에 액세스할 수 있도록 합니다.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

다음으로 함수 앱의 설정에서 **트윈스 허브** 및 **시계열 허브** 에 액세스할 수 있는 환경 변수를 추가합니다.

이전에 저장한 **트윈 허브 primaryConnectionString** 값을 사용하여 트윈 허브 연결 문자열을 포함하는 함수 앱에서 앱 설정을 만듭니다.

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

이전에 저장한 **시계열 허브 primaryConnectionString** 값을 사용하여 시계열 허브 연결 문자열을 포함하는 함수 앱에서 앱 설정을 만듭니다.

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 만들기 및 연결

이 섹션에서는 시계열 허브에서 데이터를 수신하도록 Time Series Insights 인스턴스를 설정합니다. 이 프로세스에 대한 자세한 내용은 [Azure Time Series Insights Gen2 PAYG 환경 설정](../time-series-insights/tutorial-set-up-environment.md)을 참조하세요. Time Series Insights 환경을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 ‘Time Series Insights 환경’을 검색하고 **만들기** 단추를 선택합니다. 시계열 환경을 만들려면 다음 옵션을 선택합니다.

    * **구독** - 구독을 선택합니다.
        - **리소스 그룹** - 리소스 그룹을 선택합니다.
    * **환경 이름** - 시계열 환경의 이름을 지정합니다.
    * **위치** - 위치를 선택합니다.
    * **계층** - **Gen2 (L1)** 가격 책정 계층을 선택합니다.
    * **속성 이름** - **$dtId** 를 입력합니다([시계열 ID 선택 모범 사례](../time-series-insights/how-to-select-tsid.md)에서 ID 값 선택에 대해 자세히 알아보기).
    * **스토리지 계정 이름** - 스토리지 계정 이름을 지정합니다.
    * **웜 저장소 사용** - 이 필드를 *예* 로 설정합니다.

    이 페이지의 다른 속성에 대한 기본값을 그대로 둘 수 있습니다. **다음 : 이벤트 소스 >** 단추를 선택합니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights 환경을 만드는 방법을 보여주는 Azure Portal의 스크린샷(1/3부)." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights 환경을 만드는 방법을 보여주는 Azure Portal의 스크린샷(2/3부)." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. *이벤트 원본* 탭에서 다음 필드를 선택합니다.

   * **이벤트 원본을 만드시겠습니까?** - *예* 를 선택합니다.
   * **원본 유형** - *이벤트 허브* 를 선택합니다.
   * **이름** - 이벤트 원본의 이름을 지정합니다.
   * **구독** - Azure 구독을 선택합니다.
   * **이벤트 허브 네임스페이스** - 이 문서 앞부분에서 만든 네임스페이스를 선택합니다.
   * **이벤트 허브 이름** - 이 문서 앞부분에서 만든 **시계열 허브** 이름을 선택합니다.
   * **이벤트 허브 액세스 정책 이름** - 이 문서 앞부분에서 만든 **시계열 허브 인증 규칙** 을 선택합니다.
   * **이벤트 허브 소비자 그룹** - *새로 만들기* 를 선택하고 이벤트 허브 소비자 그룹의 이름을 지정합니다. 그런 후 *추가* 를 선택합니다.
   * **속성 이름** - 이 필드는 비워 둡니다.
    
    **검토 + 만들기** 단추를 선택하여 모든 세부 정보를 검토합니다. 그런 다음, **검토 + 만들기** 단추를 다시 선택하여 시계열 환경을 만듭니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Time Series Insights 환경을 만드는 방법을 보여주는 Azure Portal의 스크린샷(3/3부)." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT 데이터를 Azure Digital Twins로 보내기

Time Series Insights로 데이터 보내기를 시작하려면 Azure Digital Twins에서 변경 데이터 값을 사용하여 디지털 트윈 속성 업데이트를 시작해야 합니다.

[az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update) CLI 명령을 사용하여 [필수 조건](#prerequisites) 섹션에서 추가한 트윈의 속성을 업데이트합니다. [IoT Hub에서 원격 분석 수집](how-to-ingest-iot-hub-data.md))의 트윈 만들기 지침을 사용한 경우 로컬 CLI 또는 Cloud Shell **bash** 터미널에서 다음 명령을 사용하여 thermostat67 트윈의 온도 속성을 업데이트할 수 있습니다.

```azurecli-interactive
az dt twin update --dt-name <your-Azure-Digital-Twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**다른 속성 값으로 명령을 4회 이상 반복** 하여 나중에 Time Series Insights 환경에서 관찰할 수 있는 여러 데이터 포인트를 만듭니다.

> [!TIP]
> 디지털 트윈 값을 수동으로 업데이트하는 대신 실시간 시뮬레이션 데이터로 이 문서를 완료하려면 먼저 [필수 구성 요소](#prerequisites) 섹션의 TIP를 완료하여 시뮬레이션된 디바이스에서 트윈을 업데이트하는 Azure 함수를 설정했는지 확인합니다.
그런 다음, 지금 디바이스를 실행하여 시뮬레이션된 데이터를 전송하고 해당 데이터 흐름을 통해 디지털 트윈을 업데이트할 수 있습니다.

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights에서 데이터 시각화

이제 데이터가 Time Series Insights 인스턴스로 이동하여 분석할 준비가 되어야 합니다. 다음 단계에 따라 들어오는 데이터를 탐색합니다.

1. [Azure Portal](https://portal.azure.com)에서 이전에 만든 시계열 환경 이름을 검색합니다. 왼쪽의 메뉴 옵션에서 *개요* 를 선택하여 *Time Series Insights 탐색기 URL* 을 확인합니다. Time Series Insights 환경에 반영된 온도 변화를 보려면 URL을 선택합니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights 환경의 개요 탭에 Time Series Insights 탐색기 URL을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. 탐색기의 왼쪽에 표시된 Azure Digital Twins 인스턴스에서 트윈을 볼 수 있습니다. 속성을 편집한 트윈을 선택하고 변경한 속성을 선택한 다음, **추가** 를 선택합니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="thermostat67을 선택하고, 온도 속성을 선택하고, 강조 표시된 추가를 선택하는 단계가 포함된 Time Series Insights 탐색기의 스크린샷." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. 이제 그래프에 반영된 속성 변경 내용이 다음과 같이 표시됩니다. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="68에서 85 사이의 임의 값 줄을 보여주는 초기 온도 데이터가 있는 Time Series Insights 탐색기의 스크린샷." lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

시뮬레이션을 훨씬 더 오랫동안 실행하도록 허용하는 경우 시각화는 다음과 같습니다.

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="각 트윈의 온도 데이터가 서로 다른 색상의 세 평행선 그래프로 표시되는 Time Series Insights 탐색기의 스크린샷." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>다음 단계

디지털 트윈은 기본적으로 Time Series Insights의 플랫 계층으로 저장되지만 모델 정보와 조직의 다단계 계층 구조를 사용하여 보강할 수 있습니다. 이 프로세스에 대한 자세한 내용은 다음을 참조하세요. 

* [모델 정의 및 적용](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Azure Digital Twins에 이미 저장된 모델 및 그래프 데이터를 사용하여 이 정보를 자동으로 제공하는 사용자 지정 논리를 작성할 수 있습니다. 트윈 그래프에서 정보를 관리, 업그레이드 및 검색하는 방법에 대한 자세한 내용은 다음 참조를 참조하세요.

* [Digital Twin 관리](./how-to-manage-twin.md)
* [쌍 그래프 쿼리](./how-to-query-graph.md)