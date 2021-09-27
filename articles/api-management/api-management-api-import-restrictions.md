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
ms.date: 01/02/2020
ms.author: danlep
ms.openlocfilehash: f23f1a4200c973a90bee84cadfa9e49eef7523dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675443"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제

## <a name="about-this-list"></a>다음 목록 정보

API를 가져올 때 가져오기에 성공하기 위해 먼저 해결해야 하는 몇 가지 제한 사항이나 이슈가 있을 수 있습니다. 이 문서에서는 제한 사항을 API 가져오기 형식별로 구성하여 설명합니다. OpenAPI 내보내기의 작동 방식에 대해서도 설명합니다.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Openapi/Swagger 가져오기 제한 사항

OpenAPI 문서를 가져올 때 오류가 표시되면 해당 문서의 유효성을 미리 검사했는지 확인하세요. 유효성 검사를 수행할 때는 Azure Portal에서 디자인 - 프런트 엔드 - OpenAPI 사양 편집기를 선택하여 디자이너를 사용할 수도 있고, <a href="https://editor.swagger.io">Swagger Editor</a> 등의 타사 도구를 사용할 수도 있습니다.

### <a name="general"></a><a name="open-api-general"> </a>일반

-   경로와 쿼리 간에 필수 매개 변수 이름은 고유해야 합니다. OpenAPI에서는 매개 변수 이름이 경로, 쿼리, 헤더 등의 특정 위치 내에서만 고유하면 됩니다. 하지만 API Management에서는 경로 및 쿼리 매개 변수를 기준으로 작업을 구분할 수 있습니다. OpenAPI에서는 이러한 구분이 지원되지 않습니다. 따라서 전체 URL 템플릿 내에서 매개 변수 이름이 고유해야 합니다.
- API Management에 인라인으로 가져올 때 OpenAPI 사양의 크기는 최대 4MB일 수 있습니다. API Management 서비스에서 액세스할 수 있는 위치에 대한 URL을 통해 OpenAPI 문서가 제공되는 경우 크기 제한이 적용되지 않습니다.
-   `\$ref` 포인터는 외부 파일을 참조할 수 없습니다.
-   지원되는 확장은 `x-ms-paths` 및 `x-servers`뿐입니다.
-   사용자 지정 확장명은 가져오기 시 무시되며 내보내기용으로 저장되거나 보존되지 않습니다.
-   `Recursion` - 자신을 참조하는 스키마 등 재귀적으로 정의된 정의는 API Management에서 지원되지 않습니다.
-   이 경우에는 원본 파일 URL(사용 가능한 경우)이 상대 서버 URL에 적용됩니다.
-   보안 정의는 무시됩니다.
-   API 작업의 인라인 스키마 정의는 지원되지 않습니다. 스키마 정의는 API 범위에서 정의되며 API 작업 요청 또는 응답 범위에서 참조할 수 있습니다.
-   정의된 URL 매개 변수는 URL 템플릿에 포함되어야 합니다.
-   `server` 개체는 API 작업 수준에서 지원되지 않습니다.
-   API에서 반환되는 MIME 형식을 설명하는 `Produces` 키워드는 지원되지 않습니다. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 버전 2

-   JSON 형식만 지원됩니다.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 버전 3

-   `servers`가 여러 개 지정된 경우 API Management는 첫 번째 HTTPs URL을 선택하려고 합니다. HTTPs URL이 없으면 첫 번째 HTTP URL이 선택됩니다. HTTP URL도 없으면 서버 URL은 비어 있게 됩니다.
-   `Examples`는 지원되지 않지만 `example`은 지원됩니다.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 가져오기, 업데이트, 내보내기 메커니즘

### <a name="general"></a><a name="open-import-export-general"> </a>일반

-   API Management 서비스에서 내보낸 API 정의는 주로 API Management 서비스에서 호스트된 API를 호출해야 하는 API Management 서비스 외부 애플리케이션을 위한 것입니다. 내보낸 API 정의는 동일하거나 다른 API Management 서비스로 다시 가져올 수 없습니다. 다양한 서비스/환경 간의 API 정의 구성 관리는 Git에서 API Management 서비스 사용에 관한 설명서를 참조하세요. 

### <a name="add-new-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 새 API 추가

