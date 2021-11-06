---
title: 앱의 인증 및 권한 부여
description: Azure Fluid Relay 서비스에서 인증 및 권한 부여를 사용하는 방법에 대한 개요입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: e2f24c5455548980318c4536b5c65f84ea6a7dfb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893826"
---
# <a name="authentication-and-authorization-in-your-app"></a>앱의 인증 및 권한 부여

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

보안은 최신 웹 애플리케이션에 중요합니다. Fluid Framework 웹 애플리케이션 아키텍처의 일부로 보안을 위한 중요한 인프라 부분입니다. Fluid Framework 계층형 아키텍처이며, 인증 관련 개념은 연결하는 유체 서비스를 기반으로 구현됩니다. 즉, 모든 Fluid 서비스에 공통 인증 테마가 있지만 세부 정보 및 세부 정보는 각 서비스에 따라 다릅니다.

## <a name="azure-fluid-relay-service"></a>Azure Fluid Relay 서비스

만든 각 Azure Fluid Relay 서비스 테넌트마다 **테넌트 ID와** 고유한 **테넌트 비밀 키** 가 할당됩니다.

비밀 키는 **공유 비밀** 입니다. 앱/서비스는 이를 알고 있으며, Azure Fluid Relay 서비스는 이를 알고 있습니다. 테넌트 비밀 키는 테넌트와 고유하게 연결되어 있기 때문에 테넌트를 사용하여 요청에 서명하면 요청이 테넌트 권한 있는 사용자로부터 오는 것을 Azure Fluid Relay 서비스에 보장합니다.

비밀 키는 Azure Fluid Relay 서비스가 앱 또는 서비스에서 요청이 들어오는 것을 알고 있는 방법입니다. 이는 Azure Fluid Relay 서비스가 요청을 만드는 *앱임을* 신뢰할 수 있으면 보내는 데이터를 신뢰할 수 있기 때문에 매우 중요합니다. 비밀이 안전하게 처리되는 것이 중요한 이유이기도 합니다.

> [!CAUTION]
> 비밀에 액세스할 수 있는 모든 사용자는 Azure Fluid Relay 서비스와 통신할 때 애플리케이션을 가장할 수 있습니다.

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON 웹 토큰 및 Azure Fluid Relay 서비스

