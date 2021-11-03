---
title: 일괄 테스트를 수행하는 방법 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 일괄 테스트 집합을 사용하여 잘못된 의도 및 엔터티가 있는 발화를 찾습니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: 0039f84305233f9894c31ba6210a2d531c567498
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005958"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>예제 발화 집합으로 일괄 테스트

일괄 처리 테스트는 활성 학습 버전의 유효성을 검사하여 예측 정확도를 측정합니다. 일괄 테스트는 활성 버전의 각 의도와 엔터티의 정확도를 확인하는 데 도움이 됩니다. 일괄 처리 테스트 결과를 검토하여 앱이 올바른 의도를 자주 식별하지 못하는 경우 의도에 예제 발화 추가 또는 발화 내 엔터티에 레이블 지정 등의 적절한 조치를 취해 정확도를 향상합니다.

## <a name="group-data-for-batch-test"></a>일괄 처리 테스트를 위해 데이터 그룹화

일괄 처리 테스트에 사용되는 발언은 LUIS에 새로 추가된 발언이어야 합니다. 발화 데이터 세트가 있는 경우 발화를 의도에 추가되는 예제 발화, 게시된 엔드포인트에서 받은 발화, 학습된 후 LUIS 일괄 처리 테스트에 사용되는 발화의 세 가지 집합으로 나눕니다.

사용하는 배치 JSON 파일에는 시작 및 끝 위치를 포함하여 레이블이 지정된 최상위 수준의 기계 학습 엔터티가 있는 발화가 포함되어야 합니다. 발화는 이미 앱에 있는 예제의 일부가 아니어야 합니다. 의도 또는 엔터티에 대해 긍정적으로 예측하려는 발화여야 합니다.

의도 및/또는 엔터티별로 테스트를 분리하거나 모든 테스트(최대 1,000개 발화)를 동일한 파일에 포함할 수 있습니다. 

### <a name="common-errors-importing-a-batch"></a>배치를 가져오는 중 발생하는 일반적인 오류

LUIS에 일괄 처리 파일을 업로드하는 동안 오류가 발생하는 경우 다음과 같은 일반적인 문제를 확인합니다.

* 일괄 처리 파일에 발화가 1,000개를 초과함
* 엔터티 속성이 없는 발언 JSON 개체입니다. 속성은 빈 배열일 수 있습니다.
* 단어에 여러 엔터티로 레이블이 지정됨
* 공간에서 시작하거나 종료하는 엔터티 레이블.

## <a name="fixing-batch-errors"></a>일괄 처리 오류 수정

일괄 처리 테스트에 오류가 있는 경우 의도에 발언을 더 추가하거나, LUIS에서 의도를 구분하는 데 도움이 되도록 더 많은 발언에 엔터티로 레이블을 지정할 수 있습니다. 발언을 추가하고 레이블을 지정했지만 일괄 처리 테스트에서 예측 오류가 계속 발생하는 경우에는 LUIS 학습 속도 향상에 도움이 되도록 도메인 특정 어휘가 포함된 [구 목록](luis-concept-feature.md) 기능을 추가하는 것이 좋습니다.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>LUIS 포털을 사용하여 일괄 테스트 

### <a name="import-and-train-an-example-app"></a>앱 예 가져오기 및 학습

`1 pepperoni pizza on thin crust`와 같은 피자 주문을 받는 앱을 가져옵니다.

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)을 다운로드하고 저장합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **새 앱** 옆의 화살표를 선택하고 **JSON으로 가져오기** 를 클릭하여 JSON을 새 앱으로 가져옵니다. 앱 이름을 `Pizza app`으로 지정합니다.


1. 탐색 영역의 오른쪽 위 모서리에서 **학습** 을 선택하여 앱을 학습시킵니다.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>일괄 처리 테스트 파일

JSON 예제에는 테스트 파일의 모양을 설명하는 레이블이 지정된 하나의 엔터티가 있는 하나의 발화가 포함되어 있습니다. 사용자 고유의 테스트에는 올바른 의도 및 레이블이 지정된 기계 학습 엔터티가 포함된 발화가 많이 있어야 합니다.

1. 텍스트 편집기에서 `pizza-with-machine-learned-entity-test.json`을 만들거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)합니다.

2. JSON 형식의 배치 파일에서 테스트에서 예측하려는 **의도** 가 포함된 발화를 추가합니다.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>일괄 처리 실행

1. 맨 위 탐색 모음에서 **테스트** 를 선택합니다.

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다.

    ![일괄 테스트 링크](./media/luis-how-to-batch-test/batch-testing-link.png)

