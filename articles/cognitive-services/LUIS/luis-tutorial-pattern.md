---
title: '자습서: 패턴 - LUIS'
description: 이 자습서에서는 패턴을 사용하여 의도 및 엔터티 예측 정확도를 높이고 발화 예제를 줄입니다. 패턴은 템플릿 발화 예제로 제공되며, 여기에는 엔터티 및 무시해도 되는 텍스트를 식별하는 구문이 포함됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 5d4ac6af77344717a9f38a96a28217de35d28d3d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454773"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>자습서: 예측을 개선하기 위해 일반적인 패턴 템플릿 발화 서식 추가

이 자습서에서는 패턴을 사용하여 의도 및 엔터티 예측 정확도를 높입니다. 그러면 발화 예제를 줄일 수 있습니다. 패턴은 의도에 할당된 템플릿 발화이며, 엔터티와 무시해도 되는 텍스트를 식별하는 구문을 포함하고 있습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 패턴 만들기
> * 향상된 패턴 예측 기능 확인
> * 무시 가능한 텍스트로 표시 및 패턴 내 중첩
> * 테스트 패널을 사용하여 패턴 성공 확인

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>의도 및 패턴의 발화

LUIS 앱에 다음과 같은 두 가지 발화 유형이 저장되어 있습니다.

* 의도의 발화 예제
* 패턴의 발화 예제

템플릿 발화를 패턴으로 추가하면 의도에 대한 발화 예제를 전체적으로 줄일 수 있습니다.

패턴은 텍스트 매칭과 기계 학습의 조합으로 적용됩니다.  의도의 예제 발화와 함께 패턴의 템플릿 발화는 의도에 맞는 발화 를 LUIS가 더 잘 이해하도록 도와줍니다.

## <a name="import-example-app-and-clone-to-new-version"></a>예제 앱을 가져와서 새 버전에 복제

