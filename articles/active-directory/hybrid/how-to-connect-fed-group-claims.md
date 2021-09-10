---
title: Azure Active Directory를 사용하여 애플리케이션에 대한 그룹 클레임 구성 | Microsoft Docs
description: Azure AD에서 사용할 그룹 클레임을 구성하는 방법에 대한 정보입니다.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/31/2021
ms.author: billmath
author: billmath
ms.openlocfilehash: 0621cc58b5cf76505de5e1f914114ad601e7a9bf
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123316186"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 애플리케이션에 대한 그룹 클레임 구성

Azure Active Directory에서 애플리케이션 내에서 사용할 토큰에 사용자 그룹 멤버 자격 정보를 제공할 수 있습니다.  다음 두 가지 주요 패턴이 지원됩니다.

- Azure Active Directory OID(개체 식별자) 특성으로 식별된 그룹
- AD(Active Directory) 동기화 그룹 및 사용자에 대한 sAMAccountName 또는 GroupSID 특성으로 식별된 그룹

> [!IMPORTANT]
> 이 기능에는 다음과 같은 몇 가지 주의 사항이 있습니다.
>
> - 온-프레미스에서 동기화된 sAMAccountName 및 SID(보안 식별자) 특성 사용에 대한 지원은 AD FS 및 기타 ID 공급자에서 기존 애플리케이션을 이동할 수 있도록 설계되었습니다. Azure AD에서 관리되는 그룹에는 이러한 클레임을 내보내는 데 필요한 특성이 없습니다.
> - 대규모 조직에서 사용자가 속한 그룹 수가 Azure Active Directory에서 토큰에 추가하는 제한을 초과할 수 있습니다. SAML 토큰의 경우 150개 그룹이며 JWT의 경우 200개 그룹입니다. 이 경우 결과를 예측할 수 없습니다. 사용자에게 많은 그룹 멤버 자격이 있는 경우 클레임에서 내보내는 그룹을 애플리케이션에 대한 관련 그룹으로 제한하는 옵션을 사용하는 것이 좋습니다.
> - 암시적 흐름을 통해 토큰이 발급되는 경우 그룹 클레임은 5개 그룹 제한이 있습니다. 암시적 흐름을 통해 요청된 토큰은 사용자가 5개를 초과하는 그룹에 있는 경우에만 "hasgroups": true 클레임을 갖습니다.
> - 새 애플리케이션 개발의 경우 또는 애플리케이션 개발을 구성할 수 있는 경우 및 중첩된 그룹 지원이 필요하지 않은 경우 앱 내 권한 부여는 그룹이 아닌 애플리케이션 역할을 기반으로 하는 것이 좋습니다.  이렇게 하면 토큰으로 이동해야 하는 정보의 양을 제한하고 보안을 강화하며 앱 구성에서 사용자 할당을 구분할 수 있습니다.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS 및 기타 ID 공급자에서 마이그레이션하는 애플리케이션에 대한 그룹 클레임

AD FS를 사용하여 인증하도록 구성된 많은 애플리케이션은 Windows AD 그룹 특성 형식의 그룹 멤버 자격 정보를 사용합니다.   이러한 특성은 도메인 이름으로 정규화될 수 있는 그룹 sAMAccountName 또는 Windows GroupSID(그룹 보안 식별자)입니다.  애플리케이션이 AD FS로 페더레이션되면 AD FS는 TokenGroups 함수를 사용하여 사용자에 대한 그룹 멤버 자격을 검색합니다.

AD FS에서 이동된 앱에는 동일한 형식의 클레임이 필요합니다. 그룹 및 역할 클레임은 그룹의 Azure Active Directory objectID가 아닌 Active Directory에서 동기화된 GroupSID 또는 정규화된 도메인 sAMAccountName을 포함하는 Azure Active Directory에서 내보낼 수 있습니다.

지원되는 그룹 클레임 형식은 다음과 같습니다.

- **Azure Active Directory 그룹 ObjectId**(모든 그룹에서 사용 가능)
- **sAMAccountName**(Active Directory에서 동기화된 그룹에 사용 가능)
- **NetbiosDomain\sAMAccountName**(Active Directory에서 동기화된 그룹에 사용 가능)
- **DNSDomainName\sAMAccountName**(Active Directory에서 동기화된 그룹에 사용 가능)
- **온-프레미스 그룹 보안 식별자**(Active Directory에서 동기화된 그룹에 사용 가능)

> [!NOTE]
> sAMAccountName 및 온-프레미스 그룹 SID 특성은 Active Directory에서 동기화된 그룹 개체에서만 사용할 수 있습니다.   Azure Active Directory 또는 Office365에서 만든 그룹에서는 사용할 수 없습니다.   동기화된 온-프레미스 그룹 특성을 가져오도록 Azure Active Directory에 구성된 애플리케이션은 동기화된 그룹에 대해서만 가져옵니다.

