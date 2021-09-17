---
title: 클라이언트 앱에서 명령 매개 변수 업데이트
titleSuffix: Azure Cognitive Services
description: 클라이언트 애플리케이션에서 명령을 업데이트하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: pafarley
ms.openlocfilehash: c5314e659379fb56aca1eea0f1834985fc303c86
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541030"
---
# <a name="update-a-command-from-a-client-app"></a>클라이언트 앱에서 명령 업데이트

이 문서에서는 클라이언트 애플리케이션에서 진행 중인 명령을 업데이트하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>명령 상태 업데이트

클라이언트 애플리케이션에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트해야 하는 경우 이벤트를 보내 명령을 업데이트할 수 있습니다.

이 시나리오를 설명하려면 다음 이벤트 작업을 전송하여 진행 중인 명령(`TurnOnOff`)의 상태를 업데이트합니다. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

이 작업의 키 특성을 검토해 보겠습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 이 작업은 `"event"` 유형입니다. |
| **name** | 이벤트 이름은 `"RemoteUpdate"`여야 합니다. |
| **value** | `"value"` 특성에는 현재 명령을 업데이트하는 데 필요한 특성이 있습니다. |
| **updatedCommand** | `"updatedCommand"` 특성에는 명령 이름이 포함됩니다. 해당 특성 내에서 `"updatedParameters"`는 매개 변수 이름과 업데이트된 값이 있는 맵입니다. |
| **cancel** | 진행 중인 명령을 취소해야 하는 경우 `"cancel"` 특성을 `true`로 설정합니다. |
| **updatedGlobalParameters** | `"updatedGlobalParameters"` 특성은 `"updatedParameters"`와 같은 맵이지만 전역 매개 변수에 사용됩니다. |
| **processTurn** | 작업을 보낸 후 설정을 처리해야 하는 경우 `"processTurn"` 특성을 `true`로 설정합니다. |

사용자 지정 명령 포털에서 이 시나리오를 테스트할 수 있습니다.

1. 이전에 만든 사용자 지정 명령 애플리케이션을 엽니다. 
1. **학습** 을 선택한 다음 **테스트** 를 선택합니다.
1. `turn`을 보냅니다.
1. 사이드 패널을 열고 **작업 편집기** 를 선택합니다.
1. 이전 섹션에서 지정한 `RemoteCommand` 이벤트를 입력하고 보냅니다.
    > [!div class="mx-imgBorder"]
    > ![원격 명령에 대한 이벤트를 보여 주는 스크린샷](media/custom-commands/send-remote-command-activity-no-mic.png)

`"OnOff"` 매개 변수의 값이 음성 또는 텍스트가 아니라, 클라이언트의 작업을 통해 `"on"`으로 설정되는 것을 확인합니다.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>명령에 대한 매개 변수 카탈로그 업데이트

매개 변수에 대해 유효한 옵션 목록을 구성할 때 매개 변수 값은 애플리케이션에 대해 전역적으로 정의됩니다. 

이 예에서 `SubjectDevice` 매개 변수는 대화에 관계없이 지원되는 값의 고정된 목록을 갖습니다.

각 대화에 대해 매개 변수의 카탈로그에 새 항목을 추가하려는 경우 다음 작업을 보낼 수 있습니다.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
이 작업을 통해 `"TurnOnOff"` 명령에서 `"SubjectDevice"` 매개 변수의 카탈로그에 `"stereo"` 항목을 추가했습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="카탈로그 업데이트를 보여 주는 스크린샷":::

다음 몇 가지 사항을 참고하세요.
- 이 작업은 한 번만 보내면 됩니다(이상적으로는 연결을 시작한 직후).
- 이 작업을 보낸 후에는 `ParameterCatalogsUpdated` 이벤트가 클라이언트로 다시 전송될 때까지 기다려야 합니다.

## <a name="add-more-context-from-the-client-application"></a>클라이언트 애플리케이션에서 더 많은 컨텍스트 추가

나중에 사용자 지정 명령 애플리케이션에서 사용할 수 있는 각 대화에 대해 클라이언트 애플리케이션에서 추가 컨텍스트를 설정할 수 있습니다. 

예를 들어 사용자 지정 명령 애플리케이션에 연결된 디바이스의 ID와 이름을 보내려는 시나리오에 대해 생각해 보겠습니다.

이 시나리오를 테스트하기 위해 현재 애플리케이션에서 새 명령을 만들어 보겠습니다.
1. `GetDeviceInfo`라는 새 명령을 작성합니다.
1. `get device info`의 문장 예를 추가합니다.
1. 완료 규칙 **완료** 에서 `clientContext`의 특성을 포함하는 **음성 응답 보내기** 작업을 추가합니다.
   ![컨텍스트와 함께 음성 전송에 대한 응답을 보여 주는 스크린샷](media/custom-commands/send-speech-response-context.png)
1. 애플리케이션을 저장, 학습 및 테스트합니다.
1. 테스트 창에서 클라이언트 컨텍스트를 업데이트하는 작업을 보냅니다.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. `get device info` 텍스트를 보냅니다.
   ![클라이언트 컨텍스트를 보내기 위한 작업을 보여 주는 스크린샷](media/custom-commands/send-client-context-activity-no-mic.png)

다음 몇 가지 사항을 참고하세요.
- 이 작업은 한 번만 보내면 됩니다(이상적으로는 연결을 시작한 직후).
- `clientContext`에 대해 복합 개체를 사용할 수 있습니다.
- 작업을 보내고 웹 엔드포인트를 호출하기 위해 음성 응답에 `clientContext`를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 엔드포인트에서 명령 업데이트](./how-to-custom-commands-update-command-from-web-endpoint.md)
