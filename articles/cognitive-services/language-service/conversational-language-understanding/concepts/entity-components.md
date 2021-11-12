---
title: 대화형 Language Understanding의 엔터티 구성 요소
titleSuffix: Azure Cognitive Services
description: 대화형 Language Understanding 텍스트에서 엔터티를 추출 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c42028b10a71a039a208f82cfacb58e5ba8e07cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347730"
---
# <a name="entity-components"></a>엔터티 구성 요소

대화형 Language Understanding에서 엔터티는 길이 발언에서 추출 되는 관련 정보입니다. 엔터티는 다른 방법으로 추출할 수 있습니다. 컨텍스트를 통해 목록에서 일치 하는 것으로 인식 되거나 미리 빌드된 인식 된 엔터티에서 검색 될 수 있습니다. 프로젝트의 모든 엔터티는 엔터티의 구성 요소로 정의 된 이러한 메서드 중 하나 이상으로 구성 됩니다. 하나 이상의 구성 요소에서 엔터티를 정의 하는 경우 해당 예측은 겹칠 수 있습니다. **겹치는 방법** 의 고정 옵션 집합을 사용 하 여 해당 구성 요소가 겹칠 때 엔터티 예측의 동작을 결정할 수 있습니다.

## <a name="component-types"></a>구성 요소 유형

엔터티 구성 요소는 엔터티를 추출 하는 방법을 결정 합니다. 엔터티는 엔터티를 추출 하는 데 사용 되는 유일한 메서드를 결정 하는 하나의 구성 요소를 포함 하거나, 엔터티가 정의 되 고 추출 되는 방법을 확장 하는 여러 구성 요소를 포함할 수 있습니다.

### <a name="learned-component"></a>학습 한 구성 요소

학습 된 구성 요소는 길이 발언에 레이블을 표시 하는 엔터티 태그를 사용 하 여 기계 학습 모델을 학습 합니다. 모델은 utterance 내의 컨텍스트를 기준으로 엔터티가 있는 위치를 예측 합니다. 레이블은 주위에 있는 단어의 의미 및 레이블이 지정 된 단어로 utterance 엔터티를 포함 해야 하는 위치의 예를 제공 합니다. 이 구성 요소는 엔터티에 대해 길이 발언 태그를 지정 하 여 레이블을 추가 하는 경우에만 정의 됩니다. 길이 발언 엔터티에 태그를 표시 하지 않으면 학습 된 구성 요소가 없습니다.

:::image type="content" source="../media/learned-component.png" alt-text="엔터티에 대해 학습 된 구성 요소의 예를 보여 주는 스크린샷" lightbox="../media/learned-component.png":::

### <a name="list-component"></a>목록 구성 요소

목록 구성 요소는 동의어와 함께 고정 된 닫힌 관련 단어 집합을 나타냅니다. 구성 요소가 동의어로 제공한 값 목록과 정확히 일치 하는 텍스트를 수행 합니다. 각 동의어가 목록 구성 요소가 일치 하는 경우 출력에 반환 되는 동의어의 정규화 된 표준 값으로 사용할 수 있는 "목록 키"에 속합니다. 목록 키가 일치 하는 데 사용 **되지 않습니다** .


:::image type="content" source="../media/list-component.png" alt-text="엔터티에 대 한 목록 구성 요소의 예를 보여 주는 스크린샷" lightbox="../media/list-component.png":::

### <a name="prebuilt-component"></a>미리 작성 한 구성 요소

미리 작성 된 구성 요소를 사용 하면 숫자, 날짜/시간 및 이름과 같은 일반적인 형식의 라이브러리에서 선택할 수 있습니다. 추가 되 면 미리 작성 된 구성 요소가 자동으로 검색 됩니다. 엔터티 마다 미리 작성 된 구성 요소를 5 개까지 포함할 수 있습니다. 자세한 내용은 [지원 되는 미리 작성 된 구성 요소 목록을](../prebuilt-component-reference.md) 참조 하십시오.


:::image type="content" source="../media/prebuilt-component.png" alt-text="엔터티에 대 한 미리 작성 된 구성 요소의 예를 보여 주는 스크린샷" lightbox="../media/prebuilt-component.png":::


## <a name="overlap-methods"></a>겹치는 방법

엔터티에 대해 여러 구성 요소가 정의 된 경우 해당 예측은 겹칠 수 있습니다. 중복이 발생 하면 각 엔터티의 최종 예측이 다음 옵션 중 하나에 따라 결정 됩니다.

