---
title: 앱의 인증 및 권한 부여
description: Azure 유체 Relay 서비스에서 인증 및 권한 부여를 사용 하는 방법에 대 한 개요입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: fc3e55a91af1e7691d1d2677435c283914521dfe
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663062"
---
# <a name="authentication-and-authorization-in-your-app"></a>앱의 인증 및 권한 부여

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

최신 웹 응용 프로그램에는 보안이 중요 합니다. Fluid Framework 웹 응용 프로그램 아키텍처의 일부는 보안을 유지 하는 인프라의 중요 한 부분입니다. Fluid Framework는 계층화 된 아키텍처 이며, 인증 관련 개념은 연결 된 유체 서비스를 기반으로 구현 됩니다. 즉, 모든 액량 service에서 일반적인 인증 테마가 있지만 세부 정보 및 세부 정보는 각 서비스 마다 다릅니다.

## <a name="azure-fluid-relay-service"></a>Azure 유체 Relay 서비스

사용자가 만드는 각 Azure 유체 Relay 서비스 테 넌 트에 **테 넌 트 ID** 및 고유한 **테 넌 트 비밀 키** 가 할당 됩니다.

비밀 키가 **공유 암호** 입니다. 앱/서비스에서이를 인식 하 고 Azure 유체 Relay 서비스에서이를 인식 합니다. 테 넌 트 비밀 키가 테 넌 트에 고유 하 게 연결 되어 있기 때문에 요청을 서명 하는 데 사용 하는 요청은 테 넌 트의 권한이 있는 사용자 로부터 요청이 발생 하는 Azure 유체 Relay 서비스를 보장 합니다.

비밀 키는 Azure 유체 Relay 서비스에서 요청이 앱 이나 서비스에서 오는 것을 인식 하는 방법입니다. Azure 유체 Relay 서비스는 앱이 요청을 수행 하는 것을 신뢰 하 고 *사용자* 가 보내는 데이터를 신뢰할 수 있으므로이는 중요 합니다. 이는 보안이 안전 하 게 처리 되는 것이 중요 한 이유 이기도 합니다.

> [!CAUTION]
> 비밀에 대 한 액세스 권한이 있는 사용자는 Azure 유체 Relay 서비스와 통신할 때 응용 프로그램을 가장할 수 있습니다.

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON 웹 토큰 및 Azure 유체 Relay 서비스