## <a name="options-for-applications-to-consume-group-information"></a>그룹 정보를 사용하는 애플리케이션에 대한 옵션

애플리케이션에서 MS Graph 그룹 엔드포인트를 호출하여 인증된 사용자에 대한 그룹 정보를 가져올 수 있습니다. 이 호출을 통해 많은 수의 그룹이 포함된 경우에도 사용자가 속한 모든 그룹을 사용할 수 있습니다.  그런 경우 그룹 열거는 토큰 크기 제한과 관계가 없습니다.

그러나 기존 애플리케이션에서 클레임을 통해 그룹 정보를 사용해야 하는 경우 다양한 클레임 형식으로 Azure Active Directory를 구성할 수 있습니다.  다음 옵션을 살펴보세요.

- 애플리케이션에서 권한 부여를 위해 그룹 멤버 자격을 사용하는 경우에는 Group ObjectID를 사용하는 것이 좋습니다. Group ObjectID는 Azure Active Directory에서 변경할 수 없고 고유하며 모든 그룹에서 사용할 수 있습니다.
- 권한 부여에 온-프레미스 그룹 sAMAccountName을 사용하는 경우 정규화된 도메인 이름을 사용합니다. 이름이 충돌할 가능성이 줄어듭니다. sAMAccountName은 Active Directory 도메인 내에서 고유할 수 있지만, 둘 이상의 Active Directory 도메인이 Azure Active Directory 테넌트와 동기화되는 경우 두 개 이상의 그룹이 동일한 이름을 가질 가능성이 있습니다.
- [애플리케이션 역할](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)을 사용하여 그룹 멤버 자격과 애플리케이션 간의 간접 참조 계층을 제공하는 것이 좋습니다.   그러면 애플리케이션은 토큰에서 역할 클레임에 따라 내부 권한 부여 결정을 내립니다.
- 애플리케이션이 Active Directory에서 동기화되는 그룹 특성을 가져오도록 구성되고 그룹에 해당 특성이 포함되지 않은 경우 클레임에 애플리케이션이 포함되지 않습니다.
- 토큰의 그룹 클레임에는 옵션을 사용하여 그룹 클레임을 애플리케이션에 할당된 그룹으로 제한하는 경우를 제외하고 중첩된 그룹이 포함됩니다.  사용자가 GroupB에 속해 있고 GroupB가 GroupA에 속한 경우 사용자에 대한 그룹 클레임에 GroupA와 GroupB가 모두 포함됩니다. 조직의 사용자에게 다수의 그룹 멤버 자격이 있는 경우 토큰에 나열된 그룹 수로 인해 토큰 크기가 늘어날 수 있습니다.  Azure Active Directory는 토큰에 내보낼 그룹의 수를 SAML 어설션의 경우 150개, JWT의 경우 200개로 제한합니다.  사용자가 더 많은 그룹에 속한 경우 그룹을 생략하고 그룹 정보를 얻을 Graph 엔드포인트에 대한 링크가 대신 포함됩니다.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Active Directory에서 동기화된 Group 특성을 사용하기 위한 필수 조건

ObjectId 형식을 사용하는 경우 그룹 구성원 클레임을 모든 그룹의 토큰으로 내보낼 수 있습니다. 그룹 ObjectId 이외의 형식으로 그룹 클레임을 사용하려면 Azure AD Connect를 사용하여 Active Directory에서 그룹을 동기화해야 합니다.

Active Directory 그룹에 대한 그룹 이름을 내보내도록 Azure Active Directory 구성하기는 다음 두 단계로 구성됩니다.

1. **Active Directory에서 그룹 이름 동기화** Azure Active Directory가 그룹 또는 역할 클레임에서 그룹 이름 또는 온-프레미스 그룹 SID를 내보내기 전에 필요한 특성을 Active Directory에서 동기화해야 합니다.  Azure AD Connect 버전 1.2.70 이상을 실행해야 합니다.   Azure AD Connect 1.2.70 이전 버전은 Active Directory에서 그룹 개체를 동기화하지만 필요한 그룹 이름 특성을 포함하지 않습니다.  현재 버전으로 업그레이드합니다.

2. **토큰에 그룹 클레임을 포함하도록 Azure Active Directory에서 애플리케이션 등록 구성** 그룹 클레임은 포털의 엔터프라이즈 애플리케이션 섹션에서 구성하거나 애플리케이션 등록 섹션의 애플리케이션 매니페스트를 사용하여 구성할 수 있습니다.  애플리케이션 매니페스트에서 그룹 클레임을 구성하려면 아래의 “그룹 특성에 대한 Azure Active Directory 애플리케이션 등록 구성”을 참조하세요.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>SSO 구성을 사용하여 SAML 애플리케이션에 대한 토큰에 그룹 클레임 추가

