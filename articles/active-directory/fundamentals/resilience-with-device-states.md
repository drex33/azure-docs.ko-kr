---
title: Azure Active Directory의 디바이스 상태를 사용하여 복원력 빌드
description: 디바이스 상태를 사용하여 복원력을 빌드하는 설계자 및 IT 관리자를 위한 가이드
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9c22974ec3452df9597999eb62569a08eb85dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754404"
---
# <a name="build-resilience-with-device-states"></a>디바이스 상태를 사용하여 복원력 빌드

Azure AD에서 [디바이스 상태](../devices/overview.md)를 사용하도록 설정하여 관리자는 디바이스 상태에 따라 애플리케이션에 대한 액세스를 제어하는 [조건부 액세스 정책](../conditional-access/overview.md)을 작성할 수 있습니다. 디바이스의 추가 혜택은 리소스 액세스를 위한 강력한 인증 요구 사항을 충족하므로 추가 MFA 인증 요청을 줄이고 복원력을 개선한다는 것입니다. 

다음 순서도는 디바이스 상태를 사용하도록 설정하는 디바이스를 Azure AD에서 온보딩하는 다양한 방법을 보여 줍니다. 조직에서는 두 가지 이상의 방법을 사용할 수 있습니다.

![디바이스 상태 선택을 위한 순서도](./media/resilience-with-device-states/admin-resilience-devices.png)

[디바이스 상태](../devices/overview.md)를 사용하는 경우 대부분은 Single Sign-On 방식으로 [PRT(주 새로 고침 토큰)](../devices/concept-primary-refresh-token.md)를 통해 리소스에 로그인합니다. PRT는 사용자 및 디바이스에 대한 클레임을 포함하며 디바이스에서 애플리케이션에 액세스하기 위한 인증 토큰을 가져오는 데 사용할 수 있습니다. PRT는 14일 동안 유효하며 사용자가 디바이스를 적극적으로 사용하는 동안에는 계속 갱신되므로 사용자는 복원력을 얻게 됩니다. PRT는 몇 가지 방식으로 MFA(Multi-Factor Authentication) 클레임을 가져올 수도 있습니다. 자세한 내용은 [PRT가 MFA 클레임을 가져오는 경우](../devices/concept-primary-refresh-token.md)를 참조하세요.

## <a name="how-do-device-states-help"></a>디바이스 상태는 어떻게 도움이 되나요?

PRT를 사용하여 애플리케이션에 대한 액세스를 요청하는 경우 해당 디바이스, 세션 및 MFA 클레임이 Azure AD에서 신뢰됩니다. 관리자가 디바이스 기반 제어 또는 Multi-Factor Authentication 제어를 요구하는 정책을 만들 때 Multi-Factor Authentication을 시도하지 않고 디바이스 상태를 통해 정책 요구 사항을 충족할 수 있습니다. 사용자의 동일한 디바이스에서 추가 Multi-Factor Authentication 프롬프트가 표시되지 않습니다. 이를 통해 Azure MFA 서비스 중단 또는 로컬 통신 공급자 등의 종속성과 관련해서 복원력이 높아집니다.

## <a name="how-do-i-implement-device-states"></a>디바이스 상태는 어떻게 구현해야 하나요?

* 회사 소유의 Windows 디바이스에 대해 [하이브리드 Azure AD 조인](../devices/hybrid-azuread-join-plan.md) 및 [Azure AD 조인](../devices/azureadjoin-plan.md)을 사용하도록 설정하고 가능하면 조인되도록 해야 합니다. 가능하지 않은 경우 등록해야 합니다.

  조직에 이전 버전의 Windows가 있는 경우 Windows 10을 사용하도록 해당 디바이스를 업그레이드합니다.

* PRT를 사용하여 웹 애플리케이션에 대해 Seamless SSO가 설정된 [지원되는](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [확장](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb)에서 [Microsoft Edge](/deployedge/microsoft-edge-security-identity) 또는 Google Chrome을 사용하도록 사용자 브라우저 액세스를 표준화합니다.

* 개인 또는 회사 소유의 iOS 및 Android 디바이스에는 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 배포합니다. Multi-Factor Authentication 및 암호 없는 로그인 기능 외에도, Microsoft Authenticator 앱에서는 최종 사용자에게 표시되는 인증 프롬프트 수를 줄이면서 [조정된 인증](../develop/msal-android-single-sign-on.md)을 통해 네이티브 애플리케이션에서 Single Sign-On 방식으로 로그인할 수 있도록 합니다.

* 개인 또는 회사 소유의 iOS 및 Android 디바이스에 대해 [모바일 애플리케이션 관리](/mem/intune/apps/app-management)를 사용하여 인증 요청 수를 줄이면서 회사 리소스에 안전하게 액세스할 수 있습니다. 

* [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 사용합니다(미리 보기)](../develop/apple-sso-plugin.md). 그러면 디바이스가 등록되고 브라우저 및 네이티브 Azure AD 애플리케이션에서 SSO가 제공됩니다. 

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [자격 증명 관리를 사용하여 복원력 빌드](resilience-in-credentials.md)

* [CAE(지속적인 액세스 권한 평가)를 사용하여 복원력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용하여 응용 프로그램 액세스에서 복원력 빌드](resilience-on-premises-access.md)


개발자를 위한 복원력 리소스

* [애플리케이션에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)