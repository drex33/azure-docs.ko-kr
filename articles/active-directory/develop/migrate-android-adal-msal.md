---
title: Android용 ADAL에서 MSAL로 마이그레이션 가이드 | Azure
titleSuffix: Microsoft identity platform
description: Azure ADAL(Active Directory 인증 라이브러리) Android 앱을 MSAL(Microsoft 인증 라이브러리)로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 67aa081bee7aba1c933ba65e4a7034d3d037d44d
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315055"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Android용 ADAL에서 MSAL로 마이그레이션 가이드

이 문서에서는 MSAL(Microsoft Authentication Library)을 사용하도록 Azure ADAL(Active Directory 인증 라이브러리)을 사용하는 앱을 마이그레이션하기 위해 변경해야 하는 사항을 강조합니다.

## <a name="difference-highlights"></a>주요 차이점

ADAL은 Azure Active Directory v1.0 엔드포인트에서 작동합니다. MSAL(Microsoft 인증 라이브러리)은 Microsoft ID 플랫폼(이전의 Azure Active Directory v2.0 엔드포인트)에서 작동합니다. Microsoft ID 플랫폼은 다음 측면에서 Azure Active Directory v1.0과 다릅니다.

지원:
  - 조직 ID(Azure Active Directory)
  - Outlook.com, Xbox Live 등의 비 조직 ID
  - (Azure AD B2C만 해당) Google, Facebook, Twitter 및 Amazon를 사용하는 페더레이션 로그인

- 다음과 호환됩니다.
  - OAuth v2.0
  - OpenID Connect(OIDC)

MSAL 공용 API에는 다음과 같은 중요한 변경 사항이 도입되었습니다.

- 토큰 액세스를 위한 새 모델:
  - ADAL은 서버를 나타내는 `AuthenticationContext`를 통해 토큰에 대한 액세스를 제공합니다. MSAL은 클라이언트를 나타내는 `PublicClientApplication`을 통해 토큰에 대한 액세스를 제공합니다. 클라이언트 개발자는 상호 작용해야 하는 모든 인증 기관에 대해 새 `PublicClientApplication` 인스턴스를 만들 필요가 없습니다. `PublicClientApplication` 구성 하나만 필요합니다.
  - 리소스 식별자 외에도 범위를 사용하여 액세스 토큰을 요청하도록 지원합니다.
  - 증분 동의를 지원합니다. 개발자는 사용자가 앱 등록 시 포함되지 않은 기능을 포함하여 앱에서 더 많은 기능에 액세스할 때 범위를 요청할 수 있습니다.
  - 인증 기관은 더 이상 런타임에 유효성이 검사되지 않습니다. 대신, 개발자는 개발 중에 '알려진 인증 기관' 목록을 선언합니다.
- 토큰 API 변경 사항:
  - ADAL에서는 `AcquireToken()`이 먼저 자동 요청을 수행합니다. 이 요청이 실패하면 대화형 요청을 수행합니다. 이 동작으로 인해 일부 개발자가 `AcquireToken`에만 의존하여 때때로 예기치 않게 사용자에게 자격 증명을 입력하라는 메시지가 표시됩니다. MSAL에서 개발자가 사용자에게 UI 프롬프트가 표시되는 시기를 의도적으로 결정해야 합니다.
    - `AcquireTokenSilent`는 성공 또는 실패하는 자동 요청을 항상 생성합니다.
    - `AcquireToken`은 UI를 통해 사용자에게 메시지를 표시하는 요청을 항상 생성합니다.
- MSAL은 기본 브라우저 또는 포함된 웹 보기에서 로그인을 지원합니다.
  - 기본적으로 디바이스의 기본 브라우저가 사용됩니다. 이를 통해 MSAL은 하나 이상의 로그인된 계정에 이미 있을 수 있는 인증 상태(쿠키)를 사용할 수 있습니다. 인증 상태가 없는 경우 권한 부여 중에 MSAL을 통해 인증하면 동일한 브라우저에서 사용할 다른 웹 애플리케이션을 위해 인증 상태(쿠키)가 생성됩니다.
- 새 예외 모델:
  - 예외는 발생한 오류의 유형과 개발자가 해당 오류를 해결하기 위해 수행해야 하는 작업을 더 명확하게 정의합니다.
- MSAL은 `AcquireToken` 및 `AcquireTokenSilent` 호출에 대한 매개 변수 개체를 지원합니다.
- MSAL은 다음에 대한 선언적 구성을 지원합니다.
  - 클라이언트 ID, 리디렉션 URI.
  - 포함된 웹 보기 또는 기본 브라우저
  - 인증 기관
  - 읽기 및 연결 시간 제한과 같은 HTTP 설정

