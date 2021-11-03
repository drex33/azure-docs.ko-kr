---
title: API 형식 지원 제한 사항 및 세부 정보
titleSuffix: Azure API Management
description: Azure API Management의 Open API, WSDL, WADL 형식 지원과 관련해서 알려진 이슈 및 제한 사항의 세부 정보입니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/26/2021
ms.author: danlep
ms.openlocfilehash: 5ddb4d615dc3d1bd7c9830d11a27341baf57edb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031815"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제

API를 가져올 때 몇 가지 제한 사항이 나 문제를 확인 하 고 수정 해야 성공적으로 가져올 수 있습니다. 이 문서에서는 다음에 대해 알아봅니다.
* OpenAPI 가져오기 중에 API Management 동작입니다. 
* API의 가져오기 형식으로 구성 된 가져오기 제한 사항입니다. 
* OpenAPI 내보내기의 작동 방식입니다.

## <a name="api-management-during-openapi-import"></a>OpenAPI 가져오기 중 API Management

OpenAPI를 가져오는 동안 다음을 API Management 합니다.
* 필수로 표시 된 쿼리 문자열 매개 변수에 대해 구체적으로 검사 합니다.
* 쿼리 문자열 매개 변수를 템플릿 매개 변수로 변환 합니다. 

다른 동작을 선호 하는 경우 다음 중 하나를 수행할 수 있습니다. 
* 양식 기반 편집기를 통해 수동으로 변경 
* OpenAPI 정의에서 "required" 특성을 제거 합니다. 따라서 템플릿 매개 변수로 변환 되지 않습니다.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Openapi/Swagger 가져오기 제한 사항

OpenAPI 문서를 가져오는 동안 오류가 발생 하는 경우 다음 중 하나를 수행 하 여 미리 유효성을 검사 해야 합니다.
* Azure Portal에서 디자이너 사용 (디자인 > 프런트 엔드 > OpenAPI 사양 편집기) 또는 
* <a href="https://editor.swagger.io">Swagger 편집기</a>와 같은 타사 도구를 사용 합니다.

### <a name="general"></a><a name="open-api-general"> </a>일반

#### <a name="url-template-requirements"></a>URL 템플릿 요구 사항

| 요구 사항 | Description |
| ----------- | ----------- |
| **필요한 경로 및 쿼리 매개 변수의 고유 이름** | OpenAPI에서: <ul><li>매개 변수 이름은 위치 내에서 고유 해야 합니다 (예: path, query, header).</li></ul>API Management:<ul><li>경로 및 쿼리 매개 변수를 사용 하 여 작업을 구분 하는 것을 허용 합니다.</li><li>OpenAPI는 이러한 판별을 지원 하지 않으므로 전체 URL 템플릿 내에서 고유 해야 하는 매개 변수 이름이 필요 합니다.</li></ul>  |
| **정의 된 URL 매개 변수** | URL 템플릿의 일부 여야 합니다. |
| **사용 가능한 원본 파일 URL** | 상대 서버 Url에 적용 됩니다. |
| **`\$ref` 포인터로** | 외부 파일을 참조할 수 없습니다. |


#### <a name="openapi-specifications"></a>OpenAPI 사양

**지원되는 버전**

API Management는 다음만 지원 합니다.
* OpenAPI 버전 2.
* OpenAPI 버전 3.0. x (최대 버전 3.0.3).

OpenAPI 버전 3.1은 현재 API Management에서 지원 되지 않습니다.

**크기 제한**

| 크기 제한 | 설명 |
| ---------- | ----------- |
| **최대 4mb** | OpenAPI 사양을 인라인으로 가져오는 경우 API Management 합니다. |
| **크기 제한이 적용 되지 않습니다.** | API Management 서비스에서 액세스할 수 있는 위치에 대 한 URL을 통해 OpenAPI 문서를 제공 하는 경우 |

#### <a name="supported-extensions"></a>지원되는 확장
유일 하 게 지원 되는 확장은 다음과 같습니다.

