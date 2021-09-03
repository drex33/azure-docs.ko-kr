---
title: Microsoft ID 플랫폼 새로 고침 토큰 | Azure
titleSuffix: Microsoft identity platform
description: Azure AD에서 내보낸 새로 고침 토큰에 대해 알아봅니다.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688112"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft ID 플랫폼 새로 고침 토큰

클라이언트가 보호된 리소스에 액세스하기 위해 액세스 토큰을 획득할 때 새로 고침 토큰도 받습니다. 새로 고침 토큰은 현재 액세스 토큰이 만료된 경우 새 액세스/새로 고침 토큰 쌍을 얻는 데 사용됩니다. 새로 고침 토큰은 다른 리소스에 대한 추가 액세스 토큰을 얻는 데에도 사용됩니다. 새로 고침 토큰은 사용자와 클라이언트의 조합에 바인딩되며 리소스나 테넌트에 연결되지 않습니다. 따라서 클라이언트는 새로 고침 토큰을 사용하여 권한이 있는 리소스와 테넌트의 조합에서 액세스 토큰을 얻을 수 있습니다. 새로 고침 토큰은 암호화되며 Microsoft ID 플랫폼에서만 읽을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 읽기 전에 다음 문서를 먼저 살펴보는 것을 권장합니다.

* Microsoft ID 플랫폼의 [ID 토큰](id-tokens.md)
* Microsoft ID 플랫폼의 [액세스 토큰](access-tokens.md)

## <a name="refresh-token-lifetime"></a>새로 고침 토큰 수명

새로 고침 토큰은 액세스 토큰보다 수명이 더 깁니다. 토큰의 기본 수명은 90일이며 사용할 때마다 새로 고침 토큰으로 자동 대체됩니다. 따라서 새로 고침 토큰을 사용하여 새 액세스 토큰을 획득할 때마다 새 새로 고침 토큰도 발급됩니다. Microsoft ID 플랫폼은 새 액세스 토큰을 가져오는 데 사용되는 경우 이전의 새로 고침 토큰을 취소하지 않습니다. 새 토큰을 획득한 후 이전의 새로 고침 토큰을 안전하게 삭제하세요. 새로 고침 토큰은 액세스 토큰이나 애플리케이션 자격 증명처럼 안전하게 저장해야 합니다. 

## <a name="refresh-token-expiration"></a>새로 고침 토큰 만료

새로 고침 토큰은 시간 제한과 해지를 사유로 언제든지 취소할 수 있습니다. 이 문제가 발생하면 앱이 로그인 서비스의 거부를 정상적으로 처리해야 합니다. 이 작업은 다시 로그인하라는 대화형 로그인 프롬프트를 사용자에게 사용자를 전송하여 수행됩니다. 

### <a name="token-timeouts"></a>토큰 제한 시간

새로 고침 토큰의 수명은 구성할 수 없습니다. 수명을 줄이거나 늘릴 수 없습니다. 사용자가 다시 로그인해야 하기까지의 기간을 정의하려면 조건부 액세스에서 로그인 빈도를 구성합니다. [조건부 액세스를 사용하여 인증 세션 관리를 구성](../conditional-access/howto-conditional-access-session-lifetime.md)하는 방법을 자세히 알아봅니다.

모든 새로 고침 토큰은 토큰 수명 정책에 설정된 규칙을 따릅니다. 특히, [단일 페이지 앱](reference-third-party-cookies-spas.md)에 사용되는 새로 고침 토큰은 마치 24시간의 `MaxAgeSessionSingleFactor` 정책이 적용된 것과 같이 항상 24시간의 활동으로 고정됩니다. 

### <a name="revocation"></a>해지

자격 증명, 사용자 작업 또는 관리자 작업의 변경을 사유로 서버에서 새로 고침 토큰을 취소할 수 있습니다.  새로 고침 토큰은 기밀 클라이언트가 발급한 토큰(가장 오른쪽 열)과 공용 클라이언트가 발급한 토큰(다른 모든 열)의 두 클래스에 속합니다.

| 변경 | 암호 기반 쿠키 | 암호 기반 토큰 | 비암호 기반 쿠키 | 비암호 기반 토큰 | 기밀 클라이언트 토큰 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 암호 만료 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자에 의해 암호가 변경됨 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 SSPR 수행 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 관리자가 암호 재설정 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 새로 고침 토큰 해지 | 해지됨 | 해지됨 | 해지됨 | 해지됨 | 해지됨 |
| 관리자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 사용자에 대한 모든 새로 고침 토큰 해지 | 해지됨 | 해지됨 |해지됨 | 해지됨 | 해지됨 |
| 웹에서 [Single Sign-Out](v2-protocols-oidc.md#single-sign-out) | 해지됨 | 활성 상태 | 해지됨 | 활성 상태 | 활성 상태 |

## <a name="next-steps"></a>다음 단계

* [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)에 대해 알아보기
* 기본 새로 고침 토큰에 대한 자세한 내용은 [기본 새로 고침 토큰](../devices/concept-primary-refresh-token.md)을 참조하세요.