OpenAPI 문서에 있는 각 작업에 대해 Azure 리소스 이름 및 표시 이름을 각각 `operationId` 및 `summary`로 설정하여 새 작업이 생성됩니다. `operationId` 값은 아래에 설명된 규칙에 따라 정규화됩니다. `summary` 값은 있는 그대로 가져오고 길이는 300자로 제한됩니다.

`operationId`가 지정되지 않은 경우(즉, 없거나 `null`이거나 비어 있음) HTTP 메서드와 경로 템플릿을 결합하여 Azure 리소스 이름 값이 생성됩니다(예: `get-foo`).

`summary`가 지정되지 않은 경우(즉, 없거나 `null`이거나 비어 있음) `display name` 값이 `operationId`로 설정됩니다. `operationId`가 지정되지 않은 경우 HTTP 메서드와 경로 템플릿을 결합하여 표시 이름 값이 생성됩니다(예: `Get - /foo`).

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI 가져오기를 통해 기존 API 업데이트

가져오는 동안 기존 API는 OpenAPI 문서에 설명된 API와 일치하도록 변경됩니다. OpenAPI 문서의 각 작업은 기존 작업의 Azure 리소스 이름과 해당 `operationId` 값을 비교하여 기존 작업과 일치됩니다.

일치 항목이 있으면 기존 작업의 속성이 “현재 위치”에서 업데이트됩니다.

일치 항목을 찾을 수 없는 경우에는 위의 섹션에 설명된 규칙을 사용하여 새 작업이 생성됩니다. 가져오기는 새 작업에 대해 각각 동일한 HTTP 메서드와 경로 템플릿을 사용하여 기존 작업의 정책을 복사하려고 합니다.

일치되지 않은 기존 작업은 모두 삭제됩니다.

가져오기를 보다 예측 가능하게 하려면 다음 지침을 따르세요.

- 모든 작업에 대해 `operationId` 속성을 지정해야 합니다.
- 초기 가져오기 후에는 `operationId`를 변경하지 않습니다.
- `operationId`와 HTTP 메서드 또는 경로 템플릿을 동시에 변경하지 않습니다.

### <a name="export-api-as-openapi"></a>OpenAPI로 API 내보내기

각 작업에 대해 Azure 리소스 이름을 `operationId`로 내보내고 표시 이름을 `summary`로 내보냅니다.
operationId의 정규화 규칙

- 소문자로 변환합니다.
- 영숫자가 아닌 문자의 각 시퀀스를 단일 대시로 바꿉니다. 예를 들어 `GET-/foo/{bar}?buzz={quix}`는 `get-foo-bar-buzz-quix-`로 변환됩니다.
- 예를 들어 양쪽에서 대시를 트리밍하는 경우 `get-foo-bar-buzz-quix-`는 `get-foo-bar-buzz-quix`가 됩니다.
- 리소스 이름의 최대 한도보다 4자 더 적은 76자에 맞게 자릅니다.
- 필요한 경우 남은 4자를 중복 제거 접미사에 `-1, -2, ..., -999` 형식으로 사용합니다.


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 파일은 SOAP 통과 API와 SOAP-REST API를 만드는 데 사용됩니다.

-   **SOAP 바인딩** - 스타일 "document" 및 "literal" 인코딩의 SOAP 바인딩만 사용할 수 있습니다. "rpc" 스타일 또는 SOAP 인코딩은 지원되지 않습니다.
-   **WSDL:Import** - 이 특성은 지원되지 않습니다. 고객은 가져오기를 문서 하나로 병합해야 합니다.
-   **여러 부분으로 된 메시지** - 이러한 유형의 메시지는 지원되지 않습니다.
-   **WCF wsHttpBinding** - Windows Communication Foundation을 사용하여 만든 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
-   **MTOM** - MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
-   **재귀** - 재귀적으로 정의된(예: 자체의 배열을 참조) 형식은 APIM에서 지원되지 않습니다.
-   **여러 네임스페이스** - 하나의 스키마에서 여러 네임스페이스를 사용할 수 있지만 메시지 파트를 정의하는 데는 대상 네임스페이스만 사용할 수 있습니다. 다른 입력 또는 출력 요소를 정의하는 데 사용되는 대상 이외의 네임스페이스는 유지되지 않습니다. 이러한 WSDL 문서를 가져올 수 있지만 내보내기 시 모든 메시지 파트의 대상 네임스페이스는 WSDL입니다.
-   **배열** - SOAP-REST 변환은 아래 예제에 표시된 래핑된 배열만 지원합니다.

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
