---
title: 조건부 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 조건부 기술을 사용하면 기술 세트 정의에서 필터링, 기본값 생성, 값 병합이 가능합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: acd3a42051fa42821013424ab0410814968b6131
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537271"
---
# <a name="conditional-cognitive-skill"></a>조건부 인식 기술

**조건부** 기술을 사용하면 부울 연산이 필요한 Azure Cognitive Search 시나리오에서 출력에 할당할 데이터를 결정할 수 있습니다. 이러한 시나리오에는 조건을 기준으로 하는 필터링, 기본값 할당 및 데이터 병합이 포함됩니다.

다음 의사 코드는 조건부 기술에서 수행하는 작업을 보여줍니다.

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되지 않습니다. 청구할 수 없으며 Cognitive Services 키 요구 사항이 없습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>평가 필드

이 기술은 해당 입력이 평가 필드이므로 특별합니다.

다음 항목은 식의 유효한 값입니다.

-   주석 경로(식의 경로는 "$(" 및 ")" 기호로 구분되어야 함)
 <br/>
    예:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  리터럴(문자열, 숫자, true, false, null) <br/>
    예:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  비교 연산자를 사용하는 식(==, !=, >=, >, <=, <) <br/>
    예:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   부울 연산자를 사용하는 식(&&, ||, !, ^) <br/>
    예:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   숫자 연산자를 사용하는 식(+, -, \*, /, %) <br/>
    예: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

조건부 기술은 평가를 지원하기 때문에 보조 변환 시나리오에서 사용할 수 있습니다. 예를 들어 [기술 정의 4](#transformation-example)를 참조하세요.

## <a name="skill-inputs"></a>기술 입력
입력은 대/소문자를 구분합니다.

| 입력   | Description |
|-------------|-------------|
| condition(조건)   | 이 입력은 평가할 조건을 나타내는 [평가 필드](#evaluated-fields)입니다. 이 조건은 부울 값(*true* 또는 *false*)으로 평가해야 합니다.   <br/>  예: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 이 입력은 조건이 *true* 로 평가될 경우 반환할 값을 나타내는 [평가 필드](#evaluated-fields)입니다. 상수 문자열은 작은따옴표(' 및 ')로 반환되어야 합니다. <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 이 입력은 조건이 *false* 로 평가될 경우 반환할 값을 나타내는 [평가 필드](#evaluated-fields)입니다. <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>기술 출력
단순히 "output"이라고 하는 단일 출력이 있습니다. 조건이 false이면 *whenFalse* 값을 반환하고 조건이 True이면 *whenTrue* 값을 반환합니다.

## <a name="examples"></a>예제

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>샘플 기술 정의 1: 문서를 필터링하여 프랑스어 문서만 반환

다음 출력은 문서 언어가 프랑스어인 경우 문장("/document/frenchSentences")의 배열을 반환합니다. 언어가 프랑스어가 아니면 값은 *null* 로 설정됩니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
"/document/frenchSentences"가 다른 기술 *컨텍스트* 로 사용되는 경우 "/document/frenchSentences"가 *null* 로 설정되지 않은 경우에만 이 기술이 실행됩니다.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>샘플 기술 정의 2: 존재하지 않는 값에 대한 기본값 설정

다음 출력은 문서의 언어로 설정되거나 언어가 설정되지 않은 경우 "es"로 설정된 주석("/document/languageWithDefault")을 만듭니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>샘플 기술 정의 3: 두 필드의 값을 하나로 병합

이 예에서는 일부 문장에 *frenchSentiment* 속성이 있습니다. *frenchSentiment* 속성이 null인 경우에는 *englishSentiment* 값을 사용합니다. *감정*("/document/sentences/*/sentiment")이라는 멤버에 출력을 할당합니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>변환 예
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>샘플 기술 정의 4: 단일 필드의 데이터 변환

이 예에서는 0에서 1 사이의 *감정* 를 받습니다. -1과 1 사이로 변환합니다. 조건부 기술을 사용하여 이러한 보조 변환을 수행할 수 있습니다.

이 예에서는 조건이 항상 *true* 이기 때문에 기술에 대한 조건부 측면을 사용하지 않습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>특별 고려 사항
일부 매개변수는 평가되므로 문서화된 패턴을 따르도록 특히 주의해야 합니다. 식은 등호(=)로 시작해야 합니다. 경로는 "$(" 및 ")"로 구분되어야 합니다. 문자열을 작은따옴표로 묶어야 합니다. 이를 통해 평가기는 문자열과 실제 경로 및 연산자를 구분할 수 있습니다. 또한 연산자 주위에 공백을 넣어야 합니다. 예를 들어 경로에서 "*"는 곱하기가 아닌 다른 것을 의미합니다.


## <a name="next-steps"></a>다음 단계

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