Azure 유체 Relay는 [JWTs (JSON 웹 토큰)](https://jwt.io/) 를 사용 하 여 비밀 키로 서명 된 데이터를 인코딩하고 확인 합니다. JSON 웹 토큰은 권한 및 사용 권한에 대 한 추가 정보를 포함할 수 있는 JSON의 부호 있는 비트입니다.

> [!NOTE]
> JWTs의 구체적인 내용은이 문서의 범위를 벗어나는 것입니다. JWT 표준에 대 한 자세한 내용은을 참조 하십시오 <https://jwt.io/introduction> .

유체 서비스 마다 인증 세부 정보는 다를 수 있지만 여러 값이 항상 있어야 합니다.

- **containerId**  유체 서비스는 호출 하는 컨테이너에 해당 하는 서비스를 식별 하기 위해 컨테이너 ID가 필요 합니다. *참고*: JWT는이 필드 documentId를 호출 하지만 유체 service는이 필드에 컨테이너 ID가 필요 합니다.
- **tenantId**: Azure 유체 Relay 서비스는 테 넌 트 ID를 사용 하 여 요청을 인증 하는 데 사용할 공유 암호를 검색 합니다. 
- **범위**: 범위는 호출 하는 컨테이너의 사용 권한을 정의 합니다. 범위 필드의 내용은 유연 하므로 사용자 지정 권한을 직접 만들 수 있습니다.

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

> [!NOTE]
> 토큰에는 사용자 정보 (위의 줄 7-9 참조)도 포함 되어 있습니다. 이를 사용 하 여 [대상](../how-tos/connect-fluid-azure-service.md#getting-audience-details) 기능을 통해 유체 코드에서 자동으로 사용할 수 있는 사용자 정보를 확대할 수 있습니다. 자세한 내용은 [토큰에 사용자 지정 데이터 추가](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens) 를 참조 하세요.

## <a name="the-token-provider"></a>토큰 공급자

Azure 유체 Relay에 대 한 모든 요청은 유효한 JWT로 서명 되어야 합니다. 유체는 토큰 공급자에 이러한 토큰을 만들고 서명 하는 책임을 위임 합니다.

토큰 공급자는에서 `@fluidframework/azure-client` Azure 유체 Relay 서비스에 요청 하는 데 사용 하는 토큰을 만들고 서명 하는 작업을 담당 합니다. 사용자 고유의 보안 토큰 공급자 구현을 제공 해야 합니다. 그러나 유체는 `InsecureTokenProvider` 테 넌 트 암호를 수락 하는를 제공 하 고, 로컬에서 서명 된 토큰을 생성 하 고 반환 합니다. 이 토큰 공급자는 테스트에 유용 하지만 프로덕션 시나리오에서는 사용할 수 없습니다.

### <a name="a-secure-serverless-token-provider"></a>서버를 사용 하지 않는 보안 토큰 공급자

보안 토큰 공급자를 구축 하는 한 가지 옵션은 서버를 사용 하지 않는 Azure 함수를 만들고 토큰 공급자로 노출 하는 것입니다. 이렇게 하면 *테 넌 트 비밀 키* 를 보안 서버에 저장할 수 있습니다. 그러면 응용 프로그램에서 Azure 함수를 호출 하 여와 같이 로컬에 서명 하는 대신 토큰을 생성 합니다 `InsecureTokenProvider` . 자세한 내용은 [방법: Azure 함수를 사용 하 여 TokenProvider 작성](../how-tos/azure-function-token-provider.md)을 참조 하세요.

## <a name="connecting-user-auth-to-fluid-service-auth"></a>사용자 인증을 유체 service auth에 연결

유체 서비스는 특정 사용자에 게 연결 되지 않은 공유 클라이언트 암호를 사용 하 여 들어오는 호출을 인증 합니다. 사용자 인증은 유체 service의 세부 정보에 따라 추가할 수 있습니다. 

사용자 인증에 대 한 간단한 옵션 중 하나는 단순히 토큰 공급자로 [Azure Function](../../azure-functions/index.yml) 을 사용 하 고 토큰을 가져오는 조건으로 사용자 인증을 적용 하는 것입니다. 응용 프로그램에서 함수를 호출 하려고 하면 인증 시스템으로 인증 되지 않은 경우 오류가 발생 합니다. 예를 들어 Azure Active Directory (azure ad)를 사용 하는 경우 azure Function에 대 한 azure ad 응용 프로그램을 만들고 조직의 인증 시스템에 연결할 수 있습니다.

이 경우 사용자는 azure AD를 사용 하 여 응용 프로그램에 로그인 할 수 있습니다 .이를 통해 Azure Function을 호출 하는 데 사용할 토큰을 얻을 수 있습니다. Azure 함수 자체는 동일 하 게 동작 하지만 이제 Azure AD로 인증 된 사용자만 액세스할 수 있습니다.

이제 Azure 함수는 유효한 토큰을 가져오기 위한 진입점 이므로 함수를 올바르게 인증 한 사용자만 클라이언트 응용 프로그램에서 Azure 유체 Relay 서비스에 해당 토큰을 제공할 수 있습니다. 이 2 단계 접근 방식을 통해 Azure 유체 Relay 서비스와 함께 사용자 지정 인증 프로세스를 사용할 수 있습니다.

## <a name="see-also"></a>참조

- [JWTs 소개](https://jwt.io/introduction)
- [JSON Web Token를 만드는 방법](../how-tos/fluid-json-web-token.md)
- [Azure 유체 Relay의 페이로드 클레임](../how-tos/fluid-json-web-token.md#payload-claims)
