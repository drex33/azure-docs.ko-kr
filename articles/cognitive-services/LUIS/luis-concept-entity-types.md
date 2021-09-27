---
title: 엔터티 형식 - LUIS
description: 엔터티는 예측 런타임에 사용자 발화에서 데이터를 추출합니다. 엔터티의 _선택적인_ 보조 목적은 엔터티를 기능으로 사용하여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/26/2021
ms.openlocfilehash: 7eb4affb75816789dca094f9ec6e2ae19a222285
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582384"
---
# <a name="entities-in-luis"></a>LUIS의 엔터티

엔터티는 항목이거나 사용자 의도와 관련된 요소입니다. 엔터티는 발화에서 추출할 수 있는 데이터를 정의하며 사용자의 필수 작업을 완료하는 데 필수적입니다. 예를 들어:

|발화|예측된 의도|추출된 엔터티|설명|
|--|--|--|--|
|Hello, how are you?|Greeting|-|추출할 항목이 없습니다.|
|I want to order a small pizza|orderPizza| “small” | “Size” 엔터티는 “small”로 추출됩니다.|
|Turn off bedroom light|turnOff| “bedroom” | “Room” 엔터티는 “bedroom”으로 추출됩니다.|
|Check balance in my savings account ending in 4406|checkBalance| “savings”, “4406” | “accountType” 엔터티는 “savings”로, “accountNumber” 엔터티는 “4406”으로 추출됩니다.|
|Buy 3 tickets to New York|buyTickets| “3”, “New York” | “ticketsCount” 엔터티는 “3”으로, “Destination” 엔터티는 “New York”으로 추출됩니다.|

엔터티는 선택 사항이지만 권장됩니다. 앱의 모든 개념에 대해 엔터티를 생성할 필요는 없으며 다음과 같은 경우에만 가능합니다.

