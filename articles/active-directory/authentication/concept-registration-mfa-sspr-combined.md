---
title: SSPR 및 Azure AD Multi-Factor Authentication에 대해 결합된 등록 - Azure Active Directory
description: 사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록할 수 있도록 하는 Azure Active Directory에 대해 결합된 등록 환경에 대해 알아봅니다
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52e50aa4097dbb405b0c37a96426440fdf002810
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763142"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory 개요에 대해 결합된 보안 정보 등록

결합된 등록 전에는 사용자가 Azure AD Multi-Factor Authentication 및 SSPR(셀프 서비스 암호 재설정)에 대해 인증 방법을 별도로 등록했습니다. Azure Multi-Factor Authentication 및 SSPR에 유사한 방법이 사용된다는 사실이 혼란스러웠지만, 사람들은 두 기능을 모두 등록해야 했습니다. 이제 사용자는 결합된 등록을 통해 한 번 등록하고 Azure Multi-Factor Authentication 및 SSPR의 혜택을 모두 얻을 수 있습니다. [Azure AD에서 SSPR을 사용하도록 설정하고 구성하는 방법](https://www.youtube.com/watch?v=rA8TvhNcCvQ)에 대해 이 비디오를 권장합니다.

> [!NOTE]
> 2020년 8월 15일부터 모든 새 Azure AD 테넌트는 결합된 등록을 사용하도록 자동으로 설정됩니다. 

이 문서에서는 결합된 보안 등록을 간략하게 설명합니다. 결합된 보안 등록을 시작하려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [결합된 보안 등록 사용](howto-registration-mfa-sspr-combined.md)

![사용자에 대해 등록된 보안 정보를 표시하는 내 계정](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

새 환경을 사용하도록 설정하기 전에 이 기능의 기능과 영향을 이해할 수 있도록 이 관리자 중심 설명서 및 사용자 중심의 설명서를 검토합니다. 학습은 [사용자용 설명 문서](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8)를 기반으로 하여 사용자가 새로운 환경을 준비하도록 하고 성공적인 출시를 보장하는 데 도움이 됩니다.

Azure AD 결합 보안 정보 등록은 Azure US Government에서 사용할 수 있지만 Azure 독일 또는 Azure 중국 21Vianet에서는 사용할 수 없습니다.

> [!IMPORTANT]
> 원본 미리 보기와 강화된 결합 등록 환경 모두에 대해 사용하도록 설정된 사용자는 새로운 동작을 볼 수 있습니다. 두 환경 모두에 대해 사용하도록 설정된 사용자에게는 내 계정 환경만 표시됩니다. *내 계정* 은 결합된 등록의 모양과 느낌을 맞추고 사용자에게 원활한 환경을 제공 합니다. 사용자는 [https://myaccount.microsoft.com](https://myaccount.microsoft.com)로 이동하여 내 계정을 볼 수 있습니다.
>
> 보안 정보 옵션에 액세스하려고 할 때 "죄송하지만 로그인할 수 없습니다"와 같은 오류 메시지가 나타날 수 있습니다. 웹 브라우저에서 제3자 쿠키를 차단하는 구성 또는 그룹 정책 개체가 없는지 확인합니다.

*내 계정* 페이지는 페이지에 액세스하는 컴퓨터의 언어 설정에 따라 지역화됩니다. Microsoft는 브라우저 캐시에서 가장 최근에 사용된 언어를 저장 하므로 페이지에 액세스하려는 이후 시도는 마지막으로 사용된 언어로 계속 렌더링됩니다. 캐시를 지우면 페이지가 다시 렌더링됩니다.

특정 언어를 강제로 사용하려는 경우 URL의 끝에 `?lng=<language>`를 추가할 수 있습니다. 여기서 `<language>`은 렌더링할 언어의 코드입니다.

![SSPR 또는 기타 보안 확인 메서드 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>결합된 등록에서 사용할 수 있는 메서드

결합된 등록은 다음과 같은 인증 방법 및 작업을 지원합니다.

| 방법 | 등록 | 변경 | DELETE |
| --- | --- | --- | --- |
| Microsoft Authenticator | 예(최대 5개) | 예 | 예 |
| 기타 인증자 앱 | 예(최대 5개) | 예 | 예 |
| 하드웨어 토큰 | 예 | 아니요 | 예 |
| Phone | 예 | 예 | 예 |
| 대체 전화 | 예 | 예 | 예 |
| 사무실 전화 | 예 | 예 | 예 |
| Email | 예 | 예 | 예 |
| 본인 확인 질문 | 예 | 아니요 | 예 |
| 앱 암호 | 예 | 아니요 | 예 |
| FIDO2 보안 키<br />*[보안 정보](https://mysignins.microsoft.com/security-info) 페이지에서만 관리 모드*| 예 | 예 | 예 |

> [!NOTE]
> 앱 암호는 다단계 인증이 적용된 사용자만 사용할 수 있습니다. 조건부 액세스 정책을 통해 다단계 인증을 사용하도록 설정된 사용자는 앱 암호를 사용할 수 없습니다.

사용자는 다음 옵션 중 하나를 기본 다단계 인증 방법으로 설정할 수 있습니다.

- Microsoft Authenticator - 푸시 알림
- Authenticator 앱 또는 하드웨어 토큰 - 코드
- 전화 통화
- 문자 메시지

타사 인증자 앱은 푸시 알림을 제공하지 않습니다. Azure AD에 더 많은 인증 방법을 계속 추가하고 있기 때문에 이러한 방법은 결합된 등록에서 사용할 수 있습니다.

## <a name="combined-registration-modes"></a>결합된 등록 모드

결합된 등록에는 인터럽트 및 관리의 두 가지 모드가 있습니다.

- **인터럽트 모드** 는 로그인 시 보안 정보를 등록하거나 새로 고침할 때 사용자에게 제공된 마법사와 같은 환경입니다.
- **관리 모드** 는 사용자 프로필의 일부이며 사용자가 보안 정보를 관리할 수 있도록 합니다.

두 모드 모두 다단계 인증에 사용할 수 있는 방법을 이전에 등록한 사용자는 보안 정보에 액세스하기 전에 다단계 인증을 수행해야 합니다. 사용자는 이전에 등록된 메서드를 계속 사용하기 전에 반드시 해당 정보를 확인해야 합니다. 



### <a name="interrupt-mode"></a>인터럽트 모드

결합된 등록은 Multi-Factor Authentication 및 SSPR 정책이 테넌트에 대해 사용하도록 설정된 경우 모두 준수됩니다. 이러한 정책은 로그인 하는 동안 사용자 등록이 인터럽트되었는지 여부와 어떤 메서드를 등록에 사용할 수 있는지 컨트롤합니다. SSPR 정책만 사용하도록 설정된 경우 사용자는 등록 중단을 건너뛰고 나중에 완료할 수 있습니다.

다음은 사용자에게 보안 정보를 등록하거나 새로 고침하라는 메시지가 표시될 수 있는 샘플 시나리오입니다.

- *ID 보호를 통해 적용되는 다단계 인증 등록:* 로그인 중 사용자에게 등록을 요청합니다. 다단계 인증 방법과 SSPR 메서드를 등록합니다(사용자가 SSPR를 사용하도록 설정된 경우).
- *사용자별 다단계 인증을 통해 적용되는 다단계 인증 등록:* 로그인 중 사용자에게 등록을 요청합니다. 다단계 인증 방법과 SSPR 메서드를 등록합니다(사용자가 SSPR를 사용하도록 설정된 경우).
- *조건부 액세스 또는 기타 정책을 통해 적용되는 다단계 인증 등록:* 사용자가 다단계 인증이 필요한 리소스를 사용하는 경우 등록 요청합니다. 다단계 인증 방법과 SSPR 메서드를 등록합니다(사용자가 SSPR를 사용하도록 설정된 경우).
- *SSPR 등록 적용:* 로그인 중 사용자에게 등록을 요청합니다. SSPR 메서드만 등록합니다.
- *SSPR 새로 고침 적용:* 사용자는 관리자가 설정한 간격에 따라 보안 정보를 검토해야 합니다. 사용자에게 정보가 표시되며, 필요한 경우 현재 정보를 확인 하거나 변경할 수 있습니다.

등록이 적용되면 사용자는 대부분의 보안 수준에서 다단계 인증 및 SSPR 정책을 준수하는 데 필요한 최소 메서드 수를 표시 합니다.

다음 예제 시나리오를 고려해 보세요.

- 사용자가 SSPR을 사용하도록 설정되어 있습니다. SSPR 정책은 두 가지 방법으로 다시 설정해야 하며, Authenticator 앱, 메일, 휴대폰을 사용하도록 설정했습니다.
- 사용자가 등록하도록 선택하는 경우 다음 두 가지 방법이 필요합니다.
   - 사용자는 기본값으로 Authenticator 앱 및 전화에 표시됩니다.
   - 사용자는 Authenticator 앱 또는 휴대폰 대신 메일을 등록하도록 선택할 수 있습니다.

다음 순서도는 로그인 중 등록이 중단된 경우 사용자에게 어떤 메서드가 표시되는지 설명합니다.

![결합된 보안 정보 순서도](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

다단계 인증과 SSPR을 모두 사용하는 경우 다단계 인증 등록을 적용하는 것이 좋습니다.

SSPR 정책에 따라 사용자가 정기적으로 보안 정보를 검토해야 하는 경우 로그인하는 동안 사용자가 중단되고 등록된 모든 메서드가 표시됩니다. 현재 정보가 최신 정보인지 확인하거나 필요한 경우 변경할 수 있습니다. 사용자는 이 페이지에 액세스할 때 다단계 인증을 수행해야 합니다.

### <a name="manage-mode"></a>모드 관리

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)으로 가거나 내 계정에서 **보안 정보** 를 선택하여 관리 모드에 액세스할 수 있습니다. 이 위치에서 사용자는 메서드를 추가하고, 기존 메서드를 삭제하거나 변경하고, 기본 메서드를 변경하는 등의 작업을 수행할 수 있습니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인하는 동안 보안 정보 설정

관리자가 등록을 적용했습니다.

사용자가 필요한 보안 정보를 모두 설정하지 않았으며 Azure Portal로 이동합니다. 사용자 이름과 암호를 입력하면 보안 정보를 설정하라는 메시지가 표시됩니다. 그런 다음, 사용자는 마법사에 표시된 단계에 따라 필요한 보안 정보를 설정합니다. 설정에서 허용하는 경우 사용자는 기본값으로 표시된 방법 이외의 방법을 설정하도록 선택할 수 있습니다. 마법사를 완료한 후 사용자는 설정한 방법과 다단계 인증에 대한 기본 방법을 검토합니다. 설정 프로세스를 완료하기 위해 사용자는 정보를 확인하고 Azure Portal로 계속 진행합니다.

### <a name="set-up-security-info-from-my-account"></a>내 계정에서 보안 정보 설정

관리자가 등록을 적용하지 않았습니다.

필요한 보안 정보를 아직 모두 설정하지 않은 사용자는 [https://myaccount.microsoft.com](https://myaccount.microsoft.com)로 이동합니다. 사용자는 왼쪽 창에서 **보안 정보** 를 선택합니다. 이 위치에서 사용자는 메서드를 추가하도록 선택하고, 사용 가능한 메서드 중 하나를 선택한 후 해당 메서드를 설정하는 단계를 따릅니다. 완료되면 사용자는 보안 정보 페이지에 설정된 메서드를 볼 수 있습니다.

### <a name="delete-security-info-from-my-account"></a>내 계정에서 보안 정보 삭제

이전에 하나 이상의 메서드를 설정한 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동합니다. 사용자는 이전에 등록된 메서드 중 하나를 삭제하도록 선택합니다. 완료되면 사용자는 보안 정보 페이지에서 해당 메서드를 더 이상 확인할 수 없습니다.

### <a name="change-the-default-method-from-my-account"></a>내 계정에서 기본 메서드 변경

이전에 다단계 인증에 사용할 수 있는 방법을 하나 이상 설정한 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동합니다. 사용자는 현재 기본 메서드를 다른 기본 메서드로 변경합니다. 완료되면 사용자는 보안 정보 페이지에서 새 기본 메서드를 볼 수 있습니다.

### <a name="switch-directory"></a>디렉터리 전환

B2B 사용자와 같은 외부 ID는 타사 테넌트의 보안 등록 정보를 변경하기 위해 디렉터리를 전환해야 할 수 있습니다. 또한 리소스 테넌트에 액세스하는 사용자는 홈 테넌트의 설정을 변경할 때 혼동을 일으킬 수 있지만 리소스 테넌트에 반영된 변경 사항을 볼 수 없습니다. 

예를 들어 사용자가 홈 테넌트에 로그인하기 위한 기본 인증으로 Microsoft Authenticator 앱 푸시 알림을 설정하고, 또 다른 옵션으로 SMS/텍스트도 있습니다. 이 사용자는 리소스 테넌트에서 SMS/텍스트 옵션으로도 구성됩니다. 이 사용자가 홈 테넌트의 인증 옵션 중 하나로서의 SMS/문자를 제거하면 리소스 테넌트에 대한 액세스가 SMS/문자 메시지에 응답하도록 요청할 때 혼동을 받게 됩니다. 

Azure Portal에서 디렉터리를 전환하려면 오른쪽 상단에서 사용자 계정 이름을 클릭하고 **디렉터리 전환** 을 클릭합니다.

![외부 사용자는 디렉터리를 전환할 수 있습니다.](media/concept-registration-mfa-sspr-combined/switch-directory.png)

## <a name="next-steps"></a>다음 단계

시작하려면 [셀프 서비스 암호 재설정 사용](tutorial-enable-sspr.md) 및 [Azure AD Multi-Factor Authentication 사용](tutorial-enable-azure-mfa.md) 자습서를 참조하세요.

[테넌트에서 결합된 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md)하거나 [사용자가 인증 방법을 다시 등록](howto-mfa-userdevicesettings.md#manage-user-authentication-options)하도록 하는 방법에 대해 알아봅니다.

[Azure AD Multi-Factor Authentication 및 SSPR에 대해 사용 가능한 방법](concept-authentication-methods.md)을 검토할 수도 있습니다.