3. **가져오기** 를 선택합니다. 표시되는 대화 상자에서 **파일 선택** 을 선택하고 테스트할 *1,000개 이하* 의 발화를 포함하는 올바른 JSON 형식의 JSON 파일을 찾습니다.

    가져오기 오류는 브라우저 위쪽의 빨간색 알림 표시줄에 보고됩니다. 가져오기에 오류가 있으면 데이터 세트가 생성되지 않습니다. 자세한 내용은 [일반 오류](#common-errors-importing-a-batch)를 참조하세요.

4. `pizza-with-machine-learned-entity-test.json` 파일의 파일 위치를 선택합니다.

5. 데이터 세트의 이름을 `pizza test`로 지정하고 **완료** 를 선택합니다.

6. **실행** 단추를 선택합니다. 

7. 일괄 처리 테스트가 완료되면 다음 열을 볼 수 있습니다.

    | 열 | Description |
    | -------- | ------------- |
    | 시스템 상태 | 테스트 상태입니다. **결과 보기** 는 테스트를 완료한 후에만 볼 수 있습니다. |
    | 이름 | 테스트에 제공한 이름입니다. |
    | 크기 | 이 일괄 처리 테스트 파일의 테스트 수입니다. |
    | 마지막 실행 | 이 일괄 처리 테스트 파일을 마지막으로 실행한 날짜입니다. |
    | 마지막 결과 | 테스트에서 성공한 예측 수입니다. |

8. 테스트의 자세한 결과를 보려면 **결과 보기** 를 선택합니다. 

    > [!TIP]
    > * **다운로드** 를 선택하면 업로드한 것과 동일한 파일이 다운로드됩니다.
    > * 일괄 처리 테스트가 실패한 것으로 확인되면 하나 이상의 발화 의도가 예측과 일치하지 않은 것입니다.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>의도에 대한 일괄 처리 결과 검토

일괄 테스트 결과를 검토하려면 **결과 보기** 를 선택합니다. 테스트 결과는 활성 버전에 대해 테스트 발화가 예측된 방법을 그래픽으로 보여 줍니다.

일괄 처리 차트는 결과의 사분면으로 표시됩니다. 차트의 오른쪽이 필터입니다. 필터에는 의도와 엔터티가 포함됩니다. [차트의 섹션](#review-batch-results-for-intents) 또는 차트 내의 한 점을 선택하면 차트 아래에 관련된 발언이 표시됩니다.

차트 위로 마우스를 가져간 후 마우스 휠로 차트의 발언을 확대하거나 축소할 수 있습니다. 차트에 많은 점이 밀집되어 있을 때 이렇게 하면 편리합니다.

차트는 사분면으로 구분되며 그중 두 개의 섹션이 빨간색으로 표시됩니다.

1. 필터 목록에서 **ModifyOrder** 의도를 선택합니다. 발화는 **진양성** 으로 예측됩니다. 즉 발화가 배치 파일에 나열된 긍정 예측과 일치합니다.

    > [!div class="mx-imgBorder"]
    > ![긍정 예측과 성공적으로 일치하는 발화](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    필터 목록의 녹색 확인 표시도 각 의도에 대한 테스트의 성공을 나타냅니다. 각 의도에 대해 발화가 일괄 처리 테스트에 나열되지 않은 의도에 대한 부정 테스트로 테스트되었으므로 다른 모든 의도는 1/1 긍정 점수로 나열됩니다.

1. **확인** 의도를 선택합니다. 이 의도는 일괄 처리 테스트에 나열되지 않으므로 일괄 처리 테스트에 나열된 발화에 대한 부정 테스트입니다.

    > [!div class="mx-imgBorder"]
    > ![배치 파일에 나열되지 않은 의도에 대해 성공적으로 부정으로 예측된 발화](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    필터의 녹색 텍스트와 그리드에서 표시한 대로 부정 테스트에 성공했습니다.

### <a name="review-batch-test-results-for-entities"></a>엔터티에 대한 일괄 처리 테스트 결과 검토

하위 엔터티가 있는 머신 엔터티인 ModifyOrder 엔터티는 최상위 엔터티가 일치하는지 여부와 하위 엔터티를 예측하는 방법을 표시합니다.

1. 필터 목록에서 **ModifyOrder** 엔터티를 선택한 다음, 그리드에서 원을 선택합니다.

1. 엔터티 예측이 차트 아래에 표시됩니다. 표시에는 예상과 일치하는 예측의 실선 및 예상과 일치하지 않는 예측의 점선이 포함됩니다.

    > [!div class="mx-imgBorder"]
    > ![배치 파일에서 성공적으로 예측된 엔터티 부모 항목](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>차트 결과 필터링

특정 의도 또는 엔터티별로 차트를 필터링하려면 오른쪽 필터링 패널에서 의도 또는 엔터티를 선택합니다. 선택 항목에 따라 그래프에서 데이터 요소 및 해당 배포가 업데이트됩니다.

![시각화된 일괄 테스트 결과](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>차트 결과 예

LUIS 포털의 차트에서는 다음 작업을 수행할 수 있습니다.
 
#### <a name="view-single-point-utterance-data"></a>단일 지점 발화 데이터 보기

차트에서 데이터 요소를 마우스로 가리키면 해당 예측의 확신도 점수가 표시됩니다. 데이터 요소를 선택하여 페이지 아래쪽의 발화 목록에서 해당 발화를 검색합니다.

![선택한 발화](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>섹션 데이터 보기

4섹션 차트의 오른쪽 위에서 **가양성** 과 같은 섹션 이름을 선택합니다. 해당 섹션의 모든 발화가 목록의 차트 아래에 표시됩니다.

![섹션별 선택한 발화](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

이 이전 이미지에서 발화 `switch on`은 TurnAllOn 의도로 레이블이 지정되지만 None 의도의 예측을 수신했습니다. 이는 예상 예측을 만들기 위해 TurnAllOn 의도에 더 많은 예제 발화가 필요함을 나타냅니다.

빨간색으로 표시된 차트의 두 섹션은 예상 예측과 일치하지 않는 발화를 나타냅니다. 이는 LUIS에 추가 학습이 필요한 발화를 나타냅니다.

녹색으로 표시된 차트의 두 섹션은 예상 예측과 일치했습니다.

## <a name="batch-testing-using-the-rest-api"></a>REST API를 사용하여 일괄 테스트 

LUIS를 사용하면 LUIS 포털 및 REST API를 사용하여 테스트를 일괄 처리할 수 있습니다. REST API에 대한 엔드포인트는 다음과 같습니다. LUIS 포털을 사용한 일괄 테스트에 대한 자세한 내용은 [자습서: 일괄 테스트 데이터 집합]()을 참조하세요. 아래 전체 URL을 사용하여 자리 표시자 값을 고유한 LUIS 예측 키 및 엔드포인트로 바꿉니다. 

헤더의 `Ocp-Apim-Subscription-Key`에 LUIS 키를 추가하고 `Content-Type`을 `application/json`으로 설정해야 합니다.

### <a name="start-a-batch-test"></a>일괄 테스트 시작

앱 버전 ID 또는 게시 슬롯을 사용하여 일괄 테스트를 시작합니다. 다음 엔드포인트 형식 중 하나로 **POST** 요청을 보냅니다. 요청 본문에 일괄 처리 파일을 포함합니다.

게시 슬롯
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

앱 버전 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

이러한 엔드포인트는 상태를 확인하고 결과를 가져오는 데 사용할 작업 ID를 반환합니다. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>진행 중인 일괄 테스트의 상태 가져오기

시작한 일괄 테스트의 작업 ID를 사용하여 다음 엔드포인트 형식에서 해당 상태를 가져옵니다. 

게시 슬롯
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

앱 버전 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>일괄 테스트에서 결과 가져오기

시작한 일괄 테스트의 작업 ID를 사용하여 다음 엔드포인트 형식에서 해당 결과를 가져옵니다. 

게시 슬롯
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

앱 버전 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>발화의 일관 처리 파일

일괄 처리 테스트를 위해 *데이터 세트* 라는 발화 일괄 처리 파일을 제출합니다. 데이터 세트는 레이블이 지정된 최대 1,000개의 발화를 포함하는 JSON 형식의 파일입니다. 앱에서 최대 10개의 데이터 세트를 테스트할 수 있습니다. 더 많은 데이터 세트를 테스트해야 하는 경우 데이터 세트를 삭제하고 새 데이터 세트를 추가합니다. 모델의 모든 사용자 지정 엔터티는 일괄 처리 파일 데이터에 해당 엔터티가 없더라도 일괄 처리 테스트 엔터티 필터에 표시됩니다.

배치 파일은 발언으로 구성됩니다. 각 발화에는 감지될 것으로 예상하는 모든 [기계 학습 엔터티](luis-concept-entity-types.md#machine-learned-ml-entity)와 함께 예상된 의도 예측이 있어야 합니다.

### <a name="batch-syntax-template-for-intents-with-entities"></a>엔터티를 사용한 의도에 대한 일괄 처리 구문 템플릿

다음 템플릿을 사용하여 일괄 처리 파일을 시작합니다.

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

일괄 처리 파일은 **startPos** 및 **endPos** 속성을 사용하여 엔터티의 시작과 끝을 나타냅니다. 값은 0부터 시작하고 공백으로 시작하거나 끝나면 안 됩니다. 이 파일은 startIndex 및 endIndex 속성을 사용하는 쿼리 로그와 다릅니다.

엔터티를 테스트하지 않는 경우 `entities` 속성을 포함하여 빈 배열 `[]`와 같이 값을 정합니다.

### <a name="rest-api-batch-test-results"></a>REST API 일괄 테스트 결과

API에서 반환되는 여러 개체가 있습니다.

* 정밀도, 재현율, F-점수와 같은 의도 및 엔터티 모델에 대한 정보
* 각 엔터티에 대한 정밀도, 재현율 및 F-점수와 같은 엔터티 모델에 대한 정보 
  * `verbose` 플래그를 사용하여 `entityTextFScore` 및 `entityTypeFScore` 등 엔터티에 대한 더 많은 정보를 가져올 수 있습니다.
* 예측되고 레이블이 지정된 의도 이름으로 제공된 발화
* 거짓 긍정 엔터티 목록과 거짓 부정 엔터티 목록

## <a name="next-steps"></a>다음 단계

테스트에서 LUIS 앱이 올바른 의도와 엔터티를 인식하지 못하는 것으로 나타나면 추가 발화에 레이블을 지정하거나 기능을 추가하여 LUIS 앱의 성능을 개선할 수 있습니다.

* [LUIS로 제안된 발화에 레이블 지정](luis-how-to-review-endpoint-utterances.md)
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md)
