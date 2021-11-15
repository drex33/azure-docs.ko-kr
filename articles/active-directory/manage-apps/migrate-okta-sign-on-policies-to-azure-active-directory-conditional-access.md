---
title: OKTA 로그온 정책을 Azure Active Directory 조건부 액세스로 마이그레이션하는 방법에 대한 자습서
titleSuffix: Active Directory
description: 이 자습서에서는 OKTA 로그온 정책을 Azure Active Directory 조건부 액세스로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 5cca89b361152d3a4a5635f008f70f145d5d68a0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459440"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>자습서: OKTA 로그온 정책을 Azure Active Directory 조건부 액세스로 마이그레이션

이 자습서에서는 조직이 OKTA의 전역 또는 애플리케이션 수준 로그온 정책에서 Azure AD(Azure Active Directory) 조건부 액세스 정책으로 마이그레이션하여 Azure AD 및 연결된 애플리케이션에서 사용자를 보호하는 방법을 알아봅니다.

이 자습서에서는 로그인 및 MFA(다단계 인증)를 사용할 수 있도록 Office 365 테넌트가 OKTA에 페더레이션되어 있다고 가정합니다. 또한 Azure AD에 사용자를 프로비저닝할 수 있도록 Azure AD Connect 서버 또는 Azure AD Connect 클라우드 프로비저닝 에이전트가 구성되어 있어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

OKTA 로그온에서 조건부 액세스로 전환할 때 라이선스 요구 사항을 이해하는 것이 중요합니다. 조건부 액세스를 사용하려면 Azure AD Multi-Factor Authentication에 등록하기 전에 사용자에게 Azure AD Premium P1 라이선스를 할당해야 합니다.

하이브리드 Azure AD 조인을 위한 단계를 수행하기 전에, SCP(서비스 연결 지점) 레코드를 구성하려면 온-프레미스 포리스트에서 엔터프라이즈 관리자 자격 증명이 필요합니다.

## <a name="catalog-current-okta-sign-on-policies"></a>현재 OKTA 로그온 정책 분류

조건부 액세스로의 전환을 성공적으로 완료하려면 기존 OKTA 로그인 정책을 평가하여 Azure AD로 전환될 사용 사례 및 요구 사항을 확인해야 합니다.

