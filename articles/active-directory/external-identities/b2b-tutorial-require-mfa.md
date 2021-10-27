---
title: 자습서 - B2B용 다단계 인증 - Azure AD
description: 이 자습서에서는 Azure AD B2B를 사용하여 외부 사용자 및 파트너 조직과 협업할 때 MFA(다단계 인증)를 요구하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 06/22/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9fa2dd3ab101a340b01f3adcbdb33f8ea36595e
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163621"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>자습서: B2B 게스트 사용자에 다단계 인증 적용

외부 B2B 게스트 사용자와 공동 작업하는 경우 MFA(다단계 인증) 정책으로 앱을 보호하는 것이 좋습니다. 이렇게 하면 외부 사용자가 사용자 이름과 암호만으로는 리소스에 액세스할 수 없습니다. Azure AD(Azure Active Directory)에서는 액세스를 위해 MFA가 필요한 조건부 액세스 정책을 사용하여 이 목표를 달성할 수 있습니다. 조직의 멤버에게 MFA 정책을 사용하는 것과 같은 방법으로 MFA 정책을 테넌트, 앱 또는 개별 게스트 사용자 수준에서 적용할 수 있습니다.

예제:

![회사 앱에 로그인하는 게스트 사용자를 보여 주는 다이어그램](media/tutorial-mfa/aad-b2b-mfa-example.png)

1. 회사 A 초대장의 관리자 또는 직원은 액세스에 MFA가 필요하도록 구성된 클라우드 또는 온-프레미스 애플리케이션을 사용하도록 게스트 사용자를 초대합니다.
1. 게스트 사용자는 자신의 회사, 학교 또는 소셜 ID로 로그인합니다.
1. 사용자는 MFA 인증 질문을 완료해야 합니다. 
1. 사용자는 회사 A와 함께 MFA를 설정하고 해당 MFA 옵션을 선택합니다. 사용자는 애플리케이션에 액세스할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> - MFA를 설정하기 전에 로그인 환경을 테스트합니다.
> - 사용자 환경의 클라우드 앱에 액세스하려면 MFA가 필요한 조건부 액세스 정책을 만듭니다. 이 자습서에서는 Microsoft Azure 관리 앱을 사용하여 프로세스를 설명합니다.
> - What If 도구를 사용하여 MFA 로그인을 시뮬레이트합니다.
> - 조건부 액세스 정책을 테스트합니다.
> - 테스트 사용자 및 정책을 정리합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서의 시나리오를 완료하려면 다음이 필요합니다.

- 조건부 액세스 정책 기능을 포함하는 **Azure AD Premium Edition에 대한 액세스 권한**. MFA를 적용하려면 Azure AD 조건부 액세스 정책을 만들어야 합니다. 파트너에게 MFA 기능이 있는지 여부에 관계없이 MFA 정책은 조직에서 항상 적용됩니다. 조직에 대한 MFA를 설정할 경우 게스트 사용자를 위해 충분한 Azure AD Premium 라이선스를 보유하고 있는지 확인해야 합니다. 
- 게스트 사용자로 테넌트 디렉터리에 추가하고 로그인하는 데 사용할 수 있는 **유효한 외부 메일 계정**. 게스트 계정을 만드는 방법을 잘 모르는 경우 [Azure Portal에서 B2B 게스트 사용자 추가](add-users-administrator.md)를 참조하세요.

## <a name="create-a-test-guest-user-in-azure-ad"></a>Azure AD에서 테스트 게스트 사용자 만들기

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
3. **관리** 에서 **사용자** 를 선택합니다.
4. **새 게스트 사용자** 를 선택합니다.

    ![새 게스트 사용자 옵션을 선택할 수 있는 위치를 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-user-3.png)

5. **사용자 이름** 에서 외부 사용자의 이메일 주소를 입력합니다. 필요에 따라 환영 메시지가 포함됩니다.

    ![게스트 초대 메시지를 입력할 수 있는 위치를 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-user-4.png)

6. **초대** 를 선택하여 게스트 사용자에게 자동으로 초청을 발송합니다. **사용자를 초대함** 메시지가 나타납니다.
7. 초대를 발송한 후 사용자 계정이 디렉터리에 게스트로 자동 추가됩니다.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>MFA를 설정하기 전에 로그인 환경 테스트

1. 테스트 사용자 이름과 암호를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 로그인 자격 증명만 사용하여 Azure Portal에 액세스할 수 있습니다. 추가 인증이 필요하지 않습니다.
1. 로그아웃합니다.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>MFA가 필요한 조건부 액세스 정책 만들기