### <a name="longest-overlap"></a>가장 긴 겹침

텍스트에 두 개 이상의 구성 요소가 있고 겹치는 방법이 사용 되는 경우 **가장 긴 문자 집합이** 있는 구성 요소가 반환 됩니다.

이 옵션은 다양 한 구성 요소에서 가능한 가장 긴 예측을 추출 하는 데 관심이 있는 경우에 가장 적합 합니다. 이 메서드는 혼동 (중복)이 있을 때마다 반환 되는 구성 요소가 가장 긴 것을 보장 합니다.

#### <a name="examples"></a>예

"Palm 해변"이 목록 구성 요소와 일치 하 고 학습 된 구성 요소에서 "팜 해변 확장"을 예측 한 경우이 겹치는 부분에서 가장 긴 문자 집합 이기 때문에 "**palm 해변 확장**"이 반환 됩니다.

:::image type="content" source="../media/return-longest-overlap-example-1.svg" alt-text="구성 요소에 대 한 가장 긴 겹치는 결과의 예를 보여 주는 스크린샷" lightbox="../media/return-longest-overlap-example-1.svg":::

"Palm 해변"이 목록 구성 요소와 일치 하 고 "해변 확장"이 학습 된 구성 요소에 의해 예측 된 경우 "**해변 확장**"은이 겹치는 부분에서 가장 긴 문자 집합을 포함 하는 구성 요소 이므로 반환 됩니다.

:::image type="content" source="../media/return-longest-overlap-example-2.svg" alt-text="구성 요소에 대 한 가장 긴 겹치는 결과의 두 번째 예제를 보여 주는 스크린샷" lightbox="../media/return-longest-overlap-example-2.svg":::

"Palm 해변"이 목록 구성 요소와 일치 하 고 학습 된 구성 요소에서 "Extension"을 예측 한 경우이 인스턴스에서 겹치는 부분이 없기 때문에 엔터티의 2 개 개별 인스턴스가 반환 됩니다. 즉, "**팜 해변**" 및 "**확장**"에 대 한 별도의 인스턴스가 반환 됩니다.

:::image type="content" source="../media/return-longest-overlap-example-3.svg" alt-text="구성 요소에 대 한 가장 긴 겹치는 결과의 세 번째 예를 보여 주는 스크린샷" lightbox="../media/return-longest-overlap-example-3.svg":::

### <a name="exact-overlap"></a>정확히 겹치기

모든 구성 요소는 반환할 엔터티에 대해 텍스트에서 **정확히 같은 문자** 를 겹쳐야 합니다. 정의 된 구성 요소 중 하나가 일치 하지 않거나 예측 되지 않으면 엔터티는를 반환 하지 않습니다.

이 옵션은 여러 구성 요소를 동시에 검색 해야 하는 엄격한 엔터티가 추출 될 때 가장 적합 합니다.

#### <a name="examples"></a>예

"Palm 해변"이 목록 구성 요소와 일치 하 고 학습 된 구성 요소에서 "Palm 해변"을 예측 했 고 이러한 구성 요소가 엔터티에 정의 된 유일한 두 구성 요소인 경우 모든 구성 요소가 정확히 동일한 문자를 중첩 하 여 "**palm** 해변"이 반환 됩니다.

:::image type="content" source="../media/require-exact-overlap-example-1.svg" alt-text="구성 요소에 대 한 정확히 겹치는 결과의 예를 보여 주는 스크린샷" lightbox="../media/require-exact-overlap-example-1.svg":::

"팜 해변"이 목록 구성 요소에 의해 일치 하 고 "해변 확장"이 학습 된 구성 요소에 의해 예측 된 경우 모든 구성 요소가 정확히 같은 문자에서 겹치면 엔터티가 반환 **되지** 않습니다.

:::image type="content" source="../media/require-exact-overlap-example-2.svg" alt-text="구성 요소에 대 한 정확한 중복 결과의 두 번째 예를 보여 주는 스크린샷" lightbox="../media/require-exact-overlap-example-2.svg":::

"팜 해변"이 목록 구성 요소와 일치 하 고 학습 된 구성 요소에서 "Extension"을 예측 한 경우이 인스턴스에서 중복이 발생 하지 않았으므로 엔터티가 반환 **되지 않습니다** .

