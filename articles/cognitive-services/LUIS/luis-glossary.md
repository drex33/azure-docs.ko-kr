---
title: 용어집 - LUIS
description: 용어집에서는 LUIS API 서비스를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/28/2021
ms.openlocfilehash: 25bdf291d7d836523655b131f485721e3a29593b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434582"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>일반적인 용어 및 개념의 언어 이해 용어집
Language Understanding(LUIS) 용어집에서는 LUIS 서비스를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.

## <a name="active-version"></a>활성 버전

활성 버전은 LUIS 포털을 사용하여 모델을 변경하는 경우 업데이트되는 앱의 [버전](luis-how-to-manage-versions.md)입니다. LUIS 포털에서 웹 사이트에서 활성 버전이 아닌 버전을 변경하려면 먼저 해당 버전을 활성으로 설정해야 합니다.

## <a name="active-learning"></a>능동적 학습

활성 학습은 기계 학습 모델을 사용하여 레이블에 대한 새로운 정보를 식별하는 기계 학습 기술입니다. LUIS에서 활성 학습은 현재 예측이 명확하지 않은 엔드포인트 트래픽에서 발화를 추가하여 모델을 개선하는 것을 말합니다. "엔드포인트 발화 검토"를 클릭하여 발화를 확인하고 레이블을 지정합니다.

