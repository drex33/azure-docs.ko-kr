---
title: Personalizer란?
description: Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 서비스로, 사용자의 실시간 동작을 통해 학습합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, 기계 학습
ms.openlocfilehash: f293cbfbf76d22ebf18f1ad4ea7f57d24806b8f2
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830260"
---
# <a name="what-is-personalizer"></a>Personalizer란?

Azure Personalizer는 애플리케이션에서 사용자를 표시하는 데 가장 적합한 콘텐츠 항목을 선택할 수 있도록 도움을 주는 클라우드 기반 서비스입니다. Personalizer 서비스를 사용하여 구매자에게 제안할 제품을 결정하거나 보급 알림의 최적 위치를 파악할 수 있습니다. 콘텐츠가 사용자에게 표시되면 애플리케이션이 사용자의 반응을 모니터링하고 보상 점수를 Personalizer 서비스에 다시 보고합니다. 이를 통해 기계 학습 모델을 지속적으로 개선하고 수신되는 컨텍스트 정보를 기반으로 가장 적합한 콘텐츠 항목을 선택하는 Personalizer의 기능이 보장됩니다.

> [!TIP]
> 콘텐츠는 텍스트, 이미지, URL, 이메일 또는 기타 항목을 선택하고 사용자에게 표시하려는 정보의 모든 단위입니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.  

