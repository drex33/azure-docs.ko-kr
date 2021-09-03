---
title: Azure IoT Central 솔루션에서 디바이스 명령을 사용하는 방법
description: Azure IoT Central 솔루션에서 디바이스 명령을 사용하는 방법입니다. 이 자습서에서는 Azure IoT Central 애플리케이션에 대한 클라이언트 앱의 디바이스 명령을 사용하는 방법을 보여줍니다.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6a1d252a6b9068697fe738d4d2b1c6bbf6ceeb5
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588276"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Azure IoT Central 솔루션에서 명령을 사용하는 방법

이 방법 가이드에서는 디바이스 템플릿에 정의된 명령을 사용하는 방법을 보여줍니다.

운영자는 IoT Central UI를 사용하여 디바이스에서 명령을 호출할 수 있습니다. 명령은 디바이스의 동작을 제어합니다. 예를 들어 운영자는 명령을 호출하여 디바이스를 다시 부팅하거나 진단 데이터를 수집할 수 있습니다.

디바이스는 다음을 수행할 수 있습니다.

* 명령에 즉시 응답합니다.
* 명령을 받으면 IoT Central에 응답하고 장기 실행 명령이 완료되면 IoT Central에 나중에 알립니다.

기본적으로 명령은 디바이스가 연결된 것으로 예상하며 디바이스에 연결할 수 없으면 실패합니다. 디바이스 템플릿 UI에서 **오프라인 상태인 경우 큐에 넣기** 옵션을 선택하면 디바이스가 온라인 상태가 될 때까지 명령을 큐에 넣을 수 있습니다. 이러한 오프라인 명령은 이 문서의 뒷부분에 있는 별도의 섹션에서 설명합니다.

## <a name="define-your-commands"></a>명령 정의

표준 명령은 디바이스에 전송되어 디바이스에 무언가를 하도록 지시합니다. 명령에는 추가 정보가 있는 매개 변수가 포함될 수 있습니다. 예를 들어 디바이스의 밸브를 여는 명령에는 밸브를 여는 정도를 지정하는 매개 변수가 있을 수 있습니다. 명령은 디바이스가 명령을 완료하면 반환 값을 받을 수도 있습니다. 예를 들어 디바이스에 진단을 실행하도록 요청하는 명령은 반환 값으로 진단 보고서를 받을 수 있습니다.

명령은 디바이스 템플릿의 일부로 정의됩니다. 다음 스크린샷은 **Thermostat** 디바이스 템플릿의 **Get Max-Min report** 명령 정의를 보여줍니다. 이 명령에는 요청 및 응답 매개 변수가 모두 있습니다. 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Thermostat 디바이스 템플릿의 Get Max Min Report 명령을 보여주는 스크린샷":::

다음 표에는 명령 기능의 구성 설정이 나와 있습니다.

| 필드             |설명|
|-------------------|-----------|
|표시 이름       |대시보드 타일과 디바이스 양식에서 사용되는 명령 값입니다.|
| 이름            | 명령 이름입니다. IoT Central은 표시 이름에서 이 필드의 값을 생성하지만, 필요한 경우 사용자 고유의 값을 선택할 수 있습니다. 이 필드는 영숫자여야 합니다. 디바이스 코드는 이 **이름** 값을 사용합니다.|
| 기능 유형 | 명령입니다.|
| 오프라인 상태인 경우 큐에 넣기 | 이 명령을 오프라인 명령으로 만들지 여부입니다. |
| 설명     | 명령 기능에 대한 설명입니다.|
| 의견     | 명령 기능에 대한 주석입니다.|
| 요청     | 디바이스 명령의 페이로드입니다.|
| 응답     | 디바이스 명령 응답의 페이로드입니다.|

다음 코드 조각은 디바이스 모델에 있는 명령의 JSON 표현을 보여줍니다. 이 예에서 응답 값은 여러 필드가 있는 복합 **개체** 유형입니다.

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> 디바이스 모델은 디바이스 템플릿 페이지에서 내보낼 수 있습니다.

이 명령 정의는 다음 필드를 사용하여 UI의 스크린샷과 연결할 수 있습니다.

* `@type` - 기능의 유형을 지정: `Command`
* `name` - 명령 값

표시 이름, 설명 등의 선택적 필드를 사용하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