참고 항목:
* [개념 정보](luis-concept-review-endpoint-utterances.md)
* [엔드포인트 발화 검토 자습서](luis-tutorial-review-endpoint-utterances.md)
* [엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하여 LUIS 앱을 개선하는 방법

## <a name="application-app"></a>애플리케이션(앱)

LUIS에서 애플리케이션 또는 앱은 동일한 데이터 집합을 기반으로 하는 기계 학습 모델 컬렉션으로, 특정 시나리오에 대한 의도 및 엔터티를 예측하기 위해 함께 작동합니다. 각 애플리케이션에는 별도의 예측 엔드포인트가 있습니다.

HR 봇을 빌드하는 경우 "종료 예약 시간", "혜택 조회" 및 "개인 정보 업데이트"와 같은 의도와 단일 애플리케이션으로 그룹화하는 이러한 각 의도에 대한 엔터티가 있을 수 있습니다.

## <a name="authoring"></a>작성

작성은 LUIS 포털 또는 작성 API를 사용하여 LUIS 앱을 생성, 관리 및 배포하는 기능입니다.

### <a name="authoring-key"></a>작성 키

[작성 키](luis-how-to-azure-subscription.md)는 앱을 작성하는 데 사용됩니다. 프로덕션 수준 엔드포인트 쿼리에는 사용되지 않습니다. 자세한 내용은 [키 제한](luis-limits.md#key-limits)을 참조하세요.

### <a name="authoring-resource"></a>작성 리소스

LUIS [제작 리소스](luis-how-to-azure-subscription.md)는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 리소스는 연결된 Azure 서비스의 작성, 학습 및 게시 기능에 대한 액세스입니다. 리소스에는 연결된 Azure 서비스에 액세스하는 데 필요한 인증, 권한 부여 및 보안 정보가 포함되어 있습니다.

제작 리소스에는 Azure "종류"의 `LUIS-Authoring`이 있습니다.

## <a name="batch-test"></a>테스트 일괄 처리

일괄 테스트는 사용자 발화의 일관되고 알려진 테스트 집합을 사용하여 현재 LUIS 앱 모델의 유효성을 검사하는 기능입니다. 일괄 테스트는 [JSON 형식의 파일](./luis-how-to-batch-test.md#batch-test-file)로 정의됩니다.


참고 항목:
* [개념](./luis-how-to-batch-test.md)
* 일괄 테스트를 실행하는 [방법](luis-how-to-batch-test.md)
* [자습서](./luis-how-to-batch-test.md) - 일괄 테스트 만들기 및 실행

### <a name="f-measure"></a>F 측정값

일괄 테스트에서 테스트 정확도의 측정값입니다.

### <a name="false-negative-fn"></a>거짓 부정(FN)

일괄 테스트에서 데이터 요소가 앱이 대상 의도/엔터티가 없음을 잘못 예측한 발언을 나타냅니다.

### <a name="false-positive-fp"></a>거짓 긍정(FP):

일괄 테스트에서 데이터 요소가 앱이 대상 의도/엔터티가 있음을 잘못 예측한 발언을 나타냅니다.

### <a name="precision"></a>전체 자릿수
일괄 테스트에서 정밀도(긍정적 예측 값이라고도 함)는 검색된 발언 중에 관련된 발언의 일부입니다.

동물 일괄 테스트에 대한 예는 총 동물 수(양과 양이 아닌 동물)로 나눈 예측된 양의 수입니다.

### <a name="recall"></a>재현율

일괄 테스트에서 재현율(민감도라고도 함)은 LUIS가 일반화하는 기능입니다.

동물 일괄 테스트에 대한 예는 제공되는 총 양의 수로 나눈 예측된 양의 수입니다.

### <a name="true-negative-tn"></a>참 부정(TN)

참 부정은 앱에서 일치하는 항목을 올바르게 예측하지 못하는 경우입니다. 일괄 테스트에서 앱이 의도나 엔터티로 레이블이 지정되지 않은 예에 대해 해당 의도 또는 엔터티를 예측하는 경우 참 부정이 발생합니다.

### <a name="true-positive-tp"></a>참 긍정(TP)

참 긍정(TP)은 앱에서 일치하는 항목을 올바르게 예측하는 경우입니다. 일괄 테스트에서 앱이 의도나 엔터티로 레이블이 지정된 예에 대해 해당 의도 또는 엔터티를 예측하는 경우 참 긍정이 발생합니다.

## <a name="classifier"></a>분류자

분류자는 입력이 들어가는 범주나 클래스를 예측하는 기계 학습 모델입니다.

[의도](#intent)는 분류자의 한 예입니다.

## <a name="collaborator"></a>협력자

협력자는 개념적으로 [기여자](#contributor)와 동일합니다. 소유자가 Azure 역할 기반 액세스 제어(Azure RBAC)를 사용하여 제어되지 않은 앱에 협력자의 이메일 주소를 추가하면 협력자에게 액세스 권한이 부여됩니다. 아직 협력자를 사용하는 경우 LUIS 계정을 마이그레이션하고 LUIS 제작 리소스를 사용하여 Azure RBAC를 통해 기여자를 관리해야 합니다.

## <a name="contributor"></a>참가자

기여자는 앱의 [소유자](#owner)가 아니지만, 의도, 엔터티, 발화를 추가, 편집 및 삭제할 동일한 권한을 가집니다. 기여자는 LUIS 앱에 대한 Azure 역할 기반 액세스 제어(Azure RBAC)를 제공합니다.

참고 항목:
* 기여자를 추가하는 [방법](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

## <a name="descriptor"></a>설명자

설명자는 이전에 기계 학습 [기능](#features)에 사용된 용어입니다.

## <a name="domain"></a>도메인

LUIS 컨텍스트에서 도메인은 정보 영역입니다. 도메인은 시나리오에 해당합니다. 서로 다른 도메인은 도메인의 컨텍스트에서 의미가 있는 특정 언어와 용어를 사용합니다. 예를 들어 음악을 재생하는 애플리케이션을 빌드하는 경우 애플리케이션에는 "노래, 트랙, 앨범, 가사, b면, 아티스트"와 같이 음악과 관련된 용어 및 언어가 있습니다. 도메인 예는 [미리 빌드된 도메인](#prebuilt-domain)을 참조하세요.

## <a name="endpoint"></a>엔드포인트

### <a name="authoring-endpoint"></a>엔드포인트 작성

LUIS 작성 엔드포인트 URL은 앱을 작성, 학습 및 게시하는 위치입니다. 엔드포인트 URL에는 앱 ID와 게시된 앱의 지역 또는 사용자 지정 하위 도메인이 포함됩니다.

[개발자 참조](developer-reference-resource.md#rest-endpoints)에서 프로그래밍 방식으로 앱을 작성하는 방법에 대해 자세히 알아보세요.

### <a name="prediction-endpoint"></a>예측 엔드포인트

LUIS 예측 엔드포인트 URL은 [LUIS 앱](#application-app)이 작성되고 게시된 후 LUIS 쿼리를 제출하는 위치입니다. 엔드포인트 URL에는 앱 ID와 게시된 앱의 지역 또는 사용자 지정 하위 도메인이 포함됩니다. 앱의 **[Azure 리소스](luis-how-to-azure-subscription.md)** 페이지에 있는 엔드포인트를 찾거나 [앱 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API에서 엔드포인트 URL을 가져올 수 있습니다.

예측 엔드포인트에 대한 액세스 권한은 LUIS 예측 키로 인증됩니다.

## <a name="entity"></a>엔터티

[엔터티](luis-concept-entity-types.md)는 의도를 충족하거나 식별하는 데 사용되는 정보를 설명하는 발화의 단어입니다. 엔터티가 복잡하고 모델이 특정 부분을 식별하게 하려는 경우 모델을 하위 엔터티로 나눌 수 있습니다. 예를 들어 주소를 예측하는 모델을 만들고, 도로 주소, 구/군/시, 시/도 및 우편 번호 등의 하위 엔터티도 확인할 수 있습니다. 엔터티는 모델에 대한 기능으로도 사용할 수 있습니다. LUIS 앱의 응답에는 예측된 의도 및 모든 엔터티가 포함됩니다.

### <a name="entity-extractor"></a>엔터티 추출기

엔터티 추출기는 경우에 따라 LUIS가 엔터티를 예측하는 데 사용하는 기계 학습 모델의 유형인 추출기로만 알려져 있습니다.

### <a name="entity-schema"></a>엔터티 스키마

엔터티 스키마는 하위 엔터티를 사용하여 기계 학습 엔터티를 정의하는 구조입니다. 예측 엔드포인트는 스키마에 정의된 모든 추출된 엔터티 및 하위 엔터티를 반환합니다.

### <a name="entitys-subentity"></a>엔터티의 하위 엔터티

하위 엔터티는 기계 학습 엔터티의 자식 엔터티입니다.

### <a name="non-machine-learning-entity"></a>비 기계 학습 엔터티

다음과 같이 텍스트 일치를 사용하여 데이터를 추출하는 엔터티입니다.
* 목록 엔터티
* 정규식 엔터티

### <a name="list-entity"></a>목록 엔터티

[목록 엔터티](reference-entity-list.md)는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. 목록 엔터티는 기계 학습 엔터티와 달리 정확히 일치합니다.

목록 엔터티의 단어가 목록에 포함되면 엔터티가 예측됩니다. 예를 들어 "크기"라는 목록 엔터티가 있고 목록에 "소, 중, 대"라는 단어가 있는 경우, 컨텍스트에 관계없이 "소", "중" 또는 "대" 단어가 사용되는 모든 발화에 대해 크기 엔터티가 예측됩니다.

### <a name="regular-expression"></a>정규식

[정규식 엔터티](reference-entity-regular-expression.md)는 정규식을 나타냅니다. 정규식 엔터티는 기계 학습 엔터티와 달리 정확히 일치합니다.
### <a name="prebuilt-entity"></a>미리 빌드된 엔터티

[미리 빌드된 엔터티](#prebuilt-entity)는 미리 빌드된 모델 항목을 참조하세요.

## <a name="features"></a>기능

기계 학습에서 기능은 모델이 특정 개념을 인식하는 데 도움이 되는 특징입니다. LUIS가 사용할 수 있는 힌트이지만 엄격한 규칙은 아닙니다.

이 용어를 **[기계 학습 기능](luis-concept-feature.md)** 이라고도 합니다.

새 데이터를 예측하는 방법을 알아보기 위해 이러한 힌트를 레이블과 함께 사용합니다. LUIS는 구문 목록과 다른 모델을 기능으로 사용하는 것을 모두 지원합니다.

### <a name="required-feature"></a>필수 기능

필수 기능은 LUIS 모델의 출력을 제한하는 방법입니다. 엔터티에 대한 기능이 필수로 표시되는 경우 이 기능은 기계 학습 모델이 예측하는 것에 관계없이 예측될 엔터티의 예에 있어야 합니다.

메뉴 순서 지정 봇의 수량 엔터티에 필요한 것으로 표시한 미리 빌드된 숫자 기능이 있는 경우를 예로 들어 보겠습니다. 봇이 `I want a bajillion large pizzas?`를 볼 경우 bajillion은 표시되는 컨텍스트에 관계없이 수량으로 예측되지 않습니다. Bajillion은 유효한 숫자가 아니며 미리 빌드된 숫자 엔터티로 예측되지 않습니다.

## <a name="intent"></a>Intent

[의도](luis-concept-intent.md)는 사용자가 수행하려는 작업을 나타냅니다. 항공편 예약 또는 결제와 같이 사용자 입력으로 표현된 목적이나 목표입니다. LUIS에서 전체 발화는 의도로 분류되지만 발화의 부분은 엔터티로 추출됩니다.

## <a name="labeling-examples"></a>레이블 지정 예

레이블 지정 또는 표시는 모델에 긍정 또는 부정 예를 연결하는 프로세스입니다.

### <a name="labeling-for-intents"></a>의도에 대한 레이블 지정
LUIS에서 앱 내의 의도는 함께 사용할 수 없습니다. 즉, 의도에 발화를 추가하면 해당 의도에 대한 _긍정_ 예와 다른 모든 의도에 대한 _부정_ 예가 고려됩니다. 부정 예를 앱 범위 밖의 발화를 나타내는 "None" 의도와 혼동해서는 안 됩니다.

### <a name="labeling-for-entities"></a>엔터티에 대한 레이블 지정
LUIS에서 엔터티를 _긍정_ 예로 사용하여 의도의 예제 발화에 있는 단어 또는 구문의 [레이블을 지정](label-entity-example-utterance.md)합니다. 레이블 지정은 해당 발화에 대해 예측해야 하는 의도를 보여 줍니다. 레이블이 지정된 발화는 의도를 학습하는 데 사용됩니다.

## <a name="luis-app"></a>LUIS 앱

[애플리케이션(앱)](#application-app)에 대한 정의를 참조하세요.

## <a name="model"></a>모델

(기계 학습) 모델은 입력 데이터에 대한 예측을 수행하는 함수입니다. LUIS에서 의도 분류자 및 엔터티 추출기를 일반적으로 "모델"이라고 지칭하며, 함께 학습, 게시 및 쿼리되는 모델 컬렉션을 "앱"으로 지칭합니다.

## <a name="normalized-value"></a>정규화된 값

[목록](#list-entity) 엔터티에 값을 추가합니다. 이러한 각 값에는 하나 이상의 동의어 목록이 있을 수 있습니다. 정규화된 값만 응답에 반환됩니다.

## <a name="overfitting"></a>과잉 맞춤

과잉 맞춤은 모델이 특정 예에 고정되어 있어 일반화할 수 없을 때 발생합니다.

## <a name="owner"></a>소유자

각 앱에는 앱을 만든 사용자인 하나의 소유자가 있습니다. 소유자는 Azure Portal에서 애플리케이션에 대한 권한을 관리합니다.

## <a name="phrase-list"></a>구문 목록

[구문 목록](luis-concept-feature.md)에는 동일한 클래스에 속하고 비슷하게 처리되어야 하는 값(단어 또는 구문) 그룹이 포함된 기계 학습 기능의 특정 유형입니다(예: 도시 또는 제품의 이름).

## <a name="prebuilt-model"></a>미리 빌드된 모델

[미리 빌드된 모델](luis-concept-prebuilt-model.md)은 레이블이 지정된 예와 함께 의도, 엔터티 또는 둘 다의 컬렉션입니다. 앱에 필요한 모델 개발 작업을 줄이기 위해 이러한 공통의 미리 빌드된 모델을 앱에 추가할 수 있습니다.

### <a name="prebuilt-domain"></a>미리 빌드된 도메인

미리 빌드된 도메인은 홈 자동화(HomeAutomation) 또는 음식점 예약(RestaurantReservation)과 같이 특정 도메인에 구성된 LUIS 앱입니다. 의도, 발화 및 엔터티가 이 도메인에 구성됩니다.

### <a name="prebuilt-entity"></a>미리 빌드된 엔터티

미리 빌드된 엔터티는 LUIS가 번호, URL 및 메일과 같은 일반적인 정보 유형에 제공하는 엔터티입니다. 이러한 엔터티는 공용 데이터를 기반으로 생성됩니다. 미리 빌드된 엔터티를 독립 실행형 엔터티로 추가하거나 엔터티에 기능으로 추가하도록 선택할 수 있습니다.

### <a name="prebuilt-intent"></a>미리 빌드된 의도

미리 빌드된 의도는 LUIS에서 일반적인 유형의 정보를 제공하고 고유한 레이블이 지정된 예제 발화를 제공하는 의도입니다.

## <a name="prediction"></a>예측

예측은 새 데이터(사용자 발화)를 사용하는 Azure LUIS 예측 서비스에 대한 REST 요청으로, 학습 및 게시된 애플리케이션을 해당 데이터에 적용하여 어떤 의도 및 엔터티를 찾을 것인지 결정합니다.

### <a name="prediction-key"></a>예측 키

[예측 키](luis-how-to-azure-subscription.md)(이전의 구독 키)는 Azure에서 만든 LUIS 서비스와 연결된 키이며 예측 엔드포인트 사용에 대한 권한을 부여합니다.

이 키는 작성 키가 아닙니다. 예측 엔드포인트 키가 있는 경우 작성 키 대신 모든 엔드포인트 요청에 사용해야 합니다. LUIS 웹 사이트의 Azure 리소스 페이지 아래쪽에 있는 엔드포인트 URL 내부에서 현재 구독 키를 확인할 수 있습니다. 이는 구독-키 이름/값 쌍의 값입니다.

### <a name="prediction-resource"></a>예측 리소스

LUIS 예측 리소스는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 리소스는 Azure 서비스의 연결된 예측에 대한 사용자 액세스입니다. 리소스에 예측이 포함됩니다.

예측 리소스에는 `LUIS`의 Azure "종류"가 있습니다.

### <a name="prediction-score"></a>예측 점수

[점수](luis-concept-prediction-score.md)는 0에서 1 사이의 숫자로, 특정 입력 발화가 특정 의도와 일치하는 시스템의 신뢰도를 측정한 것입니다. 점수가 1에 가까울수록 시스템은 출력에 대해 매우 확신하고 점수가 0에 가까울수록 시스템은 입력이 특정 출력과 일치하지 않는다고 확신하는 것을 의미합니다. 중간 점수는 시스템이 어떤 결정을 내릴지에 대해 확신이 없음을 의미합니다.

일부 고객 텍스트에 음식 주문이 포함되는지 여부를 식별하는 데 사용되는 모델을 예를 들어 보겠습니다. "커피를 주문하고 싶습니다"에는 1점(시스템은 주문임을 확신함)을 주고, "우리 팀이 어젯밤 게임에 이겼습니다"에는 0점(시스템은 주문이 아닌 것으로 확신함)을 줄 수 있습니다. "차를 마시자"는 0.5점일 수 있습니다(이게 주문인지 아닌지 알 수 없음).

## <a name="programmatic-key"></a>프로그래밍 방식 키

이름이 [작성 키](#authoring-key)로 바뀌었습니다.

## <a name="publish"></a>게시

[게시](luis-how-to-publish-app.md)는 스테이징 또는 프로덕션 [엔드포인트](#endpoint)에서 LUIS 활성 버전을 사용 가능하게 만드는 것입니다.

## <a name="quota"></a>할당량

LUIS 할당량은 Azure 구독 계층의 제한 사항입니다. LUIS 할당량은 초당 요청 수(HTTP 상태 429) 및 월별 총 요청 수(HTTP 상태 403)를 통해 제한할 수 있습니다.

## <a name="schema"></a>스키마

스키마는 하위 엔터티와 함께 사용자의 의도 및 엔터티를 포함합니다. 스키마는 초기에 계획된 후 시간이 지남에 따라 반복됩니다. 스키마에는 앱 설정, 기능 또는 예제 발화가 포함되지 않습니다.

## <a name="sentiment-analysis"></a>감정 분석
감정 분석에서는 [언어 서비스](../language-service/sentiment-opinion-mining/overview.md)에서 제공 하는 길이 발언의 양수 또는 음수 값을 제공 합니다.

## <a name="speech-priming"></a>음성 초기화

음성 프라이밍은 [음성 서비스](../speech-service/overview.md)를 사용하여 시나리오에서 일반적으로 사용되는 음성 및 구의 인식을 향상시킵니다. 음성 프라이밍 애플리케이션의 경우 레이블이 지정된 모든 LUIS 예를 사용하여 이 특정 애플리케이션에 대한 사용자 지정 음성 모델을 만들어 음성 인식 정확도를 향상시킵니다. 예를 들어, 체스 게임에서는 사용자가 "Move knight"라고 말할 때 "Move night"로 해석되지 않기를 원할 것입니다. LUIS 앱에는 "knight"가 엔터티로 레이블이 지정된 예제가 포함되어야 합니다.

## <a name="starter-key"></a>시작 키

LUIS를 사용하여 처음 시작할 때 사용할 수 있는 무료 키입니다.

## <a name="synonyms"></a>동의어

LUIS [목록 엔터티](reference-entity-list.md)에서 정규화된 값을 만들 수 있으며 각각 동의어 목록이 있을 수 있습니다. 예를 들어, 정규화된 값인 소, 중, 대 및 특대가 있는 크기 엔터티를 만든다고 가정합니다. 다음과 같이 각 값에 대한 동의어를 만들 수 있습니다.

|정규화된 값| 동의어|
|--|--|
|작음| 작은 것, 8온스|
|중간| 일반, 12온스|
|대| 큼, 16온스|
|특대| 가장 큰 것, 24온스|

입력에 동의어가 표시되면 모델은 엔터티에 대한 정규화된 값을 반환합니다.

## <a name="test"></a>테스트

LUIS 앱 [테스트](./luis-interactive-test.md)는 모델 예측을 보는 것을 의미합니다.

## <a name="timezone-offset"></a>표준 시간대 오프셋

엔드포인트에는 [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)이 포함됩니다. 이는 datetimeV2 미리 빌드된 엔터티에서 추가하거나 제거하려는 시간(분)입니다. 예를 들어, 발화가 “몇 시인가요?”인 경우 반환된 datetimeV2는 클라이언트 요청의 현재 시간입니다. 클라이언트 요청을 봇에서 가져오거나 봇의 사용자와 동일하지 않은 다른 애플리케이션에서 가져오는 경우에는 봇과 사용자 간의 오프셋을 전달해야 합니다.

[미리 빌드된 datetimeV2 엔터티의 표준 시간대 변경](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)을 참조하세요.

## <a name="token"></a>토큰
[토큰](luis-language-support.md#tokenization)은 LUIS에서 인식할 수 있는 텍스트의 가장 작은 단위입니다. 이는 여러 언어에서 약간씩 다릅니다.

**영어** 의 경우 토큰은 문자 및 숫자의 연속 범위(공백 또는 문장 부호 없음)입니다. 공백은 토큰이 아닙니다.

|문구|토큰 수|설명|
|--|--|--|
|`Dog`|1|문장 부호나 공백이 없는 단일 단어입니다.|
|`RMT33W`|1|레코드 로케이터 수입니다. 숫자와 문자를 포함할 수 있지만 문장 부호는 포함하지 않습니다.|
|`425-555-5555`|5|전화 번호입니다. 각 문장 부호도 하나의 토큰이므로 `425-555-5555`는 다음과 같이 5개의 토큰입니다.<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>학습

[학습](luis-how-to-train.md)은 마지막 학습 이후 활성 버전의 변경 내용을 LUIS에 학습시키는 프로세스입니다.

### <a name="training-data"></a>학습 데이터

학습 데이터는 모델을 학습하는 데 필요한 정보 집합입니다. 여기에는 스키마, 레이블이 지정된 발화, 기능 및 애플리케이션 설정이 포함됩니다.

### <a name="training-errors"></a>학습 오류

학습 오류는 레이블과 일치하지 않는 학습 데이터 기반의 예측입니다.

## <a name="utterance"></a>발화

[발화](luis-concept-utterance.md)는 대화의 문장에 대한 짧은 텍스트인 사용자 입력입니다. “다음 주 화요일 시애틀행 티켓 2매 예약”과 같은 자연어 구문입니다. 예제 발화를 추가하여 모델을 학습시키고 모델은 런타임에 새 발화에 대해 예측합니다.

## <a name="version"></a>버전

LUIS [버전](luis-how-to-manage-versions.md)은 LUIS 앱 ID 및 게시된 엔드포인트와 연결된 LUIS 애플리케이션의 특정 인스턴스입니다. 모든 LUIS 앱에는 하나 이상의 버전이 있습니다.