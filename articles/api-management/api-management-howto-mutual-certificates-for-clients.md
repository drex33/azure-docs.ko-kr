---
title: API Management에서 클라이언트 인증서 인증을 사용하여 API 보호
titleSuffix: Azure API Management
description: 클라이언트 인증서를 사용하여 API에 대한 액세스를 보호하는 방법을 알아봅니다. 정책 식을 사용하여 들어오는 인증서의 유효성을 검사할 수 있습니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2021
ms.author: danlep
ms.openlocfilehash: 2eac7a8de0ca49cdab85f3ab948e54e402073128
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582919"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 연결 클라이언트가 제공한 인증서의 유효성을 검사하고 정책 식을 사용하여 원하는 값에 대한 인증서 속성을 확인할 수 있습니다.

클라이언트 인증서를 사용하여 API의 백 엔드 서비스(예: 백 엔드에 대한 API Management)에 대한 액세스를 보호하는 방법에 대한 자세한 내용은 [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](./api-management-howto-mutual-certificates.md)을 참조하세요.

> [!IMPORTANT]
> 개발자, 기본, 표준 또는 프리미엄 계층에서 HTTP/2를 통해 클라이언트 인증서를 수신하고 확인하려면 아래와 같이 '사용자 지정 도메인' 블레이드에서 '클라이언트 인증서 협상' 설정을 켜야 합니다.

![클라이언트 인증서 협상](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 소비 계층에서 클라이언트 인증서를 수신하고 확인하려면 아래와 같이 '사용자 지정 도메인' 블레이드에서 '클라이언트 인증서 요청' 설정을 켜야 합니다.

![클라이언트 인증서 요청](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="policy-to-validate-client-certificates"></a>클라이언트 인증서의 유효성을 검사하는 정책

[validate-client-certificate](api-management-access-restriction-policies.md#validate-client-certificate) 정책을 사용하여 API Management 인스턴스에서 호스트되는 API에 액세스하는 데 사용되는 클라이언트 인증서의 하나 이상의 특성에 대한 유효성을 검사합니다.

인증서 발급자, 주체, 지문, 인증서가 온라인 해지 목록에 대한 인증서의 유효성 검사 여부 등을 비롯한 하나 이상의 특성에 대한 유효성을 검사하도록 정책을 구성합니다.

자세한 내용은 [Azure API Management 액세스 제한 정책](api-management-access-restriction-policies.md)을 참조하세요.

## <a name="certificate-validation-with-context-variables"></a>컨텍스트 변수를 사용한 인증서 유효성 검사

[`context` 변수](api-management-policy-expressions.md#ContextVariables)를 사용하여 클라이언트 인증서를 확인하는 정책 식을 만들 수도 있습니다. 다음 섹션의 예제에서는 `context.Request.Certificate` 속성 및 기타 `context` 속성을 사용하는 식을 보여줍니다.

> [!IMPORTANT]
> 2021년 5월부터 `context.Request.Certificate` 속성은 API Management 인스턴스의 [`hostnameConfiguration`](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration)이 `negotiateClientCertificate` 속성을 True로 설정한 경우에만 인증서를 요청합니다. 기본적으로 `negotiateClientCertificate`는 false로 설정됩니다.

### <a name="checking-the-issuer-and-subject"></a>발급자 및 주체 확인

클라이언트 인증서의 발급자 및 주체를 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 확인을 사용하지 않도록 설정하려면 `context.Request.Certificate.Verify()` 대신 `context.Request.Certificate.VerifyNoRevocation()`을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API Management로 [업로드](api-management-howto-ca-certificates.md)해야 `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()`이 작동합니다.

### <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 확인을 사용하지 않도록 설정하려면 `context.Request.Certificate.Verify()` 대신 `context.Request.Certificate.VerifyNoRevocation()`을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API Management로 [업로드](api-management-howto-ca-certificates.md)해야 `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()`이 작동합니다.

### <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management에 업로드된 인증서에 대해 지문 확인

다음 예제에서는 API Management에 업로드된 인증서에 대해 클라이언트 인증서의 지문을 확인하는 방법을 보여 줍니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 인증서 해지 목록 확인을 사용하지 않도록 설정하려면 `context.Request.Certificate.Verify()` 대신 `context.Request.Certificate.VerifyNoRevocation()`을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API Management로 [업로드](api-management-howto-ca-certificates.md)해야 `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()`이 작동합니다.

> [!TIP]
> 이 [문서](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)에서 설명하는 클라이언트 인증서 교착 상태 문제는 요청이 중지하거나, 요청 시간 초과로 인해 `403 Forbidden` 상태 코드가 발생하거나, `context.Request.Certificate`가 `null`이 되는 등 다양한 방식으로 나타날 수 있습니다. 이 문제는 일반적으로 콘텐츠 길이가 약 60KB 이상인 `POST` 및 `PUT` 요청에 영향을 줍니다.
> 이 문제가 발생하지 않도록 하려면 이 문서의 첫 번째 이미지에 표시된 것처럼 '사용자 지정 도메인' 블레이드에서 원하는 호스트 이름에 대해 '클라이언트 인증서 협상' 설정을 켭니다. 소비 계층에서는 이 기능을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

-   [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](./api-management-howto-mutual-certificates.md)
-   [인증서 업로드 방법](./api-management-howto-mutual-certificates.md)
