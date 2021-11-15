---
title: OKTA 페더레이션을 Azure Active Directory 관리형 인증으로 마이그레이션하는 방법에 대한 자습서
titleSuffix: Active Directory
description: OKTA 페더레이션 애플리케이션을 Azure AD 관리형 인증으로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: ede3537ba6bd849a3792b11dffc96a073302db20
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451581"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>자습서: OKTA 페더레이션을 Azure Active Directory 관리형 인증으로 마이그레이션

이 자습서에서는 SSO(Single Sign-On) 기능을 사용하기 위해 기존 Office 365 테넌트를 OKTA와 페더레이션하는 방법을 알아봅니다.

페더레이션을 단계적 방식으로 Azure AD(Azure Active Directory)로 마이그레이션하면 사용자에게 좋은 인증 환경을 제공할 수 있습니다. 단계적 마이그레이션에서는 나머지 OKTA SSO 애플리케이션에 대한 역방향 페더레이션 액세스를 다시 테스트할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- SSO를 사용하기 위해 OKTA에 페더레이션된 Office 365 테넌트
- Azure AD에 대한 사용자 프로비저닝을 위해 구성된 Azure AD Connect 서버 또는 Azure AD Connect 클라우드 프로비저닝 에이전트

## <a name="configure-azure-ad-connect-for-authentication"></a>Azure AD Connect 인증 구성

Office 365 도메인을 OKTA와 페더레이션한 고객에게 현재 Azure AD에서 구성된 유효한 인증 방법이 없을 수 있습니다. 관리형 인증으로 마이그레이션하기 전에 Azure AD Connect 유효성을 검사하고 사용자 로그인을 허용하도록 구성하세요.

현재 환경에 가장 적합한 로그인 방법을 설정합니다.

- **암호 해시 동기화**: [암호 해시 동기화](../hybrid/whatis-phs.md)는 Azure AD Connect 서버 또는 클라우드 프로비저닝 에이전트에서 구현하는 디렉터리 동기화 기능의 확장입니다. 이 기능을 사용하여 Microsoft 365와 같은 Azure AD 서비스에 로그인할 수 있습니다. 온-프레미스 Active Directory 인스턴스에 로그인하기 위해 사용하는 암호와 동일한 암호를 사용하여 서비스에 로그인합니다.
- **통과 인증**: Azure AD [통과 인증](../hybrid/how-to-connect-pta.md)을 사용하면 사용자가 온-프레미스와 클라우드 기반 애플리케이션 둘 다에서 동일한 암호로 로그인할 수 있습니다. 사용자가 Azure AD를 통해 로그인하면 통과 인증 에이전트가 온-프레미스 Active Directory에 대해 직접 암호의 유효성을 검사합니다.
- **Seamless SSO**: [Azure AD Seamless SSO](../hybrid/how-to-connect-sso.md)는 사용자가 회사 네트워크에 연결된 회사 데스크톱에 있을 때 사용자를 자동으로 서명합니다. Seamless SSO를 사용하면 다른 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있습니다.

Seamless SSO를 암호 해시 동기화 또는 통과 인증에 배포하여 Azure AD의 사용자를 위한 원활한 인증 환경을 만들 수 있습니다.

[배포 가이드](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)에 따라 Seamless SSO의 모든 필수 구성 요소를 사용자에게 배포해야 합니다.

이 자습서의 예제에서는 암호 해시 동기화 및 Seamless SSO를 구성할 것입니다.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Azure AD Connect의 암호 해시 동기화 및 Seamless SSO 구성

다음 단계에 따라 Azure AD Connect의 암호 해시 동기화를 구성합니다.