| 내선 번호 | 설명 |
| ----------- | ----------- |
| **`x-ms-paths`** | <ul><li>URL의 쿼리 매개 변수로 구별 되는 경로를 정의할 수 있습니다.</li><li>[AutoRest 문서](https://github.com/Azure/autorest/tree/main/docs/extensions#x-ms-paths)에서 다룹니다.</li></ul> |
| **`x-servers`** | OpenAPI 2에 대 한 [openapi 3 `servers` 개체](https://swagger.io/docs/specification/api-host-and-base-path/) 의 백 포트입니다. |

#### <a name="unsupported-extensions"></a>지원 되지 않는 확장
| 내선 번호 | 설명 |
| ----------- | ----------- |
| **`Recursion`** | API Management는 재귀적으로 정의 된 정의를 지원 하지 않습니다.<br />예를 들어 자신을 참조 하는 스키마가 있습니다. |
| **`Server` 개체가** | API 작업 수준에서는 지원 되지 않습니다. |
| **`Produces` 키워드** | API에서 반환 하는 MIME 형식을 설명 합니다. <br />지원되지 않습니다. |

#### <a name="custom-extensions"></a>사용자 지정 확장
- 가져올 때 무시 됩니다.
- 내보내기에 대해 저장 되지 않거나 유지 되지 않습니다.

#### <a name="unsupported-definitions"></a>지원 되지 않는 정의 
API 작업의 인라인 스키마 정의는 지원되지 않습니다. 스키마 정의:
- 는 API 범위에서 정의 됩니다.
- API 작업 요청 또는 응답 범위에서 참조할 수 있습니다.

#### <a name="ignored-definitions"></a>무시 정의
보안 정의는 무시됩니다.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 버전 2

OpenAPI 버전 2 지원은 JSON 형식 으로만 제한 됩니다.

### <a name="openapi-version-30x"></a><a name="open-api-v3"> </a>Openapi 버전 3.0. x

지원 되는 최신 OpenAPI 버전 3.0은 3.0.3입니다.

#### <a name="https-urls"></a>HTTPS Url
- Multiple `servers` 을 지정 하면 API Management는 찾은 첫 번째 HTTPS URL을 사용 합니다. 
- HTTPS Url이 없으면 서버 URL이 비어 있게 됩니다.

#### <a name="supported"></a>지원됨
- `example`

#### <a name="unsupported"></a>지원되지 않음
[Openapi 버전 3.0. x](https://swagger.io/specification/)에는 다음 필드가 포함 되어 있지만 지원 되지 않습니다.

| Object | 필드 |
| ----------- | ----------- |
| **OpenAPI** | `externalDocs` |
| **Components** | <ul><li>`responses`</li><li>`parameters`</li><li>`examples`</li><li>`requestBodies`</li><li>`headers`</li><li>`securitySchemes`</li><li>`links`</li><li>`callbacks`</li></ul> |
| **PathItem** | <ul><li>`trace`</li><li>`servers`</li></ul> |
| **연산** | <ul><li>`externalDocs`</li><li>`callbacks`</li><li>`security`</li><li>`servers`</li></ul> |
| **매개 변수** | <ul><li>`allowEmptyValue`</li><li>`style`</li><li>`explode`</li><li>`allowReserved`</li></ul> |

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 가져오기, 업데이트, 내보내기 메커니즘

### <a name="general"></a><a name="open-import-export-general"> </a>일반

API Management 서비스에서 내보낸 API 정의는 다음과 같습니다.
* 주로 API Management 서비스에서 호스트 되는 API를 호출 해야 하는 외부 응용 프로그램을 위한 것입니다. 
* 동일 하거나 다른 API Management 서비스로 가져올 수 없습니다. 

여러 서비스/환경에서 API 정의를 구성 하는 방법에 대 한 자세한 내용은 Git에서 API Management service 사용에 대 한 설명서를 참조 하세요. 

### <a name="add-new-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 새 API 추가

OpenAPI 문서에 있는 각 작업에 대해 다음을 사용 하 여 새 작업이 생성 됩니다.
* Azure 리소스 이름이로 설정 `operationId` 됩니다.
    * `operationId` 값은 정규화 됩니다.
    *  가 `operationId` 지정 되지 않은 경우 (존재 하지 않음, `null` 또는 비어 있지 않음) Azure 리소스 이름 값은 HTTP 메서드와 경로 템플릿을 결합 하 여 생성 됩니다.
        * 예들 들어 `get-foo`입니다.

* 표시 이름이로 설정 `summary` 됩니다. 
    * `summary` 기본값
        * 있는 그대로 가져옴
        * 길이는 300 자로 제한 됩니다.
    * 가 `summary` 지정 되지 않은 경우 (존재 하지 않음, `null` 또는 비어 있지 않음) 표시 이름 값이로 설정 됩니다 `operationId` . 

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 기존 API 업데이트

가져오는 동안 기존 API 작업은 다음과 같습니다.
* OpenAPI 문서에 설명 된 API와 일치 하도록 변경 합니다. 
* 해당 `operationId` 값을 기존 작업의 Azure 리소스 이름과 비교 하 여 OpenAPI 문서의 작업과 일치 합니다. 
    * 일치 항목이 있으면 기존 작업의 속성이 “현재 위치”에서 업데이트됩니다.
    * 일치 항목을 찾을 수 없는 경우:
        * HTTP 메서드와 경로 템플릿 (예:)을 결합 하 여 새 작업을 만듭니다 `get-foo` . 
        * 가져오기는 새 작업에 대해 각각 동일한 HTTP 메서드와 경로 템플릿을 사용하여 기존 작업의 정책을 복사하려고 합니다.

일치되지 않은 기존 작업은 모두 삭제됩니다.

가져오기를 보다 예측 가능 하 게 만들려면 다음 지침을 따르세요.

- `operationId`모든 작업에 대해 속성을 지정 합니다.
- 초기 가져오기 후에는 `operationId`를 변경하지 않습니다.
- `operationId`와 HTTP 메서드 또는 경로 템플릿을 동시에 변경하지 않습니다.

### <a name="export-api-as-openapi"></a>OpenAPI로 API 내보내기

각 작업에 대해 다음 작업을 수행 합니다.
* Azure 리소스 이름은로 내보내집니다 `operationId` .
* 표시 이름은로 내보내집니다 `summary` .

**정규화 규칙 `operationId`**
- 소문자로 변환합니다.
- 영숫자가 아닌 문자의 각 시퀀스를 단일 대시로 바꿉니다.
    - 예를 들어 `GET-/foo/{bar}?buzz={quix}` 는로 변환 됩니다 `get-foo-bar-buzz-quix-` .
- 양쪽에서 대시를 트리밍합니다.
    - 예를 들어, `get-foo-bar-buzz-quix-` 가이 됩니다. `get-foo-bar-buzz-quix`
- 리소스 이름의 최대 한도보다 4자 더 적은 76자에 맞게 자릅니다.
- 필요한 경우의 형태로 중복 제거 접미사에 대해 나머지 4 자를 사용 `-1, -2, ..., -999` 합니다.

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 파일을 사용 하 여 SOAP 통과 및 SOAP-REST Api를 만들 수 있습니다.

### <a name="soap-bindings"></a>SOAP 바인딩 
- "Document" 및 "literal" 인코딩 스타일의 SOAP 바인딩만 지원 됩니다.
- "Rpc" 스타일 또는 SOAP 인코딩을 지원 하지 않습니다.

### <a name="wsdlimport"></a>WSDL: 가져오기
지원되지 않습니다. 대신, 가져오기를 하나의 문서로 병합 합니다.

### <a name="messages-with-multiple-parts"></a>여러 부분으로 구성 된 메시지 
이 메시지 유형은 지원 되지 않습니다.

### <a name="wcf-wshttpbinding"></a>WCF wsHttpBinding 
- Windows Communication Foundation를 사용 하 여 만든 SOAP 서비스는를 사용 해야 `basicHttpBinding` 합니다.
- `wsHttpBinding`는 지원되지 않습니다.

### <a name="mtom"></a>MTOM 
- 를 사용 하 `MTOM` *는 서비스는 작동할 수 있습니다* . 
- 현재는 공식적으로 지원되지 않습니다.

### <a name="recursion"></a>재귀
- 재귀적으로 정의 된 형식은 API Management 지원 되지 않습니다.
- 예를 들어 자체 배열을 참조 합니다.

### <a name="multiple-namespaces"></a>여러 네임 스페이스
스키마에서 여러 네임 스페이스를 사용할 수 있지만 대상 네임 스페이스를 사용 하 여 메시지 파트를 정의할 수 있습니다. 이러한 네임 스페이스는 다른 입력 또는 출력 요소를 정의 하는 데 사용 됩니다.

대상이 아닌 네임 스페이스는 내보내기 시 유지 되지 않습니다. 다른 네임 스페이스를 사용 하 여 메시지 파트를 정의 하는 WSDL 문서를 가져올 수 있지만 모든 메시지 부분에는 내보내기의 WSDL 대상 네임 스페이스가 포함 됩니다.

### <a name="arrays"></a>배열 
SOAP 대 REST 변환은 아래 예제에 표시 된 래핑된 배열만 지원 합니다.

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

현재, 알려진 WADL 가져오기 문제가 없습니다.
