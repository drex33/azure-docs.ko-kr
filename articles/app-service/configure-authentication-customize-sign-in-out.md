---
title: 로그인 및 로그아웃 사용자 지정
description: App Service의 기본 제공 인증 및 권한 부여를 사용하는 동시에 로그인 및 로그아웃 동작을 사용자 지정합니다.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: bdb100afa821aa08fb831aac53b1eb80cdda043e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568144"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>Azure App Service 인증에서 로그인 및 로그아웃 사용자 지정

이 문서에서는 기본 제공되는 [App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 사용하면서 사용자 로그인과 로그아웃을 사용자 지정하는 방법을 보여 줍니다. 

## <a name="use-multiple-sign-in-providers"></a>다중 로그인 공급자 사용

포털 구성은 사용자에게 다중 로그인 공급자를 표시하는 턴키 방법을 제공하지 않습니다(예: Facebook 및 Twitter). 그러나 앱에 기능을 추가하기는 어렵지 않습니다. 단계는 다음과 같이 간략히 설명됩니다.

먼저 Azure Portal의 **인증/권한 부여** 페이지에서 사용하도록 설정하려는 각 ID 공급자를 구성합니다.

**요청이 인증되지 않은 경우 수행할 작업** 에서 **익명 요청 허용(작업 없음)** 을 선택합니다.

로그인 페이지, 탐색 모음 또는 앱의 다른 위치에서 사용하도록 설정한 각 공급자에 로그인 링크를 추가합니다(`/.auth/login/<provider>`). 예를 들면 다음과 같습니다.

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

사용자가 링크 중 하나를 클릭하면 사용자가 로그인할 수 있는 각 로그인 페이지가 열립니다.

사용자 사후 로그인을 사용자 지정 URL로 리디렉션하려면 `post_login_redirect_uri` 쿼리 문자열 매개 변수를 사용합니다(ID 공급자 구성의 리디렉션 URI와 혼동하지 않음). 예를 들어 로그인한 후에 사용자를 `/Home/Index`로 이동하려면 다음 HTML 코드를 사용합니다.

```html
<a href="/.auth/login/<provider>?post_login_redirect_uri=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>클라이언트 지시 로그인

클라이언트 지시 로그인에서는 사용자가 애플리케이션에서 공급자별 SDK를 사용하여 ID 공급자에 로그인합니다. 그런 다음, 애플리케이션 코드는 HTTP POST 요청을 사용하여 유효성 검사를 위해 App Service에 결과 인증 토큰을 제출합니다([인증 흐름](overview-authentication-authorization.md#authentication-flow) 참조). [Azure Mobile Apps SDK](https://github.com/Azure/azure-mobile-apps)에서는 이 로그인 흐름을 사용합니다. 이 유효성 검사 자체는 실제로 원하는 앱 리소스에 대한 액세스 권한을 부여하지 않지만, 유효성 검사가 성공하면 앱 리소스에 액세스하는 데 사용할 수 있는 세션 토큰이 제공됩니다.

공급자 토큰의 유효성을 검사하려면 먼저 원하는 공급자를 사용하여 App Service 앱을 구성해야 합니다. 런타임 시 공급자에서 인증 토큰을 검색한 후 유효성 검사를 위해 토큰을 `/.auth/login/<provider>`에 게시합니다. 예를 들면 다음과 같습니다.

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

토큰 형식은 공급자에 따라 약간 다릅니다. 자세한 내용은 다음 표를 참조하세요.

| 공급자 값 | 요청 본문에 필요 | 주석 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | `id_token`, `refresh_token`, `expires_in` 속성은 선택 사항입니다. |
| `microsoftaccount` | `{"access_token":"<access_token>"}` 또는 `{"authentication_token": "<token>"`| `authentication_token`이 `access_token`보다 우선합니다. `expires_in` 속성은 선택 사항입니다. <br/> Live 서비스에서 토큰을 요청하는 경우 항상 `wl.basic` 범위를 요청합니다. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` 속성은 선택 사항입니다. `authorization_code` 값을 제공하면 토큰 저장소에 액세스 토큰과 새로 고침 토큰이 추가됩니다. 지정하면 `authorization_code`에 선택적으로 `redirect_uri` 속성이 포함될 수도 있습니다. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook에서 유효한 [사용자 액세스 토큰](https://developers.facebook.com/docs/facebook-login/access-tokens)을 사용합니다. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

공급자 토큰의 유효성 검사가 성공적으로 수행되면 API는 세션 토큰인 응답 본문에 `authenticationToken`을 반환합니다. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

이 세션 토큰이 있으면 `X-ZUMO-AUTH` 헤더를 HTTP 요청에 추가하여 보호된 앱 리소스에 액세스할 수 있습니다. 예를 들면 다음과 같습니다. 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>세션에서 로그아웃

사용자는 앱의 `/.auth/logout` 엔드포인트에 `GET` 요청을 전송하여 로그아웃을 시작할 수 있습니다. `GET` 요청은 다음을 수행합니다.

- 현재 세션에서 인증 쿠키를 지웁니다.
- 토큰 저장소에서 현재 사용자의 토큰을 삭제합니다.
- Azure Active Directory 및 Google의 경우 ID 공급자에서 서버 쪽 로그아웃을 수행합니다.

웹 페이지의 단순 로그아웃 링크는 다음과 같습니다.

```html
<a href="/.auth/logout">Sign out</a>
```

기본적으로 성공적인 로그아웃은 클라이언트를 `/.auth/logout/done` URL로 리디렉션합니다. `post_logout_redirect_uri` 쿼리 매개 변수를 추가하여 로그아웃 후 리디렉션 페이지를 변경할 수 있습니다. 예를 들면 다음과 같습니다.

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri`의 값을 [인코딩](https://wikipedia.org/wiki/Percent-encoding)하는 것이 좋습니다.

정규화된 URL을 사용하는 경우 URL은 동일한 도메인에 호스팅되거나 앱에 대한 허용되는 외부 리디렉션 URL로 구성되어야 합니다. 다음 예제에서 동일한 도메인에서 호스팅되지 않는 `https://myexternalurl.com`으로 리디렉션하려면:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL 조각 유지

사용자가 앱에 로그인한 후 일반적으로 동일한 페이지의 같은 섹션으로 리디렉션되기를 원합니다(예 `/wiki/Main_Page#SectionZ`). 그러나 [URL 조각](https://wikipedia.org/wiki/Fragment_identifier)(예: `#SectionZ`)은 서버에 전송되지 않으므로 OAuth 로그인이 완료되고 앱으로 다시 리디렉션된 후 기본적으로 유지되지 않습니다. 그런 다음, 사용자는 원하는 앵커로 다시 이동해야 할 때 최적이 아닌 환경을 가져옵니다. 이 제한은 모든 서버 쪽 인증 솔루션에 적용됩니다.

App Service 인증에서 OAuth 로그인에 대해 URL 조각을 유지할 수 있습니다. 이렇게 하려면 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT`라는 앱 설정을 `true`로 설정해야 합니다. [Azure Portal](https://portal.azure.com)에서 수행하거나 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 단순히 다음 명령을 실행할 수 있습니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="limit-the-domain-of-sign-in-accounts"></a>로그인 계정의 도메인 제한

Microsoft 계정과 Azure Active Directory는 모두 여러 도메인에서 로그인할 수 있습니다. 예를 들어 Microsoft 계정은 _outlook.com_, _live.com_ 및 _hotmail.com_ 계정을 허용합니다. Azure AD는 로그인 계정을 위한 여러 사용자 지정 도메인을 허용합니다. 그러나 사용자의 브랜드 Azure AD 로그인 페이지(예: `contoso.com`)로 직접 사용자를 가속화하는 것이 좋습니다. 로그인 계정의 도메인 이름을 제안하려면 다음 단계를 수행합니다.

[https://resources.azure.com](https://resources.azure.com)에서 **구독** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **공급자** > **Microsoft.Web** > **사이트** > **_ \<app\_name> _** > **config** > **authsettings** 로 이동합니다. 

**편집** 을 클릭하고 다음 속성을 수정한 다음, **배치** 를 클릭합니다. _\<domain\_name>domain_ 을 원하는 도메인으로 바꿨는지 확인합니다.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

이 설정은 `domain_hint` 쿼리 문자열 매개 변수를 로그인 리디렉션 URL에 추가합니다. 

> [!IMPORTANT]
> 클라이언트는 리디렉션 URL을 받은 후 `domain_hint` 매개 변수를 제거하고 다른 도메인으로 로그인할 수 있습니다. 따라서 이 함수는 편리하지만, 보안 기능은 아닙니다.
>

## <a name="authorize-or-deny-users"></a>사용자 권한 부여 또는 사용자 거부

App Service는 가장 간단한 권한 부여 사례(인증되지 않은 요청 거부)를 처리하지만, 앱에는 특정 사용자 그룹만 액세스하도록 제한하는 것과 같은 보다 세분화된 권한 부여 동작이 필요할 수 있습니다. 특정 경우에는 로그인한 사용자에 대한 액세스를 허용하거나 거부하는 사용자 지정 애플리케이션 코드를 작성해야 합니다. 다른 경우에는 App Service 또는 ID 공급자가 코드를 변경하지 않고도 도움을 제공할 수 있습니다.

- [서버 수준](#server-level-windows-apps-only)
- [ID 공급자 수준](#identity-provider-level)
- [애플리케이션 수준](#application-level)

### <a name="server-level-windows-apps-only"></a>서버 수준(Windows 앱에만 해당)

모든 Windows 앱에서 *Web.config* 파일을 편집하여 IIS 웹 서버의 권한 부여 동작을 정의할 수 있습니다. Linux 앱은 IIS를 사용하지 않으며 *Web.config* 를 통해 구성할 수 없습니다.

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동

1. App Service 파일의 브라우저 탐색기에서 *site/wwwroot* 로 이동합니다. *Web.config* 가 없으면 **+**  >  **새 파일** 을 선택하여 만듭니다. 

1. *Web.config* 의 연필을 선택하여 편집합니다. 다음 구성 코드를 추가하고 **저장** 을 클릭합니다. *Web.config* 가 이미 있으면 `<authorization>` 요소와 포함된 모든 항목을 추가하면 됩니다. `<allow>` 요소에 허용하려는 계정을 추가합니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>ID 공급자 수준

ID 공급자는 특정 턴키 권한 부여를 제공할 수 있습니다. 예를 들면 다음과 같습니다.

- [Azure App Service](configure-authentication-provider-aad.md)의 경우 Azure AD에서 직접 [엔터프라이즈 수준의 액세스를 관리](../active-directory/manage-apps/what-is-access-management.md)할 수 있습니다. 지침은 [애플리케이션에 대한 사용자 액세스를 제거하는 방법 이해](../active-directory/manage-apps/methods-for-removing-user-access.md)을 참고하시기 바랍니다.
- [Google](configure-authentication-provider-google.md)의 경우 [조직](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)에 속한 Google API 프로젝트는 조직의 사용자에게만 액세스를 허용하도록 구성할 수 있습니다([Google의 **OAuth 2.0 설정** 지원 페이지](https://support.google.com/cloud/answer/6158849?hl=en)참조).

### <a name="application-level"></a>애플리케이션 수준

다른 수준 중 하나에서 필요한 권한 부여를 제공하지 않거나 플랫폼 또는 ID 공급자가 지원되지 않는 경우 [사용자 클레임](configure-authentication-user-identities.md)에 따라 사용자에게 권한을 부여하는 사용자 지정 코드를 작성해야 합니다.

## <a name="more-resources"></a>추가 리소스

- [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
- [인증을 위한 환경 변수 및 앱 설정](reference-app-settings.md#authentication--authorization)
