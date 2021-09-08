---
title: Azure API Management의 정책 | Microsoft Docs
description: API Management에서 정책을 만들고 편집하고 구성하는 방법에 대해 알아봅니다. 코드 예제를 살펴보고 다른 사용 가능한 리소스를 확인합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: apimpm
ms.openlocfilehash: c4b069d76d795bd8ae830b811c97dfe58c2f842c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106014"
---
# <a name="policies-in-azure-api-management"></a>Azure API Management의 정책

Azure API Management에서 API 게시자는 정책을 사용하여 구성을 통해 API 동작을 변경할 수 있습니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 많이 사용되는 문은 다음과 같습니다.

* XML에서 JSON으로 형식 변환
* 개발자로부터 들어오는 호출의 수를 제한하는 호출 속도 제한 

다양한 다른 정책도 바로 사용할 수 있습니다.

정책은 API 소비자와 관리되는 API 간에 있는 게이트웨이 내에서 적용됩니다. 게이트웨이가 요청을 수신하고 변경하지 않고 기본 API에 전달하는 동안 정책은 인바운드 요청과 아웃바운드 응답 모두에 변경 사항을 적용할 수 있습니다.

정책이 다르게 지정하지 않는 한 정책 식은 어떤 API Management 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름][Control flow] 및 [변수 설정][Set variable] 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책][Advanced policies] 및 [정책 식][Policy expressions] 문서를 참조하세요.

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>정책 구성 이해

정책 정의는 일련의 인바운드 및 아웃바운드 문을 설명하는 간단한 XML 문서입니다. 다음을 제공하는 정의 창에서 직접 XML을 편집할 수 있습니다.
* 오른쪽의 문 목록
* 현재 범위에 해당하는 문은 활성화되고 강조 표시됨

사용할 수 있는 명령문을 클릭하면 정의 뷰에서 커서의 위치에 적절한 XML이 추가됩니다. 

> [!NOTE]
> 추가하려는 정책이 활성화되지 않는 경우 해당 정책의 올바른 범위에 속하는지 확인하세요. 각 정책 문은 특정 범위 및 정책 섹션에서 사용하도록 되어 있습니다. 정책의 정책 섹션 및 범위를 검토하려면 **정책 참조** 에서 [사용][Policy Reference] 섹션을 확인하세요.

구성은 `inbound`, `backend`, `outbound` 및 `on-error`로 구분됩니다. 지정된 일련의 정책 문이 요청 및 응답을 위해 실행됩니다.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

요청 처리 중 오류가 발생하는 경우:
* `inbound`, `backend` 또는 `outbound` 섹션의 나머지 단계는 건너뜁니다.
* 실행은 `on-error` 섹션의 문으로 이동합니다.

`on-error` 섹션에 정책 문을 배치하여 다음을 수행할 수 있습니다.
* `context.LastError` 속성을 사용하여 오류를 검토합니다.
* `set-body` 정책을 사용하여 오류 응답을 검사하고 사용자 지정합니다.
* 오류가 발생하면 어떻게 되는지 구성합니다. 

오류 코드에 대한 자세한 내용은 [API Management 정책에서 오류 처리](./api-management-error-handling-policies.md)를 참조하세요.
* 기본 제공 단계
* 정책 문을 처리하는 동안 발생할 수 있는 오류입니다. 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>정책을 구성하는 방법

정책을 구성하는 방법에 대한 내용은 [정책 설정 또는 편집](set-edit-policies.md)을 참조하세요.

## <a name="policy-reference"></a>정책 참조

정책 문 및 해당 설정에 대한 전체 목록은 [정책 참조](./api-management-policies.md)를 참조하세요.

## <a name="policy-samples"></a>정책 샘플

자세한 코드 예제는 [정책 샘플](./policy-reference.md)을 참조하세요.

## <a name="examples"></a>예

### <a name="apply-policies-specified-at-different-scopes"></a>서로 다른 범위에서 지정된 정책 적용

전역 수준의 정책 및 API에 대해 구성된 정책이 있는 경우 특정 API를 사용할 때마다 두 정책이 모두 적용됩니다. API Management는 `base` 요소를 통해 결합된 정책 명령문의 결정적인 순서를 허용합니다. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

위의 예 정책 정의에서:
* `cross-domain` 문은 상위 정책보다 먼저 실행됩니다.
* `find-and-replace` 정책은 상위 정책 이후에 실행됩니다. 

>[!NOTE]
> API 범위에서 `<base />` 태그를 제거하면 API 범위에서 구성된 정책만 적용됩니다. 제품 또는 글로벌 범위 정책이 적용되지 않습니다.

### <a name="restrict-incoming-requests"></a>들어오는 요청 제한

들어오는 요청을 지정된 IP 주소로 제한하는 새 명령문을 추가하려면 `inbound` XML 요소의 내용 바로 안에 커서를 놓고 **호출자 IP 제한** 문을 클릭합니다.

![제한 정책][policies-restrict]

그러면 명령문을 구성하는 방법에 대한 참고 자료를 제공하는 XML 코드 조각이 `inbound` 요소에 추가됩니다.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

인바운드 요청을 제한하여 1.2.3.4의 IP 주소에서 들어오는 요청만 허용하려면 다음과 같이 XML을 수정하세요.

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API 변환](transform-api.md)
+ [정책 참조](./api-management-policies.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
