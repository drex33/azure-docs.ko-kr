---
title: SaaS 애플리케이션 등록 - Azure Marketplace
description: Azure Portal을 사용하여 SaaS 애플리케이션을 등록하고 Azure Active Directory 보안 토큰을 받는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 78a66070c6bcf03f4a279163106048d87fe27f23
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061385"
---
# <a name="register-a-saas-application"></a>SaaS 애플리케이션 등록

이 문서에서는 Microsoft [Azure Portal](https://portal.azure.com/)을 사용하여 SaaS 애플리케이션을 등록하는 방법 및 게시자의 액세스 토큰(Azure Active Directory 액세스 토큰)을 가져오는 방법을 설명합니다. 게시자는 이 토큰을 사용하여 SaaS 처리 API를 호출하여 SaaS 애플리케이션을 인증합니다.  처리 API는 Azure Active Directory(v1.0) 엔드포인트에 대한 권한 부여 흐름에 OAuth 2.0 클라이언트 자격 증명을 사용하여 서비스 간 액세스 토큰 요청을 수행합니다.

Azure Marketplace는 SaaS 서비스에서 최종 사용자에 대해 사용 하는 인증 방법에 대 한 제약 조건을 적용 하지 않습니다. 아래 흐름은 Azure Marketplace에서 SaaS 서비스를 인증하는 경우에만 필요합니다.

Azure AD에 대한 자세한 내용은 [인증이란?](../../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

## <a name="register-an-azure-ad-secured-app"></a>Azure AD 보안 앱 등록

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 애플리케이션에 대한 몇 가지 세부 정보를 Azure AD에 제공합니다. Azure Portal을 사용하여 새 애플리케이션을 등록하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 계정에서 둘 이상의 계정에 대 한 액세스를 제공 하는 경우 오른쪽 위 모서리에서 계정을 선택 합니다. 그런 다음 포털 세션을 원하는 Azure AD 테 넌 트로 설정 합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택 하 고 **앱 등록** 을 선택한 다음 **새 응용 프로그램 등록** 을 선택 합니다.

    ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1.png)

4. 만들기 페이지에서 애플리케이션의 등록\' 정보를 입력합니다.
    -   **이름**: 의미 있는 애플리케이션 이름을 입력합니다.
    -   **애플리케이션 형식**:  
        
        보안 서버에 설치된 [클라이언트 애플리케이션](../../active-directory/develop/developer-glossary.md#client-application) 및 [리소스/API 애플리케이션](../../active-directory/develop/developer-glossary.md#resource-server)의 경우 **웹앱/API** 를 선택합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](../../active-directory/develop/developer-glossary.md#web-client) 및 퍼블릭 [사용자 에이전트 기반 클라이언트](../../active-directory/develop/developer-glossary.md#user-agent-based-client)에 사용됩니다.
        동일한 애플리케이션이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.

        웹 애플리케이션의 구체적인 예제를 보려면 [Azure AD 개발자 가이드](../../active-directory/develop/index.yml)의 [시작하기](../../active-directory/develop/quickstart-create-new-tenant.md) 섹션에서 사용할 수 있는 빠른 시작 안내 설정을 확인하세요.

5. 작업을 마쳤으면 **등록** 을 선택합니다.  Azure AD는 새 애플리케이션에 고유한 *클라이언트 ID* 를 할당합니다. API에만 단일 테넌트로서 액세스하는 하나의 앱을 등록하는 것이 좋습니다.

6. 클라이언트 암호를 만들려면 **인증서 & 암호 페이지로** 이동 하 고 **+ 새 클라이언트 암호** 를 선택 합니다.  코드에서 사용하려면 암호 값을 복사해야 합니다.

**Azure AD 앱 ID** 는 게시자 ID와 연결되므로 모든 제품에서 동일한 *앱 ID* 를 사용해야 합니다.

>[!Note]
>게시자가 파트너 센터에 두 개 이상의 계정을 갖고 있는 경우 Azure AD 앱 등록 정보를 한 계정에만 사용할 수 있습니다. 동일한 테넌트 ID를 사용하는 경우 다른 게시자 계정에 속한 제품의 앱 ID 쌍을 사용할 수 없습니다.

## <a name="how-to-get-the-publishers-authorization-token"></a>게시자의 인증 토큰을 가져오는 방법

응용 프로그램을 등록 한 후에는 Azure AD V1 끝점을 사용 하 여 게시자의 권한 부여 토큰 (Azure AD 액세스 토큰)을 프로그래밍 방식으로 요청할 수 있습니다. 게시자는 다양한 SaaS 처리 API를 호출할 때 이 토큰을 사용해야 합니다. 이 토큰은 1시간 동안만 유효합니다.

이러한 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](../../active-directory/develop/access-tokens.md)을 참조하세요.  아래 흐름에서 V1 끝점 토큰이 사용 됩니다.

### <a name="get-the-token-with-an-http-post"></a>HTTP POST를 사용하여 토큰 가져오기

#### <a name="http-method"></a>HTTP 메서드

게시<br>

##### <a name="request-url"></a>*요청 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI 매개 변수*

|  매개 변수 이름    |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  등록된 AAD 애플리케이션의 테넌트 ID입니다. |

##### <a name="request-header"></a>*요청 헤더*

|  헤더 이름       |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다. |

##### <a name="request-body"></a>*요청 본문*

|  속성 이름     |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  권한 부여 유형입니다. `"client_credentials"`을 사용하세요. |
|  `client_id`       |  True      |  Azure AD 앱과 연결된 클라이언트/앱 식별자입니다. |
|  `client_secret`   |  True      |  Azure AD 앱과 연결된 암호입니다. |
|  `resource`        |  True      |  토큰이 요청된 대상 리소스입니다. 이 경우에는 Marketplace SAAS API가 항상 대상 리소스이므로 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`을 사용합니다. |

##### <a name="response"></a>*응답*

|  이름     |  Type         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 정상   |  TokenResponse    |  요청이 성공했습니다. |

##### <a name="tokenresponse"></a>*TokenResponse*

샘플 응답:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

| 요소 | Description |
| ------- | ----------- |
| `access_token` | 이 요소는 `<access_token>` 모든 SaaS 행 및 마켓플레이스 계량 api를 호출할 때 권한 부여 매개 변수로 전달 되는입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. |
| `expires_in` | 액세스 토큰의 발급 시간부터 만료 이전까지 계속 유효한 시간(초)입니다. 발급 시간은 토큰의 `iat` 클레임에서 확인할 수 있습니다. |
| `expires_on` | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함). |
| `not_before` | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함). |
| `resource` | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다. |
| `token_type` | 토큰의 형식은 "전달자" 액세스 토큰입니다. 즉, 리소스가 이 토큰의 전달자에 액세스 권한을 제공할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

이제 Azure AD 보안 앱에서 [saas 앱 구독 Api 버전 2](pc-saas-fulfillment-subscription-api.md) 및 [Saas 처리 작업 api 버전 2](pc-saas-fulfillment-operations-api.md)를 사용할 수 있습니다.