## <a name="your-app-registration-and-migration-to-msal"></a>앱 등록 및 MSAL로 마이그레이션

MSAL을 사용하기 위해 기존 앱 등록을 변경할 필요가 없습니다. 증분/점진적 동의를 활용하려면 등록을 검토하여 증분 방식으로 요청하려는 특정 범위를 확인해야 할 수 있습니다. 범위 및 증분 동의에 대한 자세한 내용은 다음과 같습니다.

포털의 앱 등록에 **API 권한** 탭이 표시됩니다. 이 탭에서 현재 앱이 액세스를 요청하도록 구성된 API 및 권한(범위)의 목록이 제공됩니다. 또한 각 API 권한과 연결된 범위 이름의 목록도 표시됩니다.

### <a name="user-consent"></a>사용자 승인

ADAL 및 Azure AD v1 엔드포인트에서는 사용자가 소유한 리소스를 처음 사용할 때 사용자 동의가 부여되었습니다. MSAL 및 Microsoft ID 플랫폼에서는 동의가 점진적으로 요청될 수 있습니다. 증분 동의는 권한이 필요한 이유를 명확하게 설명하는 경우 사용자가 높은 권한을 고려할 수 있지만 그렇지 않은 경우에는 의문을 제기할 수 있는 권한에 유용합니다. ADAL에서는 이러한 권한 때문에 사용자가 앱에 로그인할 수 없을 수 있습니다.

> [!TIP]
> 증분 동의를 사용하여 앱에 특정 권한이 필요한 이유에 대한 추가 컨텍스트를 사용자에게 제공합니다.

### <a name="admin-consent"></a>관리자 동의

조직 관리자는 조직의 모든 구성원을 대신하여 애플리케이션에 필요한 권한에 동의할 수 있습니다. 일부 조직에서는 관리자만 애플리케이션에 동의할 수 있습니다. 관리자 동의를 사용하려면 앱 등록에서 애플리케이션에 사용되는 모든 API 권한 및 범위를 포함해야 합니다.

> [!TIP]
> MSAL을 사용하여 앱 등록에 포함되지 않은 항목에 대해 범위를 요청할 수는 있지만, 앱 등록을 업데이트하여 사용자가 권한을 부여할 수 있는 모든 리소스 및 범위를 포함하는 것이 좋습니다.

## <a name="migrating-from-resource-ids-to-scopes"></a>리소스 ID에서 범위로 마이그레이션

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>처음 사용할 때 모든 권한에 대한 권한 부여 인증 및 요청

현재 ADAL을 사용 중이고 증분 동의를 사용할 필요가 없는 경우 MSAL을 사용하기 시작하는 가장 간단한 방법은 새 `AcquireTokenParameter` 개체를 사용하고 리소스 ID 값을 설정하여 `acquireToken` 요청을 수행하는 것입니다.

> [!CAUTION]
> 범위와 리소스 ID를 모두 설정할 수는 없습니다. 둘 다 설정하려고 하면 `IllegalArgumentException`이 발생합니다.

그러면 사용되는 것과 동일한 v1 동작이 발생합니다. 앱 등록에서 요청된 모든 권한은 첫 번째 상호 작용 중에 사용자가 요청한 것입니다.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>필요한 경우에만 권한 인증 및 요청

증분 동의를 활용하려면 앱 등록에서 앱이 사용하는 권한(범위)의 목록을 확인하고 다음을 기반으로 두 개의 목록으로 구성합니다.

- 사용자가 로그인할 때 앱과 처음 상호 작용하는 동안 요청하려는 범위
- 사용자에게 설명해야 하는 앱의 중요한 기능과 관련된 권한

범위를 구성한 후에는 토큰을 요청하려는 리소스(API) 및 사용자가 동시에 권한을 부여하도록 하려는 다른 모든 범위를 기준으로 각 목록을 구성합니다.

MSAL에 대한 요청을 수행하는 데 사용되는 매개 변수 개체는 다음을 지원합니다.

- `Scope`: 권한 부여를 요청하고 액세스 토큰을 받으려는 범위의 목록입니다.
- `ExtraScopesToConsent`: 다른 리소스에 대한 액세스 토큰을 요청하는 동안 권한 부여를 요청하려는 추가 범위의 목록입니다. 이 범위 목록을 사용하면 사용자 권한 부여를 요청해야 하는 횟수를 최소화할 수 있습니다. 즉, 사용자 권한 부여 또는 동의 프롬프트가 줄어듭니다.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>AuthenticationContext에서 PublicClientApplications로 마이그레이션