1. Azure AD Connect 서버에서 **Azure AD Connect** 앱을 연 다음, **구성** 을 선택합니다.

   ![Azure AD 아이콘 및 [구성] 단추를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

1. **사용자 로그인 변경** > **다음** 을 선택합니다.

   ![사용자 로그인을 변경하는 페이지를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

1. 전역 관리자 자격 증명을 입력합니다.

   ![전역 관리자 자격 증명을 입력하는 위치를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

1. 현재 서버는 OKTA와 페더레이션하도록 구성되었습니다. **암호 해시 동기화** 로 변경합니다. 그런 다음, **Single Sign-On 사용** 을 선택합니다.

1. **다음** 을 선택합니다.

다음 단계에 따라 Seamless SSO를 사용하도록 설정합니다.

1. 로컬 온-프레미스 시스템의 도메인 관리자 자격 증명을 입력합니다. 그런 후 **다음** 을 선택합니다.

   ![사용자 로그인에 대한 설정을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

1. 마지막 페이지에서 **구성** 을 선택하여 Azure AD Connect 서버를 업데이트합니다.

   ![[구성] 페이지를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

1. 지금은 하이브리드 Azure AD 조인에 대한 경고를 무시합니다. OKTA에서 페더레이션을 사용하지 않도록 설정한 후 디바이스 옵션을 다시 구성할 것입니다.

   ![디바이스 옵션을 구성하는 링크를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="configure-staged-rollout-features"></a>단계적 롤아웃 기능 구성

Azure AD에서는 [클라우드 인증의 단계적 롤아웃](../hybrid/how-to-connect-staged-rollout.md)을 사용하여 사용자 페더레이션 해제를 테스트한 후 전체 도메인의 페더레이션 해제를 테스트할 수 있습니다. 배포하기 전에 [필수 조건](../hybrid/how-to-connect-staged-rollout.md#prerequisites)을 검토하세요.

Azure AD Connect 서버에서 암호 해시 동기화 및 Seamless SSO를 사용하도록 설정한 후에는 다음 단계에 따라 단계적 롤아웃을 구성합니다.

1. [Azure Portal](https://portal.azure.com/#home)에서 **보기** 또는 **Azure Active Directory 관리** 를 선택합니다.

   ![Azure Portal을 보여 주는 스크린샷.](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

1. **Azure Active Directory** 메뉴에서 **Azure AD Connect** 를 선택합니다. 그런 다음, 테넌트에서 **암호 해시 동기화** 가 사용하도록 설정되었는지 확인합니다.

1. **관리형 사용자 로그인에 대한 단계적 롤아웃 사용** 을 선택합니다.

   ![단계적 롤아웃을 사용하도록 설정하는 옵션을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

1. 서버가 구성된 후 **암호 해시 동기화** 설정이 **켜기** 로 변경되었을 수 있습니다. 사용하도록 설정되지 않은 경우 지금 설정합니다.

   **Seamless Single Sign-On** 이 **끄기** 로 설정되어 있습니다. 사용하도록 설정하려고 시도하면 테넌트의 사용자에 대해 이미 사용하도록 설정되어 있으므로 오류가 발생합니다.

1. **그룹 관리** 를 선택합니다.

   ![그룹을 관리하는 단추를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

지침에 따라 암호 해시 동기화 롤아웃에 그룹을 추가합니다. 다음 예제에서는 보안 그룹이 10명의 멤버로 시작합니다.

![보안 그룹의 예를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

그룹을 추가한 후에는 테넌트에서 기능이 적용될 때까지 약 30분 정도 기다립니다. 기능이 적용되면 사용자가 Office 365 서비스에 액세스하려고 할 때 더 이상 OKTA로 리디렉션되지 않습니다.

단계적 롤아웃 기능이 지원하지 않는 몇 가지 시나리오가 있습니다.

- POP3 및 SMTP와 같은 레거시 인증은 지원되지 않습니다.
- OKTA와 함께 사용하도록 하이브리드 Azure AD 조인을 구성한 경우 도메인이 페더레이션 해제될 때까지 모든 하이브리드 Azure AD 조인 흐름이 OKTA로 이동합니다. 하이브리드 Azure AD 조인 Windows 클라이언트에 대한 레거시 인증을 허용하려면 로그온 정책을 OKTA에 유지해야 합니다.

## <a name="create-an-okta-app-in-azure-ad"></a>Azure AD에서 OKTA 앱 만들기

관리형 인증으로 변환한 사용자가 OKTA의 애플리케이션에 액세스해야 하는 상황이 여전히 있을 수 있습니다. OKTA 홈페이지와 연결되는 Azure AD 애플리케이션을 등록하면 사용자가 해당 애플리케이션에 쉽게 액세스할 수 있습니다.

OKTA에 대한 엔터프라이즈 애플리케이션 등록을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/#home)의 **Azure Active Directory 관리** 에서 **보기** 를 선택합니다.

1. 왼쪽 메뉴의 **관리** 에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

   !["엔터프라이즈 애플리케이션"을 선택한 것을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

1. **모든 애플리케이션** 메뉴에서 **새 애플리케이션** 을 선택합니다.

   !["새 애플리케이션"을 선택한 것을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

1. **자신만의 애플리케이션** 을 선택하세요. 열리는 메뉴에서 OKTA 앱의 이름을 지정하고 **작업 중인 애플리케이션을 Azure AD와 통합하려면 등록** 을 선택합니다. 그런 다음 **만들기** 를 선택합니다.

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="애플리케이션 등록 방법을 보여 주는 스크린샷" lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

1. **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)**  > **등록** 을 선택합니다.

   ![애플리케이션을 등록하고 애플리케이션 계정을 변경하는 방법을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

1. Azure AD 메뉴에서 **앱 등록** 을 선택합니다. 그런 다음, 새로 만든 등록을 엽니다.

   ![새 앱 등을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

1. 테넌트 ID 및 애플리케이션 ID를 기록해 둡니다.

   >[!Note]
   >OKTA에서 ID 공급자를 구성하려면 테넌트 ID와 애플리케이션 ID가 필요합니다.

   ![테넌트 ID 및 애플리케이션 ID를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

1. 왼쪽 메뉴에서 **인증서 및 비밀** 을 선택합니다. 그런 다음, **새 클라이언트 암호** 를 선택합니다. 비밀의 일반적인 이름을 지정하고 만료 날짜를 설정합니다.

1. 비밀의 값과 ID를 기록해 둡니다.

   >[!NOTE]
   >값과 ID는 나중에 표시되지 않습니다. 지금 이 정보를 기록해 두지 않으면 비밀을 다시 생성해야 합니다.

   ![비밀의 값과 ID를 기록하는 위치를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

1. 왼쪽 메뉴에서 **API 권한** 을 선택합니다. 애플리케이션에 OIDC(OpenID Connect) 스택에 대한 액세스 권한을 부여합니다.

1. **권한 추가** > **Microsoft Graph** > **위임된 권한** 을 선택합니다.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="위임된 권한을 보여주는 스크린샷" lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

1. OpenID 권한 섹션에서 **이메일**, **openid** 및 **프로필** 을 추가합니다. 그런 다음 **권한 추가** 를 선택합니다.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="권한을 추가하는 방법을 보여주는 스크린샷" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::

1. **\<tenant domain name>에 대한 관리자 동의 허용** 을 선택하고 상태가 **허가됨** 으로 표시될 때까지 기다립니다.

    ![동의가 허가된 것을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

1. 왼쪽 메뉴에서 **브랜딩** 을 선택합니다. **홈페이지 URL** 에는 사용자의 애플리케이션 홈페이지를 추가합니다.

    ![브랜딩을 추가하는 방법을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

1. OKTA 관리 포털에서 **보안** > **ID 공급자** 를 선택하여 새 ID 공급자를 추가합니다. **Microsoft 추가** 를 선택합니다.

    ![ID 공급자를 추가하는 방법을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

1. **ID 공급자** 페이지에서 애플리케이션 ID를 **클라이언트 ID** 필드에 복사합니다. 클라이언트 암호를 **클라이언트 암호** 필드에 복사합니다.

1. **고급 설정 표시** 를 선택합니다. 기본적으로 이 구성은 역방향 페더레이션 액세스를 위해 OKTA의 UPN(사용자 계정 이름)을 Azure AD의 UPN에 연결합니다.

    >[!IMPORTANT]
    >OKTA와 Azure AD의 UPN이 서로 다른 경우 사용자 간에 공통적인 특성을 선택합니다.

1. 자동 프로비저닝을 위한 선택을 완료합니다. 기본적으로 사용자가 OKTA에서 일치하지 않으면 시스템은 Azure AD에서 사용자를 프로비저닝하려고 시도합니다. OKTA에서 프로비저닝을 마이그레이션한 경우 **OKTA 로그인 페이지로 리디렉션** 을 선택합니다.

    ![OKTA 로그인 페이지로 리디렉션하는 옵션을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    IDP(ID 공급자)를 만들었으므로, 이제 사용자를 올바른 IDP에 보내야 합니다.

1. **ID 공급자** 메뉴에서 **회람 규칙** > **회람 규칙 추가** 를 선택합니다. OKTA 프로필에서 사용 가능한 특성 중 하나를 사용합니다.

1. 모든 디바이스 및 IP에서 Azure AD로 직접 로그인하려면 다음 이미지처럼 정책을 설정합니다.

    이 예제에서는 **Division** 특성을 일부 OKTA 프로필에서는 사용하지 않으므로 IDP 라우팅에 적합한 선택합니다.

    ![IDP 라우팅의 division 특성을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

1. 회람 규칙을 추가했으므로, 이제 애플리케이션 등록에 추가할 수 있도록 리디렉션 URI를 기록해 둡니다.

    ![리디렉션 URI의 위치를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

1. 애플리케이션 등록의 왼쪽 메뉴에서 **인증** 을 선택합니다. 그런 다음, **플랫폼 추가** > **웹** 을 선택합니다.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="웹 플랫폼을 추가하는 방법을 보여주는 스크린샷" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::

1. IDP에서 기록해 놓은 리디렉션 URI를 OKTA에 추가합니다. 그런 다음, **액세스 토큰** 및 **ID 토큰** 을 선택합니다.

    ![OKTA 액세스 및 ID 토큰을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

1. 관리 콘솔에서 **디렉터리** > **사람** 을 선택합니다. 프로필을 편집할 첫 번째 테스트 사용자를 선택합니다.

1. 프로필에서 다음 이미지와 같이 **ToAzureAD** 를 추가합니다. 그런 다음, **저장** 을 선택합니다.

    ![프로필을 편집하는 방법을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

1. 수정된 사용자로 [Microsoft 356 포털](https://portal.office.com)에 로그인을 시도합니다. 사용자가 관리형 인증 파일럿에 포함되지 않은 경우 작업이 반복됩니다. 반복을 종료하려면 관리형 인증 환경에 사용자를 추가합니다.

## <a name="test-okta-app-access-on-pilot-members"></a>파일럿 구성원을 상대로 OKTA 앱 액세스 테스트

Azure AD에서 OKTA 앱을 구성하고 OKTA 포털에서 IDP를 구성한 후에는 사용자에게 애플리케이션을 할당해야 합니다.

1. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 을 차례로 선택합니다.

1. 앞에서 만든 앱 등록을 선택하고 **사용자 및 그룹** 으로 이동합니다. 관리형 인증 파일럿과 상관 관계가 있는 그룹을 추가합니다.

   >[!NOTE]
   >**Enterprise 애플리케이션** 페이지에서만 사용자 및 그룹을 추가할 수 있습니다. **앱 등록** 메뉴에서는 사용자를 추가할 수 없습니다.

   ![그룹을 추가하는 방법을 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

1. 약 15분 후에 관리형 인증 파일럿 사용자 중 하나로 로그인하고 [내 앱](https://myapplications.microsoft.com)으로 이동합니다.

   ![[내 앱] 갤러리를 보여주는 스크린샷](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

1. **OKTA 애플리케이션 액세스** 타일을 선택하여 사용자를 OKTA 홈페이지로 돌려보냅니다.

## <a name="test-managed-authentication-on-pilot-members"></a>파일럿 구성원을 상대로 관리형 인증 테스트

OKTA 역방향 페더레이션 앱을 구성한 후에는 사용자가 관리형 인증 환경에서 전체 테스트를 수행하도록 합니다. 사용자가 로그인하는 테넌트를 인식할 수 있도록 회사 브랜딩을 설정하는 것이 좋습니다. 자세한 내용은 [조직의 Azure AD 로그인 페이지에 브랜딩 추가](../fundamentals/customize-branding.md)를 참조하세요.

>[!IMPORTANT]
>OKTA에서 도메인을 완전히 페더레이션 해제하기 전에 혹시 필요할 수도 있는 추가 조건부 액세스 정책을 확인합니다. 전체를 컷오프하기 전에 환경을 보호하려면 [Azure AD 조건부 액세스 마이그레이션에 대한 OKTA 로그온 정책](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)을 참조하세요.

## <a name="defederate-office-365-domains"></a>Office 365 도메인의 페더레이션 해제

조직에서 관리형 인증 환경에 익숙한 경우 도메인의 OKTA 페더레이션 해제할 수 있습니다. 페더레이션 해제를 시작하려면 다음 명령을 사용하여 MSOnline PowerShell에 연결합니다. 아직 MSOnline PowerShell 모듈이 없는 경우 `install-module MSOnline`을 입력하여 다운로드합니다.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

도메인을 관리형 인증으로 설정한 후, OKTA 홈페이지에 대한 사용자 액세스를 유지하면서 Office 365 테넌트의 OKTA 페더레이션 해제를 성공적으로 수행했습니다.

## <a name="next-steps"></a>다음 단계

- [OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [OKTA 로그온 정책을 Azure AD 조건부 액세스로 마이그레이션](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
- [Okta에서 Azure AD로 애플리케이션 마이그레이션](migrate-applications-from-okta-to-azure-active-directory.md)
