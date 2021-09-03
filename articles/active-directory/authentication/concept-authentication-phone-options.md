---
title: 전화 인증 방법 - Azure Active Directory
description: Azure Active Directory에서 전화 인증 방법을 사용하여 로그인 이벤트를 더 개선하고 안전하게 만드는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1bb9c2ad21469ad6b658795f29361760d4bcf13
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536169"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Azure Active Directory의 인증 방법 - 휴대폰 옵션

문자 메시지를 사용하는 직접 인증의 경우 [SMS 기반 인증에 대해 사용자를 구성하고 사용하도록 설정](howto-authentication-sms-signin.md)할 수 있습니다. SMS 기반 로그인은 일선 작업자에게 적합합니다. SMS 기반 로그인을 사용하면 사용자가 애플리케이션 및 서비스에 액세스하기 위해 사용자 이름과 암호를 몰라도 됩니다. 대신 사용자가 등록된 휴대폰 번호를 입력하고 확인 코드를 포함하는 문자 메시지를 받은 후 로그인 인터페이스에 입력합니다.

사용자는 Azure AD Multi-Factor Authentication 또는 SSPR(셀프 서비스 암호 재설정) 중에 사용되는 인증의 보조 형식처럼 휴대폰 또는 사무실 전화를 사용하여 자신을 확인할 수도 있습니다. 

> [!NOTE]
> 평가판 구독이 있는 Azure AD 테넌트에는 전화 통화 확인을 사용할 수 없습니다.  예를 들어 평가판 EMS 라이선스에 등록하면 전화 통화 확인 기능이 제공되지 않습니다.

올바르게 작동하려면 전화번호가 *+CountryCode PhoneNumber* 형식으로 저장되어야 합니다(예: *+1 4251234567*).

> [!NOTE]
> 국가/지역 코드와 전화번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 내선 번호를 지원하지 않습니다. *+1 4251234567X12345* 형식에서도 전화를 걸기 전에 내선 번호가 제거됩니다.

## <a name="mobile-phone-verification"></a>휴대폰 확인

Azure AD Multi-Factor Authentication 또는 SSPR의 경우 사용자는 로그인 인터페이스에 입력할 확인 코드가 포함된 문자 메시지를 수신할지, 아니면 전화를 받을지를 선택할 수 있습니다.

사용자가 자신의 휴대폰 번호를 디렉터리에 표시하지 않는 대신 암호 재설정에는 사용하도록 하려면 관리자가 디렉터리에 해당 휴대폰 번호를 채우지 않아야 합니다. 대신 사용자가 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 조합된 보안 정보 등록을 통해 **인증 전화** 특성을 채워야 합니다. 관리자는 사용자의 프로필에서 이 정보를 볼 수 있지만 다른 곳에 게시되지는 않습니다.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="전화번호가 채워진 인증 방법을 보여 주는 Azure Portal의 스크린샷":::

Microsoft는 동일한 번호를 사용한 일관적인 SMS 또는 음성 기반 Azure AD Multi-Factor Authentication 프롬프트 전송을 보장하지 않습니다. 사용자를 위해 SMS 이행성을 향상하기 위한 조정 작업을 수시로 진행하면서 언제든지 짧은 코드를 추가하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 이외의 국가/지역에는 짧은 코드를 지원하지 않습니다.

### <a name="text-message-verification"></a>문자 메시지 확인

SSPR 또는 Azure AD Multi-Factor Authentication 중에 문자 메시지를 확인하면 확인 코드를 포함한 SMS가 휴대폰 번호로 전송됩니다. 로그인 프로세스를 완료하려면 제공된 확인 코드를 로그인 인터페이스에 입력합니다.

### <a name="phone-call-verification"></a>전화 통화 확인

SSPR 또는 Azure AD Multi-Factor Authentication 중에 전화 통화 확인을 사용하면 사용자가 등록한 전화번호로 자동 음성 전화가 걸려옵니다. 로그인 프로세스를 완료하려면 키패드에서 #을 누르라는 메시지가 사용자에게 표시됩니다.