* [**빠른 시작**](quickstart-personalizer-sdk.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.  
* [**방법 가이드**](how-to-settings.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.  
* [**개념**](how-personalizer-works.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.  
* [**자습서**](tutorial-use-personalizer-web-app.md)는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.  

시작하기 전에 [이 대화형 데모가 있는 Personalizer](https://personalizationdemo.azurewebsites.net/)를 사용해 보세요.

## <a name="how-does-personalizer-select-the-best-content-item"></a>Personalizer에서 가장 적합한 콘텐츠 항목을 어떻게 선택하나요?

Personalizer는 **보충 학습** 을 사용하여 모든 사용자의 집합적 동작 및 보상 점수에 따라 가장 적합한 항목(_작업_)을 선택합니다. 작업은 뉴스 기사, 특정 영화 또는 제품과 같은 콘텐츠 항목입니다.

**순위** 호출에서는 작업 기능과 함께 작업 항목 및 컨텍스트 기능을 사용하여 상위 작업 항목을 선택합니다.

* **기능이 포함된 작업** - 각 항목에 해당하는 특정 기능이 포함된 콘텐츠 항목
* **컨텍스트 기능** - 앱을 사용하는 경우 사용자, 해당 컨텍스트 또는 해당 환경의 기능

순위 호출은 **보상 작업 ID** 필드에서 사용자에게 표시할 콘텐츠 항목인 __작업__ 의 ID를 반환합니다.

사용자에게 표시되는 __작업__ 은 시간이 지남에 따라 총 보상량을 최대화하려고 하는 기계 학습 모델로 선택됩니다.

### <a name="sample-scenarios"></a>샘플 시나리오:

Personalizer를 사용하여 사용자를 위해 렌더링할 최적의 콘텐츠를 선택할 수 있는 몇 가지 시나리오를 살펴보겠습니다.

|내용 유형|작업(기능 포함)|컨텍스트 기능|반환되는 보상 작업 ID<br>(이 콘텐츠 표시)|
|--|--|--|--|
|뉴스 목록|a. `The president...`(국가별, 정치, [텍스트])<br>b. `Premier League ...`(글로벌, 스포츠, [텍스트, 이미지, 비디오])<br> 다. `Hurricane in the ...`(국가별, 날씨, [텍스트, 이미지]|뉴스를 읽는 디바이스<br>월 또는 계절<br>|a `The president...`|
|영화 목록|1. `Star Wars`(1977, [작업, 모험, 판타지], George Lucas)<br>2. `Hoop Dreams`(1994, [다큐멘터리, 스포츠], Steve James<br>3. `Casablanca`(1942, [사랑, 드라마, 전쟁], Michael Curtiz)|영화를 보는 디바이스<br>화면 크기<br>사용자 유형<br>|3. `Casablanca`|
|제품 목록|i. `Product A` (3Kg, $$$$, 24시간 내 배달)<br>ii. `Product B` (20Kg, $$, 세관에서 2주 배송)<br>iii. `Product C` (3Kg, $$$,48시간 내 배달)|쇼핑을 읽는 디바이스<br>사용자의 지출 계층<br>월 또는 계절|ii. `Product B`|

Personalizer는 보충 학습을 사용하여 _보상 작업 ID_ 로 알려진 최적의 단일 작업을 선택했습니다. 기계 학습 모델은 다음을 사용합니다. 

* 학습된 모델 - 기계 학습 모델을 개선하는 데 사용되는 개인 설정 서비스로부터 이전에 받은 정보
* 현재 데이터 - 기능 및 컨텍스트 기능이 있는 특정 작업

## <a name="when-to-use-personalizer"></a>Personalizer 사용 시기

Personalizer의 **순위** [API](https://go.microsoft.com/fwlink/?linkid=2092082)는 애플리케이션이 콘텐츠를 제공할 때마다 호출됩니다. 이를 **이벤트** 라고 하며, _event ID_ 로 표시합니다.

Personalizer의 **보상** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)는 실시간으로 호출하거나 인프라에 맞게 조정하기 위해 지연시킬 수 있습니다. 비즈니스 요구 사항에 따라 보상 점수를 결정합니다. 보상 점수는 0에서 1 사이입니다. 이는 양호한 경우 1, 양호하지 않은 경우 0 또는 비즈니스 목표와 메트릭을 고려하여 만든 알고리즘을 통해 생성된 숫자와 같은 단일 값일 수 있습니다.

## <a name="content-requirements"></a>콘텐츠 요구 사항

콘텐츠가 다음과 같은 경우 Personalizer를 사용합니다.

* 각 개인화 이벤트에서 선택할 수 있는 제한된 작업 또는 항목 집합(최대 50개)이 있습니다. 더 큰 목록이 있는 경우 [추천 엔진을 사용](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)하여 Personalizer 서비스에서 Rank를 호출할 때마다 목록을 50개 항목으로 줄입니다.
* 순위를 지정하려는 콘텐츠를 설명하는 정보(_기능이 포함된 작업_ 및  _컨텍스트 기능_)가 있습니다.
* Personalizer가 효과적일 수 있도록 최소 1k/일의 콘텐츠 관련 이벤트가 있습니다. Personalizer가 필요한 최소 트래픽을 받지 못하는 경우 서비스에서 가장 적합한 단일 콘텐츠 항목을 결정하는 데 시간이 더 오래 걸립니다.

Personalizer는 그룹 정보를 거의 실시간으로 사용하여 가장 적합한 단일 콘텐츠 항목을 반환하므로 서비스에서 수행하지 않는 작업은 다음과 같습니다.
* 사용자 프로필 정보 유지 및 관리
* 개별 사용자의 기본 설정 또는 기록 로깅
* 정리된 콘텐츠 및 레이블이 지정된 콘텐츠 요구

## <a name="how-to-design-for-and-implement-personalizer"></a>Personalizer를 설계하고 구현하는 방법

1. 콘텐츠, **_작업_** 및 **_컨텍스트_** 를 [설계](concepts-features.md)하고 계획합니다. **_보상_** 점수에 대한 보상 알고리즘을 결정합니다.
1. 사용자가 만드는 각 [Personalizer 리소스](how-to-settings.md)는 하나의 학습 루프로 간주됩니다. 루프는 해당 콘텐츠 또는 사용자 환경에 대한 순위 및 보상 호출을 모두 받습니다.

    |리소스 유형| 목적|
    |--|--|
    |[실습생 모드](concept-apprentice-mode.md) `E0`|기존 애플리케이션에 영향을 주지 않고 Personalizer 모델을 학습한 다음, 프로덕션 환경에서의 온라인 학습 동작에 배포합니다.|
    |표준, `S0`|프로덕션 환경에서의 온라인 학습 동작|
    |체험판, `F0`| 비프로덕션 환경에서 온라인 학습 동작 시도|

1. Personalizer를 애플리케이션, 웹 사이트 또는 시스템에 추가합니다.
    1. 콘텐츠가 사용자에게 표시되기 전에 애플리케이션, 웹 사이트 또는 시스템에서 **순위** 호출을 Personalizer에 추가하여 가장 적합한 단일 _콘텐츠_ 항목을 확인합니다.
    1. 반환되는 _보상 작업 ID_ 인 가장 적합한 단일 _콘텐츠_ 항목을 사용자에게 표시합니다.
    1. 다음과 같이 _비즈니스 논리_ 를 사용자의 동작 방식에 대해 수집한 정보에 적용하여 **보상** 점수를 확인합니다.

    |동작|계산된 보상 점수|
    |--|--|
    |사용자가 가장 적합한 단일 _콘텐츠_ 항목(보상 작업 ID)을 선택함|**1**|
    |사용자가 다른 콘텐츠를 선택함|**0**|
    |가장 적합한 단일 _콘텐츠_ 항목(보상 작업 ID)을 선택하기 전에 사용자가 일시 중지하여 막연하게 스크롤함|**0.5**|

    1. 0과 1 사이의 보상 점수를 보내는 **보상** 호출을 추가합니다.
        * 콘텐츠를 표시한 직후
        * 또는 나중에 오프라인 시스템에서
    1. 사용 기간 후에 오프라인 평가를 사용하여 [루프를 평가](concepts-offline-evaluation.md)합니다. 오프라인 평가를 사용하면 코드를 변경하거나 사용자 환경에 영향을 주지 않고 Personalizer 서비스의 효율성을 테스트하고 평가할 수 있습니다.

## <a name="reference"></a>참조 

* [Personalizer C#/.NET SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Personalizer Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Personalizer JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Personalizer Python SDK](/python/api/overview/azure/cognitiveservices/personalizer)
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Personalizer 작동 방식](how-personalizer-works.md)[
> 보충 학습이란?](concepts-reinforcement-learning.md)
