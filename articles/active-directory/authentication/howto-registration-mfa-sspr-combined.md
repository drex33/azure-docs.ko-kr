---
title: Azure Active Directory에서 결합된 보안 정보 등록 사용
description: Azure AD Multi-Factor Authentication과 셀프 서비스 암호 재설정 등록을 결합하여 최종 사용자 환경을 간소화하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/28/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82eb6494041ffab4f70eb546d24f8a24729b626
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090370"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory에서 결합된 보안 정보 등록 사용

결합된 등록 전에는 사용자가 Azure AD Multi-Factor Authentication 및 SSPR(셀프 서비스 암호 재설정)에 대한 인증 방법을 별도로 등록했습니다. 당황스럽지만 Azure AD Multi-Factor Authentication 및 SSPR에 유사한 방법이 사용되지만 두 기능을 모두 등록해야 했습니다. 이제는 결합된 등록을 통해 한 번 등록하고 Azure AD Multi-Factor Authentication 및 SSPR의 이점을 모두 활용할 수 있습니다.

> [!NOTE]
> 2020년 8월 15일부터 모든 새 Azure AD 테넌트가 결합된 등록을 자동으로 사용하도록 설정됩니다. 이 날짜 이후에 생성되는 테넌트는 레거시 등록 워크플로를 사용할 수 없습니다.

새 환경을 사용하도록 설정하기 전에 기능과 효과를 이해하려면 [결합된 보안 정보 등록 개념](concept-registration-mfa-sspr-combined.md)을 참조하세요.

![결합된 보안 정보 등록 고급 환경](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>결합 등록 사용

결합된 등록을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 사용자 관리자 또는 전역 관리자로 Azure Portal에 로그인합니다.
2. **Azure Active Directory** > **사용자 설정** > **사용자 기능 미리 보기 설정 관리** 로 이동합니다.
3. **사용자는 결합된 보안 정보 등록 환경** 에서 **선택된** 사용자 그룹 또는 **모든** 사용자에 대해 이 기능을 사용하도록 선택할 수 있습니다.

   ![사용자에 대해 결합된 보안 정보 환경 사용](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> 결합된 등록을 사용하도록 설정한 후에는 새로운 환경을 통해 전화 번호나 모바일 앱을 등록한 사용자는 Azure AD Multi-Factor Authentication 및 SSPR 정책에서 해당 방법을 사용하도록 설정된 경우 Azure AD Multi-Factor Authentication 및 SSPR을 사용할 수 있습니다.
>
> 그런 다음, 이 환경을 비활성화하면 이전 SSPR 등록 페이지(`https://aka.ms/ssprsetup`)로 이동하는 사용자에게 페이지에 액세스하려면 다단계 인증을 수행해야 한다는 메시지가 표시됩니다.

Internet Explorer에서 *사이트-영역 할당 목록* 을 구성한 경우 다음 사이트가 동일한 영역에 있어야 합니다.

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://Login.windows.net](https://login.windows.net)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>결합된 등록에 대한 조건부 액세스 정책

사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록하는 시기와 방법을 보호하기 위해 조건부 액세스 정책에서 사용자 작업을 사용할 수 있습니다. 이 기능은 HR 온보딩 중에 신뢰할 수 있는 네트워크 위치와 같은 중앙 위치에서 Azure AD Multi-Factor Authentication 및 SSPR에 사용자를 등록하려는 조직에서 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 이 정책은 사용자가 결합된 등록 페이지에 액세스하는 경우에만 적용됩니다. 이 정책은 사용자가 다른 애플리케이션에 액세스할 때 MFA 등록을 적용하지 않습니다.
>
> [Azure Identity Protection - MFA 정책 구성](../identity-protection/howto-identity-protection-configure-mfa-policy.md)을 사용하여 MFA 등록 정책을 만들 수 있습니다.

조건부 액세스에서 신뢰할 수 있는 위치를 만드는 방법에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건이란?](../conditional-access/location-condition.md#named-locations)을 참조하세요.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구하는 정책 만들기

결합된 등록 환경을 사용하여 등록을 시도하고 신뢰할 수 있는 네트워크로 표시된 위치에서 연결하지 않는 한 액세스를 차단하는 모든 선택된 사용자에게 적용되는 정책을 만들려면 다음 단계를 완료합니다.

1. **Azure Portal** 에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **+ 새 정책** 을 선택합니다.
1. 이 정책의 이름을 입력합니다(예: *신뢰할 수 있는 네트워크의 결합된 보안 정보 등록)* .
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다. 이 정책을 적용할 사용자 및 그룹을 선택한 다음, **완료** 를 선택합니다.

   > [!WARNING]
   > 사용자에 대해 결합된 등록을 사용하도록 설정해야 합니다.

1. **클라우드 앱 또는 작업** 에서 **사용자 작업** 을 선택합니다. **보안 정보 등록** 을 선택하고 **완료** 를 선택합니다.

    ![보안 정보 등록을 제어하기 위한 조건부 액세스 정책 만들기](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **조건** > **위치** 에서 다음 옵션을 구성합니다.
   1. **예** 를 구성합니다.
   1. **임의의 위치** 를 포함합니다.
   1. **신뢰할 수 있는 모든 위치** 를 제외합니다.
1. *위치* 창에서 **완료** 를 선택한 다음, *조건* 창에서 **완료** 를 선택 합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 차단**, **선택** 을 선택합니다.
1. **정책 사용** 을 **켜기** 로 설정합니다.
1. 정책을 마무리하려면 **만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [결합된 보안 정보 등록 문제 해결](howto-registration-mfa-sspr-combined-troubleshoot.md)을 참조하거나 [Azure Active Directory 조건부 액세스의 위치 조건이란?](../conditional-access/location-condition.md)을 살펴보세요.

사용자가 결합된 등록을 사용하도록 설정하면 [셀프 서비스 암호 재설정을 사용하도록 설정](tutorial-enable-sspr.md)하고 [Azure AD Multi-Factor Authentication을 사용하도록 설정](tutorial-enable-azure-mfa.md)할 수 있습니다.

필요한 경우 [강제로 사용자가 인증 방법을 다시 등록하도록 하는 방법](howto-mfa-userdevicesettings.md#manage-user-authentication-options)에 대해 알아봅니다.