## <a name="office-phone-verification"></a>사무실 전화 확인

SSPR 또는 Azure AD Multi-Factor Authentication 중에 전화 통화 확인을 사용하면 사용자가 등록한 전화번호로 자동 음성 전화가 걸려옵니다. 로그인 프로세스를 완료하려면 키패드에서 #을 누르라는 메시지가 사용자에게 표시됩니다. 

## <a name="troubleshooting-phone-options"></a>전화 옵션 문제 해결

Azure AD에 관한 전화 인증에 문제가 있는 경우 다음 문제 해결 단계를 검토합니다.

* 로그인하는 동안 “인증 통화 제한에 도달했습니다.” 또는 “텍스트 인증 코드 제한에 도달했습니다.”라는 오류 메시지가 표시됩니다.
   * Microsoft는 동일한 사용자가 짧은 시간 동안 수행하는 반복적인 인증 시도를 제한할 수 있습니다. Microsoft Authenticator 또는 확인 코드에는 이러한 제한이 적용되지 않습니다. 이러한 제한에 도달하면 Authenticator 앱, 확인 코드를 사용하거나 몇 분 후에 다시 로그인해 보면 됩니다.
* 로그인 중 “죄송합니다. 계정을 인증하는 동안 문제가 발생했습니다.” 오류 메시지
   * Microsoft는 음성 또는 SMS 인증 시도 실패 횟수가 너무 많을 경우 동일한 사용자, 전화 번호 또는 조직에서 수행하는 음성 또는 SMS 인증 시도를 제한하거나 차단할 수 있습니다. 이 오류가 발생하는 경우 Authenticator 앱 또는 확인 코드와 같은 다른 방법을 시도하거나 관리자에게 문의하여 지원을 받을 수 있습니다.
* 단일 디바이스에서 차단된 발신자 ID입니다.
   * 디바이스에 구성된 모든 차단된 숫자를 검토합니다.
* 전화번호 또는 국가/지역 코드가 잘못되었거나 개인 전화번호와 회사 전화번호 간에 혼란이 있습니다.
   * 사용자 개체 및 구성된 인증 방법의 문제를 해결합니다. 올바른 전화번호가 등록되었는지 확인합니다.
* 잘못된 PIN을 입력했습니다.
   * 사용자가 자신의 계정에 등록된 대로 올바른 PIN을 사용했는지 확인합니다(MFA 서버 사용자에게만 해당).
* 통화가 음성 사서함으로 전달됩니다.
   * 사용자의 전화기가 켜져 있고 사용자의 지역에서 해당 서비스를 사용할 수 있는지 확인하거나 대체 방법을 사용합니다.
* 사용자가 차단됨
   * Azure AD 관리자가 Azure Portal에서 사용자의 차단을 해제하도록 합니다.
* 디바이스가 SMS에 가입되지 않았습니다.
   * 사용자가 디바이스에서 방법을 변경하거나 SMS를 활성화하도록 합니다.
* 검색된 휴대폰 입력 없음, DTMF 톤 누락 문제, 여러 디바이스에서 차단된 발신자 ID, 여러 디바이스에서 차단된 SMS 등의 결함 있는 통신 공급자
   * Microsoft는 여러 통신 공급자를 사용하여 인증을 위한 전화 통화와 SMS 메시지를 라우팅합니다. 위의 문제가 발생한 경우 사용자가 5분 이내에 5번 이상 방법을 시도하도록 하고 Microsoft 지원에 문의할 때 해당 사용자의 정보를 제공하도록 합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

SSPR 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

MFA 옵션에 대해 자세히 알아보려면 [Azure AD Multi-Factor Authentication 작동 방법][concept-mfa]을 참조하세요.

[Microsoft Graph REST API 베타](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)를 사용하는 인증 구성 방법에 대해 자세히 알아보세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
