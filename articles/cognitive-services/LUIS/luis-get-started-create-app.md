---
title: '빠른 시작: LUIS 포털에서 앱 빌드'
description: 이 빠른 시작에서는 미리 빌드된 도메인 `HomeAutomation`을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만드는 방법을 보여줍니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 완료하면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70972607788568fbc06f9efe06fe083fd8d8ac01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073627"
---
# <a name="quickstart-build-your-app-in-luis-portal"></a>빠른 시작: LUIS 포털에서 앱 빌드

> [!NOTE]
> 이제 최신 버전의 Language Understanding 기능을 Azure Cognitive Service for Language의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Cognitive Service for Language 설명서](../language-service/index.yml)를 참조하세요. 언어 서비스 내의 언어 이해 기능에 대해서는 [대화형 Language Understanding](../language-service/conversational-language-understanding/overview.md), [사용자 지정 명명된 엔터티 인식](../language-service/custom-named-entity-recognition/overview.md) 및 [사용자 지정 분류](../language-service/custom-classification/overview.md)를 참조하세요.

이 빠른 시작에서는 미리 빌드된 홈 자동 도메인을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 다음으로, 더 많은 의도와 엔터티를 추가하여 앱을 사용자 지정해 봅니다. 완료하면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>새 앱 만들기

애플리케이션은 **내 앱** 에서 만들고 관리할 수 있습니다.

### <a name="create-an-application"></a>애플리케이션 만들기

애플리케이션을 만들려면  **+ 새 앱** 을 클릭합니다. 

표시되는 창에 다음 정보를 입력합니다.

|이름  |설명  |
|---------|---------|
|이름     | 앱의 이름입니다. 예: "홈 자동화".        |
|culture     | 앱이 이해하고 말하는 언어입니다.   |
|설명 | 앱에 대한 설명입니다.
|예측 리소스 | 쿼리를 수신할 예측 리소스입니다. |

**완료** 를 선택합니다.

>[!NOTE]
>애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다.

## <a name="add-prebuilt-domain"></a>미리 빌드된 도메인 추가

LUIS는 애플리케이션을 시작하는 데 도움이 될 수 있는 미리 빌드된 도메인 세트를 제공합니다. 미리 빌드된 도메인 앱은 이미 [의도](./luis-concept-intent.md), [엔터티](./luis-concept-entity-types.md) 및 [발화](./luis-concept-utterance.md)로 채워져 있습니다.

1. 왼쪽 탐색 영역에서 **미리 빌드된 도메인** 을 선택합니다.
2. **HomeAutomation** 을 검색합니다.
3. HomeAutomation 카드에서 **도메인 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > !['미리 빌드된 도메인'을 선택한 다음, 'HomeAutomation'을 검색합니다. HomeAutomation 카드에서 '도메인 추가'를 선택합니다.](media/luis-quickstart-new-app/home-automation.png)

    도메인 추가에 성공하면 미리 작성된 도메인 상자에 **도메인 제거** 단추가 표시됩니다.

## <a name="check-out-intents-and-entities&quot;></a>의도 및 엔터티 확인

1. 왼쪽 탐색 메뉴에서 **의도** 를 선택하여 HomeAutomation 도메인 의도를 확인합니다. `HomeAutomation.QueryState` 및 `HomeAutomation.SetDevice`와 같은 예제 발화가 있습니다.

    > [!NOTE]
    > **없음** 은 모든 LUIS 앱에 제공되는 의도입니다. 이는 앱에 제공되는 기능과 일치하지 않는 발언을 처리하는 데 사용됩니다.