## <a name="standard-commands"></a>표준 명령

이 섹션에서는 디바이스가 명령을 수신하는 즉시 응답 값을 보내는 방법을 보여줍니다.

다음 코드 조각은 디바이스가 성공 코드를 즉시 보내는 명령에 응답하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용합니다. 다른 언어 예제에 대해서는 [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md) 자습서를 참조하세요.

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

`onDeviceMethod`에 대한 호출은 `commandHandler` 메서드를 설정합니다. 이 명령 처리기는 다음을 수행합니다.

1. 명령의 이름을 확인합니다.
1. `getMaxMinReport` 명령의 경우 `getMaxMinReportObject`를 호출하여 반환 개체에 포함할 값을 검색합니다.
1. `sendCommandResponse`를 호출하여 응답을 IoT Central에 다시 보냅니다. 이 응답에는 성공을 나타내는 `200` 응답 코드가 포함됩니다.

다음 스크린샷은 성공적인 명령 응답이 IoT Central UI에 어떻게 표시되는지 보여줍니다.

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="표준 명령에 대한 명령 페이로드를 보는 방법을 보여주는 스크린샷":::

## <a name="long-running-commands"></a>장기 실행 명령

이 섹션에서는 명령이 완료되었다는 확인을 보내는 것을 디바이스에서 지연할 수 있는 방법을 보여줍니다.

다음 코드 조각은 디바이스가 장기 실행 명령을 구현하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용합니다. 다른 언어 예제에 대해서는 [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md) 자습서를 참조하세요.

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

`onDeviceMethod`에 대한 호출은 `commandHandler` 메서드를 설정합니다. 이 명령 처리기는 다음을 수행합니다.

1. 명령의 이름을 확인합니다.
1. `sendCommandResponse`를 호출하여 응답을 IoT Central에 다시 보냅니다. 이 응답에는 보류 중인 결과를 나타내는 `202` 응답 코드가 포함됩니다.
1. 장기 실행 작업을 완료합니다.
1. 명령과 이름이 같은 reported 속성을 사용하여 명령이 완료되었음을 IoT Central에 알립니다.

다음 스크린샷은 202 응답 코드를 수신할 때 IoT Central UI에 명령 응답이 어떻게 표시되는지 보여줍니다.

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="디바이스의 즉각적인 응답을 보여주는 스크린샷":::

다음 스크린샷은 명령이 완료되었음을 나타내는 속성 업데이트를 받을 때 IoT Central UI를 보여줍니다.

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="장기 실행 명령이 완료되었음을 보여주는 스크린샷":::

## <a name="offline-commands"></a>오프라인 명령

이 섹션에서는 디바이스에서 오프라인 명령을 처리하는 방식을 보여줍니다. 디바이스가 온라인인 경우 오프라인 명령을 받는 즉시 처리할 수 있습니다. 디바이스가 오프라인 상태이면 다음에 IoT Central에 연결할 때 오프라인 명령을 처리합니다. 디바이스는 오프라인 명령에 대한 응답으로 반환 값을 보낼 수 없습니다.

> [!NOTE]
> 이 문서에서는 편의를 위해 Node.js를 사용합니다.

다음 스크린샷은 **GenerateDiagnostics** 라는 오프라인 명령을 보여줍니다. 요청 매개 변수는 **StartTime** 이라는 datetime 속성과 **Bank** 라는 정수 열거형 속성이 있는 개체입니다.

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="오프라인 명령의 UI를 보여주는 스크린샷":::

다음 코드 조각은 클라이언트가 오프라인 명령을 수신 대기하고 메시지 내용을 표시하는 방법을 보여줍니다.

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

이전 코드 조각의 출력에는 **StartTime** 및 **Bank** 값이 있는 페이로드가 표시됩니다. 속성 목록에는 **method-name** 목록 항목에 명령 이름이 포함됩니다.

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> 오프라인 명령의 기본 TTL(Time-to-Live)은 24시간이며 이 시간이 지나면 메시지가 만료됩니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 명령을 사용하는 방법을 알아봤으면, [페이로드](concepts-telemetry-properties-commands.md)를 참조하여 명령 매개 변수에 대해 자세히 알아보고 [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)에서 다른 언어로 된 전체 코드 샘플을 확인합니다.
