---
title: IoT Hub에서 원격 분석 수집
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 인스턴스에서 디바이스 원격 분석 메시지를 Azure IoT Hub에서 디지털 트윈으로 전송하는 방법을 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 10/28/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b10a87312fd7b27c22bbfa56a928a0357d4698e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500942"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins로 IoT Hub 원격 분석 수집

이 가이드에서는 IoT Hub 원격 분석을 받아서 Azure Digital Twins 인스턴스로 보낼 수 있는 함수를 작성하는 프로세스를 안내합니다.

Azure Digital Twins는 IoT 디바이스 및 기타 원본의 데이터를 기반으로 합니다. Azure Digital Twins에서 사용할 디바이스 데이터의 공통 원본은 [IoT Hub](../iot-hub/about-iot-hub.md)입니다.

Azure Digital Twins로 데이터를 수집하는 프로세스는 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 만든 함수와 같은 외부 계산 리소스를 설정하는 것입니다. 함수는 데이터를 수신하고 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)를 사용하여 그에 따라 [디지털 트윈](concepts-twins-graph.md)에서 속성을 설정하거나 원격 분석 이벤트를 발생시킵니다. 

이 방법 문서에서는 IoT Hub에서 원격 분석을 수집할 수 있는 함수를 작성하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 예를 계속하기 전에 다음 리소스를 필수 구성 요소로 설정해야 합니다.
* **IoT Hub**. 지침은 이 [IoT Hub 빠른 시작](../iot-hub/quickstart-send-telemetry-cli.md)의 *IoT Hub 만들기* 섹션을 참조하세요.
* 디바이스 원격 분석을 수신하는 **Azure Digital Twins 인스턴스**. 자세한 지침은 [Azure Digital Twins 인스턴스 및 인증 설정](./how-to-set-up-instance-portal.md)을 참조하세요.

이 문서에서는 **Visual Studio** 도 사용합니다. [Visual Studio 다운로드](https://visualstudio.microsoft.com/downloads/)에서 최신 버전을 다운로드할 수 있습니다.

## <a name="example-telemetry-scenario"></a>원격 분석 시나리오 예제

이 방법에서는 Azure의 함수를 사용하여 IoT Hub에서 Azure Digital Twins로 메시지를 보내는 방법을 간략하게 설명합니다. 메시지를 보내는 데 사용할 수 있는 여러 가지 구성 및 일치 전략이 있지만 이 문서의 예에는 다음과 같은 부분이 포함되어 있습니다.
* IoT Hub의 자동 온도 조절기 디바이스, 알려진 디바이스 ID 사용
* 디바이스를 나타내는 디지털 트윈, 일치하는 ID 사용

> [!NOTE]
> 이 예에서는 디바이스 ID와 해당하는 디지털 트윈의 ID 사이에 간단한 ID 일치를 사용하지만, 디바이스에서 해당 트윈(예: 매핑 테이블)으로 보다 정교한 매핑을 제공할 수 있습니다.

자동 온도 조절기 디바이스에서 온도 원격 분석 이벤트를 보낼 때마다 함수는 원격 분석을 처리하고 디지털 트윈의 *temperature* 속성은 업데이트해야 합니다. 이 시나리오는 아래 다이어그램에 설명되어 있습니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Azure Digital Twins의 트윈에 대한 온도 속성을 업데이트하는 Azure에서 함수로 온도 원격 분석을 보내는 IoT Hub 디바이스의 다이어그램." border="false":::

## <a name="add-a-model-and-twin"></a>모델 및 트윈 추가

이 섹션에서는 Azure Digital Twins에서 자동 온도 조절기 디바이스를 나타내고 IoT Hub 정보로 업데이트되는 [디지털 트윈](concepts-twins-graph.md)을 설정합니다.

자동 온도 조절기 형식 트윈을 만들려면 먼저 자동 온도 조절기 [모델](concepts-models.md)을 인스턴스에 업로드해야 합니다. 이 모델은 자동 온도 조절기의 속성을 설명하고 나중에 트윈을 만드는 데 사용됩니다.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

그런 다음 **이 모델을 사용하여 하나의 트윈을 만들어야** 합니다. 다음 명령을 사용하여 thermostat67이라는 자동 온도 조절기 트윈을 만들고 초기 온도 값으로 0.0을 설정합니다.

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

트윈이 성공적으로 만들어지면 명령의 CLI 출력은 다음과 같습니다.
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>함수 만들기

이 섹션에서는 Azure 함수를 만들어 Azure Digital Twins에 액세스하고 수신되는 IoT 원격 분석 이벤트에 따라 트윈을 업데이트합니다. 아래 단계에 따라 함수를 만들고 게시합니다.

1. 먼저 Visual Studio에서 새 함수 앱 프로젝트를 만듭니다. 이 작업을 수행하는 방법에 관한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)을 참조하세요.