2. **HomeAutomation.TurnOff** 의도를 선택합니다. 의도에는 엔터티를 사용하여 레이블이 지정된 예제 발화 목록이 포함되어 있습니다.

    > [!div class=&quot;mx-imgBorder&quot;]
    > [![HomeAutomation.TurnOff 의도의 스크린샷](media/luis-quickstart-new-app/home-automation-turnoff.png &quot;HomeAutomation.TurnOff 의도의 스크린샷")](media/luis-quickstart-new-app/home-automation-turnoff.png)

3. 앱의 엔터티를 보려면 **엔터티** 를 선택합니다. **HomeAutomation.DeviceName** 과 같은 엔터티 중 하나를 클릭하면 연결된 값 목록이 표시됩니다. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="이미지 대체 텍스트" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>LUIS 앱 학습
애플리케이션이 의도, 엔터티 및 발화로 채워지면 변경 내용이 반영될 수 있도록 애플리케이션을 학습시켜야 합니다.

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>앱 테스트

앱을 학습시킨 후에는 테스트할 수 있습니다.

1. 오른쪽 상단 탐색에서 **테스트** 를 선택합니다.

1. 대화형 테스트 창에 테스트 발화를 입력하고 Enter 키를 누릅니다. 예를 들어 *광원을 해제* 합니다.

    이 예제에서 *Turn off the lights* 는 **HomeAutomation.TurnOff** 의 최고 득점 의도로 올바르게 식별되었습니다.

    :::image type="content" source="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png" alt-text="발화가 강조 표시된 테스트 패널의 스크린샷" lightbox="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png":::

1. **검사** 를 선택하여 예측에 대한 자세한 정보를 봅니다.

    :::image type="content" source="media/luis-quickstart-new-app/test.png" alt-text="검사 정보가 포함된 테스트 패널의 스크린샷" lightbox="media/luis-quickstart-new-app/test.png":::

1. 테스트 창을 닫습니다.

## <a name="customize-your-application"></a>애플리케이션 사용자 지정

미리 빌드된 도메인 외에 LUIS를 사용하면 자체 사용자 지정 애플리케이션을 만들거나 미리 빌드된 애플리케이션을 기반으로 사용자 지정할 수 있습니다.

### <a name="create-intents"></a>의도 만들기

앱에 더 많은 의도 추가하기

1. 왼쪽 탐색 메뉴에서 **의도** 를 선택합니다.
2. **만들기** 를 선택합니다.
3. 의도 이름(`HomeAutomation.AddDeviceAlias`)을 입력한 다음, 완료를 선택합니다.

### <a name="create-entities"></a>엔터티 만들기

앱에 더 많은 엔터티 추가하기

1. 왼쪽 탐색 메뉴에서 **엔터티** 를 선택합니다.
2. **만들기** 를 선택합니다.
3. 의도 이름(`HomeAutomation.DeviceAlias`)을 입력하고 **유형** 에서 학습한 머신을 선택한 다음, **만들기** 를 선택합니다.

### <a name="add-example-utterances"></a>예제 발화 추가

예제 발언은 챗봇이나 기타 클라이언트 애플리케이션에 사용자가 입력하는 텍스트입니다. 사용자 텍스트의 의도를 LUIS 의도에 매핑합니다.

`HomeAutomation.AddDeviceAlias`에 대한 **의도** 페이지에서 **예제 발화** 아래에 다음 예제 발화를 추가합니다.

|#|예제 발화|
|--|--|
|1|`Add alias to my fan to be wind machine`|
|2|`Alias lights to illumination`|
|3|`nickname living room speakers to our speakers a new fan`|
|4|`rename living room tv to main tv`|

[!INCLUDE [best-practice-utterances](./includes/best-practice-utterances.md)]


### <a name="label-example-utterances"></a>예제 발화의 레이블 지정

ML 엔터티를 추가했으므로 발화에 레이블을 지정해야 합니다. 레이블 지정은 만든 ML 엔터티를 추출하는 방법을 학습시키기 위해 애플리케이션에서 사용됩니다.

[!INCLUDE [how-to-label](./includes/how-to-label.md)]

## <a name="create-prediction-resource"></a>예측 리소스 만들기
이 시점에서 애플리케이션 제작을 완료했습니다. 예측 엔드포인트를 통해 채팅 봇이나 다른 클라이언트 애플리케이션에서 예측을 수신하려면 예측 리소스를 만들어 애플리케이션에 게시해야 합니다.

LUIS 포털에서 예측 리소스 만들기

[!INCLUDE [add-pred-resource-portal](./includes/add-prediction-resource-portal.md)]


## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]



## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 예측 엔드포인트 쿼리

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. 브라우저 주소 표시줄에서 쿼리 문자열에 대해 다음 값이 URL에 있는지 확인합니다. 쿼리 문자열에 없으면 추가하세요.

    * `verbose=true`
    * `show-all-intents=true`

3. 브라우저 주소 표시줄에서 URL 끝으로 이동하고 쿼리 문자열에 *turn off the living room light* 를 입력한 다음, Enter 키를 누릅니다.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* [반복적인 앱 디자인](./luis-concept-app-iteration.md)
* [모범 사례](./luis-concept-best-practices.md)
