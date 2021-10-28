---
title: Azure AD ID 보호를 사용하는 사용자 환경
description: Azure AD ID 보호의 사용자 환경
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 484136b1c01cf93515971a42030eacfdda51715e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784169"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD ID 보호를 사용하는 사용자 환경

Azure Active Directory ID 보호를 사용하여 다음을 수행할 수 있습니다.

* 사용자에게 Azure AD MFA(Multi-Factor Authentication)에 등록하도록 요구
* 위험한 로그인 및 손상된 사용자의 수정 자동화

모든 ID 보호 정책은 사용자의 로그인 환경에 영향을 줍니다. 사용자가 Azure AD MFA 및 셀프 서비스 암호 재설정과 같은 도구에 등록하고 사용할 수 있도록 하면 영향을 줄일 수 있습니다. 이러한 도구는 적절한 정책 선택과 함께 사용자에게 필요할 때 자체 수정 옵션을 제공합니다.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 등록

다단계 인증 등록을 요구하고 모든 사용자를 대상으로 하는 ID 보호 정책을 사용하도록 설정하면 사용자가 나중에 Azure AD MFA를 사용하여 스스로 수정할 수 있습니다. 이 정책을 구성하면 사용자에게 등록을 선택할 수 있는 14일 기간을 제공하고 기간이 끝나면 등록이 강제로 적용됩니다. 사용자 환경에 대한 개요가 아래에 설명되어 있습니다. 자세한 내용은 [2단계 인증 및 회사 또는 학교 계정에 대한 개요](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc) 문서의 최종 사용자 설명서에서 찾을 수 있습니다.

### <a name="registration-interrupt"></a>등록 인터럽트

1. Azure AD 통합 애플리케이션에 로그인하면 사용자는 다단계 인증에 대해 계정을 설정하기 위한 요구 사항에 대한 알림을 받습니다. 이 정책은 새로운 디바이스를 사용하는 새로운 사용자를 위한 Windows 10 기본 환경에서도 트리거됩니다.
   
    ![추가 정보 필요](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 단계별 단계를 완료하여 Azure AD Multi-Factor Authentication을 등록하고 로그인을 완료합니다.

## <a name="risky-sign-in-remediation"></a>위험한 로그인 수정

관리자가 로그인 위험에 대한 정책을 구성한 경우 영향을 받는 사용자가 로그인하려고 할 때 알림이 제공되고 정책 위험 수준이 트리거됩니다. 

### <a name="risky-sign-in-self-remediation"></a>위험한 로그인 자체 수정

1. 사용자는 새 위치, 디바이스 또는 앱에서 로그인과 같은 해당 로그인에 대해 비정상적인 점이 검색되었음을 통보받습니다.
   
    ![비정상적인 프롬프트](./media/concept-identity-protection-user-experience/120.png)

1. 사용자는 이전에 등록한 방법 중 하나로 Azure AD MFA를 완료하여 ID를 증명해야 합니다. 

### <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단하도록 선택할 수 있습니다. 차단을 해제하려면 최종 사용자는 IT 직원에게 문의해야 하고 또는 익숙한 위치 또는 디바이스에서 로그인을 시도할 수 있습니다. 다단계 인증을 수행하는 자체 수정은 이 경우에 불가능합니다.

![로그인 위험 정책에 의해 차단됨](./media/concept-identity-protection-user-experience/200.png)

IT 직원은 사용자가 다시 로그인할 수 있도록 [사용자 차단 해제](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) 섹션의 지침을 따를 수 있습니다.

## <a name="risky-user-remediation"></a>위험한 사용자 수정

사용자 위험 정책이 구성된 경우 사용자 위험 수준 손상 확률을 충족하는 사용자는 사용자 손상 복구 흐름을 통과해야 로그인할 수 있습니다. 

### <a name="risky-user-self-remediation"></a>위험한 사용자 자체 수정

1. 사용자는 해당 계정 보안이 의심스러운 작업 또는 유출된 자격 증명으로 인해 위험에 노출되었다는 알림을 받습니다.
   
    ![수정](./media/concept-identity-protection-user-experience/101.png)

1. 사용자는 이전에 등록한 방법 중 하나로 Azure AD MFA를 완료하여 ID를 증명해야 합니다. 
1. 마지막으로, 다른 사람이 해당 계정에 액세스했을 수 있으므로 사용자는 셀프 서비스 암호 재설정을 사용하여 해당 암호를 변경하도록 강제됩니다.

## <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단하도록 선택할 수 있습니다. 차단을 해제하려면 최종 사용자가 IT 직원에게 문의해야 합니다. 다단계 인증 및 셀프 서비스 암호 재설정을 수행하는 자체 수정은 이 경우 불가능합니다.

![사용자 위험 정책에 의해 차단됨](./media/concept-identity-protection-user-experience/104.png)

IT 직원은 사용자가 다시 로그인할 수 있도록 [사용자 차단 해제](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) 섹션의 지침을 따를 수 있습니다.

## <a name="see-also"></a>참고 항목

- [위험 수정 및 사용자 차단 해제](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory ID 보호](./overview-identity-protection.md)