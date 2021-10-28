---
title: 오류와 예외(MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: MSAL Android 애플리케이션에서 오류와 예외, 조건부 액세스 및 클레임 챌린지를 처리하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: marsma
ms.reviewer: marsma
ms.openlocfilehash: 5a4efe81fae5e98c588141e6dff85544dea2961a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567994"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Android용 MSAL에서 예외 및 오류 처리

MSAL(Microsoft 인증 라이브러리)의 예외는 앱 개발자가 애플리케이션 문제를 해결하는 데 도움을 주기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

로그인 환경에서 동의, 조건부 액세스(MFA, 디바이스 관리, 위치 기반 제한), 토큰 발급 및 상환 및 사용자 속성에 대한 오류가 발생할 수 있습니다.


|Error 클래스 | 원인/오류 문자열| 처리 방법 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: 액세스 토큰에 사용되는 새로 고침 토큰이 잘못되었거나 만료되었거나 해지되었습니다. 이 예외는 암호 변경으로 인해 발생할 수 있습니다. </li><li>`NO_TOKENS_FOUND`: 액세스 토큰이 없고 액세스 토큰을 사용하는 새로 고침 토큰을 찾을 수 없습니다.</li> <li>버전 업그레이드 필요<ul><li>MFA</li><li>누락된 클레임</li></ul></li><li>조건부 액세스에 의해 차단(예: [인증 브로커](./msal-android-single-sign-on.md) 설치 필요)</li><li>`NO_ACCOUNT_FOUND`: 자동 인증을 위해 캐시에서 사용할 수 있는 계정이 없습니다.</li></ul> |`acquireToken()`를 호출하여 사용자 이름 및 암호를 입력하라는 메시지를 표시하고 사용자가 다단계 인증에 동의하고 인증을 수행하게 할 수 있습니다.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: 사용자 또는 서버가 모든 범위를 수락하지 않았습니다. 요청된 범위가 지원되지 않거나 인식되지 않거나 특정 계정에 대해 지원되지 않는 경우 서버에서 범위를 거부할 수 있습니다. </li></ul>| 개발자는 부여된 범위로 인증을 계속할지 아니면 인증 프로세스를 종료할지 결정해야 합니다. 부여된 범위에 대해서만 토큰 획득 요청을 다시 제출하고 `silentParametersForGrantedScopes`를 전달하고 `acquireTokenSilent`를 호출하여 사용 권한이 부여된 힌트를 제공하는 옵션입니다. |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: 요청이 필수 매개 변수를 누락하거나, 잘못된 매개 변수를 포함하거나, 매개 변수를 두 번 이상 포함하거나 형식이 잘못되었습니다. </li><li>`SERVICE_NOT_AVAILABLE`: 서비스가 중단되어 500/503/506 오류 코드를 나타냅니다. </li><li>`UNAUTHORIZED_REQUEST`: 클라이언트에 인증 코드를 요청할 권한이 없습니다.</li><li>`ACCESS_DENIED`: 리소스 소유자 또는 권한 부여 서버에서 요청을 거부했습니다.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` 유효성 검사에 실패했습니다.</li><li>`UNKNOWN_ERROR`: 서버에 대한 요청이 실패했지만 오류가 발생하지 않았고 `error_description`이 서비스에서 반환됩니다.</li><ul>| 이 예외 클래스는 서비스와 통신할 때의 오류를 나타내며 권한 부여 또는 토큰 엔드포인트에서 발생할 수 있습니다. MSAL은 서버 응답에서 오류와 error_description을 읽습니다. 일반적으로 이러한 오류는 코드 또는 앱 등록 포털에서 앱 구성을 수정하여 해결됩니다. 드물게 서비스 중단으로 인해 이 경고가 트리거될 수 있으며, 복구될 때까지 대기하면 완화할 수 있습니다.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: 캐시 항목을 여러 개 찾았으며 SDK가 캐시에서 올바른 액세스 또는 새로 고침 토큰을 식별할 수 없습니다. 이 예외는 토큰을 저장하는 SDK의 버그를 나타내거나, 권한이 자동 요청에서 제공되지 않고 일치하는 여러 토큰이 발견되었음을 나타냅니다. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: 디바이스에서 활성 네트워크를 사용할 수 없습니다. </li><li>`JSON_PARSE_FAILURE`: SDK가 JSON 형식을 구문 분석하지 못했습니다.</li><li>`IO_ERROR`: `IOException`이 발생했으며 디바이스 또는 네트워크 오류일 수 있습니다. </li><li>`MALFORMED_URL`: URL 형식이 잘못되었습니다. 인증 요청, 권한 또는 리디렉션 URI를 생성할 때 발생할 수 있습니다. </li><li>`UNSUPPORTED_ENCODING`: 디바이스가 인코딩을 지원하지 않습니다. </li><li>`NO_SUCH_ALGORITHM`: [PKCE](https://tools.ietf.org/html/rfc7636) 챌린지를 생성하는 데 사용되는 알고리즘은 지원되지 않습니다. </li><li>`INVALID_JWT`: 서버에서 반환된 `JWT`가 유효하지 않거나 형식이 잘못되었습니다. </li><li>`STATE_MISMATCH`: 권한 부여 응답의 상태가 권한 부여 요청의 상태와 일치하지 않습니다. 권한 부여 요청의 경우 SDK는 리디렉션에서 반환된 상태와 요청에서 보낸 상태를 확인합니다. </li><li>`UNSUPPORTED_URL`: 지원되지 않는 url이며 ADFS 권한 유효성 검사를 수행할 수 없습니다. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: 권한에 유효성 검사가 지원되지 않습니다. SDK는 B2C 권한을 지원하지만 B2C 권한 유효성 검사는 지원하지 않습니다. 잘 알려진 호스트만 지원됩니다. </li><li>`CHROME_NOT_INSTALLED`: Chrome이 디바이스에 설치되어 있지 않습니다. SDK는 사용 가능한 경우 권한 부여 요청에 Chrome 사용자 지정 탭을 사용하고 Chrome 브라우저로 대체합니다. </li><li>`USER_MISMATCH`: 토큰 획득 요청에 제공된 사용자가 서버에서 반환된 사용자와 일치하지 않습니다.</li></ul>|이 예외 클래스는 라이브러리에 로컬인 일반 오류를 나타냅니다. 해당 예외는 요청을 수정하여 처리할 수 있습니다.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: 사용자가 대화형 흐름을 시작했으며 토큰을 받기 전에 요청을 취소했습니다. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: `acquireTokenSilent`의 권한을 지정해야 합니다.</li></ul>|해당 오류는 개발자가 인수를 수정하고 대화형 인증, 완료 콜백, 범위 및 유효한 ID가 있는 계정이 제공되었는지 여부를 확인하여 완화할 수 있습니다.|


## <a name="catching-errors"></a>오류 catch

다음 코드 조각은 자동 `acquireToken` 호출의 경우에 오류를 포착하는 예제를 보여 줍니다.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>다음 단계

[Android용 MSAL 로깅](msal-logging-android.md)에 대한 자세한 내용을 알아봅니다.