### <a name="constructing-publicclientapplication"></a>PublicClientApplication 생성

MSAL을 사용하는 경우 `PublicClientApplication`을 인스턴스화합니다. 이 개체는 앱 ID를 모델링하고 하나 이상의 인증 기관에 요청을 수행하는 데 사용됩니다. 이 개체를 사용하여 클라이언트 ID, 리디렉션 URI, 기본 인증 기관, 디바이스 브라우저 또는 포함된 웹 보기 사용 여부, 로그 수준 등을 구성합니다.

JSON을 사용하여 이 개체를 선언적으로 구성할 수 있습니다. 이 개체는 파일로 제공하거나 APK 내에 리소스로 저장합니다.

이 개체는 싱글톤이 아니지만 내부적으로 대화형 및 자동 요청 모두에 대해 공유 `Executors`를 사용합니다.

### <a name="business-to-business"></a>B2B(Business to Business)

ADAL에서는 액세스 토큰이 요청되는 모든 조직에 별도의 `AuthenticationContext` 인스턴스가 필요합니다. MSAL에서는 이것이 더 이상 요구 사항이 아닙니다. 자동 또는 대화형 요청의 일부로 토큰을 요청하려는 인증 기관을 지정할 수 있습니다.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>인증 기관 유효성 검사에서 알려진 인증 기관으로 마이그레이션

MSAL은 인증 기관 유효성 검사를 사용하거나 사용하지 않도록 설정하는 플래그를 포함하지 않습니다. 인증 기관 유효성 검사는 ADAL의 기능으로, MSAL의 초기 릴리스에서 코드가 잠재적으로 악의적인 인증 기관에서 토큰을 요청하는 것을 방지합니다. 이제 MSAL은 Microsoft에 알려진 인증 기관 목록을 검색하고 해당 목록을 구성에 지정된 인증 기관과 병합합니다.

> [!TIP]
> Azure B2C(Business to Consumer) 사용자인 경우 이는 더 이상 인증 기관 유효성 검사를 사용하지 않도록 설정할 필요가 없다는 의미입니다. 대신, 지원되는 각 Azure AD B2C 정책을 MSAL 구성의 인증 기관으로 포함합니다.

Microsoft에 알려지지 않았고 구성에 포함되지 않은 인증 기관을 사용하려고 시도하면 `UnknownAuthorityException`이 발생합니다.

### <a name="logging"></a>로깅
이제 다음과 같이 구성의 일부로 로깅을 선언적으로 구성할 수 있습니다.

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>UserInfo에서 계정으로 마이그레이션

ADAL에서는 `AuthenticationResult`가 인증된 계정에 대한 정보를 검색하는 데 사용되는 `UserInfo` 개체를 제공합니다. 인간 사용자 또는 소프트웨어 에이전트를 의미하는 '사용자'라는 용어는 일부 앱이 여러 계정을 보유하는 단일 사용자(인간 사용자 또는 소프트웨어 에이전트)를 지원한다는 것을 알리기 어려운 방식으로 적용되었습니다.

은행 계좌를 생각해보면 여러 금융 기관에 여러 계좌가 있을 수 있습니다. 계좌를 개설하면 고객(사용자)에게 각 계좌의 잔액, 청구서 등에 액세스하는 데 사용되는 ATM 카드 및 PIN과 같은 자격 증명이 발급됩니다. 이러한 자격 증명은 해당 자격 증명을 발급한 금융 기관에서만 사용할 수 있습니다.

금융 기관 계좌와 마찬가지로, Microsoft ID 플랫폼의 계정은 자격 증명을 사용하여 액세스됩니다. 이러한 자격 증명은 Microsoft에 등록되거나 Microsoft에 의해 발급됩니다. 또는 Microsoft가 조직을 대신하여 수행합니다.

이 비유에서 Microsoft ID 플랫폼이 금융 기관과 다른 점은, Microsoft ID 플랫폼은 사용자가 한 계정 및 연결된 자격 증명을 사용하여 여러 개인 및 조직에 속한 리소스에 액세스할 수 있도록 허용하는 프레임워크를 제공한다는 것입니다. 마치 한 금융 기관에서 발급한 카드를 다른 금융 기관에서 사용할 수 있는 것과 같습니다. 이는 관련 조직이 모두 Microsoft ID 플랫폼을 사용하여 하나의 계정을 여러 조직에서 사용하도록 허용하기 때문에 작동합니다. 예를 들면 다음과 같습니다.

