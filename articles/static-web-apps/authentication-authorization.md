---
title: Azure Static Web Apps에 대한 인증 및 권한 부여
description: 다른 권한 부여 공급자를 사용하여 정적 앱을 보호하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: cshoe
ms.openlocfilehash: e38cc40407f636f8bfd53a9196ecaf9c431d34db
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729827"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Azure Static Web Apps에 대한 인증 및 권한 부여

Azure Static Web Apps는 간소화된 인증 환경을 제공합니다. 기본적으로 사전 구성된 일련의 공급자에 액세스하거나 [사용자 지정 공급자 등록](./authentication-custom.md) 옵션을 사용할 수 있습니다.

- 모든 사용자는 사용 가능한 공급자를 사용하여 인증할 수 있습니다.
- 로그인하면 사용자는 기본적으로 `anonymous` 및 `authenticated` 역할에 속합니다.
- 승인된 사용자는 [staticwebapp.config.json 파일](./configuration.md)에 정의된 규칙에 따라 제한된 [경로](configuration.md#routes)에 액세스할 수 있습니다.
- 기본 제공 초대 시스템을 사용하여 사용자에게 사용자 지정 역할이 [할당됩니다.](#invitations)
- API 함수를 통해 로그인할 때 사용자에게 프로그래밍 방식으로 사용자 지정 역할을 할당할 수 있습니다.
- 모든 인증 공급자는 기본적으로 사용하도록 설정됩니다.
  - 인증 공급자를 제한하려면 사용자 지정 경로 규칙을 사용하여 [액세스를 차단](#block-an-authorization-provider)합니다.
- 미리 구성된 공급자는 다음과 같습니다.
  - Azure Active Directory
  - GitHub
  - Twitter

인증 및 권한 부여의 주제는 [애플리케이션 구성 지침](configuration.md#routes)에 자세히 설명된 라우팅 개념과 상당히 겹칩니다.

## <a name="roles"></a>역할

정적 웹앱에 액세스하는 모든 사용자는 하나 이상의 역할에 속해 있습니다. 사용자가 속할 수 있는 두 가지 기본 제공 역할은 다음과 같습니다.

- **익명**: 모든 사용자는 _익명_ 역할에 자동으로 속합니다.
- **인증됨**: 로그인된 모든 사용자는 _인증됨_ 역할에 속합니다.

기본 제공 역할 외에도 사용자 지정 역할을 사용자에게 할당하고 _staticwebapp.config.json_ 파일에서 참조할 수 있습니다.

## <a name="role-management"></a>역할 관리

# <a name="invitations"></a>[초대](#tab/invitations)

### <a name="add-a-user-to-a-role"></a>역할에 사용자 추가

사용자를 역할에 추가하려면 특정 역할에 사용자를 연결하는 데 사용할 수 있는 초대를 생성합니다. 역할은 _staticwebapp.config.json_ 파일에서 정의되고 유지됩니다.

> [!NOTE]
> 그룹 관리를 위한 초대를 발급하지 않으려면 [사용자 지정 Azure Active Directory 공급자를 등록](./authentication-custom.md)하도록 선택할 수 있습니다.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>초대 만들기

초대는 개별 인증 공급자와 관련이 있으므로 지원할 공급자를 선택할 때 앱의 요구 사항을 고려해야 합니다. 일부 공급자는 사용자의 이메일 주소를 노출하지만 다른 공급자는 사이트의 사용자 이름만 제공합니다.

<a name="provider-user-details" id="provider-user-details"></a>

| 권한 부여 공급자 | 사용자의 다음 사항 노출 |
| ---------------------- | ---------------- |
| Azure Active Directory | 이메일 주소    |
| GitHub                 | 사용자 이름         |
| Twitter                | 사용자 이름         |

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리** 를 클릭합니다.
1. **초대** 단추를 클릭합니다.
1. 옵션 목록에서 _권한 부여 공급자_ 를 선택합니다.
1. _초대 대상자 정보_ 상자에 받는 사람의 사용자 이름 또는 이메일 주소를 추가합니다.
   - GitHub 및 Twitter의 경우 사용자 이름을 입력합니다. 다른 모든 경우에는 받는 사람의 이메일 주소를 입력합니다.
1. _도메인_ 드롭다운에서 정적 사이트의 도메인을 선택합니다.
   - 선택한 도메인은 초대에 표시되는 도메인입니다. 사이트와 연결된 사용자 지정 도메인이 있는 경우 사용자 지정 도메인을 선택하는 것이 좋습니다.
1. _역할_ 상자에 쉼표로 구분된 역할 이름 목록을 추가합니다.
1. 초대가 유효한 상태로 유지되도록 할 최대 시간을 입력합니다.
   - 가능한 최대 제한은 168시간(7일)입니다.
1. **생성** 단추를 클릭합니다.
1. _초대 링크_ 상자에서 링크를 복사합니다.
1. 앱에 대한 액세스를 허용하는 사람에게 초대 링크를 이메일로 보냅니다.

사용자가 초대의 링크를 클릭하면 해당 계정으로 로그인하라는 메시지가 표시됩니다. 성공적으로 로그인하면 사용자는 선택한 역할과 연결됩니다.

> [!CAUTION]
> 경로 규칙이 선택한 인증 공급자와 충돌하지 않는지 확인합니다. 경로 규칙을 사용하여 공급자를 차단하면 사용자가 초대를 수락하지 못합니다.

### <a name="update-role-assignments"></a>역할 할당 업데이트

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리** 를 클릭합니다.
1. 목록에서 사용자를 클릭합니다.
1. _역할_ 상자에서 역할 목록을 편집합니다.
1. **업데이트** 단추를 클릭합니다.

### <a name="remove-user"></a>사용자 제거

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리** 를 클릭합니다.
1. 목록에서 사용자를 찾습니다.
1. 사용자의 행에 대한 확인란을 선택합니다.
1. **삭제** 단추를 클릭합니다.

사용자를 제거하는 경우 다음 항목을 염두에 두어야 합니다.

1. 사용자를 제거하면 해당 권한이 무효화됩니다.
1. 전 세계 전파는 몇 분 정도 걸릴 수 있습니다.
1. 사용자를 앱에 다시 추가하면 [`userId`가 변경됩니다](user-information.md).

# <a name="function-preview"></a>[함수(미리 보기)](#tab/function)

기본 제공 초대 시스템을 사용하는 대신 서버리스 함수를 사용하여 사용자가 로그인할 때 프로그래밍 방식으로 역할을 할당할 수 있습니다.

함수에서 사용자 지정 역할을 할당하려면 사용자가 ID 공급자를 사용하여 성공적으로 인증할 때마다 자동으로 호출되는 API 함수를 정의할 수 있습니다. 함수는 공급자로부터 사용자 정보를 전달합니다. 사용자에게 할당된 사용자 지정 역할 목록을 반환해야 합니다.

이 함수의 예제 사용은 다음과 같습니다.

- 데이터베이스를 쿼리하여 사용자에게 할당해야 하는 역할 확인
- Microsoft [Graph API를](https://developer.microsoft.com/graph) 호출하여 Active Directory 그룹 멤버 자격에 따라 사용자의 역할을 결정합니다.
- ID 공급자가 반환한 클레임에 따라 사용자 역할 결정

> [!NOTE]
> 함수를 통해 역할을 할당하는 기능은 [사용자 지정 인증이](authentication-custom.md) 구성된 경우에만 사용할 수 있습니다.
>
> 이 기능을 사용하도록 설정하면 기본 제공 초대 시스템을 통해 할당된 모든 역할이 무시됩니다.

### <a name="configure-a-function-for-assigning-roles"></a>역할을 할당하기 위한 함수 구성

API 함수를 역할 할당 함수로 사용하도록 Static Web Apps 구성하려면 `rolesSource` `auth` 앱 구성 [파일](configuration.md)의 섹션에 속성을 추가합니다. `rolesSource`속성의 값은 API 함수의 경로입니다.

```json
{
  "auth": {
    "rolesSource": "/api/GetRoles",
    "identityProviders": {
      // ...
    }
  }
}
```

> [!NOTE]
> 구성된 후에는 외부 HTTP 요청에서 역할 할당 함수에 더 이상 액세스할 수 없습니다.

### <a name="create-a-function-for-assigning-roles"></a>역할을 할당하기 위한 함수 만들기

앱의 구성에서 속성을 정의한 후 `rolesSource` 지정한 경로에 정적 웹앱에 [API 함수를](apis.md) 추가합니다. 관리되는 함수 앱 또는 Bring Your Own 함수 앱을 사용할 수 있습니다.

사용자가 ID 공급자를 성공적으로 인증할 때마다 지정된 함수가 호출됩니다. 함수는 공급자의 사용자 정보를 포함하는 요청 본문에 JSON 개체를 전달합니다. 일부 ID 공급자의 경우 사용자 정보에는 `accessToken` 함수가 사용자의 ID를 사용하여 API 호출을 만드는 데 사용할 수 있는 도 포함됩니다.

다음은 Azure Active Directory 예제 페이로드입니다.

```json
{
  "identityProvider": "aad",
  "userId": "72137ad3-ae00-42b5-8d54-aacb38576d76",
  "userDetails": "ellen@contoso.com",
  "claims": [
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname",
          "val": "Contoso"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname",
          "val": "Ellen"
      },
      {
          "typ": "name",
          "val": "Ellen Contoso"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/objectidentifier",
          "val": "7da753ff-1c8e-4b5e-affe-d89e5a57fe2f"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
          "val": "72137ad3-ae00-42b5-8d54-aacb38576d76"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/tenantid",
          "val": "3856f5f5-4bae-464a-9044-b72dc2dcde26"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "ver",
          "val": "1.0"
      }
  ],
  "accessToken": "eyJ0eXAiOiJKV..."
}
```

함수는 사용자의 정보를 사용하여 사용자에게 할당할 역할을 결정할 수 있습니다. 사용자에게 할당할 사용자 지정 역할 이름 목록을 포함하는 JSON 본문이 포함된 HTTP 200 응답을 반환해야 합니다.

예를 들어 사용자를 및 역할에 할당하려면 `Reader` `Contributor` 다음 응답을 반환합니다.

```json
{
  "roles": [
    "Reader",
    "Contributor"
  ]
}
```

사용자에게 추가 역할을 할당하지 않으려면 빈 `roles` 배열을 반환합니다.

자세한 내용은 [자습서: 함수를 사용하여 사용자 지정 역할 할당 및 Microsoft Graph](assign-roles-microsoft-graph.md)를 참조하세요.

---

## <a name="remove-personal-identifying-information"></a>개인 식별 정보 제거

최종 사용자로 애플리케이션에 동의하면 애플리케이션은 ID 공급자에 따라 이메일 주소 또는 사용자 이름에 액세스할 수 있습니다. 이 정보가 제공되면 애플리케이션의 소유자는 개인 식별 정보를 관리하는 방법을 결정합니다.

최종 사용자는 개별 웹앱의 관리자에게 문의하여 시스템에서 이 정보를 해지해야 합니다.

Azure Static Web Apps 플랫폼에서 개인 식별 정보를 제거하고, 플랫폼이 향후 요청에 대해 이 정보를 제공하지 않도록 하려면 URL을 사용하여 요청을 제출합니다.

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

플랫폼이 이후 요청에 대해 이 정보를 개별 앱에 제공하지 않도록 하려면 다음 URL에 요청을 제출합니다.

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Azure Active Directory를 사용하는 경우 `aad` 자리 표시자에 대한 값으로 `<AUTHENTICATION_PROVIDER_NAME>`을 사용합니다.

## <a name="system-folder"></a>시스템 폴더

Azure Static Web Apps는 `/.auth` system 폴더를 사용하여 권한 부여 관련 API에 대한 액세스를 제공합니다. `/.auth` 폴더 아래의 경로를 최종 사용자에게 직접 노출하는 대신, 친숙한 URL을 만들기 위해 [라우팅 규칙](configuration.md#routes)을 만드는 것이 좋습니다.

## <a name="login"></a>로그인

공급자별 경로를 찾으려면 다음 표를 사용합니다.

| 권한 부여 공급자 | 로그인 경로             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| GitHub                 | `/.auth/login/github`   |
| Twitter                | `/.auth/login/twitter`  |

예를 들어 GitHub를 사용하여 로그인하려면 다음 코드 조각과 같은 링크를 포함할 수 있습니다.

```html
<a href="/.auth/login/github">Login</a>
```

둘 이상의 공급자를 지원하도록 선택한 경우에는 웹 사이트의 각 공급자별 링크를 노출해야 합니다.

[경로 규칙](./configuration.md#routes)을 사용하여 기본 공급자를 _/login_ 과 같은 친숙한 경로에 매핑할 수 있습니다.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>사후 로그인 리디렉션

사용자가 로그인한 후 특정 페이지로 돌아가도록 하려면 `post_login_redirect_uri` 쿼리 문자열 매개 변수에 정규화된 URL을 제공합니다.

예를 들면 다음과 같습니다.

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Logout

`/.auth/logout` 경로는 웹 사이트에서 사용자를 로그아웃합니다. 다음 예제와 같이 사용자가 로그아웃할 수 있도록 사이트 탐색에 링크를 추가할 수 있습니다.

```html
<a href="/.auth/logout">Log out</a>
```

[경로 규칙](./configuration.md#routes)을 사용하여 _/logout_ 과 같은 친숙한 경로를 매핑할 수 있습니다.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>사후 로그아웃 리디렉션

사용자가 로그아웃한 후 특정 페이지로 돌아가도록 하려면 `post_logout_redirect_uri` 쿼리 문자열 매개 변수에 URL을 제공합니다.

## <a name="block-an-authorization-provider"></a>권한 부여 공급자 차단

앱에서 권한 부여 공급자 사용을 제한하는 것이 좋습니다. 예를 들어 앱이 [이메일 주소를 노출하는 공급자](#provider-user-details)에서만 표준화하려고 할 수 있습니다.

공급자를 차단하기 위해 [경로 규칙](configuration.md#routes)을 만들어 차단된 공급자별 경로에 대한 요청에 대해 404를 반환할 수 있습니다. 예를 들어 Twitter를 공급자로 제한하려면 다음 경로 규칙을 추가합니다.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": 404
}
```

## <a name="restrictions"></a>제한

일반적인 제한 사항은 [할당량 문서](quotas.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 인증 및 권한 부여 데이터 액세스](user-information.md)

<sup>1</sup> 보류 중인 외부 인증
