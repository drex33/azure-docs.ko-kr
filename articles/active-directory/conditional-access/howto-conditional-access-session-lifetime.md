---
title: 인증 세션 관리 구성 - Azure Active Directory
description: 사용자 로그인 빈도 및 브라우저 세션 지속성을 포함하는 Azure AD 인증 세션 구성을 사용자 지정합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50c168bffad24646aad0badcdb849d9d3408725b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566387"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>조건부 액세스로 인증 세션 관리 구성

복잡한 배포에서는 조직에서 인증 세션을 제한해야 할 수 있습니다. 시나리오에는 다음이 포함될 수 있습니다.

* 관리되지 않는 디바이스 또는 공유 디바이스에 대한 리소스 액세스
* 외부 네트워크를 통한 중요 정보에 액세스
* 영향력이 높은 사용자
* 중요한 비즈니스 애플리케이션

조건부 액세스 제어를 통해 모든 사용자에게 영향을 주지 않고 조직 내의 특정 사용 사례를 대상으로 하는 정책을 만들 수 있습니다.

정책을 구성하는 방법에 대한 세부 정보를 살펴보기 전에 기본 구성을 검토해 보겠습니다.

## <a name="user-sign-in-frequency"></a>사용자 로그인 빈도

로그인 빈도는 리소스에 액세스하려고 할 때 사용자에게 다시 로그인하라는 메시지가 표시되는 기간을 정의합니다.

사용자 로그인 빈도에 대한 Azure AD(Azure Active Directory) 기본 구성은 90일의 이동 기간입니다. 사용자에게 자격 증명을 요청하는 것은 중요한 것처럼 보일 수 있지만, 생각하지 않고 자격 증명을 입력하도록 학습된 사용자가 실수로 자격 증명을 악의적인 자격 증명 프롬프트에 제공할 수 있는 역효과도 불러올 수 있습니다.

사용자가 다시 로그인하도록 요청하지 않는 것이 걱정스러울 수 있지만 실제로는 IT 정책을 위반하면 세션이 해지됩니다. 일부 이러한 예로는 암호 변경, 비준수 디바이스 또는 계정 비활성화가 포함되며 이에 국한되지 않습니다. [PowerShell을 사용하여 사용자 세션을 명시적으로 해지](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)할 수도 있습니다. Azure AD 기본 구성은 “세션의 보안 상태가 변경되지 않은 경우 사용자에게 자격 증명을 제공하라고 요구하지 않습니다.”라는 메시지가 표시됩니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱과 함께 작동합니다. 다음 웹 애플리케이션을 포함하여 Windows, Mac, 모바일용 Microsoft 네이티브 앱은 대부분 이 설정을 준수합니다.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

로그인 빈도 설정은 자신의 쿠키를 삭제하지 않고 정기적으로 인증을 위해 Azure AD로 다시 리디렉션되는 한 SAML 애플리케이션에서도 작동합니다.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>사용자 로그인 빈도 및 다단계 인증

이전에 로그인 빈도는 Azure AD 및 하이브리드 Azure AD에 조인되고 Azure AD에 등록된 디바이스의 첫 번째 단계 인증에만 적용됐습니다. 고객이 디바이스에서 쉽게 MFA(다단계 인증)를 다시 적용할 수 없었습니다. 고객 피드백에 따라 로그인 빈도는 MFA에도 적용됩니다.