:::image type="content" source="../media/require-exact-overlap-example-3.svg" alt-text="구성 요소에 대 한 정확한 중복 결과의 세 번째 예를 보여 주는 스크린샷" lightbox="../media/require-exact-overlap-example-3.svg":::

### <a name="union-overlap"></a>합집합 겹치기

텍스트에 두 개 이상의 구성 요소가 있고 겹칠 경우 구성 요소 범위의 **합집합** 이 반환 됩니다.

이 옵션은 회수를 최적화 하 고 결합할 수 있는 가장 긴 일치 항목을 가져오려고 시도 하는 경우에 가장 적합 합니다.

#### <a name="examples"></a>예

"Palm 해변"이 목록 구성 요소에 의해 일치 하 고 "Palm 해변 확장"이 학습 된 구성 요소에 의해 예측 된 경우 "palm **해변 extension**"이 반환 됩니다 .이는 겹치는 부분의 시작 부분에 있는 첫 번째 문자가 "palm"의 "P"이 고 겹치는 구성 요소의 끝에 있는 마지막 문자는 "Extension"의 "n"입니다.

:::image type="content" source="../media/return-union-example-1.svg" alt-text="구성 요소에 대 한 union 결과의 예를 보여 주는 스크린샷" lightbox="../media/return-union-example-1.svg":::

"Palm 해변"이 목록 구성 요소에 의해 일치 하 고 "해변 확장"이 학습 된 구성 요소에 의해 예측 된 경우 겹치는 부분의 시작 부분에 있는 첫 번째 문자가 "Palm"의 "P"이 고 겹치는 구성 요소의 끝에 있는 마지막 문자가 "Extension"의 "n" 인 경우 "**Palm 해변 extension**"이 반환 됩니다.

:::image type="content" source="../media/return-union-example-2.svg" alt-text="구성 요소에 대 한 union 결과의 두 번째 예제를 보여 주는 스크린샷" lightbox="../media/return-union-example-2.svg":::

"뉴욕"이 미리 작성 된 구성 요소에 의해 예측 된 경우 "서울 해변"은 목록 구성 요소에 의해 일치 되었으며 학습 된 구성 요소에서 "비치 확장"을 예측 한 후 " __**New 서울 해변 extension**__"이 반환 됩니다. 겹치는 시작 부분의 첫 번째 문자는 "new"의 "n"이 고 겹치는 구성 요소의 끝에 있는 마지막 문자는 "Extension"의 "n"입니다.

:::image type="content" source="../media/return-union-example-3.svg" alt-text="구성 요소에 대 한 union 결과의 세 번째 예제를 보여 주는 스크린샷" lightbox="../media/return-union-example-3.svg":::

### <a name="return-all-separately"></a>모두 별도로 반환

모든 구성 요소의 일치 또는 예측은 엔터티의 **개별 인스턴스로** 반환 됩니다.

이 옵션은 예측 후에 엔터티에 대해 고유한 중첩 논리를 적용 하려는 경우에 가장 적합 합니다.

#### <a name="examples"></a>예

"Palm 해변"이 목록 구성 요소와 일치 하 고 학습 된 구성 요소에서 "팜 해변 확장"을 예측 한 경우 엔터티는 두 개의 인스턴스를 반환 합니다. 하나는 "**팜 해변**"이 고 다른 하나는 "**팜 해변 확장**"입니다.

:::image type="content" source="../media/return-all-overlaps-example-1.svg" alt-text="구성 요소에 대 한 모든 중복 결과를 반환 하는 예제를 보여 주는 스크린샷" lightbox="../media/return-all-overlaps-example-1.svg":::

"뉴욕"이 미리 작성 된 구성 요소에 의해 예측 된 경우 "서울 해변"은 목록 구성 요소에 의해 일치 되 고 "해변 확장"은 학습 된 구성 요소에 의해 예측 된 것입니다. 즉, 엔터티는 "**뉴욕", "****서울 해변**" 및 "**해변 확장**"에 대 한 3 개의 인스턴스로 반환 됩니다.

:::image type="content" source="../media/return-all-overlaps-example-2.svg" alt-text="구성 요소에 대 한 모든 중복 결과를 반환 하는 두 번째 예제를 보여 주는 스크린샷" lightbox="../media/return-all-overlaps-example-2.svg":::

## <a name="next-steps"></a>다음 단계

[지원 되는 미리 빌드된 구성 요소](../prebuilt-component-reference.md)