1. **보안** > **인증** > **로그온** 으로 이동하여 전역 로그온 정책을 확인합니다.

   ![전역 로그온 정책을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   이 예제에서 전역 로그온 정책은 구성된 네트워크 영역 외부의 모든 세션에서 MFA를 적용합니다.

   ![MFA를 적용하는 전역 로그온 정책을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. **애플리케이션** 으로 이동하여 애플리케이션 수준 로그온 정책을 확인합니다. 하위 메뉴에서 **애플리케이션** 을 선택한 다음, **활성 앱 목록** 에서 Office 365 연결 인스턴스를 선택합니다.

3. **로그온** 을 선택하고 페이지 맨 아래로 스크롤합니다.

다음 예제의 Office 365 애플리케이션 로그온 정책에는 다음과 같은 네 가지 별도의 규칙이 있습니다.

- **모바일 세션에 MFA 적용**: iOS 또는 Android의 모든 최신 인증 또는 브라우저 세션에서 MFA를 요구합니다.
- **신뢰할 수 있는 Windows 디바이스 허용**: 신뢰할 수 있는 OKTA 디바이스에서 더 많은 확인 또는 인증을 요구하지 않도록 합니다.
- **신뢰할 수 없는 Windows 디바이스에서 MFA 요구**: 신뢰할 수 없는 Windows 디바이스의 모든 최신 인증 또는 브라우저 세션에서 MFA를 요구합니다.
- **레거시 인증 차단**: 레거시 인증 클라이언트가 서비스에 연결하지 못하게 차단합니다.

  ![Office 365 로그온 규칙을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>조건 필수 요소 구성

추가 구성 없이 대부분의 시나리오에서 OKTA의 조건과 일치하도록 조건부 액세스 정책을 구성할 수 있습니다.

일부 시나리오에서는 조건부 액세스 정책을 구성하기 전에 추가 설정이 필요할 수 있습니다. 이 문서를 작성할 당시 알려진 두 가지 시나리오는 다음과 같습니다.

- **Azure AD의 명명된 위치에 대한 OKTA 네트워크 위치**: [조건부 액세스 정책에서 위치 조건 사용](../conditional-access/location-condition.md#named-locations)의 지침에 따라 Azure AD에서 명명된 위치를 구성합니다.
- **디바이스 기반 CA에 대한 OKTA 디바이스 신뢰**: 조건부 액세스는 사용자의 디바이스를 평가할 때 사용할 수 있는 다음 두 가지 옵션을 제공합니다.

  - [하이브리드 Azure AD 조인 사용](#hybrid-azure-ad-join-configuration) - Azure AD Connect 서버 내에서 사용하도록 설정된 기능으로 Windows 10, Windows Server 2016, Windows Server 2019와 같은 Windows 현재 디바이스를 Azure AD에 동기화합니다.
  - [Endpoint Manager에 디바이스를 등록](#configure-device-compliance)하고 규정 준수 정책 할당

### <a name="hybrid-azure-ad-join-configuration"></a>하이브리드 Azure AD 조인 구성

Azure AD Connect 서버에서 하이브리드 Azure AD 조인을 사용하려면 구성 마법사를 실행합니다. 디바이스를 자동으로 등록하려면 구성 후 단계를 수행해야 합니다.

>[!NOTE]
>하이브리드 Azure AD 조인은 Azure AD Connect 클라우드 프로비저닝 에이전트에서 지원되지 않습니다.

1. 하이브리드 Azure AD 조인을 사용하려면 다음 [지침](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)을 따릅니다.

1. **SCP 구성** 페이지에서 **인증 서비스** 드롭다운을 선택합니다. OKTA 페더레이션 공급자 URL을 선택하고 **추가** 를 선택합니다. 온-프레미스 엔터프라이즈 관리자 자격 증명을 입력하고 **다음** 을 선택합니다.

   ![SCP 구성을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. Windows 클라이언트의 전역 또는 앱 수준 로그온 정책에서 레거시 인증을 차단한 경우 하이브리드 Azure AD 조인 프로세스가 완료되도록 허용하는 규칙을 만듭니다.

1. 모든 Windows 클라이언트에 대해 전체 레거시 인증 스택을 허용합니다. OKTA 지원에 문의하여 기존 앱 정책에서 사용자 지정 클라이언트 문자열을 사용하도록 설정할 수도 있습니다.

### <a name="configure-device-compliance"></a>디바이스 규정 준수 구성

하이브리드 Azure AD 조인은 Windows에서 OKTA 디바이스 신뢰를 직접 대체합니다. 또한 조건부 액세스 정책은 Endpoint Manager에 완전히 등록된 디바이스의 디바이스 규정 준수를 확인할 수 있습니다.

- **규정 준수 개요**: [Intune의 디바이스 규정 준수 정책](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows)을 참조하세요.
- **디바이스 규정 준수**: [의 정책](/mem/intune/protect/create-compliance-policy)을 만듭니다.
- **Windows 등록**: 하이브리드 Azure AD 조인을 배포하도록 선택한 경우 [Intune에서 이러한 디바이스의 자동 등록 프로세스](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy)를 완료하는 또 다른 그룹 정책을 배포할 수 있습니다.
- **iOS/iPadOS 등록**: iOS 디바이스를 등록하려면 먼저 엔드포인트 관리 콘솔에서 [추가 구성](/mem/intune/enrollment/ios-enroll)이 필요합니다.
- **Android 등록**: Android 디바이스를 등록하려면 먼저 엔드포인트 관리 콘솔에서 [추가 구성](/mem/intune/enrollment/android-enroll)이 필요합니다.

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Azure AD Multi-Factor Authentication 테넌트 설정 구성

조건부 액세스로 변환하기 전에 조직의 기본 Azure AD Multi-Factor Authentication 테넌트 설정을 확인합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 전역 관리자 계정으로 로그인합니다.

1. **Azure Active Directory** > **사용자** > **Multi-Factor Authentication** 을 선택하여 레거시 Azure AD Multi-Factor Authentication 포털로 이동합니다.

   ![레거시 Azure AD Multi-Factor Authentication 포털을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   [Azure AD Multi-Factor Authentication 포털](https://aka.ms/mfaportal)의 레거시 링크를 사용해도 됩니다.

1. 레거시 **다단계 인증** 메뉴에서 **사용** 및 **적용** 을 통해 상태 메뉴를 변경하여 레거시 MFA를 사용하도록 설정된 사용자가 없는지 확인합니다. 다음 보기의 사용자가 테넌트에 있는 경우 레거시 메뉴에서 해당 사용자를 비활성화해야 합니다. 그래야만 조건부 액세스 정책이 해당 사용자의 계정에 적용됩니다.

   ![레거시 Azure AD Multi-Factor Authentication 포털에서 사용자를 비활성화하는 것을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   또한 **적용** 필드를 비워 두어야 합니다.

1. **서비스 설정** 옵션을 선택합니다. **앱 암호** 옵션을 **사용자가 브라우저에 기반하지 않는 앱에 로그인하기 위해 앱 암호를 만들 수 없음** 으로 변경합니다.

   ![사용자가 앱 암호를 만들지 못하게 하는 애플리케이션 암호 설정을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/app-password-selection.png)

1. **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기** 및 **사용자가 신뢰하는 디바이스에 대한 다단계 인증을 저장하도록 허용(1~365일 사이)** 확인란을 선택 취소한 다음, **저장** 을 선택합니다.

  >[!NOTE]
   >[MFA 프롬프트 설정을 구성하는 모범 사례](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)를 참조하세요.

![레거시 Azure AD Multi-Factor Authentication 포털에서 확인란을 선택 취소한 것을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>조건부 액세스 정책 구성

필수 구성 요소를 구성하고 기본 설정을 지정한 후에는 첫 번째 조건부 액세스 정책을 만들어야 합니다.

1. Azure AD에서 조건부 액세스 정책을 구성하려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **Azure Active Directory 관리** 에서 **보기** 를 선택합니다.

   [조건부 액세스 배포 및 디자인 모범 사례](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components)에 따라 조건부 액세스 정책을 구성합니다.

1. OKTA의 전역 로그온 MFA 정책을 모방하려면 [정책을 만듭니다](../conditional-access/howto-conditional-access-policy-all-users-mfa.md).

1. [디바이스 신뢰 기반 조건부 액세스 규칙](../conditional-access/require-managed-devices.md)을 만듭니다.

   이 정책은 이 자습서의 다른 정책과 마찬가지로 특정 애플리케이션, 사용자 테스트 그룹 또는 둘 모두를 대상으로 지정할 수 있습니다.

   ![사용자 테스트를 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![사용자 테스트가 성공한 것을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. 위치 기반 정책 및 디바이스 신뢰 정책을 구성한 후에는 해당하는 [레거시 인증 차단](../conditional-access/howto-conditional-access-policy-block-legacy.md) 정책을 구성해야 합니다.

이러한 세 가지 조건부 액세스 정책이 모두 준비되면 원래 OKTA 로그온 정책 환경이 Azure AD에 복제된 것입니다. 다음 단계는 Azure AD Multi-Factor Authentication을 통해 사용자를 등록하고 정책을 테스트하는 것입니다.

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication에서 파일럿 구성원 등록

조건부 액세스 정책을 구성한 후에는 사용자가 Azure AD Multi-Factor Authentication 방법을 등록해야 합니다. 사용자에게 여러 가지 방법으로 등록하도록 요구할 수 있습니다.

1. 개별 등록의 경우 사용자를 [Microsoft 로그인 창](https://aka.ms/mfasetup)으로 안내하여 등록 정보를 수동으로 입력하게 합니다.

1. 사용자는 [Microsoft 보안 정보 페이지](https://aka.ms/mysecurityinfo)로 이동하여 정보를 입력하거나 MFA 등록 양식을 관리할 수 있습니다.

MFA 등록 프로세스를 완전히 이해하려면 [이 가이드](../authentication/howto-registration-mfa-sspr-combined.md)를 참조하세요.

[Microsoft 로그인 창](https://aka.ms/mfasetup)으로 이동합니다. OKTA MFA를 통해 로그인하면 Azure AD를 사용하여 MFA에 등록하라는 메시지가 표시됩니다.

>[!NOTE]
>이전에 사용자 등록이 이미 수행된 경우 사용자가 MFA 프롬프트를 충족하면 **내 보안** 정보 페이지로 이동됩니다.
[MFA 등록에 대한 사용자 설명서](../user-help/security-info-setup-signin.md)를 참조하세요.

## <a name="enable-conditional-access-policies"></a>조건부 액세스 정책 사용

1. 테스트를 시작하기 위해, 이전 예제에서 만든 정책을 **테스트 사용자 로그인 사용** 으로 변경합니다.

   ![테스트 사용자를 사용하는 것을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. 다음 Office 365 **로그인** 창에서는 OKTA MFA 및 Azure AD Multi-Factor Authentication을 사용하여 로그인하라는 메시지가 테스트 사용자 John Smith에게 표시됩니다.

   ![Azure 로그인 창을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. OKTA를 통해 MFA 확인을 완료합니다.

   ![OKTA를 통한 MFA 확인을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. 사용자가 OKTA MFA 프롬프트를 완료하면 조건부 액세스에 대한 메시지가 표시됩니다. 정책이 적절하게 구성되었고 MFA에 대해 트리거될 조건 내에 있는지 확인합니다.

   ![조건부 액세스를 위해 표시된 OKTA를 통한 MFA 확인을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>로그온에서 조건부 액세스 정책으로 컷오버

파일럿 구성원에 대한 철저한 테스트를 수행하여 조건부 액세스가 예상대로 적용되는 것을 확인했으면 등록이 완료된 후 나머지 조직 구성원을 조건부 액세스 정책에 추가할 수 있습니다.

Azure AD Multi-Factor Authentication과 OKTA MFA 간에 이중 프롬프트를 방지하려면 로그온 정책을 수정하여 OKTA MFA를 옵트아웃합니다.

조건부 액세스로 마이그레이션하는 마지막 단계는 단계별 또는 컷오버 방식으로 수행할 수 있습니다.

1. OKTA 관리 콘솔로 이동하여 **보안** > **인증** 을 선택한 다음, **로그온 정책** 으로 이동합니다.

   >[!NOTE]
   > OKTA의 모든 애플리케이션이 자체 애플리케이션 로그온 정책으로 보호되는 경우에만 전역 정책을 **비활성** 으로 설정합니다.
1. **MFA 적용** 정책을 **비활성** 으로 설정합니다. Azure AD 사용자를 포함하지 않는 새 그룹에 정책을 할당할 수도 있습니다.

   ![전역 MFA 로그온 정책이 비활성 상태인 것을 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. 애플리케이션 수준 로그온 정책 창에서 **규칙 사용 안 함** 옵션을 선택하여 정책을 **비활성** 으로 업데이트합니다. Azure AD 사용자를 포함하지 않는 새 그룹에 정책을 할당할 수도 있습니다.

1. MFA 없이 액세스를 허용하는 애플리케이션에 대해 활성화된 애플리케이션 수준 로그온 정책이 하나 이상 있는지 확인합니다.

   ![MFA 없는 애플리케이션 액세스를 보여주는 스크린샷](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. OKTA 로그온 정책을 사용하지 않도록 설정하거나 마이그레이션된 Azure AD 사용자를 적용 그룹에서 제외하면 사용자가 다음에 로그인할 때 조건부 액세스에 대한 메시지 *만* 표시됩니다.

## <a name="next-steps"></a>다음 단계

OKTA에서 Azure AD로의 마이그레이션에 대한 자세한 내용은 다음을 참조하세요.

- [Okta에서 Azure AD로 애플리케이션 마이그레이션](migrate-applications-from-okta-to-azure-active-directory.md)
- [OKTA 페더레이션을 Azure AD로 마이그레이션](migrate-okta-federation-to-azure-active-directory.md)
- [OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션](migrate-okta-sync-provisioning-to-azure-active-directory.md)