[![로그인 빈도 및 MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>사용자 로그인 빈도 및 디바이스 ID

Azure AD 또는 하이브리드 Azure AD에 조인되거나 Azure AD에 등록된 디바이스를 사용하는 경우 사용자가 디바이스를 잠금 해제하거나 대화형으로 로그인하면 이 이벤트는 로그인 빈도 정책에 부합합니다. 다음 두 예제에서는 사용자 로그인 빈도를 1시간으로 설정합니다.

예제 1:

- 00:00에 사용자는 Windows 10 Azure AD에 조인된 디바이스에 로그인하여 SharePoint Online에 저장된 문서에서 작업을 시작합니다.
- 사용자는 디바이스에서 한 시간 동안 동일한 문서 작업을 계속합니다.
- 01:00에 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 사용자에게 다시 로그인하라는 메시지가 표시됩니다.

예제 2:

- 00:00에 사용자는 Windows 10 Azure AD에 조인된 디바이스에 로그인하여 SharePoint Online에 저장된 문서에서 작업을 시작합니다.
- 00:30에 사용자는 일어나서 휴식을 취하고 디바이스를 잠급니다.
- 00:45에 사용자는 휴식을 끝내고 디바이스의 잠금을 해제합니다.
- 01:45에 00:45에 발생한 마지막 로그인 이후 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 사용자에게 다시 로그인하라는 메시지가 표시됩니다.

## <a name="persistence-of-browsing-sessions"></a>검색 세션의 지속성

영구 브라우저 세션을 사용하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다.

브라우저 세션 지속성의 Azure AD 기본값은 개인 디바이스의 사용자가 “로그인 상태를 유지하시겠나요?”를 표시하여 세션을 유지할지 여부를 선택할 수 있도록 허용합니다. 인증이 성공한 후에 메시지가 표시됩니다. [AD FS Single Sign-On 설정](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
) 문서의 지침에 따라 AD FS에서 브라우저 지속성이 구성된 경우 해당 정책을 준수하고 Azure AD 세션도 지속할 것입니다. 또한 [Azure AD 로그인 페이지 사용자 지정](../fundamentals/customize-branding.md) 문서에 있는 지침에 따라 Azure Portal의 회사 브랜딩 창에서 적절한 설정을 변경하여 테넌트의 사용자에게 "로그인 상태 유지" 프롬프트가 표시되는지 여부를 구성할 수 있습니다.

## <a name="configuring-authentication-session-controls"></a>인증 세션 제어 구성

조건부 액세스는 Azure AD Premium 기능이며 프리미엄 라이선스가 필요합니다. 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory의 조건부 액세스란?](overview.md#license-requirements)을 참조하세요.

> [!WARNING]
> 현재 퍼블릭 미리 보기로 제공되는 [구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md) 기능을 사용하는 경우 동일한 사용자 또는 앱 조합에 대해 두 개의 다른 정책(이 기능을 사용하는 하나의 정책 및 구성 가능한 토큰 수명 기능을 사용하는 또 다른 정책)을 생성하도록 지원하지 않습니다. Microsoft는 2021년 1월 30일에 새로 고침 및 세션 토큰 수명에 대해 구성 가능한 토큰 수명 기능을 중단하고 조건부 액세스 인증 세션 관리 기능으로 대체했습니다.  
>
> 로그인 빈도를 사용하도록 설정하기 전에 테넌트에서 다른 재인증 설정이 사용하지 않도록 설정되어 있는지 확인합니다. "신뢰할 수 있는 디바이스에서 MFA 기억"을 사용하도록 설정한 경우 로그인 빈도를 사용하기 전에 사용하지 않도록 설정해야 합니다. 이 두 설정을 함께 사용하면 사용자에게 예기치 않은 메시지가 표시될 수 있기 때문입니다. 재인증 프롬프트 및 세션 수명에 대해 자세히 알아보려면 [Azure AD Multi-Factor Authentication의 재인증 프롬프트 최적화 및 세션 수명 이해](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md) 문서를 참조하세요.

### <a name="policy-1-sign-in-frequency-control"></a>정책 1: 로그인 빈도 제어

1. 새 정책 만들기
1. 대상 클라우드 앱을 포함하여 고객 환경에 필요한 모든 조건을 선택합니다.

   > [!NOTE]
   > 최상의 사용자 환경을 위해 Exchange Online, SharePoint Online 등의 주요 Microsoft Office 앱에 대해 동일한 인증 프롬프트 빈도를 설정하는 것이 좋습니다.

1. **액세스 제어** > **세션** 으로 이동하여 **로그인 빈도** 를 클릭합니다.
1. 첫 번째 텍스트 상자에 날짜 및 시간에 대한 필수 값을 입력합니다.
1. 드롭다운에서 **시간** 또는 **일** 값을 선택합니다.
1. 정책을 저장합니다.

![로그인 빈도에 대해 구성된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD 등록 Windows 디바이스에는 디바이스에 대한 로그인은 프롬프트로 간주됩니다. 예를 들어, Office 앱에 대해 로그인 빈도를 24시간으로 구성한 경우 Azure AD 등록 Windows 디바이스의 사용자는 디바이스에 로그인하여 로그인 빈도 정책을 충족하고, Office 앱을 열 때 메시지가 다시 표시되지 않습니다.

### <a name="policy-2-persistent-browser-session"></a>정책 2: 영구 브라우저 세션

1. 새 정책 만들기
1. 모든 필수 조건을 선택합니다.

   > [!NOTE]
   > 이 제어를 위해서는 "모든 클라우드 앱"을 조건으로 선택해야 합니다. 브라우저 세션 지속성은 인증 세션 토큰에 의해 제어됩니다. 브라우저 세션의 모든 탭은 단일 세션 토큰을 공유하므로 모두 지속성 상태를 공유해야 합니다.

1. **액세스 제어** > **세션** 으로 이동하고 **영구 브라우저 세션** 을 클릭합니다.
1. 드롭다운에서 값을 선택합니다.
1. 정책을 저장합니다.

![영구 브라우저에 대해 구성된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> 두 정책을 모두 구성한 경우 Azure AD 조건부 액세스의 영구 브라우저 세션 구성이 동일한 사용자에 대한 Azure Portal의 회사 브랜딩 창의 "로그인 상태를 유지하시겠어요?" 설정을 덮어씁니다.

## <a name="validation"></a>유효성 검사

What-If 도구를 사용하여 사용자의 로그인을 대상 애플리케이션으로 시뮬레이션하고 정책을 구성한 방법에 따라 다른 조건을 시뮬레이트할 수 있습니다. 인증 세션 관리 제어가 도구의 결과에 표시됩니다.

![조건부 액세스 What If 도구 결과](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>정책 배포

정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다. 자세한 내용은 [조건부 액세스 배포 계획](plan-conditional-access.md) 문서를 참조하세요.

## <a name="known-issues"></a>알려진 문제
- 모바일 디바이스에 대한 로그인 빈도를 구성하는 경우 내부 로그인 빈도에 따라 인증 속도가 느려집니다(평균 30초 정도 걸릴 수 있음). 또한 다양한 앱에서 동시에 발생할 수 있습니다. 
- iOS 디바이스에서 앱이 첫 번째 인증 단계로 인증서를 구성하고 앱에 로그인 빈도와 [Intune 모바일 애플리케이션 관리](/mem/intune/apps/app-lifecycle) 정책이 적용된 경우 정책이 트리거될 때 최종 사용자가 앱에 로그인하지 못하도록 차단됩니다.

## <a name="next-steps"></a>다음 단계

* 사용자 환경에 대해 조건부 액세스 정책을 구성할 준비가 된 경우 [조건부 액세스 배포 계획](plan-conditional-access.md) 문서를 참조하세요.
