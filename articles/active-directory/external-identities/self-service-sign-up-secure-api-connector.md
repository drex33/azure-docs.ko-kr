---
title: Azure AD 셀프 서비스 등록 사용자 흐름에서 API 커넥터로 사용되는 API 보안 유지
description: 셀프 서비스 등록 사용자 흐름에서 API 커넥터로 사용되는 사용자 지정 RESTful API를 보호합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9f580c903ccc806ab4cfe5229c4931a4810bf8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481886"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>Azure AD External Identities 셀프 서비스 등록 사용자 흐름에서 API 커넥터를 사용한 API 보안 유지

Azure AD External Identities 셀프 서비스 등록 사용자 흐름 내에서 REST API를 통합하는 경우 인증을 사용하여 REST API 엔드포인트를 보호해야 합니다. REST API 인증을 사용하면 Azure AD와 같이 적절한 자격 증명이 있는 서비스만 엔드포인트에 대한 호출을 수행할 수 있습니다. 이 문서에서는 REST API를 보호하는 방법을 살펴봅니다. 

## <a name="prerequisites"></a>사전 요구 사항
[연습: 가입 사용자 흐름에 API 커넥터 추가](self-service-sign-up-add-api-connector.md) 가이드의 단계를 완료합니다.

HTTP 기본 인증 또는 HTTPS 클라이언트 인증서 인증을 사용하여 API 엔드포인트를 보호할 수 있습니다. 두 경우 모두 API 엔드포인트를 호출할 때 Azure AD에서 사용할 자격 증명을 제공합니다. 그런 다음, API 엔드포인트는 자격 증명을 확인하고 권한 부여 결정을 내립니다.


## <a name="http-basic-authentication"></a>HTTP 기본 인증

HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의되어 있습니다. 기본 인증은 다음과 같이 작동합니다. Azure AD는 `Authorization` 헤더에서 클라이언트 자격 증명(`username` 및 `password`)을 사용하여 HTTP 요청을 보냅니다. 자격 증명은 base64로 인코딩된 문자열 `username:password`로 형식이 지정됩니다. 그러면 API는 이러한 값을 검사하여 다른 권한 부여 결정을 수행해야 합니다.

HTTP 기본 인증을 사용하여 API 커넥터를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD** 를 선택합니다.
3. **API 커넥터** 를 선택한 다음, 구성하려는 **API 커넥터** 를 선택합니다.
4. **인증 형식** 으로 **기본** 을 선택합니다.
5. REST API 엔드포인트의 **사용자 이름** 및 **암호** 를 제공합니다.
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="API 커넥터에 대한 기본 인증 구성을 제공합니다.":::
6. **저장** 을 선택합니다.

## <a name="https-client-certificate-authentication"></a>HTTPS 클라이언트 인증서 인증

클라이언트 인증서 인증은 클라이언트, Azure AD에서 해당 ID를 증명하기 위해 해당 클라이언트 인증서를 서버에 제공하는 상호 인증서 기반 인증입니다. 이는 SSL 핸드셰이크의 일부로 발생합니다. API는 인증서가 Azure AD와 같은 유효한 클라이언트에 속하는지 확인하고 권한 부여 결정을 수행할 책임이 있습니다. 클라이언트 인증서는 X.509 디지털 인증서입니다. 

> [!IMPORTANT]
> 프로덕션 환경에서는 인증 기관에서 인증서에 서명해야 합니다.

### <a name="create-a-certificate"></a>인증서 만들기

#### <a name="option-1-use-azure-key-vault-recommended"></a>옵션 1: Azure Key Vault 사용(권장)

인증서를 만들려면 자체 서명된 인증서 및 서명된 인증서의 인증서 발급자 공급자와의 통합 옵션이 포함된 [Azure Key Vault](../../key-vault/certificates/create-certificate.md)를 사용할 수 있습니다. 권장 설정은 다음과 같습니다.
- **주체**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **콘텐츠 형식**: `PKCS #12`
- **수명 주기 작업 유형**: `Email all contacts at a given percentage lifetime` 또는 `Email all contacts a given number of days before expiry`
- **키 유형**: `RSA`
- **키 크기**: `2048`
- **내보낼 수 있는 프라이빗 키**: `Yes`(`.pfx` 파일을 내보내는 데 필요)