갤러리 또는 비갤러리 SAML 애플리케이션에 대한 그룹 클레임을 구성하려면 **엔터프라이즈 애플리케이션** 을 열고 목록에서 애플리케이션을 클릭한 다음 **Single Sign On 구성** 을 선택하고 **사용자 특성 및 클레임** 을 선택합니다.

**그룹 클레임 추가** 를 클릭합니다.

![“그룹 클레임 추가”가 선택된 “사용자 특성 및 클레임” 페이지를 보여주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

라디오 단추를 사용하여 토큰에 포함해야 하는 그룹을 선택합니다.

![“보안 그룹”이 선택된 “그룹 클레임” 창을 보여 주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 선택 영역 | 설명 |
|----------|-------------|
| **모든 그룹** | 보안 그룹과 배포 목록 및 역할을 내보냅니다.  |
| **보안 그룹** | 그룹 클레임에서 사용자가 구성원인 보안 그룹을 내보냅니다. |
| **디렉터리 역할** | 사용자에게 디렉터리 역할이 할당된 경우 디렉터리 역할을 ‘wids’ 클레임으로 내보냅니다(그룹 클레임은 내보내지 않음). |
| **애플리케이션에 할당된 그룹** | 애플리케이션에 명시적으로 할당되고 사용자가 속한 그룹만 내보냅니다. |

예를 들어 사용자가 속한 모든 보안 그룹을 내보내려면 보안 그룹을 선택합니다.

![“보안 그룹”이 선택되어 있고 “원본 특성” 드롭다운 메뉴가 열려 있는 “그룹 클레임” 창을 보여 주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD objectIDs 대신 Active Directory에서 동기화된 Active Directory 특성을 사용하여 그룹을 내보내려면 드롭다운에서 필요한 형식을 선택합니다. Active Directory에서 동기화된 그룹만 클레임에 포함됩니다.

![열려 있는 “원본 특성” 드롭다운 메뉴를 표시한 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

애플리케이션에 할당된 그룹만 내보내려면 **애플리케이션에 할당된 그룹** 을 선택합니다.

![“애플리케이션에 할당된 그룹”이 선택된 “그룹 클레임” 창을 보여 주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

애플리케이션에 할당된 그룹이 토큰에 포함됩니다.  사용자가 속한 다른 그룹은 생략됩니다.  이 옵션을 선택하면 중첩된 그룹이 포함되지 않으며 사용자는 애플리케이션에 할당된 그룹의 직접 구성원이어야 합니다.

애플리케이션에 할당된 그룹을 변경하려면 **엔터프라이즈 애플리케이션** 목록에서 애플리케이션을 선택하고 애플리케이션의 왼쪽 탐색 메뉴에서 **사용자 및 그룹** 을 클릭합니다.

애플리케이션에 그룹 할당을 관리하는 방법에 대한 자세한 내용은 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) 문서를 참조하세요.

### <a name="advanced-options"></a>고급 옵션

그룹 클레임을 내보내는 방법은 고급 옵션 아래의 설정에서 수정할 수 있습니다.

그룹 클레임 이름 사용자 지정: 선택하는 경우 그룹 클레임에 대해 다른 클레임 유형을 지정할 수 있습니다.   이름 필드에 클레임 유형을 입력하고 네임스페이스 필드에 클레임에 대한 선택적 네임스페이스를 입력합니다.

![“그룹 클레임 이름 사용자 지정”을 선택하고 “이름” 및 “네임스페이스” 값을 입력한 “고급 옵션” 섹션을 보여 주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

일부 애플리케이션에는 ‘역할’ 클레임에 표시할 그룹 멤버 자격 정보가 필요합니다. 필요에 따라 ‘그룹을 역할 클레임으로 내보내기’ 상자를 선택하여 사용자 그룹을 역할로 내보낼 수 있습니다.

![“그룹 클레임 이름 사용자 지정”및 “그룹을 역할 클레임으로 내보내기”가 선택된 “고급 옵션” 섹션을 보여 주는 스크린샷.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 그룹 데이터를 역할로 내보내는 옵션을 사용하면 그룹만 역할 클레임에 표시됩니다.  사용자가 할당되어 있는 모든 애플리케이션 역할은 역할 클레임에 표시되지 않습니다.

### <a name="edit-the-group-claims-configuration"></a>그룹 클레임 구성 편집

그룹 클레임 구성이 사용자 특성 및 클레임 구성에 추가되면 그룹 클레임을 추가하는 옵션이 회색으로 표시됩니다. 그룹 클레임 구성을 변경하려면 **추가 클레임** 목록에서 그룹 클레임을 클릭합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>그룹 특성에 대한 Azure AD 애플리케이션 등록 구성

그룹 클레임은 [애플리케이션 매니페스트](../../active-directory/develop/reference-app-manifest.md)의 [선택적 클레임](../../active-directory/develop/active-directory-optional-claims.md) 섹션에서 구성할 수도 있습니다.

1. 포털 -> Azure Active Directory -> 애플리케이션 등록 -> 애플리케이션 선택 -> 매니페스트를 선택합니다.

2. groupMembershipClaim을 변경하여 그룹 멤버 자격 클레임을 사용하도록 설정합니다.

유효한 값은 다음과 같습니다.

| 선택 영역 | 설명 |
|----------|-------------|
| **“All”** | 보안 그룹, 배포 목록, 역할을 내보냅니다. |
| **"SecurityGroup"** | 그룹 클레임에서 사용자가 구성원인 보안 그룹을 내보냅니다. |
| **"DirectoryRole"** | 사용자에게 디렉터리 역할이 할당된 경우 디렉터리 역할을 ‘wids’ 클레임으로 내보냅니다(그룹 클레임은 내보내지 않음). |
| **“ApplicationGroup”** | 애플리케이션에 명시적으로 할당되고 사용자가 속한 그룹만 내보냅니다. |
| **“None”** | 그룹이 반환되지 않습니다(대/소문자를 구분하지 않으므로 아무것도 작동하지 않으며 애플리케이션 매니페스트에서 직접 설정할 수 있습니다.). |

   예를 들면 다음과 같습니다.

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   기본적으로 그룹 ObjectID는 그룹 클레임 값으로 내보내집니다.  온-프레미스 그룹 특성을 포함하도록 클레임 값을 수정하거나 클레임 유형을 역할로 변경하려면 다음과 같이 OptionalClaims 구성을 사용합니다.

3. 그룹 이름 구성 선택적 클레임을 설정합니다.

   토큰의 그룹이 온-프레미스 AD 그룹 특성을 포함하도록 하려면 선택적 클레임 섹션에서 선택적 클레임을 적용해야 하는 토큰 유형을 지정합니다.  여러 토큰 유형이 나열될 수 있습니다.

   - OIDC ID 토큰에 대한 idToken
   - OAuth/OIDC 액세스 토큰에 대한 accessToken
   - SAML 토큰에 대한 Saml2Token

   > [!NOTE]
   > Saml2Token 형식은 SAML 1.1 및 SAML 2.0 형식 토큰에 모두 적용됩니다.

   관련된 각 토큰 유형에 대해 매니페스트에 OptionalClaims 섹션을 사용하도록 그룹 클레임을 수정합니다. OptionalClaims 스키마는 다음과 같습니다.

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 선택적 클레임 스키마 | 값 |
   |----------|-------------|
   | **name:** | "groups"여야 합니다. |
   | **source:** | 사용되지 않습니다. 생략하거나 Null을 지정합니다. |
   | **essential:** | 사용되지 않습니다. 생략하거나 false를 지정합니다. |
   | **additionalProperties:** | 추가 속성 목록입니다.  유효한 옵션은 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles"입니다. |

   AdditionalProperties에서는 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" 중 하나만 필요합니다.  두 가지 이상의 속성이 있으면 첫 번째 속성이 사용되고 나머지는 무시됩니다.

   일부 애플리케이션에는 역할 클레임에 사용자에 대한 그룹 정보가 필요합니다.  클레임 유형을 그룹 클레임에서 역할 클레임으로 변경하려면 추가 속성에 "emit_as_roles"를 추가합니다.  그룹 값은 역할 클레임에 내보내집니다.

   > [!NOTE]
   > "emit_as_roles"를 사용하는 경우 사용자가 할당되어 있는 모든 구성된 애플리케이션 역할이 역할 클레임에 표시되지 않습니다.

### <a name="examples"></a>예

그룹을 dnsDomainName\SAMAccountName 형식을 사용하여 OAuth 액세스 토큰의 그룹 이름으로 내보냅니다.

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
```

netbiosDomain\samAccountName 형식으로 반환될 그룹 이름을 SAML 및 OIDC ID 토큰의 역할 클레임으로 내보냅니다.

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
}
```

## <a name="next-steps"></a>다음 단계

- [그룹 및 그룹 클레임을 사용하여 ASP.NET Core 웹앱에 권한 부여 추가(코드 샘플)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [엔터프라이즈 앱에 사용자 또는 그룹 할당](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [역할 클레임 구성](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
