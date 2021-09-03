---
title: 인증 방법 및 기능 - Azure Active Directory
description: 로그인 이벤트 개선 및 보안을 지원하는 데 사용할 수 있는 Azure Active Directory의 다양한 인증 방법 및 기능에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 5adc3bd8ef03b2613198518fc22284686c2bfee9
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730754"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법은 무엇인가요?

Microsoft는 Windows Hello, FIDO2 보안 키 및 Microsoft Authenticator 앱과 같은 암호 없는 인증 방법이 가장 안전한 로그인 환경을 제공하기 때문에 권장합니다. 사용자는 사용자 이름 및 암호와 같은 다른 일반적인 방법을 사용하여 로그인할 수 있지만 암호는 보다 안전한 인증 방법으로 대체해야 합니다.

![Azure AD의 장점 및 기본 설정 인증 방법에 관한 표](media/concept-authentication-methods/authentication-methods.png)

Azure AD MFA(Multi-Factor Authentication)는 사용자가 로그인할 때 암호만 사용하는 것보다 더 보안을 강화합니다. 푸시 알림에 응답하거나, 소프트웨어 또는 하드웨어 토큰에서 코드를 입력하거나, SMS 또는 전화 통화에 응답하는 등의 추가 인증 양식을 입력하라는 메시지가 사용자에게 표시될 수 있습니다.

사용자가 온보딩 환경을 간소화하고 MFA 및 SSPR(셀프 서비스 암호 재설정)에 등록하려면 [결합된 보안 정보 등록을 사용](howto-registration-mfa-sspr-combined.md)하는 것이 좋습니다. 복원력을 보장하기 위해 사용자에게 여러 인증 방법을 등록하도록 요구하는 것이 좋습니다. 사용자가 로그인 또는 SSPR 중에 한 가지 방법을 사용할 수 없는 경우 다른 방법으로 인증하도록 선택할 수 있습니다. 자세한 내용은 [Azure AD에서 복원력 있는 액세스 제어 관리 전략 만들기](concept-resilient-controls.md)를 참조하세요.

다음은 조직을 안전하게 유지하기 위해 가장 적합한 인증 방법을 선택하는 데 도움이 되는 [동영상](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be)입니다.

## <a name="authentication-method-strength-and-security"></a>인증 방법 장점 및 보안

조직에서 Azure AD Multi-Factor Authentication 같은 기능을 배포하는 경우 사용 가능한 인증 방법을 검토합니다. 보안, 유용성 및 가용성 측면에서 요구 사항을 충족하거나 초과하는 방법을 선택합니다. 가능한 경우 보안 수준이 가장 높은 인증 방법을 사용합니다.

다음 표에서는 사용 가능한 인증 방법의 보안 고려 사항을 간략하게 설명합니다. 가용성은 사용자가 Azure AD의 서비스 가용성이 아니라 인증 방법을 사용할 수 있음을 나타냅니다.

| 인증 방법          | 보안 | 사용 편의성 | 가용성 |
|--------------------------------|:--------:|:---------:|:------------:|
| 비즈니스용 Windows Hello     | 높음     | 높음      | 높음         |
| Microsoft Authenticator 앱    | 높음     | 높음      | 높음         |
| FIDO2 보안 키             | 높음     | 높음      | 높음         |
| OATH 하드웨어 토큰(미리 보기) | 중간   | 중간    | 높음         |
| OATH 소프트웨어 토큰           | 중간   | 중간    | 높음         |
| sms                            | 중간   | 높음      | 중간       |
| 음성                          | 중간   | 중간    | 중간       |
| 암호                       | 낮음      | 높음      | 높음         |

보안에 대한 최신 정보는 블로그 게시물을 확인하세요.

- [It's time to hang up on phone transports for authentication](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)(전화기를 사용한 인증은 이제 그만)
- [Authentication vulnerabilities and attack vectors](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)(인증 취약성 및 공격 벡터)

> [!TIP]
> 유연성과 유용성을 보장하기 위해 Microsoft Authenticator 앱을 사용하는 것이 좋습니다. 이 인증 방법은 암호 없음, MFA 푸시 알림 및 OATH 코드와 같은 최상의 사용자 환경과 여러 모드를 제공합니다.

## <a name="how-each-authentication-method-works"></a>각 인증 방법의 작동 방식

일부 인증 방법은 FIDO2 보안 키 또는 암호를 사용하는 것과 같이 애플리케이션 또는 디바이스에 로그인할 때 기본 요소로 사용할 수 있습니다. 다른 인증 방법은 Azure AD Multi-Factor Authentication 또는 SSPR을 사용하는 경우에만 보조 요소로 사용할 수 있습니다.

다음 표에서는 로그인 이벤트 중에 인증 방법을 사용할 수 있는 경우를 간략하게 설명합니다.

| 방법                         | Primary authentication(기본 인증) | 보조 인증  |
|--------------------------------|:----------------------:|:-------------------------:|
| 비즈니스용 Windows Hello     | 예                    | MFA                       |
| Microsoft Authenticator 앱    | 예                    | MFA 및 SSPR              |
| FIDO2 보안 키             | 예                    | MFA                       |
| OATH 하드웨어 토큰(미리 보기) | 예                     | MFA 및 SSPR              |
| OATH 소프트웨어 토큰           | 예                     | MFA 및 SSPR              |
| sms                            | 예                    | MFA 및 SSPR              |
| 음성 통화                     | 예                     | MFA 및 SSPR              |
| 암호                       | 예                    |                           |

모든 관련 인증 방법은 Azure Portal에서 구성하며 [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)를 점점 더 많이 사용하여 구성할 수 있습니다.

각 인증 방법의 작동 방식에 대한 자세한 내용은 다음의 개별 개념 문서를 참조하세요.

* [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator 앱](concept-authentication-authenticator-app.md)
* [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH 하드웨어 토큰(미리 보기)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH 소프트웨어 토큰](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS 로그인](howto-authentication-sms-signin.md) 및 [확인](concept-authentication-phone-options.md#mobile-phone-verification)
* [음성 통화 확인](concept-authentication-phone-options.md)
* 암호

> [!NOTE]
> Azure AD에서 암호는 대개 기본 인증 방법 중 하나입니다. 암호 인증 방법을 사용하지 않도록 설정할 수는 없습니다. 암호를 기본 인증 요소로 사용하는 경우 Azure AD Multi-Factor Authentication을 사용하여 로그인 이벤트의 보안을 강화합니다.

특정 시나리오에서 다음 추가 인증 방법을 사용할 수 있습니다.

* [앱 암호](howto-mfa-app-passwords.md) - 최신 인증을 지원하지 않는 이전 애플리케이션에 사용되며 사용자별 Azure AD Multi-Factor Authentication에 대해 구성할 수 있습니다.
* [보안 질문](concept-authentication-security-questions.md) - SSPR에만 사용됩니다.
* [메일 주소](concept-sspr-howitworks.md#authentication-methods) - SSPR에만 사용됩니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

SSPR 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

MFA 옵션에 대해 자세히 알아보려면 [Azure AD Multi-Factor Authentication 작동 방법][concept-mfa]을 참조하세요.

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)를 사용하는 인증 구성 방법에 대해 자세히 알아봅니다.

사용 중인 인증 방법을 검토하려면 [PowerShell을 사용한 Azure AD Multi-Factor Authentication 인증 방법 분석](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)을 참조하세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
