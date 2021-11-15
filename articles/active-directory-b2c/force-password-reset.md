---
title: Azure AD B2C에서 암호 강제 재설정 흐름 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 암호 강제 재설정 흐름을 설정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c5802d9cd1fc9a7a41d38bc70e9d384882a1ea1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424328"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 강제 재설정 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>개요

관리자는 사용자가 암호를 잊어버린 경우 [사용자의 암호를 재설정](manage-users-portal.md#reset-a-users-password)할 수 있습니다. 또는 암호를 강제로 재설정하도록 할 수 있습니다. 이 문서에서는 이러한 시나리오에서 암호를 강제로 재설정하는 방법을 알아봅니다.

관리자가 Azure Portal을 통해 사용자의 암호를 재설정하면 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이 `true`로 설정됩니다. [로그인 및 등록](add-sign-up-and-sign-in-policy.md) 과정에서 이 특성의 값을 확인합니다. 특성이 `true`로 설정된 경우 사용자는 로그인을 완료한 후 암호를 재설정해야 합니다. 그런 다음, 특성의 값이 다시 `false`로 설정됩니다.

![암호 강제 재설정 흐름](./media/force-password-reset/force-password-reset-flow.png)

암호 재설정 흐름은 로그인을 위해 암호와 함께 [이메일 주소](sign-in-options.md#email-sign-in) 또는 [사용자 이름](sign-in-options.md#username-sign-in)을 사용하는 Azure AD B2C의 로컬 계정에 적용됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

이 기능은 현재 사용자 흐름에만 사용할 수 있습니다. 설정 단계에서 위의 **사용자 흐름** 을 선택합니다. 사용자 지정 정책의 경우 아래 필수 구성 요소와 함께 첫 번째 로그온 시 암호를 강제 재설정하는 [GitHub 샘플](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset-first-logon)을 사용합니다.

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-your-user-flow"></a>사용자 흐름 구성

등록 또는 로그인 사용자 흐름에서 **Forced password reset(암호 강제 재설정)** 설정을 사용하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정하려는 등록 및 로그인 또는 로그인 사용자 흐름(**권장** 형식)을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **속성** 을 선택합니다.
1. **암호 구성** 에서 **강제 암호 재설정** 을 선택합니다.
1. **저장** 을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 사용자 관리자 또는 암호 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 사용 가능한 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/roles/permissions-reference.md#all-roles)을 참조하세요.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자** 를 선택합니다. 암호 재설정을 테스트하는 데 사용할 사용자를 검색하여 선택하고 **암호 재설정** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 테스트하려는 등록 또는 로그인 사용자 흐름(**권장** 형식)을 선택합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 암호를 재설정한 사용자 계정으로 로그인합니다.
1. 이제 사용자의 암호를 변경해야 합니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="force-password-reset-on-next-login"></a>다음 로그인에서 암호 강제 재설정

다음에 로그인할 때 암호를 강제 재설정하려면 MS Graph [사용자 업데이트](/graph/api/user-update) 작업을 사용하여 계정 암호 프로필을 업데이트합니다. 다음 예는 암호 프로필 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성을 `true`로 업데이트하여 사용자가 다음에 로그인할 때 암호를 강제 재설정하도록 합니다.

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
"passwordProfile": {
  "forceChangePasswordNextSignIn": true
}
```

계정 암호 프로필이 설정되면 이 문서에 설명된 대로 암호 강제 재설정 흐름도 구성해야 합니다.

## <a name="force-a-password-reset-after-90-days"></a>90일 후 암호 강제 재설정

관리자는 [MS Graph](microsoft-graph-operations.md)를 사용하여 사용자의 암호 만료를 90일로 설정할 수 있습니다. 90일 후 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이 `true`로 자동 설정됩니다. 90일 후에 암호를 강제로 재설정하려면 사용자의 프로필 [암호 정책](user-profile-attributes.md#password-policy-attribute) 특성에서 `DisablePasswordExpiration` 값을 제거합니다.

다음 예는 암호 정책을 `None`으로 업데이트하여 90일 후에 암호를 강제 재설정합니다.

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "None"
}
```

강력한 [암호 복잡성](password-complexity.md)을 사용하지 않는 경우 암호 정책을 [DisableStrongPassword](user-profile-attributes.md#password-policy-attribute)로 업데이트합니다.

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "DisableStrongPassword"
}
```

암호 만료 정책이 설정되면 이 문서에 설명된 대로 암호 강제 재설정 흐름도 구성해야 합니다.

### <a name="password-expiry-duration"></a>암호 만료 기간

암호 만료 기간 기본값은 **90** 일입니다. 이 값은 Windows PowerShell용 Azure Active Directory 모듈에서 [Set-MsolPasswordPolicy](/powershell/module/msonline/set-msolpasswordpolicy) cmdlet을 사용하여 구성할 수 있습니다. 이 명령은 테넌트를 업데이트하여 구성한 일 수 후에 모든 사용자의 암호가 만료되도록 합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[셀프 서비스 암호 재설정](add-password-reset-policy.md)을 지정합니다.
