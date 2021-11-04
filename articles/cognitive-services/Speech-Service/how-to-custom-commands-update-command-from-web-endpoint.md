---
title: 웹 엔드포인트에서 명령 업데이트
titleSuffix: Azure Cognitive Services
description: 웹 엔드포인트에 대한 호출을 사용하여 명령 상태를 업데이트하는 방법을 알아봅니다.
services: cognitive-services
author: eric-urban
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: eur
ms.openlocfilehash: b1bc8cc4420c2865cefcb8b719a566f68f4d250e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511262"
---
# <a name="update-a-command-from-a-web-endpoint"></a>웹 엔드포인트에서 명령 업데이트

클라이언트 애플리케이션에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트해야 하는 경우 웹 엔드포인트에 대한 호출을 사용하여 명령을 업데이트할 수 있습니다.

이 문서에서는 웹 엔드포인트에서 진행 중인 명령을 업데이트하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure Function 만들기 

이 예에서는 다음 입력(또는 이 입력의 하위 집합)을 지원하는 HTTP 트리거 [Azure 함수](../../azure-functions/index.yml)가 필요합니다.

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

이 입력의 키 특성을 검토해 보겠습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 대화의 고유 식별자입니다. 클라이언트 앱에서 이 ID를 생성할 수 있습니다. |
| **currentCommand** | 대화에서 현재 활성화된 명령입니다. |
| **name** | 명령 이름입니다. `parameters` 특성은 매개 변수의 현재 값을 포함하는 맵입니다. |
| **currentGlobalParameters** | `parameters`와 같은 맵이지만 전역 매개 변수에 사용됩니다. |

Azure 함수의 출력은 다음 형식을 지원해야 합니다.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

이 형식은 [클라이언트에서 명령을 업데이트](./how-to-custom-commands-update-command-from-client.md)할 때 사용한 것과 동일하기 때문에 인식할 수 있습니다. 

이제 Node.js를 기반으로 Azure 함수를 만듭니다. 이 코드를 복사하여 붙여넣습니다.

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

사용자 지정 명령에서 이 Azure 함수를 호출하면 대화의 현재 값을 보냅니다. 업데이트할 매개 변수를 반환하거나 현재 명령을 취소하려는 경우 반환됩니다.

## <a name="update-the-existing-custom-commands-app"></a>기존 사용자 지정 명령 앱 업데이트

Azure 함수를 기존 사용자 지정 명령 앱에 연결해 보겠습니다.

1. `IncrementCounter`라는 새 명령을 추가합니다.
1. 값이 `increment`인 문장 예를 하나 추가합니다.
1. 기본값이 `0`인 `Number` 형식의 새 매개 변수 `Counter`(Azure 함수에 지정된 것과 동일한 이름)를 추가합니다.
1. Azure 함수의 URL을 사용하여 `IncrementEndpoint`라는 새 웹 엔드포인트를 추가하고 **원격 업데이트** 를 **사용** 으로 설정합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="원격 업데이트가 설정된 웹 엔드포인트 설정을 보여 주는 스크린샷":::
1. **IncrementRule** 이라는 새 상호 작용 규칙을 만들고 **웹 엔드포인트 호출** 작업을 추가합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="상호 작용 규칙 생성을 보여 주는 스크린샷":::
1. 작업 구성에서 `IncrementEndpoint`를 선택합니다. **성공 시** 를 값이 `Counter`인 **음성 응답 전송** 으로 구성하고 **실패 시** 를 오류 메시지로 구성합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="웹 엔드포인트를 호출하기 위한 증분 카운터 설정을 보여 주는 스크린샷":::
1. 규칙의 실행 후 상태를 **사용자 입력 대기** 로 설정합니다.

## <a name="test-it"></a>테스트

1. 앱을 저장하고 학습시킵니다.
1. **테스트** 를 선택합니다.
1. `increment`를 몇 번 보냅니다(`IncrementCounter` 명령에 대한 예 문장).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example-no-mic.png" alt-text="증분 카운터의 예를 보여 주는 스크린샷":::

Azure 함수가 매 차례마다 `Counter` 매개 변수의 값을 증가시키는 방법을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 애플리케이션에 CI/CD 프로세스 사용](./how-to-custom-commands-deploy-cicd.md)
