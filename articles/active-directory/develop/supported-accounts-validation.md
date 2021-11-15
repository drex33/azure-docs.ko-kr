---
title: 지원되는 계정 유형별 유효성 검사 차이 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에 앱을 등록할 때 지원되는 다양한 계정 유형에 대한 다양한 속성의 유효성 검사 차이에 대해 알아봅니다.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 09/29/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: manrath
ms.openlocfilehash: 3a143a40f6c6faa4f846a5d77a39a0afdb52068e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355668"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>지원되는 계정 유형별 유효성 검사 차이(signInAudience)

개발자용 Microsoft ID 플랫폼에 애플리케이션을 등록할 때 애플리케이션에서 지원하는 계정 유형을 선택하라는 메시지가 표시됩니다. 애플리케이션 개체 및 매니페스트에서 이 속성은 `signInAudience`입니다.

옵션에는 다음과 같은 값이 포함됩니다.

- **AzureADMyOrg**: 앱이 등록된 조직 디렉터리(단일 테넌트)의 계정만 해당
- **AzureADMultipleOrgs**: 모든 조직 디렉터리의 계정(다중 테넌트)
- **AzureADandPersonalMicrosoftAccount**: 모든 조직 디렉터리(다중 테 넌 트)의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com).

등록된 애플리케이션의 경우 애플리케이션의 **인증** 섹션에서 지원되는 계정 유형의 값을 찾을 수 있습니다. **매니페스트** 의 `signInAudience` 속성에서도 찾을 수 있습니다.

이 속성에 대해 선택한 값은 다른 앱 개체 속성에 영향을 미칩니다. 따라서 이 속성을 변경하는 경우 먼저 다른 속성을 변경해야 할 수 있습니다.

지원되는 다양한 계정 유형에 대한 다양한 속성의 유효성 검사 차이는 다음 표를 참조하세요.

| 속성                                                     | `AzureADMyOrg`                                                                                                                                                                                                                                      | `AzureADMultipleOrgs`                                                                                                                                                                                                                          | `AzureADandPersonalMicrosoftAccount` 및 `PersonalMicrosoftAccount`                                                                                                                                                |
| ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 애플리케이션 ID URI(`identifierURIs`)                        | 테넌트에서 고유해야 합니다. <br><br> urn:// 체계가 지원됩니다. <br><br> 와일드카드는 지원되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원됩니다. <br><br> 최대 길이 255자 <br><br> identifierURI 수에 대한 제한 없음\* | 전역적으로 고유해야 합니다. <br><br> urn:// 체계가 지원됩니다. <br><br> 와일드카드는 지원되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원됩니다. <br><br> 최대 길이 255자 <br><br> identifierURI 수에 대한 제한 없음\* | 전역적으로 고유해야 합니다. <br><br> urn:// 체계는 지원되지 않습니다. <br><br> 와일드카드, 조각 및 쿼리 문자열은 지원되지 않습니다. <br><br> 최대 길이 120자 <br><br> 최대 identifierURI 50개 |
| 인증서(`keyCredentials`)                              | 대칭 서명 키                                                                                                                                                                                                                               | 대칭 서명 키                                                                                                                                                                                                                          | 암호화 및 비대칭 서명 키                                                                                                                                                                              |
| 클라이언트 암호(`passwordCredentials`)                       | 제한 없음\*                                                                                                                                                                                                                                          | 제한 없음\*                                                                                                                                                                                                                                     | liveSDK를 사용하는 경우: 클라이언트 암호 최대 2개                                                                                                                                                               |
| 리디렉션 URI(`replyURLs`)                                  | 자세한 정보는 [리디렉션 URI/회신 URL 제한 및 한도](reply-url.md)를 참조하세요.                                                                                                                                                              |                                                                                                                                                                                                                                                |                                                                                                                                                                                                                    |
| API 권한(`requiredResourceAccess`)                   | 제한 없음\*                                                                                                                                                                                                                                          | 제한 없음\*                                                                                                                                                                                                                                     | 애플리케이션당 리소스 최대 50개, 리소스당 권한 최대 30개(예: Microsoft Graph). 애플리케이션당 총 제한 200개(리소스 x 권한)                                              |
| 이 API에서 정의된 범위(`oauth2Permissions`)             | 최대 범위 이름 길이 120자 <br><br> 정의된 범위 수에 대한 제한 없음\*                                                                                                                                                     | 최대 범위 이름 길이 120자 <br><br> 정의된 범위 수에 대한 제한 없음\*                                                                                                                                                | 최대 범위 이름 길이 40자 <br><br> 최대 100개의 범위 정의                                                                                                                                  |
| 권한 있는 클라이언트 애플리케이션(`preAuthorizedApplications`) | 제한 없음\*                                                                                                                                                                                                                                          | 제한 없음\*                                                                                                                                                                                                                                     | 총 최대 500개 <br><br> 클라이언트 앱 최대 100개 정의 <br><br> 클라이언트당 범위 최대 30개 정의                                                                                                  |
| appRoles                                                     | 지원됨 <br> 제한 없음\*                                                                                                                                                                                                                           | 지원됨 <br> 제한 없음\*                                                                                                                                                                                                                      | 지원되지 않음                                                                                                                                                                                                      |
| 프런트-채널 로그아웃 URL                                     | https://localhost 는 허용됩니다. <br><br> `http` 체계는 허용되지 않습니다. <br><br> 최대 길이 255자                                                                                                                                         | https://localhost 는 허용됩니다. <br><br> `http` 체계는 허용되지 않습니다. <br><br> 최대 길이 255자                                                                                                                                    | https://localhost 는 허용되지만, http://localhost 는 실패합니다. <br><br> `http` 체계는 허용되지 않습니다. <br><br> 최대 길이 255자 <br><br> 와일드카드는 지원되지 않습니다.                                            |

\* 앱 개체의 모든 컬렉션 속성에서 약 1000개의 항목에 대한 전역 제한이 있습니다.

## <a name="next-steps"></a>다음 단계

애플리케이션 등록 및 해당 JSON 매니페스트에 대한 자세한 내용은 다음을 참조하세요.

- [애플리케이션 등록](app-objects-and-service-principals.md)
- [애플리케이션 매니페스트](reference-app-manifest.md)