그런 다음, [인증서를 내보낼](../../key-vault/certificates/how-to-export-certificate.md)수 있습니다.

#### <a name="option-2-prepare-a-self-sized-certificate-using-powershell-module"></a>옵션 2: PowerShell 모듈을 사용하여 자체 크기 조정 인증서 준비

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>API 커넥터 구성

클라이언트 인증서 인증을 사용하여 API 커넥터를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD** 를 선택합니다.
3. **API 커넥터** 를 선택한 다음, 구성하려는 **API 커넥터** 를 선택합니다.
4. **인증 형식** 으로 **인증서** 를 선택합니다.
5. **인증서 업로드** 상자에서 프라이빗 키가 있는 인증서의 .pfx 파일을 선택합니다.
6. **암호 입력** 상자에 인증서의 암호를 입력합니다.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="API 커넥터에 대한 인증서 인증 구성을 제공합니다.":::
7. **저장** 을 선택합니다.

### <a name="perform-authorization-decisions"></a>권한 부여 결정 수행 
API는 API 엔드포인트를 보호하기 위해 전송된 클라이언트 인증서를 기준으로 권한 부여를 구현해야 합니다. Azure App Service 및 Azure Functions의 경우 [TLS 상호 인증 구성](../../app-service/app-service-web-configure-tls-mutual-auth.md)을 참조하여 *API 코드에서 인증서를 사용하도록 설정하고 유효성을 검사* 하는 방법을 알아보세요.  또는 Azure API Management를 API 서비스 앞의 계층으로 사용하여 [클라이언트 인증서 속성이 원하는 값인지 확인](
../../api-management/api-management-howto-mutual-certificates-for-clients.md)할 수 있습니다.

### <a name="renewing-certificates"></a>인증서 갱신
인증서가 만료되는 경우에 대한 미리 알림 경고를 설정하는 것이 좋습니다. 새 인증서를 생성하고 사용된 인증서가 만료될 예정일 때 위의 단계를 반복해야 합니다. 새 인증서 사용을 "이동"하기 위해 API 서비스는 새 인증서가 배포되는 동안 일시적으로 이전 인증서와 새 인증서를 계속 수락할 수 있습니다. 

기존 API 커넥터에 새 인증서를 업로드하려면 **API 커넥터** 에서 API 커넥터를 선택하고 **새 인증서 업로드** 를 클릭합니다. 만료되지 않고 시작 날짜가 경과된 가장 최근에 업로드된 인증서가 Azure AD에서 자동으로 사용됩니다.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="새 인증서가 이미 있는 경우 API 커넥터에 제공":::

## <a name="api-key-authentication"></a>API 키 인증

일부 서비스는 "API 키" 메커니즘을 사용하여 개발 중에 호출자가 고유 키를 HTTP 헤더 또는 HTTP 쿼리 매개 변수로 포함하도록 요구하여 HTTP 엔드포인트에 대한 액세스를 난독 처리합니다. [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)의 경우 `code`를 API 커넥터의 **엔드포인트 URL** 에 쿼리 매개 변수로 포함하여 이 작업을 수행할 수 있습니다. (예: `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>) 

프로덕션 환경에서 단독으로 사용해야 하는 메커니즘이 아닙니다. 따라서 기본 또는 인증서 인증에 대한 구성이 항상 필요합니다. 개발 목적으로 인증 방법을 구현하지 않으려는 경우(권장되지 않음) API 커넥터 구성에서 '기본' 인증을 선택하고, API에서 적절한 권한 부여를 구현하는 동안 무시할 수 있는 `username` 및 `password`에 대해 임시 값을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 시작해 보세요.