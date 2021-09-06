---
title: Microsoft ID 플랫폼 범위, 권한 및 동의
description: 범위, 권한 및 동의를 비롯한 Microsoft ID 플랫폼 엔드포인트의 권한 부여에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40, has-adal-ref
ms.openlocfilehash: fca6234742958f363d45c02780c2d01246ac58a9
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539119"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼의 권한 및 동의

Microsoft ID 플랫폼과 통합되는 애플리케이션은 사용자와 관리자가 데이터 액세스 방법을 제어할 수 있는 권한 부여 모델을 따릅니다. Microsoft ID 플랫폼에서 권한 부여 모델의 구현이 업데이트되었습니다. 앱이 Microsoft ID 플랫폼과 상호 작용하는 방식이 변경되었습니다. 이 문서에서는 범위, 사용 권한 및 동의를 포함하여 이 권한 부여 모델의 기본 개념에 대해 설명합니다.

## <a name="scopes-and-permissions"></a>범위 및 사용 권한

Microsoft ID 플랫폼은 [OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 프로토콜을 구현합니다. OAuth 2.0은 사용자 대신 타사 앱에서 웹에 호스트된 리소스에 액세스할 수 있도록 하는 방법입니다. Microsoft ID 플랫폼과 통합되는 웹 호스트팅 리소스에는 리소스 식별자 또는 *애플리케이션 ID URI* 가 있습니다. 

Microsoft 웹 호스팅 리소스의 몇 가지 예는 다음과 같습니다.

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 메일 API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Microsoft ID 플랫폼과 통합된 타사 리소스의 경우도 마찬가지입니다. 이러한 리소스는 해당 리소스의 기능을 더 작은 청크로 나누는 데 사용할 수 있는 사용 권한 집합을 정의할 수도 있습니다. 예를 들어 [Microsoft Graph](https://graph.microsoft.com)는 특히 다음 작업을 수행할 수 있는 사용 권한을 정의했습니다.

* 사용자의 일정 읽기
* 사용자의 일정에 쓰기
* 사용자로 메일 보내기

이러한 유형의 권한 정의로 인해 리소스는 자체 데이터 및 API 기능이 공개되는 방식을 세밀하게 제어할 수 있습니다. 타사 앱은 사용자 및 관리자에게 이러한 사용 권한을 요청할 수 있고, 요청을 받은 사용자 또는 관리자가 요청을 승인해야만 앱이 사용자 대신 데이터에 액세스하거나 작업을 수행할 수 있습니다. 

리소스 기능이 작은 권한 집합으로 분할되면 기능을 수행하는 데 필요한 권한만 요청하도록 타사 앱을 빌드할 수 있습니다. 사용자 및 관리자는 앱이 어떤 데이터에 액세스할 수 있는지 알 수 있습니다. 그리고 앱이 악의적인 의도를 가지고 작동하지 않는다고 확신할 수 있습니다. 개발자는 항상 최소 권한의 원칙을 준수하여 애플리케이션이 작동하는 데 필요한 권한만 요청해야 합니다.

OAuth 2.0에서는 이러한 유형의 권한 집합을 범위라고 합니다. 권한이라고 하는 경우도 있습니다. Microsoft ID 플랫폼에서 권한은 문자열 값으로 표시됩니다. 앱은 `scope` 쿼리 매개 변수에 사용 권한을 지정하여 필요한 권한을 요청합니다. ID 플랫폼은 리소스 기반 권한(각 권한은 리소스의 식별자 또는 애플리케이션 ID URI에 권한 값을 추가하여 표시됨)뿐만 아니라 잘 정의된 여러 [OpenID Connect 범위](#openid-connect-scopes)를 지원합니다. 예를 들어 권한 문자열 `https://graph.microsoft.com/Calendars.Read`는 Microsoft Graph에서 사용자 일정을 읽을 수 있는 권한을 요청하는 데 사용됩니다.

앱은 대부분 Microsoft ID 플랫폼 권한 부여 엔드포인트에 대한 요청에서 범위를 지정하여 이러한 사용 권한을 요청합니다. 그러나 일부 높은 수준의 권한은 관리자의 동의를 통해서만 부여할 수 있습니다. [관리자 동의 엔드포인트](#admin-restricted-permissions)를 사용하여 요청하거나 부여할 수 있습니다. 자세히 알아보려면 계속 읽어보세요.

Microsoft ID 플랫폼에 대한 권한 부여, 토큰 또는 동의 엔드포인트에 대한 요청에서 리소스 식별자가 범위 매개 변수에서 생략되는 경우 리소스를 Microsoft Graph로 간주됩니다. 예를 들어 `scope=User.Read`는 `https://graph.microsoft.com/User.Read`와 같습니다.

## <a name="permission-types"></a>사용 권한 유형

Microsoft ID 플랫폼은 *위임된 권한* 및 *애플리케이션 권한* 이라는 두 가지 권한을 지원합니다.

* **위임된 권한** 은 로그인한 사용자가 있는 앱에서 사용합니다. 이러한 앱의 경우 사용자 또는 관리자가 앱이 요청하는 권한에 동의합니다. 이 앱은 대상 리소스를 호출할 때 로그인한 사용자 역할을 할 수 있는 권한을 위임받습니다. 

    일부 위임된 권한은 비관리자가 동의할 수 있습니다. 하지만 일부 높은 수준의 권한은 [관리자 동의](#admin-restricted-permissions)가 필요합니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD(Azure Active Directory)의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

* **애플리케이션 권한** 은 로그인한 사용자 없이 실행되는 앱(예: 백그라운드 서비스 또는 디먼으로 실행되는 앱)에서 사용합니다. 애플리케이션 권한에는 [관리자만 동의할 수 있습니다](#requesting-consent-for-an-entire-tenant).

_유효 사용 권한_ 은 앱이 대상 리소스에 요청할 때 갖게 되는 권한입니다. 위임된 권한, 앱에 부여되는 애플리케이션 권한, 앱이 대상 리소스를 호출할 때 부여되는 유효 사용 권한의 차이를 이해하는 것이 중요합니다.

- 위임된 권한의 경우 앱의 _유효 사용 권한_ 은 (동의를 통해) 앱에 부여한 위임된 권한과 현재 로그인한 사용자가 가진 권한의 최소 권한 교집합입니다. 앱은 로그인한 사용자보다 더 많은 권한을 가질 수 없습니다. 

   조직 내에서 로그인한 사용자의 권한은 정책 또는 관리자 역할 하나 이상의 멤버 자격에 의해 결정될 수 있습니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

   예를 들어 앱에 _User.ReadWrite.All_ 위임된 권한이 부여되었다고 가정해 봅시다. 이 권한은 일반적으로 조직에 있는 모든 사용자의 프로필을 읽고 업데이트하는 앱 권한을 부여합니다. 로그인한 사용자가 전역 관리자인 경우 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있습니다. 하지만 로그인한 사용자에게 관리자 역할이 없으면 앱은 로그인한 사용자의 프로필만 업데이트할 수 있습니다. 조직 내 다른 사용자의 프로필은 업데이트할 수 없습니다. 앱이 대신 작업을 수행할 권한이 있는 사용자에게 해당 권한이 없기 때문입니다.

- 애플리케이션 권한의 경우 앱의 _유효 사용 권한_ 은 권한이 암시하는 권한의 전체 수준입니다. 예를 들어 _User.ReadWrite.All_ 애플리케이션 권한을 가진 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있습니다.

## <a name="openid-connect-scopes"></a>OpenID Connect 범위

OpenID Connect의 Microsoft ID 플랫폼 구현에는 Microsoft Graph에도 호스팅되는 잘 정의된 몇 가지 범위(`openid`, `email`, `profile`, `offline_access`)가 있습니다. `address` 및 `phone` OpenID Connect 범위는 지원되지 않습니다.

OpenID Connect 범위와 토큰을 요청하면 [UserInfo 엔드포인트](userinfo.md)를 호출하는 토큰을 받게 됩니다.

### <a name="openid"></a>openid

앱이 [OpenID Connect](active-directory-v2-protocols.md)를 사용하여 로그인하는 경우 `openid` 범위를 요청해야 합니다. `openid` 범위는 회사 계정 동의 페이지에 **사용자를 로그인** 권한으로 표시됩니다. 개인 Microsoft 계정 동의 페이지에서는 **Microsoft 계정을 사용하여 프로필 보기 및 앱과 서비스에 연결** 권한으로 표시됩니다. 

이 사용 권한을 사용하여 앱은 `sub` 클레임 형식으로 사용자에 대한 고유 식별자를 받을 수 있습니다. 이 권한은 앱에 UserInfo 엔드포인트에 대한 액세스 권한도 부여합니다. `openid` 범위는 Microsoft ID 플랫폼 토큰 엔드포인트에서 ID 토큰을 획득하는 데 사용할 수 있습니다. 앱은 이러한 토큰을 인증에 사용할 수 있습니다.

### <a name="email"></a>이메일

`email` 범위는 `openid` 범위 및 기타 범위와 함께 사용할 수 있습니다. 이는 앱이 `email` 클레임의 형식으로 사용자의 기본 전자 메일 주소에 액세스할 수 있도록 해줍니다. 

`email` 클레임은 이메일 주소가 사용자 계정과 연결된 경우에만 토큰에 포함되며, 항상 그런 것은 아닙니다. 앱에서 `email` 범위를 사용하는 경우 앱은 토큰에 `email` 클레임이 없는 경우를 처리할 수 있어야 합니다.

### <a name="profile"></a>profile

`profile` 범위는 `openid` 범위 및 기타 범위와 함께 사용할 수 있습니다. 이를 통해 앱은 사용자에 대한 많은 양의 정보에 액세스할 수 있습니다. 액세스할 수 있는 정보에는 사용자의 이름, 성, 기본 설정된 사용자 이름, 개체 ID 등이 포함됩니다. 

특정 사용자에 대한 `id_tokens` 매개 변수에서 사용할 수 있는 `profile` 클레임의 전체 목록은 [`id_tokens` 참조](id-tokens.md)를 확인하세요.

### <a name="offline_access"></a>offline_access

[`offline_access` 범위](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)를 사용하면 앱이 연장된 기간 동안 사용자 대신 리소스에 액세스할 수 있습니다. 동의 페이지에서 이 범위는 **액세스 권한을 부여한 데이터에 대한 액세스 유지** 권한으로 나타납니다. 

사용자가 `offline_access` 범위를 승인하면 앱은 Microsoft ID 플랫폼 토큰 엔드포인트에서 새로 고침 토큰을 받을 수 있습니다. 새로 고침 토큰은 장기적으로 존재합니다. 오래된 액세스 토큰이 만료되면 앱에서 새 액세스 토큰을 가져올 수 있습니다.

> [!NOTE]
> 이 권한은 현재 모든 동의 페이지에 표시되며 새로 고침 토큰을 제공하지 않는 흐름(예: [암시적 흐름](v2-oauth2-implicit-grant-flow.md))에 대해서도 마찬가지입니다. 이러한 설정은 클라이언트가 암시적 흐름 내에서 시작한 다음, 새로 고침 토큰이 필요한 코드 흐름으로 이동할 수 있는 시나리오를 해결합니다.

Microsoft ID 플랫폼(v2.0 엔드포인트에 대한 요청)에서 앱은 새로 고침 토큰을 받기 위해 `offline_access` 범위를 명시적으로 요청해야 합니다. 따라서 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)에서 권한 부여 코드를 교환하는 경우 `/token` 엔드포인트에서 액세스 토큰만 받게 됩니다. 

액세스 토큰은 짧은 시간 동안 유효합니다. 일반적으로 1시간 후에 만료됩니다. 이 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 권한 부여 코드를 가져와야 합니다. 이 리디렉션 중에 앱 형식에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수 있습니다.

새로 고침 토큰을 가져오고 사용하는 방법에 대한 자세한 내용은 [Microsoft ID 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)를 확인하세요.

## <a name="consent-types"></a>동의 유형

Microsoft ID 플랫폼의 애플리케이션이 필요한 리소스 또는 API에 액세스하려면 동의가 필요합니다. 앱이 성공하기 위해 알아야 할 수 있는 여러 종류의 동의가 있습니다. 권한을 정의하는 경우 사용자가 앱 또는 API에 액세스 권한을 얻는 방법도 이해해야 합니다.

### <a name="static-user-consent"></a>고정적 사용자 동의 

고정적 사용자 동의 시나리오에서 Azure Portal의 앱 구성에 필요한 모든 권한을 지정해야 합니다. 사용자(또는 해당하는 경우 관리자)가 이 앱에 대한 동의를 받지 못한 경우 Microsoft ID 플랫폼은 사용자에게 지금 동의를 제공하라는 메시지를 표시합니다.

또한 정적 사용 권한을 통해 관리자는 조직의 [모든 사용자를 대신하여 동의](#requesting-consent-for-an-entire-tenant)를 얻을 수 있습니다.

Azure Portal에 정의된 앱의 정적 권한은 코드를 멋지고 간단하게 유지했지만, 개발자에게 몇 가지 가능한 문제를 줄 수 있습니다.

- 사용자가 처음 로그인할 때 앞으로 앱에서 필요로 하게 될 모든 권한을 요청해야 합니다. 이로 인해 권한 목록이 매우 길어서 최종 사용자는 처음 로그인 시 앱의 액세스를 승인하지 않을 수 있습니다.

- 앱이 액세스할 수도 있는 모든 리소스를 미리 알아야 합니다. 리소스의 임의 개수에 액세스할 수 있는 앱을 만드는 것은 어렵습니다.

### <a name="incremental-and-dynamic-user-consent"></a>증분 및 동적 사용자 동의

Microsoft ID 플랫폼 엔드포인트를 사용하면 Azure Portal의 앱 등록 정보에 정의된 정적 권한을 무시하고, 대신에 권한을 점진적으로 요청할 수 있습니다.  최소한의 권한 집합을 미리 요구하고 고객이 추가 앱 기능을 사용함에 따라 더 많은 시간을 요청할 수 있습니다. 이렇게 하려면 애플리케이션 등록 정보에 미리 정의할 필요 없이, [액세스 토큰을 요청](#requesting-individual-user-consent)할 때 `scope` 매개 변수에 새 범위를 포함시켜서 언제든지 앱에 필요한 범위를 지정할 수 있습니다. 사용자가 요청에 추가된 새 범위에 아직 동의하지 않은 경우에는 새 권한에만 동의하라는 메시지가 표시됩니다. 증분 또는 동적 동의는 위임된 권한에만 적용되며 애플리케이션 권한에는 적용되지 않습니다.

`scope` 매개 변수를 통해 앱이 동적으로 권한을 요청할 수 있도록 허용하면 개발자가 사용자 환경을 완전히 제어할 수 있습니다. 초기 단계에 동의 환경을 배치하고 하나의 초기 권한 부여 요청으로 모든 권한을 요청할 수도 있습니다. 앱이 많은 권한을 요청하는 경우 시간의 경과에 따라 앱의 특정 기능을 사용하도록 시도하는 것처럼 점진적으로 사용자로부터 권한을 수집할 수 있습니다.

> [!IMPORTANT]
> 동적 동의는 편리할 수 있지만 권한에 대해 관리자 동의가 필요한데 관리자 동의 환경은 동의 시간에 해당 권한에 대해 인식하지 못한다는 큰 문제가 있습니다. 관리자 권한이 필요하거나 앱에 동적 동의가 사용되는 경우에는 Azure Portal에 관리자 동의가 필요한 권한의 하위 집합뿐만 아니라 모든 권한을 등록해야 합니다. 이렇게 하면 테넌트 관리자가 모든 사용자를 대신하여 동의할 수 있습니다.

### <a name="admin-consent"></a>관리자 동의

[관리자 동의](#using-the-admin-consent-endpoint)는 앱이 높은 권한의 특정 사용 권한에 액세스해야 하는 경우 필요합니다. 관리자 동의는 관리자가 몇몇 추가 제어를 포함해야 앱 또는 사용자가 조직의 높은 권한 데이터에 액세스하는 것을 인증합니다.

[조직을 대신하여 관리자 동의](#requesting-consent-for-an-entire-tenant)를 얻으려면 앱에 대해 등록된 정적 권한이 있어야 합니다. 전체 조직을 대신하여 동의를 제공할 관리자가 필요한 경우 앱 등폴 포털에서 앱에 대해 해당 권한을 설정합니다. 이렇게 하면 조직 관리자가 애플리케이션을 설정하는 데 필요한 주기가 줄어듭니다.

## <a name="requesting-individual-user-consent"></a>개별 사용자의 동의 요청

[OpenID Connect 또는 OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 요청에서 앱은 `scope` 쿼리 매개 변수를 사용하여 필요한 사용 권한을 요청할 수 있습니다. 예를 들어 사용자가 앱에 로그인하면 앱은 다음 예제와 같은 요청을 보냅니다. (읽기 쉽도록 줄 바꿈이 추가되었습니다.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 매개 변수는 앱이 요청하는 공백으로 구분된 위임된 권한 범위 목록입니다. 각 권한은 리소스 식별자(애플리케이션 ID URI)에 권한 값을 추가하여 표시됩니다. 요청 예제에서 앱에는 사용자의 일정을 읽고 사용자로 메일을 보낼 수 있는 권한이 필요합니다.

사용자가 자격 증명을 입력하면 Microsoft ID 플랫폼은 사용자 동의와 일치하는 레코드를 확인합니다. 사용자가 과거에 요청된 권한에 동의하지 않은 경우 관리자가 전체 조직을 대신하여 해당 권한에 동의하지 않으면 Microsoft ID 플랫폼은 요청된 권한을 부여해 달라고 사용자에게 요청합니다.

이때, `offline_access`("액세스 권한을 부여한 데이터에 대한 액세스 권한 유지") 권한 및 `user.read`("사용자를 로그인하고 프로필 읽기") 권한이 애플리케이션에 대한 초기 동의에 자동으로 포함됩니다.  이러한 권한은 일반적으로 적절한 앱 기능을 위해 필요합니다. `offline_access` 권한은 네이티브 앱 및 웹앱에 중요한 토큰을 새로 고칠 수 있는 권한을 앱에 부여합니다. `user.read` 권한은 `sub` 클레임에 대한 액세스를 제공합니다. 이를 통해 클라이언트나 앱은 시간 경과에 따라 사용자를 올바르게 식별하고 기본적인 사용자 정보에 액세스할 수 있습니다.

![회사 계정 동의를 보여 주는 예제 스크린샷](./media/v2-permissions-and-consent/work_account_consent.png)

권한 요청을 사용자가 승인하면 동의가 기록됩니다. 나중에 애플리케이션에 로그인할 때 사용자가 다시 동의할 필요가 없습니다.

## <a name="requesting-consent-for-an-entire-tenant"></a>전체 테넌트에 대한 동의 요청

조직에서 애플리케이션의 라이선스나 구독을 구매하면 조직 내 모든 구성원이 사용할 수 있도록 애플리케이션을 사전에 설정하려는 경우가 있습니다. 이러한 프로세스의 일부로, 관리자는 테넌트의 사용자를 대신하여 애플리케이션에 대한 동의를 부여할 수 있습니다. 관리자가 전체 테넌트에 대한 동의를 부여하면 조직의 사용자에게는 애플리케이션에 대한 동의 페이지가 표시되지 않습니다.

조직을 대신하여 관리자 동의를 얻으려면 앱에 대해 등록된 정적 권한이 있어야 합니다. 전체 조직을 대신하여 동의를 제공할 관리자가 필요한 경우 앱 등폴 포털에서 앱에 대해 해당 권한을 설정합니다.

테넌트의 모든 사용자에 대한 위임된 권한에 대한 동의를 요청하기 위해 앱에서 [관리자 동의 엔드포인트](#using-the-admin-consent-endpoint)를 사용할 수 있습니다.

또한 애플리케이션에서는 관리자 동의 엔드포인트를 사용하여 애플리케이션 권한을 요청해야 합니다.

## <a name="admin-restricted-permissions"></a>관리 제한 권한

Microsoft 리소스에 있는 일부 높은 수준의 권한은 관리 제한(*admin-restricted*)으로 설정할 수 있습니다. 다음은 이러한 종류의 권한에 대한 몇 가지 예입니다.

* `User.Read.All`을 사용하여 모든 사용자의 전체 프로필 읽기
* `Directory.ReadWrite.All`를 사용하여 조직의 디렉터리에 데이터 쓰기
* `Groups.Read.All`을 사용하여 조직 디렉터리의 모든 그룹 읽기

소비자인 사용자가 이러한 종류의 데이터에 대한 액세스 권한을 애플리케이션에 부여할 수 있더라도 조직 사용자는 동일한 종류의 중요한 회사 데이터 세트에 대한 액세스 권한을 부여할 수 없습니다. 애플리케이션이 조직 사용자에게 이러한 사용 권한 중 하나에 대한 액세스를 요청하는 경우 사용자에게는 앱의 사용 권한에 동의할 권한이 부여되지 않음을 나타내는 오류 메시지가 표시됩니다.

앱에 관리 제한 권한의 범위가 필요한 경우에는 조직의 관리자가 조직의 사용자를 대신하여 해당 범위에 동의해야 합니다. 사용자가 부여할 수 없는 권한에 대한 동의를 요청하는 메시지가 사용자에게 표시되지 않도록 하려면 앱에서 관리자 동의 엔드포인트를 사용하면 됩니다. 관리자 동의 엔드포인트는 다음 섹션에서 설명합니다.

애플리케이션이 높은 수준의 위임 권한을 요청하고, 관리자가 관리자 동의 엔드포인트를 통해 해당 권한을 부여하면 테넌트 내 모든 사용자에게 동의가 부여됩니다.

애플리케이션이 애플리케이션 권한을 요청하고 관리자가 관리자 동의 엔드포인트를 통해 해당 권한을 부여하는 경우 이러한 권한 부여는 특정 사용자를 대신하여 수행되지 않습니다. 그 대신 클라이언트 애플리케이션에 *직접* 권한이 부여됩니다. 이러한 유형의 권한은 백그라운드에서 실행되는 디먼 서비스 및 기타 비대화형 애플리케이션에서만 사용됩니다.

## <a name="using-the-admin-consent-endpoint"></a>관리 동의 엔드포인트 사용

관리자 동의 엔드포인트를 사용하여 관리자 동의를 부여하면 완료됩니다. 사용자는 추가로 작업을 수행할 필요가 없습니다. 관리자 동의가 부여되면 사용자는 일반적인 인증 흐름을 통해 액세스 토큰을 얻을 수 있습니다. 이렇게 얻은 액세스 토큰에는 이미 동의한 권한이 있습니다.

전역 관리자가 애플리케이션을 사용하고 권한 부여 엔드포인트로 연결되면 Microsoft ID 플랫폼은 사용자의 역할을 감지합니다. 요청한 권한에 대해 전역 관리자가 전체 테넌트를 대신하여 동의하기를 원하는지 묻습니다. 전용 관리자 동의 엔드포인트를 대신 사용하면 전체 테넌트를 대신하여 권한을 부여하도록 관리자에게 사전에 요청할 수 있습니다. 이 엔드포인트는 애플리케이션 권한을 요청하는 데도 필요합니다. 애플리케이션 권한은 인증 엔드포인트를 사용하여 요청할 수 없습니다.

다음 단계를 따르는 경우 앱은 관리 제한 범위를 포함하여 테넌트의 모든 사용자에 대한 사용 권한을 요청할 수 있습니다. 이 작업은 높은 수준의 권한입니다. 시나리오에 필요한 경우에만 이 작업을 사용하십시오.

단계를 구현하는 코드 샘플을 보려면 GitHub에서 [관리 제한 범위 샘플](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)을 참조하세요.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>앱 등록 포털에서 사용 권한 요청

앱 등록 포털에서 애플리케이션은 위임된 권한 및 애플리케이션 권한을 비롯하여 필요한 권한을 나열할 수 있습니다. 이렇게 설정하면 `/.default` 범위와 Azure Portal의 **관리자 동의 허용** 옵션을 사용할 수 있습니다.  

일반적으로 권한은 지정된 애플리케이션에 대해 정적으로 정의되어야 합니다. 앱이 동적으로 또는 점진적으로 요청할 권한의 상위 집합이어야 합니다.

> [!NOTE]
>애플리케이션 권한은 [`/.default`](#the-default-scope)를 사용하는 경우에만 요청할 수 있습니다. 따라서 앱에 애플리케이션 권한이 필요하면 해당 권한이 앱 등록 포털에 나열되어 있는지 확인하세요.

애플리케이션에 대해 정적으로 요청된 권한 목록을 구성하려면:

1. <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal - 앱 등록</a> 빠른 시작 환경에서 애플리케이션으로 이동합니다.
1. 애플리케이션을 선택하거나 아직 만들지 않았으면 [앱을 만듭니다](quickstart-register-app.md).
1. 애플리케이션 **개요** 페이지의 **관리** 에서 **API 권한** > **권한 추가** 를 선택합니다.
1. 사용 가능한 API 목록에서 **Microsoft Graph** 를 선택합니다. 그런 다음, 앱에 필요한 권한을 추가합니다.
1. **권한 추가** 를 선택합니다.

### <a name="recommended-sign-the-user-in-to-your-app"></a>사용자가 앱에 로그인하는 것이 좋습니다.

일반적으로 관리 동의 엔드포인트를 사용하는 애플리케이션을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지 또는 보기가 필요합니다. 이 페이지는 다음 항목일 수 있습니다.

* 앱 등록 흐름의 일부
* 앱 설정의 일부
* 전용 "연결" 흐름 

대부분의 경우 사용자가 회사 Microsoft 계정 또는 학교 Microsoft 계정으로 로그인한 후에만 앱에 "연결" 보기를 표시하는 것이 일반적입니다.

사용자가 앱에 로그인하면, 필요한 권한을 승인하도록 요청하기 전에 관리가 속한 조직을 식별할 수 있습니다. 이 단계가 반드시 필요한 것은 아니지만 조직 사용자에게 보다 직관적인 환경을 제공할 수 있습니다. 

사용자가 로그인되도록 하려면 [Microsoft ID 플랫폼 프로토콜 자습서](active-directory-v2-protocols.md)를 따르세요.

### <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청

조직 관리자의 사용 권한을 요청할 준비가 되면 Microsoft ID 플랫폼 관리자 동의 엔드포인트로 사용자를 리디렉션할 수 있습니다.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 매개 변수        | 조건        | 설명                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다. GUID 또는 친숙한 이름 형식으로 제공할 수 있습니다. 또는 예제에서 볼 수 있듯이 일반적으로 조직에서 참조할 수 있습니다. "common"은 사용하지 마십시오. 개인 계정은 테넌트의 컨텍스트를 제외하고 관리자 동의를 제공할 수 없기 때문입니다. 테넌트를 관리하는 개인 계정과 최고의 호환성을 보장하려면 되도록 테넌트 ID를 사용합니다. |
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `redirect_uri` | 필수 |리디렉션 URI는 처리할 앱에 응답을 전송하려는 위치입니다. 앱 등록 포털에 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| `state` | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태를 사용하여 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
|`scope`        | 필수        | 애플리케이션에서 요청하는 사용 권한 세트를 정의합니다. 범위는 정적([`/.default`](#the-default-scope) 사용) 또는 동적일 수 있습니다.  이 집합에는 OpenID Connect 범위(`openid`, `profile`, `email`)가 포함될 수 있습니다. 애플리케이션 권한이 필요하면 `/.default`를 사용하여 정적으로 구성된 권한 목록을 요청해야 합니다.  |


이 시점에서 Azure AD는 테넌트 관리자에게 요청을 완료하기 위해 로그인하도록 요구합니다. 관리자는 `scope` 매개 변수로 요청한 모든 권한을 승인하라는 요청을 받습니다.  정적(`/.default`) 값을 사용하면, v1.0 관리자 동의 엔드포인트처럼 작동하고 앱의 필수 권한에 있는 모든 범위에 대한 동의를 요청합니다.

#### <a name="successful-response"></a>성공적인 응답

관리자가 앱에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| --- | --- |
| `tenant` | 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 애플리케이션에 부여합니다. |
| `state` | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| `admin_consent` | `True`로 설정합니다. |

#### <a name="error-response"></a>오류 응답

관리자가 앱에 대한 권한을 승인하지 않는 경우 실패한 응답은 다음과 같습니다.

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| --- | --- |
| `error` | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 오류에 대응하는 데 사용할 수도 있습니다. |
| `error_description` | 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

관리 동의 엔드포인트에서 성공적인 응답을 받은 후 앱은 요청한 사용 권한을 얻게 됩니다. 이제 원하는 리소스에 대한 토큰을 요청할 수 있습니다.

## <a name="using-permissions"></a>사용 권한 사용

사용자가 앱에 대한 권한에 동의하면 앱은 리소스에 액세스할 수 있는 권한을 나타내는 액세스 토큰을 어느 정도 얻을 수 있습니다. 액세스 토큰은 단일 리소스에만 사용할 수 있습니다. 하지만 해당 리소스에 대해 앱에 부여된 모든 권한이 액세스 토큰 내에 인코딩됩니다. 액세스 토큰을 얻기 위해 앱은 Microsoft ID 플랫폼 토큰 엔드포인트에 다음과 같이 요청할 수 있습니다.

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

리소스에 대한 HTTP 요청에 결과 액세스 토큰을 사용할 수 있습니다. 이를 통해 앱에 특정 작업을 수행할 수 있는 적절한 권한이 있음을 리소스에 안정적으로 알려줍니다.

OAuth 2.0 프로토콜 및 액세스 토큰을 얻는 방법에 대한 자세한 내용은 [Microsoft ID 플랫폼 엔드포인트 프로토콜 참조](active-directory-v2-protocols.md)를 확인하세요.

## <a name="the-default-scope"></a>/.default 범위

`/.default` 범위를 사용하면 앱을 v1.0 엔드포인트에서 Microsoft ID 플랫폼 엔드포인트로 마이그레이션하는 데 유용합니다. `/.default` 범위는 애플리케이션 등록에 구성된 정적 권한 목록을 참조하는 모든 애플리케이션에 기본 제공됩니다. 

`scope` 값 `https://graph.microsoft.com/.default`는 v1.0 엔드포인트의 `resource=https://graph.microsoft.com`과 기능적으로 동일합니다. 요청에 `https://graph.microsoft.com/.default` 범위를 지정하면 애플리케이션은 앱 등록 포털에서 앱에 대해 선택한 모든 Microsoft Graph 권한의 범위를 포함하는 액세스 토큰을 요청합니다. 범위는 리소스 URI 및 `/.default`를 사용하여 구성됩니다. 따라서 리소스 URI가 `https://contosoApp.com`이면 요청된 범위는 `https://contosoApp.com/.default`입니다.  토큰을 올바르게 요청하기 위해 두 번째 슬래시를 포함해야 하는 경우 [후행 슬래시에 대한 섹션](#trailing-slash-and-default)을 참조하세요.

`/.default` 범위는 모든 OAuth 2.0 흐름에서 사용할 수 있습니다. 하지만 [On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md) 및 [클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)에서는 필요합니다. v2 관리자 동의 엔드포인트를 사용하여 애플리케이션 권한을 요청할 때도 필요합니다.

클라이언트는 정적(`/.default`) 동의와 동적 동의를 단일 요청에 결합할 수 없습니다. 따라서 `scope=https://graph.microsoft.com/.default+mail.read`는 범위 유형을 결합하기 때문에 오류가 발생합니다.

### <a name="default-and-consent"></a>/.default 및 동의

`/.default` 범위는 `prompt=consent`에 대한 v1.0 엔드포인트 동작도 트리거합니다. 리소스에 관계없이 애플리케이션이 등록한 모든 권한에 대한 동의를 요청합니다. 요청의 일부로 포함된 경우, `/.default` 범위는 요청된 리소스의 범위가 포함된 토큰을 반환합니다.

### <a name="default-when-the-user-has-already-given-consent"></a>사용자가 이미 동의를 제공한 경우의 /.default

`/.default` 범위는 `resource` 중심 v1.0 엔드포인트의 동작과 기능적으로 동일합니다. v1.0 엔드포인트의 동의 동작도 전달합니다. 즉, `/.default`는 사용자가 클라이언트와 리소스 간에 권한을 부여하지 않은 경우에만 동의 프롬프트를 트리거합니다. 

그러한 동의가 있으면 반환된 토큰에는 사용자가 해당 리소스에 대해 부여한 모든 범위가 포함됩니다. 하지만 권한이 부여되지 않았거나 `prompt=consent` 매개 변수가 제공된 경우에는 클라이언트 애플리케이션이 등록한 모든 범위에 대해 동의 프롬프트가 표시됩니다.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>예제 1: 사용자 또는 테넌트 관리자가 권한을 부여한 경우

이 예제에서는 사용자 또는 테넌트 관리자가 클라이언트에 `mail.read` 및 `user.read` Microsoft Graph 권한을 부여했습니다. 

클라이언트가 `scope=https://graph.microsoft.com/.default`를 요청하면 Microsoft Graph에 대한 클라이언트 애플리케이션의 등록된 권한 내용에 관계없이 동의 프롬프트가 표시되지 않습니다. 반환된 토큰에는 `mail.read` 및 `user.read` 범위가 포함됩니다.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>예제 2: 사용자가 클라이언트와 리소스 간에 권한을 부여하지 않은 경우

이 예에서는 사용자가 클라이언트와 Microsoft Graph 간에 동의를 부여하지 않았습니다. 클라이언트는 `user.read` 및 `contacts.read` 권한에 등록했습니다. Azure Key Vault 범위 `https://vault.azure.net/user_impersonation`에도 등록했습니다. 

클라이언트가 `scope=https://graph.microsoft.com/.default`에 대한 토큰을 요청하면 `user.read` 범위, `contacts.read` 범위 및 Key Vault `user_impersonation` 범위에 대한 동의 페이지가 사용자에게 표시됩니다. 반환된 토큰에는 `user.read` 및 `contacts.read` 범위만 포함됩니다. 이것은 Microsoft Graph에 대해서만 사용할 수 있습니다.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>예제 3: 사용자가 동의했고 클라이언트가 범위를 더 요청하는 경우

이 예에서 사용자는 클라이언트의 `mail.read`에 이미 동의했습니다. 클라이언트는 `contacts.read` 범위에 등록했습니다. 

클라이언트가 `scope=https://graph.microsoft.com/.default`를 사용하여 토큰을 요청하고 `prompt=consent`를 통해 동의를 요청하면, 애플리케이션이 등록한 모든 권한에 대한 동의 페이지만 사용자에게 표시됩니다. `contacts.read` 범위는 동의 페이지에 있지만 `mail.read`는 그렇지 않습니다. 반환된 토큰은 Microsoft Graph용입니다. 여기에는 `mail.read` 및 `contacts.read`가 포함됩니다.

### <a name="using-the-default-scope-with-the-client"></a>클라이언트에서 /.default 범위 사용

클라이언트가 자체 `/.default` 범위를 요청하는 경우도 있습니다. 다음 예제에서는 이 시나리오를 보여 줍니다.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

이 코드 예제는 동의 및 `/.default`에 대한 앞의 설명이 시나리오에 적용되는 경우 등록된 모든 권한에 대한 동의 페이지를 생성합니다. 그런 다음, 코드는 액세스 토큰이 아닌 `id_token`을 반환합니다.  

이 동작은 ADAL(Azure AD 인증 라이브러리)에서 MSAL(Microsoft 인증 라이브러리)로 이동하는 일부 레거시 클라이언트를 수용합니다. Microsoft ID 플랫폼을 대상으로 하는 새 클라이언트에는 이 설정을 사용해서는 안 됩니다.

### <a name="client-credentials-grant-flow-and-default"></a>클라이언트 자격 증명 부여 흐름 및 /.default  

`/.default`의 다른 용도는 [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 부여 흐름을 사용하여 웹 API를 호출하는 디먼 앱과 같은 비대화형 애플리케이션에서 애플리케이션 권한(또는 역할)을 요청하는 것입니다.

웹 API에 대한 애플리케이션 권한(역할)을 생성하려면 [애플리케이션에서 앱 역할 추가](howto-add-app-roles-in-azure-ad-apps.md)를 참조하세요.

클라이언트 앱의 클라이언트 자격 증명 요청에는 `scope={resource}/.default`가 반드시 포함되어야 합니다. 여기서 `{resource}`는 앱이 호출하려는 웹 API입니다. 개별 애플리케이션 권한(역할)을 사용하여 클라이언트 자격 증명 요청을 실행하는 것은 지원되지 않습니다. 반환된 액세스 토큰에는 웹 API에 대해 부여된 모든 애플리케이션 권한(역할)이 포함됩니다.

정의한 애플리케이션 권한에 대한 액세스 권한을 부여하려면(애플리케이션에 대한 관리자 동의 부여 포함) [웹 API에 액세스하도록 클라이언트 애플리케이션 구성](quickstart-configure-app-access-web-apis.md)을 참조하세요.

### <a name="trailing-slash-and-default"></a>후행 슬래시 및 /.default

일부 리소스 URI에는 후행 슬래시가 있습니다(예: `https://contoso.com`과 달리 `https://contoso.com/`). 후행 슬래시는 토큰 유효성 검사에 문제를 일으킬 수 있습니다. Azure Resource Manager(`https://management.azure.com/`)에 대해 토큰이 요청될 때 주로 문제가 발생합니다. 이 경우 리소스 URI의 후행 슬래시는 토큰이 요청될 때 슬래시가 있어야 한다는 의미입니다.  따라서 `https://management.azure.com/`에 대한 토큰을 요청하고 `/.default`를 사용할 때는 `https://management.azure.com//.default`를 요청해야 합니다(이중 슬래시 확인!). 일반적으로 토큰이 발행되고 있음을 확인하고 토큰을 수락해야 하는 API가 토큰을 거부한 경우에는 두 번째 슬래시를 추가하고 다시 시도해보십시오. 

## <a name="troubleshooting-permissions-and-consent"></a>권한 및 동의 문제 해결

문제 해결 단계는 [애플리케이션에 대한 동의를 수행할 때 예기치 않은 오류](../manage-apps/application-sign-in-unexpected-user-consent-error.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Microsoft ID 플랫폼의 ID 토큰](id-tokens.md)
* [Microsoft ID 플랫폼의 액세스 토큰](access-tokens.md)
