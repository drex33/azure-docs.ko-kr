---
title: Azure 의료 Api의 FHIR 검색 개요
description: 이 문서에서는 Azure 의료 Api에 구현 된 FHIR 검색의 개요를 설명 합니다.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/25/2021
ms.author: cavoeg
ms.openlocfilehash: c95123adc8962df5fdd20b766ffe72f5aec72ca0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040456"
---
# <a name="overview-of-fhir-search"></a>FHIR 검색 개요

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

FHIR 사양은 FHIR 리소스 검색의 기본 사항을 정의 합니다. 이 문서에서는 FHIR에서 리소스를 검색 하는 몇 가지 주요 측면을 안내 합니다. FHIR 리소스를 검색 하는 방법에 대 한 자세한 내용은 HL7 FHIR 사양 [검색](https://www.hl7.org/fhir/search.html) 을 참조 하세요. 이 문서 전체에서는 검색 구문의 예를 제공 합니다. 각 검색은 일반적으로 URL이 인 FHIR 서버에 대 한 것입니다 `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com` . 이 예제에서는이 URL에 대 한 자리 표시자 {{FHIR_URL}}를 사용 합니다. 

FHIR 검색은 특정 리소스 종류, 지정 된 [구획](https://www.hl7.org/fhir/compartmentdefinition.html)또는 모든 리소스에 대해 수행할 수 있습니다. FHIR에서 검색을 실행 하는 가장 간단한 방법은 요청을 사용 하는 것입니다 `GET` . 예를 들어 데이터베이스에 있는 모든 환자를 가져오려는 경우 다음 요청을 사용할 수 있습니다. 

```rest
GET {{FHIR_URL}}/Patient
```

을 사용 하 여 검색할 수도 있습니다 `POST` .이는 쿼리 문자열이 너무 긴 경우에 유용 합니다. 을 사용 하 여 검색 하려면 `POST` 검색 매개 변수를 양식 본문으로 제출할 수 있습니다. 이를 통해 쿼리 문자열에서 확인 하 고 이해 하기 어려울 수 있는 보다 길고 복잡 한 일련의 쿼리 매개 변수를 사용할 수 있습니다.

검색 요청이 성공적으로 완료 되 면 해당 유형의 FHIR 번들 응답을 받게 됩니다 `searchset` . 검색에 실패 하는 경우에서 검색에 실패 한 이유를 `OperationOutcome` 이해 하는 데 도움이 되는 오류 세부 정보를 확인할 수 있습니다.

다음 섹션에서는 검색에 관련 된 다양 한 측면을 다룹니다. 이러한 세부 정보를 검토 한 후에는 Azure 의료 Api의 FHIR 서비스에서 수행할 수 있는 검색 예제가 있는 [샘플 페이지](search-samples.md) 를 참조 하세요.

## <a name="search-parameters"></a>검색 매개 변수

검색을 수행 하는 경우 리소스의 다양 한 특성에 따라 검색 합니다. 이러한 특성을 검색 매개 변수 라고 합니다. 각 리소스에는 정의 된 검색 매개 변수 집합이 있습니다. 검색을 성공적으로 검색 하려면 검색 매개 변수를 정의 하 고 데이터베이스에서 인덱싱해야 합니다.

각 검색 매개 변수에는 정의 된 [데이터 형식이](https://www.hl7.org/fhir/search.html#ptypes)있습니다. 다양 한 데이터 형식에 대 한 지원은 아래에 설명 되어 있습니다.


| **검색 매개 변수 유형**  | **FHIR용 Azure API** | **Azure 의료 Api의 FHIR 서비스** | **설명**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  number                    | 예                  | 예                       |
|  date                      | 예                  | 예                       |
|  문자열                    | 예                  | 예                       |
|  token                     | 예                  | 예                       |
|  reference                 | 예                  | 예                       |
|  복합                 | Partial              | Partial                   | 지원 되는 복합 형식 목록에 대해서는이 문서의 뒷부분에서 설명 합니다. |
|  quantity                  | 예                  | 예                       |
|  uri                       | 예                  | 예                       |
|  특수                   | 아니요                   | 아니요                        |

### <a name="common-search-parameters"></a>공통 검색 매개 변수

모든 리소스에 적용 되는 [일반적인 검색 매개 변수가](https://www.hl7.org/fhir/search.html#all) 있습니다. 이러한 목록은 다음과 같이 지원 됩니다.

| **공통 검색 매개 변수** | **FHIR용 Azure API** | **Azure 의료 Api의 FHIR 서비스** | **설명**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | 예                  | 예                       
| _lastUpdated                | 예                  | 예                       |
| _tag                        | 예                  | 예                       |
| _type                       | 예                  | 예                       |
| _security                   | 예                  | 예                       |
| _profile                    | 예                  | 예                       |
| _has                        | 예.                 | Yes                       |  |
| _query                      | 아니요                   | 아니요                        |
| _filter                     | 아니요                   | 아니요                        |
| _list                       | 아니요                   | 아니요                        |
| _text                       | 아니요                   | 아니요                        |
| _content                    | 아니요                   | 아니요                        |

### <a name="resource-specific-parameters"></a>리소스별 매개 변수

Azure Healthcare API의 FHIR 서비스를 통해 FHIR 사양에 정의된 거의 모든 [리소스별 검색 매개 변수를](https://www.hl7.org/fhir/searchparameter-registry.html) 지원합니다. 지원되지 않는 검색 매개 변수는 아래 링크에서만 사용할 수 있습니다.

* [STU3 지원되지 않는 검색 매개 변수](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [R4 지원되지 않는 검색 매개 변수](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

다음 요청을 통해 [FHIR 기능 문에서](https://www.hl7.org/fhir/capabilitystatement.html) 검색 매개 변수에 대한 현재 지원을 확인할 수도 있습니다.

```rest
GET {{FHIR_URL}}/metadata
```

capability 문에서 검색 매개 변수를 보려면 으로 `CapabilityStatement.rest.resource.searchParam` 이동하여 각 리소스에 대한 검색 매개 변수를 확인하고 `CapabilityStatement.rest.searchParam` 모든 리소스에 대한 검색 매개 변수를 찾습니다.

> [!NOTE]
> Azure Healthcare API의 FHIR 서비스는 FHIR 사양에 의해 정의되지 않은 검색 매개 변수를 자동으로 만들거나 인덱싱하지 않습니다. 그러나 사용자 고유의 [검색 매개 변수](how-to-do-custom-search.md)를 정의할 수 있도록 지원합니다.

### <a name="composite-search-parameters"></a>복합 검색 매개 변수
복합 검색을 사용하면 값 쌍을 검색할 수 있습니다. 예를 들어 사람이 60인치인 높이 관찰을 검색하는 경우 관찰의 단일 구성 요소에 높이 **코드와** 값 60이 포함되어 있는지 확인할 수 있습니다. 다른 구성 요소 섹션에서만 값 60 및 높이 코드로 한정된 항목이 관찰에 있더라도 가중치 60 및 높이 48이 저장된 위치를 관찰하고 싶지는 않을 것입니다. 

Azure Healthcare API에 대한 FHIR 서비스를 통해 다음과 같은 검색 매개 변수 형식 페어링을 지원합니다.

* 참조, 토큰
* 토큰, 날짜
* 토큰, 숫자, 숫자
* 토큰, 수량
* 토큰, 문자열
* 토큰, 토큰

자세한 내용은 HL7 복합 검색 매개 변수 를 [참조하세요.](https://www.hl7.org/fhir/search.html#composite) 

> [!NOTE]
> 복합 검색 매개 변수는 FHIR 사양에 따라 한정자를 지원하지 않습니다.

 ### <a name="modifiers--prefixes"></a>접두사 & 한정자

[한정자를](https://www.hl7.org/fhir/search.html#modifiers) 사용하면 검색 매개 변수를 수정할 수 있습니다. 다음은 모든 FHIR 한정자 및 지원에 대한 개요입니다. 

| **한정자** | **FHIR용 Azure API** | **Azure Healthcare API의 FHIR 서비스** | **설명**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  :missing     | 예                  | 예                       |
|  :exact       | 예                  | 예                       |
|  :contains    | 예                  | 예                       |
|  :text        | 예                  | 예                       |
|  :type(참조) | 예             | 예                       |
|  :not         | 예                  | 예                       |
|  :below(uri) | 예                  | 예                       |
|  :above(uri) | 예                  | 예                       |
|  :in(토큰)  | 아니요                   | 아니요                        |
|  :below(토큰) | 아니요                 | 아니요                        |
|  :above(토큰) | 아니요                 | 아니요                        |
|  :not-in(토큰) | 아니요                | 아니요                        |

특정 순서(숫자, 날짜 및 수량)가 있는 검색 매개 변수의 경우 매개 변수에 [접두사](https://www.hl7.org/fhir/search.html#prefix) 를 사용하여 일치 항목을 찾을 수 있습니다. Azure Healthcare API의 FHIR 서비스는 모든 접두사만 지원합니다.

 ### <a name="search-result-parameters"></a>검색 결과 매개 변수
반환된 리소스를 관리하기 위해 검색에 사용할 수 있는 검색 결과 매개 변수가 있습니다. 각 검색 결과 매개 변수를 사용하는 방법에 대한 자세한 내용은 [HL7](https://www.hl7.org/fhir/search.html#return) 웹 사이트를 참조하세요. 

| **검색 결과 매개 변수**  | **FHIR용 Azure API** | **Azure Healthcare API의 FHIR 서비스** | **설명**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | 예                  | 예                       |
| _count                        | 예                  | 예                       | _count 1000개의 리소스로 제한됩니다. 1000보다 높게 설정된 경우 1,000개만 반환되고 번들에 경고가 반환됩니다.                               |
| _include                      | 예                  | 예                       | 포함된 항목은 100으로 제한됩니다. Cosmos DB의 PaaS 및 OSS에 대한 _include :iterate [지원(#2137)을](https://github.com/microsoft/fhir-server/issues/2137)포함하지 않습니다.                               |
| _revinclude                   | 예                  | 예                       |포함된 항목은 100으로 제한됩니다. Cosmos DB의 PaaS 및 OSS에 대한 _revinclude :iterate [지원(#2137)을](https://github.com/microsoft/fhir-server/issues/2137)포함하지 않습니다. 잘못된 요청에 대한 잘못된 상태 [코드도 있습니다.#1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | 예             | 예                   |
| _total                        | Partial              | Partial                   | _total = none 및 _total = 정확도                               |
| _sort                         | Partial              | Partial                   | sort = _lastUpdated은 FHIR 및 FHIR 서비스에 대 한 Azure API에서 지원 됩니다. fhir 서비스 및 OSS SQL DB fhir 서버에 대해 문자열 및 날짜/시간 필드를 기준으로 정렬이 지원 됩니다. 2021 년 4 월 20 일 이후에 생성 된 데이터베이스에 대 한 Azure API 및 OSS Cosmos DB의 경우 first name, last name 및 임상 date에서 정렬이 지원 됩니다.             |
| _contained                    | 아니요                   | 아니요                        |
| _containedType                | 아니요                   | 아니요                        |
| _score                        | 아니요                   | 아니요                        |

> [!NOTE]
> 기본적으로 `_sort` 는 레코드를 오름차순으로 정렬 합니다. 접두사를 사용 하 여 `'-'` 내림차순으로 정렬할 수 있습니다. 또한 FHIR 서비스와 FHIR 용 Azure API를 사용 하면 한 번에 하나의 필드를 기준으로 정렬할 수 있습니다.

기본적으로 Azure 의료 Api의 FHIR 서비스는 관대 처리로 설정 됩니다. 즉, 서버에서 알 수 없거나 지원 되지 않는 매개 변수를 무시 합니다. 엄격한 처리를 사용 하려면 **선호** 하는 헤더 및 집합을 사용할 수 있습니다 `handling=strict` .

 ## <a name="chained--reverse-chained-searching"></a>체인 & 역방향 연결 된 검색

연결 된 [검색](https://www.hl7.org/fhir/search.html#chaining) 을 사용 하면 다른 리소스에서 참조 하는 리소스에서 검색 매개 변수를 사용 하 여 검색할 수 있습니다. 예를 들어 환자 이름이 Jane 인 경우를 찾으려면 다음을 사용 합니다.

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

마찬가지로 역방향 연결 된 검색을 수행할 수 있습니다. 이렇게 하면 리소스를 참조 하는 다른 리소스에 대 한 조건을 지정 하는 리소스를 가져올 수 있습니다. 연결 된 검색 및 역방향 연결 된 검색에 대 한 추가 예제를 보려면 [Fhir 검색 예제](search-samples.md) 페이지를 참조 하세요. 


## <a name="pagination"></a>페이지 매김

위에서 설명한 것 처럼 검색 결과는 페이징된 번들이 됩니다. 기본적으로 검색은 페이지당 10 개 결과를 반환 하지만를 지정 하 여 증가 또는 감소 시킬 수 있습니다 `_count` . 번들 내에는 검색의 현재 결과를 포함 하는 자체 링크가 있습니다. 추가 일치 항목이 있는 경우 번들에는 다음 링크가 포함 됩니다. 계속 해 서 다음 링크를 사용 하 여 결과의 후속 페이지를 가져올 수 있습니다. `_count` 는 1000 개 항목 이하로 제한 됩니다. 

현재 Azure 의료 Api의 FHIR 서비스는 번들의 다음 링크만 지원 하며 첫 번째, 마지막 또는 이전 링크를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 검색의 기본 사항에 대해 알아보았습니다. 다른 검색 매개 변수, 한정자 및 기타 다른 검색 시나리오를 사용 하 여 검색 하는 방법에 대 한 자세한 내용은 샘플 검색 페이지를 참조 하세요.

>[!div class="nextstepaction"]
>[FHIR 검색 예제](search-samples.md)
