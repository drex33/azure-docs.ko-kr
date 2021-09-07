---
title: MFA 등록 정책 구성 - Azure Active Directory ID 보호
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2bfaef8f00f6246dfef17145666c817ed26d9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567237"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>방법: Azure AD Multi-Factor Authentication 등록 정책 구성

Azure AD ID 보호는 어떤 최신 인증 앱에 로그인하는지 관계없이 MFA 등록을 요구하도록 조건부 액세스 정책을 구성하여 Azure AD MFA(Multi-Factor Authentication) 등록 롤아웃을 관리하는 데 도움이 됩니다.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Azure AD Multi-Factor Authentication 등록 정책이란?

Azure AD Multi-Factor Authentication은 사용자 이름과 암호 이외에 추가 기능을 사용하는 사용자를 확인하는 수단을 제공합니다. 또한 사용자 로그인에 대한 두 번째 보안 계층을 제공합니다. 사용자가 MFA 프롬프트에 응답할 수 있으려면 먼저 Azure AD Multi-Factor Authentication에 등록해야 합니다.

다음과 같은 이유로, 사용자 로그인에 대해 Azure AD Multi-Factor Authentication을 요구하는 것이 좋습니다.

- 다양한 확인 옵션을 통해 강력한 인증을 제공합니다.
- 조직에서 ID 보호의 위험 검색으로부터 자체 수정할 수 있도록 준비하는 데 중요한 역할을 합니다.

Azure Multi-Factor Authentication에 대한 자세한 내용은 [Azure AD Multi-Factor Authentication이란?](../authentication/howto-mfa-getstarted.md)을 참조하세요.

## <a name="policy-configuration"></a>정책 구성

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory** > **보안** > **ID 보호** > **MFA 등록 정책** 으로 이동합니다.
   1. **할당** 에서
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자** 또는 **개인 및 그룹 선택** 을 선택할 수 있습니다.
         1. 원하는 경우 정책에서 사용자를 제외할 수 있습니다.
   1. **정책 적용** - **설정**
   1. **저장**

## <a name="user-experience"></a>사용자 환경

Azure Active Directory Identity Protection은 사용자가 다음에 대화형으로 로그인할 때 등록하라는 메시지를 표시하고 사용자에게 등록을 완료하는 데 14일이 주어집니다. 이 14일 기간 동안 등록을 무시할 수 있지만 MFA가 조건으로 필요하지 않은 경우 등록을 무시할 수 있지만, 기간이 끝나면 등록해야만 로그인 프로세스를 완료할 수 있습니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Azure AD ID 보호를 사용하는 로그인 환경](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>다음 단계

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure AD Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)