1. [Azure Portal](https://portal.azure.com/)에 보안 관리자 또는 조건부 액세스 관리자 권한으로 로그인합니다.
2. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
3. **Azure Active Directory** 페이지의 **보안** 섹션에서 **조건부 액세스** 를 선택합니다.
4. **조건부 액세스** 페이지의 위쪽 도구 모음에서 **추가** 를 선택합니다.
5. **새로 만들기** 페이지의 **이름** 텍스트 상자에 **B2B에 액세스하려면 MFA 필요** 를 입력합니다.
6. **할당** 섹션에서 **사용자 및 그룹** 을 선택합니다.
7. **사용자 및 그룹** 페이지에서 **사용자 및 그룹 선택** 을 선택한 다음, **모든 게스트 및 외부 사용자** 를 선택합니다.

    ![선택한 모든 게스트 사용자를 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9. **완료** 를 선택합니다.
10. **새로 만들기** 페이지의 **할당** 섹션에서 **클라우드 앱** 을 선택합니다.
11. **클라우드 앱** 페이지에서 **앱 선택** 을 선택한 후 **선택** 을 선택합니다.

    ![클라우드 앱 페이지 및 선택 옵션을 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. **선택** 페이지에서 **Microsoft Azure 관리** 를 선택한 후 **선택** 을 선택합니다.

    ![Microsoft Azure 관리 옵션을 강조 표시하는 스크린샷.](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. **클라우드 앱** 페이지에서 **완료** 를 클릭합니다.
14. **새로 만들기** 페이지의 **액세스 제어** 섹션에서 **허용** 을 선택합니다.
15. **허용** 페이지에서 **액세스 허용** 을 선택하고, **다단계 인증 필요** 확인란을 선택한 후, **선택** 을 선택합니다.

    ![다단계 인증 필요 옵션을 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. **정책 사용** 에서 **켜기** 를 선택합니다.

    ![켜기로 설정된 정책 사용 옵션을 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. **만들기** 를 선택합니다.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>What If 옵션을 사용하여 로그인 시뮬레이트

1. **조건부 액세스 - 정책** 페이지에서 **What If** 를 선택합니다.

    ![조건부 액세스 - 정책 페이지에서 What if 옵션을 선택할 수 있는 위치를 강조 표시하는 스크린샷.](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2. **사용자** 를 선택하고, 테스트 게스트 사용자를 선택한 후, **선택** 을 선택합니다.

    ![선택한 게스트 사용자를 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3. **클라우드 앱** 을 선택합니다.
4. **클라우드 앱** 페이지에서 **앱 선택** 을 선택한 후 **선택** 을 클릭합니다. 애플리케이션 목록에서 **Microsoft Azure 관리** 를 선택한 후 **선택** 을 클릭합니다.

    ![선택한 Microsoft Azure 관리 앱을 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5. **클라우드 앱** 페이지에서 **완료** 를 클릭합니다.
6. **What If** 를 선택하고 **적용되는 정책** 탭의 **평가 결과** 아래에 새 정책이 표시되는지 확인합니다.

    ![What If 옵션을 선택할 수 있는 위치를 보여 주는 스크린샷](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>조건부 액세스 정책 테스트

1. 테스트 사용자 이름과 암호를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 추가 인증 방법에 대한 요청이 표시되어야 합니다. 정책이 적용되는 데 다소 시간이 걸릴 수 있습니다.

    ![자세한 정보 필요 메시지를 보여 주는 스크린샷](media/tutorial-mfa/mfa-required.png)

3. 로그아웃합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 테스트 사용자 및 테스트 조건부 액세스 정책을 제거합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
3. **관리** 에서 **사용자** 를 선택합니다.
4. 테스트 사용자를 선택한 후 **사용자 삭제** 를 선택합니다.
5. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
6. **보안** 아래에서 **조건부 액세스** 를 선택합니다.
7. **정책 이름** 목록에서 테스트 정책의 상황에 맞는 메뉴(…)를 선택한 후 **삭제** 를 선택합니다. **예** 를 선택하여 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 게스트 사용자가 클라우드 앱 중 하나에 로그인할 때 MFA를 사용해야 하는 조건부 액세스 정책을 만들었습니다. 협업을 위해 게스트 사용자를 추가하는 방법을 자세히 알아보려면 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](add-users-administrator.md)를 참조하세요.