Azure Fluid Relay는 [JWT(JSON 웹 토큰)를](https://jwt.io/) 사용하여 비밀 키로 서명된 데이터를 인코딩하고 확인합니다. JSON 웹 토큰은 권한 및 권한에 대한 추가 정보를 포함할 수 있는 서명된 JSON 비트입니다.

> [!NOTE]
> JWT의 세부 사항은 이 문서에서 다루지 않습니다. JWT 표준에 대한 자세한 내용은 <https://jwt.io/introduction> 를 참조하세요.

인증 세부 정보는 Fluid 서비스 간에 다르지만 여러 값이 항상 있어야 합니다.

- **containerId**  Fluid 서비스는 호출 컨테이너에 해당하는 서비스를 식별하기 위해 컨테이너 ID가 필요합니다. *참고:* JWT는 이 필드 documentId를 호출하지만, Fluid 서비스는 이 필드에 컨테이너 ID를 예상합니다.
- **tenantId:** Azure Fluid Relay 서비스는 테넌트 ID를 사용하여 요청을 인증하는 데 사용할 공유 비밀을 검색합니다. 
- **scopes:** 범위는 호출 컨테이너의 권한을 정의합니다. 범위 필드의 콘텐츠는 유연하여 사용자 고유의 사용자 지정 권한을 만들 수 있습니다.

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

사용자의 모드는 연결이 읽기 또는 읽기/쓰기 모드인지 여부를 나타냅니다. 의 필드에서 볼 수 `connections` `AzureAudience` 있습니다. 서버리스 Azure Function의 함수에서 토큰 범위 권한을 업데이트할 수 `generateToken` 있습니다.

```ts
const token = generateToken(
  tenantId,
  documentId,
  key,
  scopes ?? [ "Token Scope" ],
  user
);
```

컨테이너 동작 및 모드와 함께 토큰 범위는 다음과 같습니다.

| 토큰 범위 | 내 문서 동작 | 대상 문서 동작 | 
|-------------|----------------------|----------------------------|
| DocRead     | 문서를 읽고 씁니다. 문서의 변경 내용은 다른 대상 문서에 반영되지 않습니다. <br /> 모드: 읽기 | 문서를 읽고 씁니다. 변경 내용은 다른 대상 문서에 반영되지 않습니다. <br /> 모드: 쓰기 | 
| DocWrite    | 문서를 읽고 씁니다. 변경 내용은 다른 모든 대상 문서에 반영됩니다. <br />모드: 쓰기 | 문서를 읽고 씁니다. 변경 내용은 다른 모든 대상 문서에 반영됩니다. <br />모드: 쓰기 |
| DocRead, DocWrite | 문서를 읽고 씁니다. 변경 내용은 다른 모든 대상 문서에 반영됩니다. <br />모드: 쓰기 | 문서를 읽고 씁니다. 변경 내용은 다른 모든 대상 문서에 반영됩니다. <br />모드: 쓰기 |

> [!NOTE]
> 토큰에는 사용자 정보도 포함됩니다(위의 7-9줄 참조). 이 기능을 사용하여 [대상](../how-tos/connect-fluid-azure-service.md#getting-audience-details) 기능을 사용하여 Fluid 코드에서 자동으로 사용할 수 있는 사용자 정보를 보강할 수 있습니다. 자세한 내용은 [토큰에 사용자 지정 데이터 추가를](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens) 참조하세요.

## <a name="the-token-provider"></a>토큰 공급자

Azure Fluid Relay에 대한 모든 요청은 유효한 JWT로 서명해야 합니다. 유체는 이러한 토큰을 만들고 토큰 공급자에게 서명하는 책임을 위임합니다.

토큰 공급자는 에서 Azure Fluid Relay 서비스에 대한 요청을 만드는 데 사용하는 토큰을 만들고 서명하는 것을 `@fluidframework/azure-client` 담당합니다. 사용자 고유의 보안 토큰 공급자 구현을 제공해야 합니다. 그러나 Fluid는 `InsecureTokenProvider` 테넌트 비밀을 수락한 다음, 로컬에서 서명된 토큰을 생성하고 반환하는 를 제공합니다. 이 토큰 공급자는 테스트에 유용하지만 프로덕션 시나리오에서는 사용할 수 없습니다.

### <a name="a-secure-serverless-token-provider"></a>보안 서버리스 토큰 공급자

보안 토큰 공급자를 빌드하는 한 가지 옵션은 서버리스 Azure Function을 만들어 토큰 공급자로 노출하는 것입니다. 이렇게 하면 보안 서버에 *테넌트 비밀 키를* 저장할 수 있습니다. 그러면 애플리케이션에서 Azure Function을 호출하여 토큰을 로컬로 서명하는 대신 `InsecureTokenProvider` 생성합니다. 자세한 내용은 [방법: Azure 함수를 사용하여 TokenProvider 작성을 참조하세요.](../how-tos/azure-function-token-provider.md)

## <a name="connecting-user-auth-to-fluid-service-auth"></a>사용자 인증을 유체 서비스 인증에 연결

유체 서비스는 특정 사용자에 연결되지 않은 공유 클라이언트 비밀을 사용하여 들어오는 호출을 인증합니다. 사용자 인증은 Fluid 서비스의 세부 정보를 기반으로 추가할 수 있습니다. 

사용자 인증을 위한 간단한 옵션 중 하나는 단순히 [Azure 함수를](../../azure-functions/index.yml) 토큰 공급자로 사용하고 토큰을 가져오는 조건으로 사용자 인증을 적용하는 것입니다. 애플리케이션이 함수를 호출하려고 하면 인증 시스템으로 인증되지 않는 한 실패합니다. 예를 들어 azure AD(Azure Active Directory)를 사용하는 경우 Azure Function에 대한 Azure AD 애플리케이션을 만들고 조직의 인증 시스템에 연결합니다.

이 경우 사용자는 Azure AD를 사용하여 애플리케이션에 로그인하며, 이를 통해 Azure Function을 호출하는 데 사용할 토큰을 얻습니다. Azure Function 자체는 동일하게 동작하지만 이제 Azure AD를 사용하여 인증한 사람만 액세스할 수 있습니다.

이제 Azure Function이 유효한 토큰을 얻기 위한 진입점이므로 함수에 제대로 인증된 사용자만 해당 토큰을 클라이언트 애플리케이션에서 Azure Fluid Relay 서비스에 제공할 수 있습니다. 이 2단계 접근 방식을 사용하면 Azure Fluid Relay 서비스와 함께 사용자 고유의 사용자 지정 인증 프로세스를 사용할 수 있습니다.

## <a name="see-also"></a>추가 정보

- [JWT 소개](https://jwt.io/introduction)
- [JSON Web Token 만드는 방법](../how-tos/fluid-json-web-token.md)
- [Azure Fluid Relay의 페이로드 클레임](../how-tos/fluid-json-web-token.md#payload-claims)