2. 프로젝트에 다음 패키지를 추가합니다.
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. Visual Studio에서 생성한 *Function1.cs* 샘플 함수의 이름을 *IoTHubtoTwins.cs* 로 바꿉니다. 파일의 코드를 다음 코드로 바꿉니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    함수 코드를 저장합니다.

4. *IoTHubtoTwins.cs* 함수가 포함된 프로젝트를 Azure의 함수 앱에 게시합니다. 이 작업을 수행하는 방법에 관한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

함수 앱이 Azure Digital Twins에 액세스하려면 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한이 있는 시스템 관리 ID가 있어야 합니다. 이는 다음에 설정하게 됩니다.

### <a name="configure-the-function-app"></a>함수 앱 구성

다음으로, 함수에 대한 **액세스 역할을 할당** 하고 **애플리케이션 설정을 구성** 하여 Azure Digital twins 인스턴스에 액세스할 수 있도록 합니다.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>함수를 IoT Hub에 연결

이 섹션에서는 IoT 허브 디바이스 데이터의 이벤트 대상으로 함수를 설정합니다. 이러한 방식으로 함수를 설정하면 IoT Hub 자동 온도 조절기 디바이스의 데이터가 처리를 위해 Azure 함수로 전송됩니다.

[Azure Portal](https://portal.azure.com/)에서 [필수 구성 요소](#prerequisites) 섹션에서 만든 IoT Hub 인스턴스로 이동합니다. **이벤트** 아래에서 함수에 대한 구독을 만듭니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="이벤트 구독을 추가하는 것을 보여 주는 Azure Portal의 스크린샷.":::

**이벤트 구독 만들기** 페이지에서 다음과 같이 필드를 입력합니다.
  1. **이름** 에서 이벤트 구독에 대해 원하는 이름을 선택합니다.
  2. **이벤트 스키마** 에서 _Event Grid 스키마_ 를 선택합니다.
  3. **시스템 항목 이름** 에서 원하는 이름을 선택합니다.
  1. **이벤트 유형으로 필터링** 에서 _디바이스 원격 분석_ 확인란을 선택하고 다른 이벤트 유형을 선택 취소합니다.
  1. **엔드포인트 유형** 에서 _Azure 함수_ 를 선택합니다.
  1. **엔드포인트** 의 경우 _엔드포인트 선택_ 링크를 사용하여 엔드포인트에 사용할 Azure Function을 선택합니다.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="이벤트 구독 세부 정보를 만드는 Azure Portal의 스크린샷.":::

_Azure 함수 선택_ 페이지가 열리면 아래 세부 정보를 확인하거나 입력합니다.
 1. **구독**: Azure 구독.
 2. **리소스 그룹**: 리소스 그룹.
 3. **함수 앱**: 함수 앱 이름.
 4. **슬롯**: _프로덕션_.
 5. **함수**: 드롭다운에서 이전의 함수 *IoTHubtoTwins* 를 선택합니다.

_선택 확인_ 단추를 사용하여 세부 정보를 저장합니다.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="함수를 선택하는 Azure Portal의 스크린샷.":::

_만들기_ 단추를 선택하여 이벤트 구독을 만듭니다.

## <a name="send-simulated-iot-data"></a>시뮬레이트된 IoT 데이터 보내기

새 수신 함수를 테스트하려면 [엔드투엔드 솔루션 연결](./tutorial-end-to-end.md)의 디바이스 시뮬레이터를 사용합니다. 해당 자습서는 [C#으로 작성된 Azure Digital Twins 엔드투엔드 샘플 프로젝트](/samples/azure-samples/digital-twins-samples/digital-twins-samples)를 기반으로 합니다. 해당 리포지토리에서 **DeviceSimulator** 프로젝트를 사용하게 됩니다.

엔드투엔드 자습서에서 다음 단계를 완료합니다.
1. [IoT Hub에 시뮬레이션된 디바이스 등록](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [시뮬레이션 구성 및 실행](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>결과 유효성 검사

위의 디바이스 시뮬레이터를 실행하는 동안 디지털 트윈의 온도 값이 변경됩니다. Azure CLI에서 다음 명령을 실행하여 온도 값을 확인합니다.

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

출력에는 다음과 같은 온도 값이 포함되어야 합니다.

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

값 변경을 보려면 위의 쿼리 명령을 반복해서 실행합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins를 사용한 데이터 수신 및 송신에 대해 알아봅니다.
* [데이터 수신 및 송신](concepts-data-ingress-egress.md)
