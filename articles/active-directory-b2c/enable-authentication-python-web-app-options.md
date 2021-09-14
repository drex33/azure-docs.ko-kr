---
title: Azure Active Directory B2C를 통해 Python 웹 애플리케이션 옵션 사용
description: 이 문서에서는 Python 웹 애플리케이션 옵션을 사용하도록 설정하는 방법을 보여 줍니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 97f1cb1f13ab990a6590576ae93ba86a4c935300
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220360"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 통해 Python 웹앱의 인증 옵션 사용 

이 문서에서는 Python 웹 애플리케이션용 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용하도록 설정하고, 사용자 지정하고, 개선하는 방법을 설명합니다. 

시작하기 전에 [Azure AD B2C를 사용하여 샘플 Python 웹앱에서 인증을 구성](configure-authentication-sample-python-web-app.md)하는 방법을 숙지하는 것이 중요합니다.

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 사용자 지정 도메인 및 테넌트 ID를 사용하려면 다음을 수행합니다. 

1. [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따릅니다.
1. *app_config.py* 파일에서 `authority_template` 클래스 멤버를 사용자 정의 도메인으로 업데이트합니다.

다음 Python 코드는 변경 전의 앱 설정을 보여 줍니다.

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

다음 Python 코드는 변경 후의 앱 설정을 보여 줍니다.

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우, [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. `initiate_auth_code_flow` 메서드를 찾은 다음, ID 공급자 도메인 이름(예: *facebook.com*)과 함께 `login_hint` 매개 변수를 추가합니다.

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. `initiate_auth_code_flow` 메서드를 찾은 다음, 로그인 힌트와 함께 `domain_hint` 매개 변수를 추가합니다.

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Python용 MSAL 구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki)을 참조하세요.
