---
title: 반복적인 앱 디자인 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 모델 변경, 발화 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 92991ef4b9889b0be2e887884ed46086b87ff9f3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588887"
---
# <a name="iterative-app-design-for-luis"></a>LUIS를 위한 반복적인 앱 디자인

LUIS(Language Understanding) 앱은 반복을 통해 가장 효율적으로 학습하고 수행합니다. 일반적인 반복 주기는 다음과 같습니다.

* 새 버전 만들기
* LUIS 앱 스키마 편집. 다음 내용이 포함됩니다.
    * 예제 발화를 사용한 의도
    * 엔터티
    * 기능
* 학습, 테스트 및 게시
    * 활성 학습을 위해 예측 엔드포인트에서 테스트
* 엔드포인트 쿼리에서 데이터 수집

![제작 주기](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS 스키마 빌드

앱의 스키마는 사용자에게 요청하는 항목(_의도_ )과 답변을 확인하는 데 사용되는 세부 정보를 제공하는 의도의 일부(_엔터티_)를 정의합니다.

일반적인 단어 순서뿐 아니라 관련된 단어 및 구문을 확인하려면 앱 스키마가 앱 도메인에 관련되어 있어야 합니다.

예제 발화는 인식된 음성 또는 텍스트와 같이 앱이 런타임에 기대하는 사용자 입력을 나타냅니다.

스키마에는 의도가 필요하며 엔터티가 _포함_ 되어야 합니다.

### <a name="example-schema-of-intents"></a>의도 스키마 예

가장 일반적인 스키마는 의도를 사용하여 구성된 의도 스키마입니다. 이 스키마 형식은 LUIS를 사용하여 사용자의 의도를 확인합니다.

의도 스키마 형식은 LUIS가 사용자의 의도를 결정하는 데 도움이 되는 경우 엔터티를 포함할 수 있습니다. 예를 들어, 배송 엔터티(의도에 대한 _기계 학습 기능_ 으로)를 사용하면 LUIS가 배송 의도를 확인하는 데 도움이 됩니다.

### <a name="example-schema-of-entities"></a>엔터티 스키마 예

엔터티 스키마는 사용자 발화에서 추출된 데이터인 엔터티를 중심으로 합니다. 사용자가 "I'd like to order three pizzas."라고 말하는 경우를 예로 들어 보겠습니다. 추출될 엔터티는 두 개(_three_ 및 _pizzas_)입니다. 이 엔터티는 주문을 수행하는 의도를 충족하는 데 사용됩니다.

엔터티 스키마의 경우에는 발화의 의도가 클라이언트 애플리케이션에 크게 중요하지 않습니다.

### <a name="example-of-a-mixed-schema"></a>혼합 스키마 예

가장 강력하고 완성도 높은 스키마는 모든 범위의 엔터티 및 기능이 포함된 의도 스키마입니다. 클라이언트 애플리케이션에 이러한 정보가 필요하므로 이 스키마는 의도 또는 엔터티 스키마로 시작할 수 있으며 두 개념을 모두 포함하도록 확장할 수 있습니다.

## <a name="add-example-utterances-to-intents"></a>의도에 예제 발화 추가

LUIS에는 각 **의도** 에 몇 가지 예제 발화가 필요합니다. 예제 발화에는 발화의 목적인 의도를 결정할 수 있는 단어 선택 및 단어 순서의 충분한 변형이 필요합니다.

> [!CAUTION]
> 예제 발화를 대량으로 추가하지 않습니다. 15~30개의 구체적이고 다양한 예로 시작합니다.

각 예제 발화는 **엔터티** 를 사용하여 디자인하고 레이블이 지정된 **추출할 필수 데이터** 가 있어야 합니다.

|Key 요소|목적|
|--|--|
|Intent|사용자 발화를 단일 의도 또는 작업으로 **분류** 합니다. 예에는 `BookFlight` 및 `GetWeather`가 포함됩니다.|
|엔터티|발화에서 의도를 완료하는 데 필요한 데이터를 **추출** 합니다. 예에서는 여행 날짜 및 시간과 위치가 포함됩니다.|

**None** 의도에 발화를 할당하여 앱 도메인에 관련되지 않은 발화를 무시하도록 LUIS 앱을 디자인할 수 있습니다.

## <a name="test-and-train-your-app"></a>앱 테스트 및 학습

각 의도에 15~30개의 다른 예가 있고 필수 엔터티가 레이블이 지정되면 LUIS 앱을 테스트하고 [학습](luis-how-to-train.md)시켜야 합니다.

## <a name="publish-to-a-prediction-endpoint"></a>예측 엔드포인트에 게시

LUIS 앱은 [예측 엔드포인트 지역](luis-reference-regions.md) 목록에서 사용할 수 있도록 게시해야 합니다.

## <a name="test-your-published-app"></a>게시된 앱 테스트

HTTPS 예측 엔드포인트에서 게시된 LUIS 앱을 테스트할 수 있습니다. 예측 엔드포인트에서 테스트하면 LUIS가 [검토](luis-how-to-review-endpoint-utterances.md)를 위해 신뢰도가 낮은 발화를 선택할 수 있습니다.

## <a name="create-a-new-version-for-each-cycle"></a>각 주기에 대해 새 버전 만들기

각 버전은 LUIS 앱의 시간별 스냅샷입니다. 앱을 변경하기 전에 새 버전을 만듭니다. 이 방법은 의도와 발화를 제거해 이전 상태로 돌아가려고 시도하는 것보다 기존 버전으로 돌아가기가 쉽습니다.

버전 ID는 문자, 숫자 또는 ‘.’으로 구성되며 10자 이하여야 합니다.

초기 버전(0.1)은 기본 활성 버전입니다.

### <a name="begin-by-cloning-an-existing-version"></a>기존 버전을 복제하여 시작

각 새 버전에 대한 시작점으로 사용할 기존 버전을 복제합니다. 버전을 복제하면 새 버전이 **활성** 버전이 됩니다.

### <a name="publishing-slots"></a>슬롯 게시

스테이지 및/또는 프로덕션 슬롯에 게시할 수 있습니다. 각 슬롯에는 다른 버전 또는 동일한 버전이 있을 수 있습니다. 버전은 프로덕션에 게시하기 전에 봇 또는 다른 LUIS 호출 앱에서 사용할 수 있는 변경 사항을 확인하는 데 유용합니다.

LUIS 앱의 [엔드포인트](luis-glossary.md#endpoint)에서 학습된 버전을 자동으로 사용할 수 없습니다. LUIS 앱 엔드포인트에서 사용할 수 있으려면 버전을 [게시](luis-how-to-publish-app.md)하거나 다시 게시해야 합니다. **스테이징** 및 **프로덕션** 에 게시하면 엔드포인트에서 사용할 수 있는 두 개의 앱 버전이 제공됩니다. 엔드포인트에서 사용할 수 있는 추가 앱 버전이 필요한 경우, 버전을 내보내고 새 앱으로 다시 가져와야 합니다. 새 앱에는 다른 앱 ID가 포함됩니다.

### <a name="import-a-version"></a>버전 가져오기

버전을 다음과 같은 새 항목으로 **가져올** 수 있습니다.
* 앱, 새 앱 ID 사용
* 기존 앱 버전

해당 버전은 활성 버전이 되고 앱 파일의 `versionId` 속성에 있는 버전 ID를 사용했습니다.

### <a name="export-a-version"></a>버전 내보내기

LUIS 포털에서 앱 수준 또는 버전 수준으로 버전을 **내보낼** 수 있습니다.

* 앱 수준 - **내 앱** 페이지에서 앱을 선택한 다음 **내보내기** 를 선택합니다.
* 버전 수준 - **내 앱** 페이지에서 앱 링크를 선택하고 **관리** 를 선택하고 **버전** 을 선택합니다.

유일한 차이는 앱 수준에서 내보낸 버전은 현재 활성 버전이지만, 버전 수준에서는 **[설정](luis-how-to-manage-versions.md)** 페이지에서 내보낼 버전을 선택할 수 있다는 것입니다.

내보낸 파일에는 다음이 포함되지 **않습니다**.

* 기계 학습 정보(앱을 가져온 후에 앱이 보존됨)
* 기여자 정보

LUIS 앱 스키마를 백업하려면 [LUIS 포털](https://www.luis.ai/applications)에서 버전을 내보냅니다.

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>버전 및 기여자를 사용하여 기여자 관리

LUIS는 Azure 리소스 수준 권한을 제공하여 앱에 대한 기여자 개념을 사용합니다. 이 개념을 버전 관리와 결합하여 대상으로 하는 협업을 제공합니다.

다음 기술을 사용하여 앱에 대한 기여자 변경 내용을 관리할 수 있습니다.

### <a name="manage-multiple-versions-inside-the-same-app"></a>동일한 앱 내부에서 여러 버전 관리

먼저 각 작성자에 기본 버전에서 [복제](luis-how-to-manage-versions.md#clone-a-version)합니다.

각 작성자는 자신의 앱 버전으로 변경합니다. 작성자가 모델에 만족하면 새 버전을 JSON 파일로 내보냅니다.

내보낸 앱, `.json` 또는 `.lu` 파일의 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 파일을 만듭니다. 새 병합 버전을 나타내도록 `versionId` 속성을 변경합니다. 해당 버전을 원래 앱으로 가져옵니다.

이 방법을 사용하면 하나의 활성 버전, 하나의 스테이지 버전 및 하나의 게시된 버전을 사용할 수 있습니다. 활성 버전의 결과를 [대화형 테스트 창](luis-interactive-test.md)에 게시된 버전(스테이지 또는 프로덕션)과 비교할 수 있습니다.

### <a name="manage-multiple-versions-as-apps"></a>여러 버전을 앱으로 관리

기본 버전을 [내보냅니다](luis-how-to-manage-versions.md#export-version). 각 작성자는 버전을 가져옵니다. 앱을 가져오는 사용자는 버전의 소유자입니다. 앱 수정을 완료하면 버전을 내보냅니다.

내보낸 앱은 JSON 형식 파일이며 기본 내보내기와 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 JSON 파일을 만듭니다. JSON에서 **versionId** 속성을 변경하여 새 병합 버전을 표시합니다. 해당 버전을 원래 앱으로 가져옵니다.

[협력자](luis-how-to-collaborate.md)의 작성 기여에 대해 자세히 알아보세요.

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>엔드포인트 발화를 검토하여 새 반복 주기 시작

반복 주기가 완료되면 프로세스를 반복할 수 있습니다. LUIS에서 신뢰도를 낮게 표시한 [예측 엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하기 시작합니다. 올바른 예측 의도 및 올바른 전체 엔터티 추출에 대해 이러한 발화를 확인합니다. 변경 내용을 검토하고 적용한 후에는 검토 목록이 비어 있어야 합니다.

## <a name="next-steps"></a>다음 단계

[협업](luis-how-to-azure-subscription.md) 개념을 알아봅니다.