* 클라이언트 애플리케이션에 데이터가 필요한 경우 또는 
* 엔터티가 다른 엔터티 또는 의도에 대한 힌트 또는 신호 역할을 하는 경우 기능으로서의 엔터티에 대해 자세히 알아보려면 [기능으로서의 엔터티](#entities-as-features)를 참조하세요.

## <a name="entity-types"></a>엔터티 형식

엔터티를 만들려면 이름을 지정하고 형식을 지정해야 합니다. LUIS에서 엔터티에는 몇 가지 형식이 있습니다. 

## <a name="list-entity"></a>목록 엔터티

목록 엔터티는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. 목록 엔터티를 사용하여 여러 동의어 또는 변형을 인식하고 정규화된 출력을 추출할 수 있습니다. *권장* 옵션을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 

목록 엔터티는 컴퓨터에서 인식되지 않습니다. 즉, LUIS는 목록 엔터티의 추가 값을 검색하지 않습니다. LUIS는 모든 목록의 항목과 일치하는 항목을 응답의 엔터티로 표시합니다.

목록 엔터티의 일치는 대/소문자를 구분하며, 정확히 일치하는 항목을 추출해야 합니다. 정규화된 값은 목록 엔터티와 일치하는 경우에도 사용됩니다. 예를 들어:

|정규화된 값|동의어|
|--|--|
|작음|sm, sml, 매우 작음, 가장 작음|
|중간|md, mdm, 보통, 평균, 중간|
|큰|lg, lrg, 큼|

자세한 내용은 [목록 엔터티 참조 문서](reference-entity-list.md)를 참조하세요.

## <a name="regex-entity"></a>정규식 엔터티

정규식 엔터티는 사용자가 제공하는 정규식 패턴을 기반으로 하여 엔터티를 추출합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다. 정규식은 특정 형식으로 예상되는 구조화된 텍스트 또는 미리 정의된 영숫자 값의 시퀀스에 가장 적합합니다. 예를 들어:

|엔터티|정규식|예제|
|--|--|--|
|항공편 번호|항공편 [A-Z]{2} [0-9]{4}| 항공편 AS 1234|
|신용 카드 번호|[0-9]{16}|5478789865437632|

자세한 내용은 [정규식 엔터티 참조 문서](reference-entity-regular-expression.md)를 참조하세요.

## <a name="prebuilt-entity"></a>미리 빌드된 엔터티

LUIS는 이름, 날짜, 숫자 및 통화와 같은 일반적인 형식의 데이터를 인식하기 위해 미리 빌드된 엔터티 세트를 제공합니다.  미리 빌드된 엔터티의 동작은 고정되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다. 예를 들어:

|미리 빌드된 엔터티|예제 값|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, May 2nd, 8am on may 2nd 2019|

자세한 내용은 [미리 빌드된 엔터티 참조 문서](./luis-reference-prebuilt-entities.md)를 참조하세요.

## <a name="patternany-entity"></a>Pattern.Any 엔터티

pattern.Any 엔터티는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다. 특정 규칙이나 패턴을 따르며 고정된 어휘 구조를 사용하는 문장에 가장 적합합니다. 예를 들어:

|예제 발화|무늬|엔터티|
|--|--|--|
|Can I have a burger please?|Can I have a {meal} [please][?]| burger
|Can I have a pizza?|Can I have a {meal} [please][?]| 피자
|Where can I find The Great Gatsby?|Where can I find {bookName}?| The Great Gatsby|

자세한 내용은 [Pattern.Any 엔터티 참조 문서](./reference-entity-pattern-any.md)를 참조하세요.

## <a name="machine-learned-ml-entity"></a>기계 학습된(ML) 엔터티

기계 학습된 엔터티는 컨텍스트를 사용하여 레이블이 지정된 예제에 따라 엔터티를 추출합니다. 이는 LUIS 애플리케이션을 빌드하기 위한 기본 엔터티입니다. 기계 학습 알고리즘을 사용하고 애플리케이션에 맞게 레이블을 지정해야 합니다. ML 엔터티를 사용하여 항상 올바른 형식은 아니지만 의미가 동일한 데이터를 식별합니다. 

|예제 발화|추출된 *제품* 엔터티|
|--|--|
|I want to buy a book.|“book”|
|Can I get these shoes please?|“shoes”|
|Add those shorts to my basket.|“shorts”|

[여기](./reference-entity-machine-learned-entity.md)에서 기계 학습 엔터티에 대해 자세히 알아볼 수 있습니다.

자세한 내용은 [기계 학습 엔터티 참조 문서](./reference-entity-machine-learned-entity.md)를 참조하세요.

#### <a name="ml-entity-with-structure"></a>구조가 있는 ML 엔터티

ML 엔터티는 각각 고유한 속성을 가질 수 있는 더 작은 하위 엔터티로 구성될 수 있습니다. 예를 들어 *주소* 의 구조는 다음과 같습니다.

* 주소: 4567 Main Street, NY, 98052, USA
    * 건물 번호: 4567
    * 거리 이름: Main Street
    * 주: NY
    * 우편 번호: 98052
    * 국가: USA


## <a name="building-effective-ml-entities"></a>효과적인 ML 엔터티 빌드

기계 학습 엔터티를 효과적으로 빌드하려면 다음 모범 사례를 따르세요.

* 하위 엔터티가 포함된 기계 학습 엔터티가 있는 경우 엔터티 및 하위 엔터티의 다른 순서 및 변형이 레이블이 지정된 발화에 표시되는지 확인합니다. 레이블이 지정된 예제 발화는 유효한 모든 형태를 포함하며 발화 내에 표시되는 엔터티와 존재하지 않고 재배열된 엔터티도 포함해야 합니다.

* 엔터티를 매우 고정된 세트에 과잉 맞춤하는 것을 피하세요. 과잉 맞춤은 모델을 일반화하지 않은 경우에 발생하며 기계 학습 모델의 일반적인 문제입니다. 이는 앱이 새 유형의 예제에서 적절하게 작동하지 않음을 의미합니다. 또한 앱이 사용자가 제공하는 제한된 예제를 넘어 일반화할 수 있도록 레이블이 지정된 예제 발화를 변경할 수 있습니다.

* 레이블 지정은 전체 의도에서 일관되어야 합니다. 여기에는 이 엔터티를 포함하는 *None* 의도에서 제공하는 발화도 포함됩니다. 그렇지 않으면 모델에서 시퀀스를 효과적으로 확인할 수 없습니다.

## <a name="entities-as-features"></a>기능으로서의 엔터티

엔터티의 또 다른 중요한 기능은 시스템이 관찰하고 학습할 수 있도록 다른 의도나 엔터티에 대해 구별되는 특성이나 기능으로 엔터티를 사용하는 것입니다.

## <a name="entities-as-features-for-intents"></a>의도에 대한 기능으로서의 엔터티

엔터티를 의도에 대한 신호로 사용할 수 있습니다. 예를 들어 발화에 특정 엔터티가 있으면 어떤 의도가 있는지 구분할 수 있습니다.

|예제 발화|엔터티|Intent|
|--|--|--|
|Book me a *fight to New York*.|구/군/시|Book Flight|
|Book me the *main conference room*.|공간|Reserve Room|

## <a name="entities-as-feature-for-entities"></a>엔터티에 대한 기능으로서의 엔터티

다른 엔터티의 존재 여부에 대한 표시기로 엔터티를 사용할 수도 있습니다. 이에 대한 일반적인 예제는 미리 빌드된 엔터티를 다른 ML 엔터티에 대한 기능으로 사용하는 것입니다.
항공편 예약 시스템을 빌드하는 경우 “Book me a flight from Cairo to Seattle”과 같은 발화에서 ML 엔터티로 *Origin City* 와 *Destination City* 를 가집니다. 미리 빌드된 `GeographyV2` 엔터티를 두 엔터티 모두에 대한 기능으로 사용하는 것이 좋습니다.

자세한 내용은 [GeographyV2 엔터티 참조 문서](./luis-reference-prebuilt-geographyv2.md)를 참조하세요.

엔터티를 다른 엔터티에 대해 필수 기능으로 사용할 수도 있습니다. 그러면 추출된 엔터티를 확인하는 데 유용합니다. 예를 들어 피자 주문 애플리케이션을 만드는 중에 `Size` ML 엔터티가 있는 경우 `SizeList` 목록 엔터티를 만들어 `Size` 엔터티에 대한 필수 기능으로 사용할 수 있습니다. 애플리케이션은 발화에서 추출된 엔터티로 정규화된 값을 반환합니다. 

문화권에서 사용할 수 있는 미리 빌드된 엔터티 확인에 대한 자세한 내용은 [기능](luis-concept-feature.md) 및 [미리 빌드된 엔터티](./luis-reference-prebuilt-entities.md)를 참조하세요. 


## <a name="entity-prediction-status-and-errors"></a>엔터티 예측 상태 및 오류

LUIS 포털에서는 예제 발화에 대해 레이블을 지정한 엔터티와 다른 엔터티 예측이 엔터티에 있는 경우 다음을 보여 줍니다. 이 다른 점수는 현재 학습된 모델을 기반으로 합니다. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS 포털은 예제 발화에 대해 선택한 엔터티와 다른 엔터티 예측이 엔터티에 있는 경우를 표시합니다.":::

오류를 발생시키는 텍스트가 예제 발화 내에서 강조 표시되고 예제 발화 줄의 오른쪽에는 빨간색 삼각형으로 표시된 오류 표시기가 있습니다. 

엔터티 오류를 해결하려면 다음 방법 중 하나 이상을 시도해 보세요.

* 강조 표시된 텍스트의 레이블이 잘못 지정되어 있습니다. 수정하려면 레이블을 검토하고 수정하고 앱을 다시 학습합니다. 
* 엔터티의 개념을 식별할 수 있도록 엔터티에 대한 [기능](luis-concept-feature.md)을 만듭니다.
* [예제 발화](luis-concept-utterance.md)를 추가하고 엔터티에 레이블을 지정합니다.
* 엔터티의 개념을 식별하는 데 도움이 될 수 있는 예측 엔드포인트에서 수신한 모든 발화에 대해 [활성 학습 제안을 검토](luis-concept-review-endpoint-utterances.md)합니다.


## <a name="next-steps"></a>다음 단계

* [발화](luis-concept-utterance.md) 모범 예제에 대해 알아봅니다.
* LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
* LUIS [애플리케이션 한도](./luis-limits.md)에 대해 자세히 알아봅니다. 
* [자습서](tutorial-machine-learned-entity.md)를 통해 기계 학습 엔터티를 사용하여 발화에서 정형 데이터를 추출하는 방법을 알아봅니다.