Sam은 Contoso.com에서 근무하지만 Fabrikam.com에 속한 Azure 가상 머신을 관리합니다. Sam이 가상 머신을 관리하려면 해당 컴퓨터에 액세스할 수 있는 권한이 있어야 합니다. Fabrikam.com에 Sam의 계정을 추가하고 해당 계정에 가상 머신을 사용할 수 있는 역할을 할당하여 이 액세스 권한을 부여할 수 있습니다. 이러한 작업은 Azure Portal에서도 수행할 수 있습니다.

Sam의 Contoso.com 계정을 Fabrikam.com의 멤버로 추가하면 Fabrikam의 Azure Active Directory에 Sam에 대한 레코드가 새로 만들어집니다. Sam의 Azure Active Directory 레코드를 사용자 개체라고 합니다. 이 경우 이 사용자 개체가 Sam의 Contoso.com 사용자 개체를 다시 가리킵니다. Sam의 Fabrikam 사용자 개체는 Sam의 로컬 표현이며 Fabrikam.com의 컨텍스트에서 Sam과 연결된 계정에 대한 정보를 저장하는 데 사용됩니다. Contoso.com에서 Sam의 직책은 선임 DevOps 컨설턴트입니다. Fabrikam에서 Sam의 직책은 계약자-가상 머신입니다. Contoso.com에서 Sam은 가상 머신 관리를 담당하거나 권한을 부여 받지 않습니다. Fabrikam.com에서는 이것이 유일한 직무입니다. 그러나 Sam에게는 Contoso.com에서 발급한 자격 증명 세트가 하나 더 있습니다.

`acquireToken`이 성공적으로 호출되면 이후의 `acquireTokenSilent` 요청에서 사용할 수 있는 `IAccount` 개체에 대한 참조가 표시됩니다.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

계정이 표현되는 각 테넌트의 계정에 대한 클레임에 액세스하는 앱이 있는 경우 `IAccount` 개체를 `IMultiTenantAccount`로 캐스팅할 수 있습니다. 이 인터페이스는 테넌트 ID로 키가 지정된 `ITenantProfiles`의 맵을 제공합니다. 이 맵을 사용하면 현재 계정을 기준으로 토큰을 요청한 각 테넌트의 계정에 속하는 클레임에 액세스할 수 있습니다.

`IAccount` 및 `IMultiTenantAccount`의 루트에 있는 클레임은 항상 홈 테넌트의 클레임을 포함합니다. 홈 테넌트 내의 토큰에 대한 요청을 아직 수행하지 않은 경우 이 컬렉션은 비어 있습니다.

## <a name="other-changes"></a>기타 변경 내용

### <a name="use-the-new-authenticationcallback"></a>새 AuthenticationCallback 사용

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>새 예외로 마이그레이션

ADAL에는 `ADALError` 열거형 값을 검색하기 위한 메서드를 포함하는 하나의 예외 형식 `AuthenticationException`이 있습니다.
MSAL에는 예외 계층 구조가 있으며 각 계층에는 연결된 특정 오류 코드의 집합이 있습니다.

| 예외                                        | 설명                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | 하나 이상의 입력 인수가 잘못된 경우 throw됩니다.                 |
| `MsalClientException`                            | 오류가 클라이언트 쪽인 경우 throw됩니다.                                 |
| `MsalDeclinedScopeException`                     | 서버에서 하나 이상의 요청된 범위를 거부한 경우 throw됩니다. |
| `MsalException`                                  | MSAL이 throw하는 기본 확인된 예외입니다.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | 리소스가 MAMCA 보호 정책을 사용하도록 설정된 경우 throw됩니다.         |
| `MsalServiceException`                           | 오류가 서버 쪽인 경우 throw됩니다.                                 |
| `MsalUiRequiredException`                        | 토큰을 자동으로 새로 고칠 수 없는 경우 throw됩니다.                    |
| `MsalUserCancelException`                        | 사용자가 인증 흐름을 취소한 경우 throw됩니다.                |

### <a name="adalerror-to-msalexception-translation"></a>ADALError에서 MsalException으로 변환

| ADAL에서 다음 오류를 catch하는 경우...  | ...다음 MSAL 예외를 catch                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| 해당 ADALError 없음 | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| 해당 ADALError 없음 | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| 해당 ADALError 없음 | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| 해당 ADALError 없음 | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>ADAL 로깅에서 MSAL 로깅으로

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