다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true)을 다운로드하고 저장합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. JSON을 [LUIS 포털](https://www.luis.ai)에 있는 새 앱으로 가져옵니다. **내 앱** 페이지에서 **+ 대화용 새 앱** 을 선택한 다음, **JSON으로 가져오기** 를 선택합니다. 이전 단계에서 다운로드한 파일을 선택하고 앱 이름을 `Patterns tutorial`이라고 지정합니다.

## <a name="create-new-intents-and-their-utterances"></a>새 의도 및 해당 발언 만들기

두 의도는 발화 텍스트를 기반으로 관리자 또는 관리자의 부하 직원을 찾습니다. 어려운 점은 두 의도가 서로 다른 것을 _의미_ 하지만 대부분의 단어는 동일하기 때문입니다. 단어 순서만 다릅니다. 의도를 정확하게 예측하려면 많은 예제가 있어야 합니다.

1. 탐색 모음에서 **빌드** 를 선택합니다.

1. **의도** 페이지에서 **+ 만들기** 를 선택하여 새 의도를 만듭니다.

1. 팝업 대화 상자에서 `OrgChart-Manager`을 입력하고 **완료** 를 선택합니다.

    ![새 메시지 만들기 팝업 창](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. 의도에 발화 예제를 추가합니다. 이러한 발화는 _완벽하게_ 같지는 않지만 추출 가능한 패턴을 갖고 있습니다.

    |예제 발화|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. 왼쪽 탐색 영역에서 **의도** 를 선택합니다.

1. **+ 만들기** 를 선택하여 새 의도를 만듭니다. 팝업 대화 상자에서 `OrgChart-Reports`을 입력하고 **완료** 를 선택합니다.

1. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>예제 발언 수에 대한 주의 사항

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>테스트하거나 게시하기 전에 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>엔드포인트에서 쿼리할 앱 게시

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소 표시줄의 URL 끝으로 이동하여 _YOUR_QUERY_HERE_ 를 `Who is the boss of Jill Jones?`로 바꿉니다.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
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

최상위 의도인 `OrgChart-Manager`가 제대로 예측되었지만, 점수가 70% 를 초과하지 않아서 다음으로 높은 의도보다 충분히 높지 않습니다. 패턴을 사용하면 올바른 의도 점수가 훨씬 높은 백분율 값을 가지고 다음으로 가장 높은 점수보다 훨씬 높게 만들 수 있습니다.

두 번째 브라우저 창을 열어 둡니다. 자습서에서 나중에 다시 사용할 예정입니다.

## <a name="template-utterances"></a>템플릿 발언
Human Resource 주체 도메인의 특성상 조직에서 직원 관계를 묻는 몇 가지 일반적인 방법이 있습니다. 예를 들면 다음과 같습니다.

|발언|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

이러한 발언이 너무 가까워서 많은 발언 예제를 제공하지 않고는 각각의 컨텍스트 고유성을 확인하기가 어렵습니다. 의도에 대한 패턴을 추가하면 발언 예제를 더 많이 제공하지 않아도 LUIS가 의도에 대한 일반적인 발언 패턴을 학습합니다.

이 의도에 대한 템플릿 발화 예제는 다음과 같습니다.

|템플릿 발화 예제|구문 의미|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|서로 교환 가능한 `{EmployeeListEntity}`<br>`[?]` 무시|
|`Who reports to {EmployeeListEntity}[?]`|서로 교환 가능한 `{EmployeeListEntity}`<br>`[?]` 무시|

`{EmployeeListEntity}` 구문은 템플릿 발화 내의 엔터티 위치 및 엔터티 형식을 표시합니다. 선택적 구문인 `[?]`는 선택적인 단어 또는 [문장 부호](luis-reference-application-settings.md#punctuation-normalization)를 표시합니다. LUIS는 대괄호로 묶인 선택적 텍스트는 무시하고 발언을 일치시킵니다.

구문은 정규식처럼 보이지만 정규식이 아닙니다. 중괄호(`{}`) 및 대괄호(`[]`) 구문만 지원됩니다. 최대 두 수준까지 중첩될 수 있습니다.

패턴을 발언와 일치시키려면 _먼저_ 발언 내의 엔터티가 템플릿 발언의 엔터티와 일치되어야 합니다. 즉, 엔터티를 사용하는 패턴이 성공하려면 엔터티의 예제에 예측 수준이 높은 발화 예제가 충분히 있어야 합니다. 그러나 의도와 달리 템플릿은 엔터티 예측에 도움이 되지 않습니다.

**패턴을 사용하면 몇 가지 예제 발언을 제공할 수 있지만 엔터티가 검색되지 않으면 패턴이 일치되지 않습니다.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>OrgChart-Manager 의도의 패턴 추가

1. 위쪽 메뉴에서 **빌드** 를 선택합니다.

1. 왼쪽 탐색의 **앱 성능 개선** 아래에서 왼쪽 탐색의 **패턴** 을 선택합니다.

1. **OrgChart-Manager** 의도를 선택하고, 다음 템플릿 발화를 입력합니다.

    |템플릿 발언|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    이러한 템플릿 발언에는 중괄호 표기법을 사용하는 **EmployeeListEntity** 엔터티가 포함됩니다.

1. 패턴 페이지를 나가지 않은 상태에서 **OrgChart-Reports** 의도를 선택하고, 다음 템플릿 발화를 입력합니다.

    |템플릿 발언|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>패턴이 사용될 때의 엔드포인트 쿼리

이제 패턴이 앱에 추가되었으므로 예측 런타임 엔드포인트에서 앱을 학습, 게시 및 쿼리합니다.

1. **학습** 을 선택합니다. 학습이 완료되면 **게시** 를 선택하고 **프로덕션** 슬롯을 선택한 다음, **완료** 를 선택합니다.

1. 게시가 완료되면 브라우저 탭을 다시 엔드포인트 URL 탭으로 전환합니다.

1. 주소 표시줄에서 URL 끝으로 이동하여 쿼리가 아직 `Who is the boss of Jill Jones?`인지 확인한 다음, 새 예측을 위한 URL을 제출합니다.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
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

이제 의도 예측에 대한 확신이 훨씬 더 높고, 다음으로 높은 의도의 점수가 매우 낮습니다. 이러한 두 의도는 학습 시 전환되지 않습니다.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>선택적 텍스트 및 미리 작성된 엔터티 사용

이 자습서의 이전 패턴 템플릿 발언에는 문자 s의 소유격 `'s` 사용 및 물음표 `?` 사용과 같은 몇 가지 선택적 텍스트가 있었습니다. 발화 텍스트에 현재 및 미래의 날짜를 포함할 수 있도록 허용해야 한다고 가정할 경우

예제 발언은 다음과 같습니다.

|Intent|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

이 예제 각각에 올바른 예측을 위해 LUIS에 필요한 동사 시제 `was`, `is`, `will be`는 물론 날짜 `March 3`, `now` 및 `in a month`가 사용되었습니다. 표의 마지막 두 예제에는 `in` 및 `on`을 제외하고 거의 동일한 텍스트가 사용되었습니다.

이 선택적 정보를 허용하는 예제 템플릿 발화:

|Intent|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


대괄호 `[]`의 선택적 구문을 사용하면 템플릿 발언에 선택적 텍스트를 쉽게 추가할 수 있고 두 번째 수준 `[[]]`까지 중첩할 수 있으며 엔터티 또는 텍스트를 포함할 수 있습니다.


**질문: 각 템플릿 발화의 첫 글자인 `w`가 모두 소문자인 이유가 무엇인가요? 필요에 따라 대문자나 소문자가 되어야 하지 않나요?** 클라이언트 애플리케이션이 쿼리 엔드포인트에 제출한 발언은 소문자로 변환됩니다. 템플릿 발언은 대문자나 소문자일 수 있으며 엔드포인트 발언도 마찬가지입니다. 비교는 항상 소문자로 변환한 후 수행됩니다.

**질문: March 3이 예측된 경우 템플릿 발화의 미리 작성된 번호 부분이 숫자 `3`과 날짜 `March 3` 모두로 예측되지 않는 이유가 무엇인가요?** 템플릿 발언은 날짜를 컨텍스트에 따라 문자 그대로 `March 3`으로 사용하거나 `in a month`로 추상화하여 사용합니다. 날짜에는 숫자가 포함될 수 있지만 숫자가 반드시 날짜로 보이지는 아닙니다. 예측 JSON 결과로 반환하려는 유형을 가장 잘 나타내는 엔터티를 항상 사용하십시오.

**질문: `Who will {EmployeeListEntity}['s] manager be on March 3?`와 같이 표현이 부족한 발화의 경우는 어떤가요?** `will`과 `be`가 구분되는 경우와 같이 문법적으로 다른 동사 시제는 새로운 템플릿 발언이 되어야 합니다. 기존 템플릿 발언은 일치하지 않습니다. 발언의 의도는 변경되지 않았지만 발언의 단어 배치가 변경되었습니다. 이러한 변화는 LUIS의 예측에 영향을 미칩니다. 동사 시제에 대해 [Group 및 OR](#use-the-or-operator-and-groups)을 수행하여 이러한 발화를 결합할 수 있습니다.

> [!CAUTION]
> **기억해야 할 사항: 엔터티를 먼저 찾은 다음, 패턴을 일치시킵니다.**

### <a name="add-new-pattern-template-utterances"></a>새 패턴 템플릿 발언 추가

1. **빌드** 의 **패턴** 섹션에서 몇 가지 패턴 템플릿 발언을 새로 추가합니다. 의도 드롭 다운 메뉴에서 **OrgChart-Manager** 를 선택하고 다음 템플릿 발언을 각각 입력합니다.

    |Intent|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. 탐색 모음에서 **학습** 을 선택하여 앱을 학습시킵니다.

3. 학습이 완료되면 패널 위쪽에서 **테스트** 를 선택하여 테스트 패널을 엽니다.

4. 몇 가지 테스트 발언을 입력하여 패턴이 일치하는지 의도 점수가 상당히 높은지 확인합니다.

    첫 번째 발언을 입력한 다음, 모든 예측 결과를 볼 수 있도록 결과 아래에서 **검사** 를 선택합니다. 각 발언에는 **OrgChart-Manager** 의도가 있어야 하고 `EmployeeListEntity` 및 `datetimeV2` 엔터티 값을 추출해야 합니다.

    |발화|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

모든 발언의 내부에서 엔터티를 찾았으므로 동일한 패턴과 일치하며 예측 점수가 높습니다. 여러 가지 발화를 매칭할 몇 가지 패턴을 추가했습니다. 템플릿 발화가 패턴에서 작동하도록 만들기 위해 의도에 발화 예제를 추가할 필요는 없었습니다.

이 패턴 사용 방법은 다음을 제공했습니다.
* 가장 높은 예측 점수
* 의도의 동일한 발화 예제
* 패턴에 있는 소수의 체계적인 템플릿 발화

### <a name="use-the-or-operator-and-groups"></a>OR 연산자 및 group 사용

몇 가지 이전 템플릿 발화는 매우 유사합니다. 템플릿 발화를 줄이려면 **group** `()` 및 **OR** `|` 구문을 사용합니다.

다음 2가지 패턴은 group `()` 및 OR `|` 구문을 사용하여 단일 패턴으로 결합할 수 있습니다.

|Intent|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

새 템플릿 발화는 다음과 같습니다.

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

여기서는 필수 동사 시제를 **group** 으로 묶고, 선택적 `in` 및 `on`과 **or** 파이프를 사이에 사용합니다.

> [!NOTE]
> _OR_ 기호인 `|`(파이프)를 사용하는 경우, 예제 템플릿에서 파이프 기호 앞뒤에 공백을 두어 구분해야 합니다.

1. **패턴** 페이지에서 **OrgChart-Manager** 필터를 선택합니다. `manager`를 검색하여 목록의 범위를 좁힙니다.

1. 템플릿 발화의 한 가지 버전을 유지하고(다음 단계에서 편집) 다른 변형을 모두 삭제합니다.

1. 템플릿 발화를 다음으로 변경합니다.

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. 탐색 모음에서 **학습** 을 선택하여 앱을 학습시킵니다.

3. 학습이 완료되면 패널 위쪽에서 **테스트** 를 선택하여 테스트 패널을 엽니다.

    다음과 같이 테스트 창에서 발화 버전을 테스트합니다.

    |테스트 창에 입력할 발화|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

더 많은 패턴 구문을 사용하면 앱에서 유지 관리해야 하는 템플릿 발화의 수를 줄이면서도 높은 예측 점수를 얻을 수 있습니다.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>발화의 시작 및 끝 앵커 사용

이 패턴 구문은 시작 및 끝 발화 앵커 구문으로 캐럿 `^`을 제공합니다. 시작 및 끝 발화 앵커를 함께 사용하여 매우 구체적인 리터럴 발화를 지정할 수도 있고, 따로 사용하여 의도를 대상으로 지정할 수도 있습니다.

## <a name="using-patternany-entity"></a>Pattern.any 엔터티 사용

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Pattern.any를 사용하여 발화 예제 추가

1. 위쪽 탐색 영역에서 **빌드** 를 선택한 후 왼쪽 탐색 영역에서 **의도** 를 선택합니다.

1. 의도 목록에서 **FindForm** 을 선택합니다.

1. 일부 예제 발언을 추가합니다. Pattern.any패로 예측해야 하는 텍스트는 **굵은 텍스트** 에 있습니다. 양식 이름은 발화의 다른 단어에서 확인하기 어렵습니다. Pattern.any는 엔터티의 경계를 표시하여 도움을 줍니다.

    |예제 발화|양식 이름|
    |--|--|
    |**랩에서 화재 발생 시 해야 할 일** 양식은 어디에 있으며, 내가 읽은 후 누가 서명해야 하나요?|랩에서 화재 발생 시 해야 할 일
    |**신입 사원의 재배치 요청** 은 어디에 있나요?|신입 사원의 재배치 요청|
    |"**주 캠퍼스의 건강 및 웰빙 요청**"을 누가 작성했으며, 가장 최신 버전은 무엇인가요?|주 캠퍼스의 건강 및 웰빙 요청|
    |"**물리적 자산을 포함한 Office 이동 요청**" 양식을 찾고 있습니다. |물리적 자산을 포함한 Office 이동 요청|

    양식 이름이 다양한 형태로 존재하므로 Pattern.any 엔터티가 없으면 LUIS에서 양식 제목이 끝나는 위치를 이해하기 어렵습니다.

### <a name="create-a-patternany-entity"></a>Pattern.any 엔터티 만들기
Pattern.any 엔터티는 다양한 길이의 엔터티를 추출합니다. 패턴은 구문을 사용하여 엔터티의 시작과 끝을 표시하므로 이 엔터티는 패턴에서만 작동합니다.

1. 왼쪽 탐색에서 **엔터티** 를 선택합니다.

1. **+ 만들기** 를 선택하고, 이름으로 `FormName`을 입력하고, 형식으로 **Pattern.any** 를 선택합니다. **만들기** 를 선택합니다.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any를 사용하는 패턴 추가

1. 왼쪽 탐색에서 **패턴** 을 선택합니다.

1. **FindForm** 의도를 선택합니다.

1. 새 엔터티를 사용하는 다음 템플릿 발언을 입력합니다.

    |템플릿 발언|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. 앱을 학습합니다.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>자유 형식 데이터 추출의 새 패턴 테스트
1. 위쪽 막대에서 **테스트** 를 선택하여 테스트 패널을 엽니다.

1. 다음 발언을 입력합니다.

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. 결과 아래에서 **검사** 를 선택하여 엔터티 및 의도의 테스트 결과를 확인합니다.

    먼저 엔터티 `FormName`이 발견된 다음, 의도를 결정하는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다.

1. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

### <a name="using-an-explicit-list"></a>명시적 목록 사용

Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다.

## <a name="what-did-this-tutorial-accomplish"></a>이 자습서에서는 무엇을 했나요?

이 자습서에서는 발화 예제를 더 이상 추가하지 않고도 LUIS의 의도 예측 점수를 높일 수 있는 패턴을 추가했습니다. 엔터티와 무시 가능한 텍스트를 표시하면 LUIS에서 더 다양한 발화에 패턴을 적용할 수 있었습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> [패턴 구문에 대해 자세히 알아보기](./reference-pattern-syntax.md)
