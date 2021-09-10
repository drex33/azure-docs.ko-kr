---
title: 기술 세트의 사용자 지정 AML 기술
titleSuffix: Azure Cognitive Search
description: Azure Machine Learning 모델을 통해 Azure Cognitive Search 기술 세트의 기능을 확장합니다.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 529be70de61fe9adb025ee801bd417bc860ef5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536113"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Cognitive Search 보강 파이프라인의 AML 기술

> [!IMPORTANT] 
> 이 기술은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기 상태입니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기술을 지원합니다.

**AML** 기술을 사용하면 사용자 지정 AML([Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md)) 모델을 통해 AI 보강을 확장할 수 있습니다. AML 모델이 [학습되고 배포되면](../machine-learning/concept-azure-machine-learning-architecture.md#workspace) **AML** 기술은 이를 AI 보강에 통합합니다.

기본 제공 기술과 마찬가지로 **AML** 기술에는 입력과 출력이 있습니다. 입력은 배포된 AML 서비스에 JSON 개체로 전송되며, JSON 페이로드가 성공 상태 코드와 함께 응답으로 출력됩니다. 응답은 **AML** 기술로 지정된 출력을 포함해야 합니다. 다른 응답은 오류로 간주되며 강화는 수행되지 않습니다.

> [!NOTE]
> 인덱서는 AML 서비스에서 반환된 특정 표준 HTTP 상태 코드에 대해 다시 두 번 시도합니다. 이러한 HTTP 상태 코드는 다음과 같습니다.
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>필수 구성 요소

* [AML 작업 영역](../machine-learning/concept-workspace.md)
* [배포된 모델](../machine-learning/how-to-deploy-azure-kubernetes-service.md)이 있는 이 작업 영역의 [Azure Kubernetes Service AML 컴퓨팅 대상](../machine-learning/concept-compute-target.md)
  * [컴퓨팅 대상에는 SSL이 사용되어야 합니다](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Cognitive Search는 **https** 엔드포인트에 대한 액세스만 허용합니다.
  * 자체 서명된 인증서는 사용할 수 없습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.AmlSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다. 사용할 매개 변수는 [AML 서비스에 필요한 인증(있는 경우)](#WhatSkillParametersToUse)에 따라 달라집니다.

| 매개 변수 이름 | Description |
|--------------------|-------------|
| `uri` | ([인증 없음 또는 키 인증](#WhatSkillParametersToUse)에 필요) _JSON_ 페이로드를 보낼 [AML 서비스의 채점 URI](../machine-learning/how-to-consume-web-service.md)입니다. **https** URI 체계만 허용됩니다. |
| `key` | ([키 인증](#WhatSkillParametersToUse)에 필요) [AML 서비스의 키](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)입니다. |
| `resourceId` | ([토큰 인증](#WhatSkillParametersToUse)에 필요) AML 서비스의 Azure Resource Manager 리소스 ID입니다. ID는 subscriptions/{guid}/resourceGroups/{resource-group-name}/Microsoft.MachineLearningServices/workspaces/{workspace-name}/services/{service_name} 형식이어야 합니다. |
| `region` | ([토큰 인증](#WhatSkillParametersToUse)의 경우 선택 사항). AML 서비스가 배포되는 [지역](https://azure.microsoft.com/global-infrastructure/regions/)입니다. |
| `timeout` | (선택 사항) 지정할 경우 API 호출을 수행하는 http 클라이언트에 대한 시간 제한을 나타냅니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 예를 들어, 60초인 경우 `PT60S`입니다. 설정하지 않으면 기본값 30초가 선택됩니다. 시간 제한은 최대 230초, 최소 1초로 설정할 수 있습니다. |
| `degreeOfParallelism` | (선택 사항) 시간 제한이 지정되면 인덱서가 제공된 엔드포인트와 병렬로 수행할 호출 수를 나타냅니다. 엔드포인트가 요청 로드를 너무 많이 초과하여 실패하는 경우 이 값을 줄일 수 있으며, 엔드포인트가 더 많은 요청을 허용할 수 있고 인덱서 성능을 높이려는 경우 이 값을 높일 수 있습니다.  설정하지 않으면 기본값으로 5초가 사용됩니다. degreeOfParallelism은 최대 10개까지 설정할 수 있으며 최솟값은 1입니다.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>사용할 기술 매개 변수

사용해야 하는 AML 기술 매개 변수는 AML 서비스에서 사용하는 인증(있는 경우)에 따라 달라집니다. AML 서비스는 세 가지 인증 옵션을 제공합니다.

* [키 기반 인증](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). AML 기술의 채점 요청을 인증하기 위한 정적 키가 제공됩니다.
  * _uri_ 와 _키_ 매개 변수 사용
* [토큰 기반 인증](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). AML 서비스는 [토큰 기반 인증을 사용하여 배포](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)됩니다. Azure Cognitive Search 서비스의 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에는 AML 서비스의 작업 영역에서 [reader 역할](../machine-learning/how-to-assign-roles.md)이 부여됩니다. 그런 다음, AML 기술은 Azure Cognitive Search 서비스의 관리 ID를 사용하여 정적 키가 필요 없는 AML 서비스에 대해 인증합니다.
  * _resourceId_ 매개 변수를 사용합니다.
  * Azure Cognitive Search 서비스가 AML 작업 영역과 다른 지역에 있는 경우 _지역_ 매개 변수를 사용하여 AML 서비스가 배포된 지역을 설정합니다.
* 인증 없음. AML 서비스를 사용하는 데 인증이 필요하지 않습니다.
  * _uri_ 매개 변수 사용

## <a name="skill-inputs"></a>기술 입력

이 기술에 대해 "미리 정의된" 입력은 없습니다. 이 기술을 실행하는 시점에 사용 가능하게 될 필드를 하나 이상 입력으로 선택할 수 있으며 AML 서비스로 전송되는 _JSON_ 페이로드는 다른 필드를 가지게 됩니다.

## <a name="skill-outputs"></a>기술 출력

이 기술에 대해 "미리 정의된" 출력은 없습니다. AML 서비스가 반환하는 응답에 따라 _JSON_ 응답에서 선택할 수 있도록 출력 필드를 추가합니다.

## <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>샘플 입력 JSON 구조

이 _JSON_ 구조는 AML 서비스로 보낼 페이로드를 나타냅니다. 구조의 최상위 필드는 기술 정의의 `inputs` 섹션에 지정된 “names”에 해당합니다. 해당 필드의 값은 해당 필드의 `source`에서 가져옵니다(문서의 필드 또는 다른 기술에서 가져올 수 있음).

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>샘플 출력 JSON 구조

출력은 AML 서비스에서 반환된 응답에 해당합니다. AML 서비스는 (`Content-Type` 응답 헤더에서 확인된) _JSON_ 페이로드만 반환해야 하며, 필드가 `output`의 “이름”과 일치하고 값이 보강으로 간주되는 강화 개체여야 합니다.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>인라인 셰이핑 샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>인라인 셰이핑 입력 JSON 구조

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>인라인 셰이핑 샘플 출력 JSON 구조

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>오류 사례
AML가 사용 가능하지 않거나 성공하지 못한 상태 코드를 보내는 것 외에도 다음 상황이 잘못된 경우로 간주됩니다.

* AML 서비스가 성공 상태 코드를 반환하지만 응답에 `application/json`이 아니라고 표시되므로 응답은 잘못된 것으로 간주되고 강화는 수행되지 않습니다.
* AML 서비스에서 잘못된 json을 반환하는 경우

AML 서비스가 사용 가능하지 않거나 HTTP 오류를 반환하는 경우 HTTP 오류에 대해 사용 가능한 모든 세부 정보를 포함하는 친숙한 오류가 인덱서 실행 기록에 추가됩니다.

## <a name="see-also"></a>참고 항목

+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [AML 서비스 문제 해결](../machine-learning/how-to-troubleshoot-deployment.md)