---
title: 페더레이션에서 Azure Active Directory의 통과 인증으로 마이그레이션
description: 이 문서에는 하이브리드 ID 환경을 페더레이션에서 클라우드 인증으로 이동하는 방법에 대한 정보가 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537643"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>페더레이션에서 클라우드 인증으로 마이그레이션 

이 문서에서는 Azure Active Directory [PHS(암호 해시 동기화)](whatis-phs.md) 또는 [PTA(통과 인증)](how-to-connect-pta.md)를 사용하여 클라우드 사용자 인증을 배포하는 방법에 대해 알아봅니다. [AD FS(Active Directory Federation Services)](whatis-fed.md)에서 클라우드 인증 방법으로 이동하는 사용 사례를 제시하지만, 이 지침은 다른 온-프레미스 시스템에도 실질적으로 적용됩니다.

계속하기 전에 [올바른 인증 방법 선택](choose-ad-authn.md) 가이드를 검토하고 조직에 가장 적합한 방법을 비교하는 것이 좋습니다.

클라우드 인증에는 PHS를 사용하는 것이 좋습니다.

## <a name="staged-rollout"></a>단계적 롤아웃

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

단계적 롤아웃은 도메인을 중단하기 전에 Azure AD MFA(Multi-Factor Authentication), 조건부 액세스, 누출된 자격 증명에 대한 ID 보호, ID 거버넌스 등과 같은 클라우드 인증 기능을 사용하여 사용자 그룹을 선택적으로 테스트할 수 있는 좋은 방법입니다. 

[지원되는 시나리오](how-to-connect-staged-rollout.md#supported-scenarios)와 [지원되지 않는 시나리오](how-to-connect-staged-rollout.md#unsupported-scenarios)를 이해하려면 단계적 롤아웃 구현 계획을 참조하세요. 도메인을 중단하기 전에 단계적 롤아웃을 사용하여 테스트하는 것이 좋습니다.

단계적 롤아웃을 구성하는 방법을 알아보려면 [단계적 롤아웃 대화형 가이드](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD)인 'Azure AD에서 단계적 롤아웃을 사용하여 클라우드 인증으로의 마이그레이션'을 참조하세요.

## <a name="migration-process-flow"></a>마이그레이션 프로세스 흐름

![클라우드 인증으로 마이그레이션하는 프로세스 흐름](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>필수 구성 요소

마이그레이션을 시작하기 전에 다음 필수 구성 요소를 충족하는지 확인합니다.

### <a name="required-roles"></a>필요한 역할

단계적 롤아웃의 경우 테넌트의 전역 관리자여야 합니다. 

특정 Windows Active Directory 포리스트에서 Seamless SSO를 사용하도록 설정하려면 도메인 관리자여야 합니다.

### <a name="step-up-azure-ad-connect-server"></a>Azure AD Connect 서버 버전 업그레이드

[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)(Azure Active Directory Connect)를 설치하거나 [최신 버전으로 업그레이드](how-to-upgrade-previous-version.md)합니다. Azure AD Connect 서버의 버전을 업그레이드하면 AD FS에서 클라우드 인증 방법으로 마이그레이션하는 데 걸리는 시간이 잠재적으로 몇 시간에서 몇 분으로 줄어듭니다. 

### <a name="document-current-federation-settings"></a>현재 페더레이션 설정 문서화

현재 페더레이션 설정을 확인하려면 [Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) cmdlet을 실행합니다. 

페더레이션 설계 및 배포 설명서에 맞게 사용자 지정되었을 수 있는 설정을 확인합니다. 특히 **PreferredAuthenticationProtocol**, **SupportsMfa** 및 **PromptLoginBehavior** 에서 사용자 지정을 찾습니다.

### <a name="back-up-federation-settings"></a>페더레이션 설정 백업

이 배포는 AD FS 팜의 다른 신뢰 당사자를 변경하지 않지만 설정을 백업할 수 있습니다.

 - Microsoft [AD FS 신속 복원 도구](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)를 사용하여 기존 팜을 복원하거나 새 팜을 만듭니다.

- 다음 PowerShell 예제를 사용하여 추가한 Microsoft 365 ID 플랫폼 신뢰 당사자 트러스트 및 연결된 사용자 지정 클레임 규칙을 내보냅니다.

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>프로젝트 계획

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 위험을 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)시키고 프로젝트의 관련자 역할을 잘 이해해야 합니다.

### <a name="plan-communications"></a>통신 계획

클라우드 인증으로 마이그레이션되면 Azure AD를 통해 인증된 Microsoft 365 및 기타 리소스에 액세스하기 위한 사용자 로그인 환경이 변경됩니다. 네트워크 외부에 있는 사용자에게는 Azure AD 로그인 페이지만 표시됩니다. 

사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-the-maintenance-window"></a>유지 관리 기간 계획

도메인 변환 후 Azure AD는 일부 레거시 인증 요청을 Exchange Online에서 AD FS 서버로 최대 4시간 동안 계속 보낼 수 있습니다. 이 지연은 [레거시 애플리케이션 인증](../fundamentals/concept-fundamentals-block-legacy-authentication.md)에 대한 Exchange Online 캐시가 페더레이션에서 클라우드 인증으로의 중단을 인식하는 데 최대 4시간이 걸릴 수 있기 때문입니다.

이 4시간 동안 레거시 인증을 사용하는 애플리케이션에 다시 인증할 때 사용자에게 반복적으로 자격 증명을 요구하는 메시지가 표시될 수 있습니다. 사용자는 여전히 AD FS에 대해 성공적으로 인증할 수 있지만, 이제는 해당 페더레이션 트러스트가 제거되었으므로 Azure AD에서 사용자가 발급한 토큰을 더 이상 수락하지 않습니다.

Exchange Online에서 자격 증명의 캐시를 일정 기간 동안 유지하므로 기존 레거시 클라이언트(Exchange ActiveSync, Outlook 2010/2013)는 영향을 받지 않습니다. 캐시는 사용자를 자동으로 다시 인증하는 데 사용됩니다. 사용자는 AD FS로 돌아갈 필요가 없습니다. 캐시된 자격 증명이 지워지면 디바이스에 저장된 이러한 클라이언트에 대한 자격 증명이 자동으로 자체를 다시 인증하는 데 사용됩니다. 도메인 변환 프로세스의 결과로 사용자에게 암호 프롬프트가 표시되지 않습니다.

최신 인증 클라이언트(Office 2016 및 Office 2013, iOS 및 Android 앱)는 유효한 새로 고침 토큰을 사용하여 AD FS로 돌아가는 대신 리소스에 계속 액세스할 수 있는 새 액세스 토큰을 얻습니다. 이러한 클라이언트는 도메인 변환 프로세스에서 발생하는 모든 암호 프롬프트에 영향을 받지 않습니다. 클라이언트는 추가 구성 없이 계속 작동합니다.

### <a name="plan-for-rollback"></a>롤백 계획

> [!TIP]
> 롤백 요구 사항이 있는 경우 업무가 중단된 시간 동안 도메인을 중단하도록 계획하는 것이 좋습니다. 

롤백을 계획하려면 [문서화된 현재 페더레이션 설정](#document-current-federation-settings)을 사용하고 [페더레이션 설계 및 배포 설명서](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)를 확인합니다. 

롤백 프로세스에는 [Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated) cmdlet을 사용하여 관리되는 도메인을 페더레이션된 도메인으로 변환하는 작업이 포함되어야 합니다. 필요한 경우 추가 클레임 규칙을 구성합니다.

## <a name="migration-considerations"></a>마이그레이션 고려 사항 

마이그레이션에 대한 주요 고려 사항은 다음과 같습니다.

### <a name="plan-for-customizations-settings"></a>사용자 지정 설정 계획 

onload.js 파일은 Azure AD에서 복제할 수 없습니다. AD FS 인스턴스가 많이 사용자 지정되고 onload.js 파일의 특정 사용자 지정 설정을 사용하는 경우 Azure AD에서 현재 사용자 지정 요구 사항을 충족하는지 확인하여 계획을 수립합니다. 이러한 예정된 변경 내용을 사용자에게 알립니다.

#### <a name="sign-in-experience"></a>로그인 환경

Azure AD 로그인 환경은 사용자 지정할 수 없습니다. 사용자가 이전에 로그인한 방법에 관계없이 Azure AD에 로그인하려면 UPN(사용자 계정 이름) 또는 이메일과 같은 정규화된 도메인 이름이 필요합니다. 

#### <a name="organization-branding"></a>조직 브랜딩

[Azure AD 로그인 페이지는 사용자 지정](../fundamentals/customize-branding.md)할 수 있습니다. 변환 후에는 로그인 페이지에 AD FS의 일부 시각적 변경이 있습니다. 

>[!NOTE] 
>Microsoft 365 라이선스가 없는 경우 조직 브랜딩은 무료 Azure AD 라이선스에서 사용할 수 없습니다.

### <a name="plan-for-conditional-access-policies"></a>조건부 액세스 정책 계획

현재 조건부 액세스를 인증에 사용하는지 또는 AD FS에서 액세스 제어 정책을 사용하는지를 평가합니다. 

AD FS 액세스 제어 정책을 동등한 Azure AD [조건부 액세스 정책](../conditional-access/overview.md) 및 [Exchange Online 클라이언트 액세스 규칙](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)으로 바꾸는 것이 좋습니다. Azure AD 또는 온-프레미스 그룹은 조건부 액세스에 사용할 수 있습니다.

**레거시 인증 사용 안 함** - 레거시 인증 프로토콜과 관련된 위험이 증가하고 있으므로 [레거시 인증을 차단하는 조건부 액세스 정책](../conditional-access/howto-conditional-access-policy-block-legacy.md)을 만듭니다.

### <a name="plan-support-for-mfa"></a>MFA 지원 계획

Azure AD에서 페더레이션된 각 도메인에는 SupportsMFA 플래그가 있습니다. 

**SupportsMFA 플래그가 True로 설정** 된 경우 Azure AD는 AD FS 또는 다른 페더레이션 공급자에서 MFA를 수행하도록 사용자를 리디렉션합니다. 예를 들어 사용자가 MFA를 요구하는 조건부 액세스 정책이 구성된 애플리케이션에 액세스하는 경우 해당 사용자는 AD FS로 리디렉션됩니다. Azure AD MFA를 AD FS의 인증 방법으로 추가하면 구성이 완료된 후에 Azure AD MFA를 호출할 수 있습니다.

**SupportsMFA 플래그가 False로 설정** 된 경우 Azure MFA를 사용하지 않을 가능성이 높습니다. 아마도 AD FS 신뢰 당사자에 대한 클레임 규칙을 사용하여 MFA를 트리거하고 있을 것입니다. 

**SupportsMFA** 플래그의 상태는 다음 Windows PowerShell cmdlet을 사용하여 확인할 수 있습니다.
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Microsoft MFA 서버의 지원 수명이 거의 끝나가고 있으며, 이 서버를 사용하고 있는 경우 Azure AD MFA로 이동해야 합니다. 자세한 내용은 **[Microsoft MFA 서버에서 Azure 다단계 인증으로 마이그레이션 설명서](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)** 를 참조하세요.
Azure AD MFA를 사용하려는 경우 **[SSPR(셀프 서비스 암호 재설정) 및 Multi-Factor Authentication에 대한 결합된 등록](../authentication/concept-registration-mfa-sspr-combined.md)** 을 사용하여 사용자가 인증 방법을 한 번만 등록하도록 하는 것이 좋습니다. 

## <a name="plan-for-implementation"></a>구현 계획

이 섹션에는 로그인 방법을 전환하고 도메인을 변환하기 전의 사전 작업이 포함되어 있습니다.

### <a name="create-necessary-groups-for-staged-rollout"></a>단계적 롤아웃에 필요한 그룹 만들기

*단계적 롤아웃을 사용하지 않는 경우 이 단계를 건너뛰세요.*

단계적 롤아웃에 대한 그룹을 만듭니다. 또한 조건부 액세스 정책을 추가하려는 경우 해당 정책에 대한 그룹을 만들어야 합니다.

클라우드 전용 그룹이라고도 하는 Azure AD에서 마스터된 그룹을 사용하는 것이 좋습니다. Azure AD 보안 그룹 또는 Microsoft 365 그룹은 사용자를 MFA로 이동하는 데와 조건부 액세스 정책 모두에 사용할 수 있습니다. 자세한 내용은 [Azure AD 보안 그룹 만들기](../fundamentals/active-directory-groups-create-azure-portal.md) 및 [관리자를 위한 Microsoft 365 그룹 개요](/microsoft-365/admin/create-groups/office-365-groups)를 참조하세요.

그룹의 멤버는 단계적 롤아웃을 사용하도록 자동으로 설정됩니다. 중첩 그룹 및 동적 그룹은 단계적 롤아웃을 지원하지 않습니다.

### <a name="pre-work-for-sso"></a>SSO 사전 작업

사용하는 SSO 버전은 디바이스 OS 및 조인 상태에 따라 달라집니다.

- **Windows 10, Windows Server 2016 이상 버전의 경우** [Azure AD 조인 디바이스](../devices/concept-azure-ad-join.md), [하이브리드 Azure AD 조인 디바이스](../devices/concept-azure-ad-join-hybrid.md) 및 [Azure AD 등록 디바이스](../devices/concept-azure-ad-register.md)에서 [PRT(주 새로 고침 토큰)](../devices/concept-primary-refresh-token.md)를 통해 SSO를 사용하는 것이 좋습니다. 

- **Windows 7 및 8.1 디바이스의 경우** 컴퓨터를 Azure AD에 등록하기 위해 도메인에 조인된 [Seamless SSO](how-to-connect-sso.md)를 사용하는 것이 좋습니다. 이러한 계정은 Windows 10 디바이스에서와 같이 동기화할 필요가 없습니다. 그러나 [PowerShell을 사용하여 이 Seamless SSO 사전 작업을 완료](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)해야 합니다.

### <a name="pre-work-for-phs-and-pta"></a>PHS 및 PTA 사전 작업

선택한 로그인 방법에 따라 [PHS](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) 또는 [PTA](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication) 사전 작업을 완료합니다.

## <a name="implement-your-solution"></a>솔루션 구현

마지막으로, 계획한 대로 로그인 방법을 PHS 또는 PTA로 전환하고, 도메인을 페더레이션에서 클라우드 인증으로 변환합니다. 

### <a name="using-staged-rollout"></a>단계적 롤아웃 사용

단계적 롤아웃을 사용하는 경우 아래 링크의 단계를 따릅니다.

1. [테넌트에서 특정 기능의 단계적 롤아웃을 사용하도록 설정](how-to-connect-staged-rollout.md#enable-staged-rollout)합니다.

2. 테스트가 완료되면 [도메인을 페더레이션에서 관리형으로 변환](#convert-domains-from-federated-to-managed)합니다.

### <a name="without-using-staged-rollout"></a>단계적 롤아웃을 사용하지 않는 경우 

이 변경을 사용하도록 설정하기 위한 두 가지 옵션이 있습니다.

- **옵션 A:** Azure AD Connect를 사용하여 전환
  
  *처음에 Azure AD Connect를 사용하여 AD FS/ping에서 페더레이션된 환경을 구성한 경우 사용할 수 있습니다*.

- **옵션 B:** Azure AD Connect 및 PowerShell을 사용하여 전환
 
  *처음에 Azure AD Connect를 사용하여 페더레이션된 도메인을 구성하지 않았거나 타사 페더레이션 서비스를 사용하는 경우 사용할 수 있습니다*.

이러한 옵션 중 하나를 선택하려면 현재 설정을 알고 있어야 합니다.

#### <a name="verify-current-azure-ad-connect-settings"></a>현재 Azure AD Connect 설정 확인

[Azure AD 포털](https://aad.portal.azure.com/)에 로그인하고, **Azure AD Connect** 를 선택하고, 다음 다이어그램과 같이 **USER SIGN_IN** 설정을 확인합니다.

![현재 Azure AD Connect 설정 확인](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


**페더레이션이 구성된 방법을 확인하려면**

1. Azure AD Connect 서버에서 **Azure AD Connect** 를 열고, **구성** 을 선택합니다.

2. **추가 작업 > 페더레이션 관리** 아래에서 **페더레이션 구성 보기** 를 선택합니다. 

    ![페더레이션 관리 보기](media/deploy-cloud-user-authentication/manage-federation.png)

    이 섹션에 AD FS 구성이 표시되면 AD FS가 원래 Azure AD Connect를 사용하여 구성되었다고 가정할 수 있습니다. 아래 이미지를 예로 참조하세요.

    ![AD FS 구성 보기](media/deploy-cloud-user-authentication/federation-configuration.png)

    AD FS가 현재 설정에 나열되지 않으면 PowerShell을 사용하여 도메인을 페더레이션 ID에서 관리 ID로 수동으로 변환해야 합니다.

#### <a name="option-a"></a>옵션 A

**Azure AD Connect를 사용하여 페더레이션에서 새 로그인 방법으로 전환**

1. Azure AD Connect 서버에서 **Azure AD Connect** 를 열고, **구성** 을 선택합니다.

2. **추가 작업** 페이지 아래에서 **사용자 로그인 변경**, **다음** 을 차례로 선택합니다.
   
    ![추가 작업 보기](media/deploy-cloud-user-authentication/additional-tasks.png)

3. **Azure AD에 연결** 페이지에서 전역 관리자 계정 자격 증명을 입력합니다.

4. **사용자 로그인** 페이지에서:

    - **통과 인증** 옵션 단추를 선택하는 경우 **Single Sign-On 사용**, **다음** 을 차례로 선택합니다.

    -  **암호 해시 동기화** 옵션 단추를 선택하는 경우 **사용자 계정 변환 안 함** 확인란을 선택해야 합니다. 이 옵션은 더 이상 사용되지 않습니다. **Single Sign-On 사용**, **다음** 을 차례로 선택합니다.

      ![사용자 로그인 페이지에서 Single Sign-On 사용 선택](media/deploy-cloud-user-authentication/user-sign-in.png)

5. **Single Sign-On 사용** 페이지에서 도메인 관리자 계정의 자격 증명을 입력하고, **다음** 을 선택합니다.

    ![Single Sign-On 사용 페이지](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    Seamless SSO를 사용하도록 설정하려면 도메인 관리자 계정 자격 증명이 필요합니다. 이 프로세스는 이러한 관리자 권한이 필요한 다음 작업을 완료합니다.
      - AZUREADSSO(Azure AD를 나타냄)라는 컴퓨터 계정이 온-프레미스 Active Directory 인스턴스에 만들어집니다.
      - 컴퓨터 계정의 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다.
      - Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)이 만들어집니다.

    도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지 않으며, 프로세스가 성공적으로 완료되면 삭제됩니다. 이러한 자격 증명은 이 기능을 설정하는 데 사용됩니다.

6. **구성 준비 완료** 페이지에서 **구성이 완료되면 동기화 프로세스 시작** 확인란이 선택되어 있는지 확인합니다. 그런 다음, **구성** 을 선택합니다.

    ![구성 준비 완료 페이지](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > 이 시점에서 모든 페더레이션된 도메인이 관리형 인증으로 변경됩니다. 선택한 사용자 로그인 방법이 새로운 인증 방법입니다.

1. Azure AD 포털에서 **Azure Active Directory** 를 선택한 다음, **Azure AD Connect** 를 선택합니다.

2. 다음 설정을 확인합니다.

      - **페더레이션** 이 **사용 안 함** 으로 설정되어 있습니다.
      - **Seamless Single Sign-On** 이 **사용** 으로 설정되어 있습니다.
      - **암호 해시 동기화** 가 **사용** 으로 설정되어 있습니다.

  ![ 현재 사용자 설정 다시 확인](media/deploy-cloud-user-authentication/reverify-settings.png)

3. PTA로 전환하는 경우 다음 단계를 수행합니다.

##### <a name="deploy-more-authentication-agents-for-pta"></a>PTA용 추가 인증 에이전트 배포

>[!NOTE]
> PTA를 사용하려면 Azure AD Connect 서버 및 Windows 서버를 실행하는 온-프레미스 컴퓨터에 경량 에이전트를 배포해야 합니다. 대기 시간을 줄이려면 Active Directory 도메인 컨트롤러에 최대한 가깝게 에이전트를 설치합니다.

대부분의 고객의 경우 고가용성과 필요한 용량을 제공하는 데 2~3개의 인증 에이전트로 충분합니다. 테넌트는 최대 12개의 에이전트를 등록할 수 있습니다. 첫 번째 에이전트는 항상 Azure AD Connect 서버 자체에 설치됩니다. 에이전트 제한 사항과 에이전트 배포 옵션에 대한 자세한 내용은 [Azure AD 통과 인증: 현재 제한 사항](how-to-connect-pta-current-limitations.md)을 참조하세요.

1. **통과 인증** 을 선택합니다.
2. **통과 인증** 페이지에서 **다운로드** 단추를 선택합니다.
3. **에이전트 다운로드** 페이지에서 **약관 동의 및 다운로드** 를 선택합니다.

    추가 인증 에이전트 다운로드가 시작됩니다. 도메인에 조인된 서버에 보조 인증 에이전트를 설치합니다.

4. 인증 에이전트 설치를 실행합니다. 설치 중에 글로벌 관리자 계정의 자격 증명을 입력해야 합니다.

    ![ Microsoft Azure AD Connect 인증 에이전트](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. 인증 에이전트가 설치되면 PTA 상태 페이지로 돌아가서 추가 에이전트의 상태를 확인할 수 있습니다.

#### <a name="option-b"></a>옵션 B

**Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 새 로그인 방법으로 전환**

*처음에 Azure AD Connect를 사용하여 페더레이션된 도메인을 구성하지 않았거나 타사 페더레이션 서비스를 사용하는 경우 사용할 수 있습니다.*

Azure AD Connect 서버에서 [옵션 A](#option-a)의 1~5단계를 수행합니다. [사용자 로그인] 페이지에서 **구성 안 함** 옵션이 미리 선택되어 있습니다.

![ 사용자 로그인 페이지의 구성 안 함 옵션 표시](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. Azure AD 포털에서 **Azure Active Directory** 를 선택한 다음, **Azure AD Connect** 를 선택합니다. 

2. 다음 설정을 확인합니다.

  - **페더레이션** 이 **사용** 으로 설정되어 있습니다.
  - **Seamless Single Sign-On** 이 **사용 안 함** 으로 설정되어 있습니다.
  - **암호 해시 동기화** 가 **사용** 으로 설정되어 있습니다.

    ![ Azure Portal에서 현재 사용자 설정 확인](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

**PTA의 경우에만** 다음 단계에 따라 추가 PTA 에이전트 서버를 설치합니다.

1. Azure AD 포털에서 **Azure Active Directory** 를 선택한 다음, **Azure AD Connect** 를 선택합니다.

2. **통과 인증** 을 선택합니다. 상태가 **활성** 인지 확인합니다.

    ![ 통과 인증 설정](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   인증 에이전트가 활성 상태가 아닌 경우 다음 단계에서 도메인 변환 프로세스를 계속 진행하기 전에 이러한 [문제 해결 단계](tshoot-connect-pass-through-authentication.md)를 완료합니다. Azure Portal에서 PTA 에이전트가 성공적으로 설치되었고 해당 상태가 **활성** 인지 확인하기 전에 도메인을 변환하면 인증이 중단될 위험이 있습니다.

3. [추가 인증 에이전트를 배포](#deploy-more-authentication-agents-for-pta)합니다.

### <a name="convert-domains-from-federated-to-managed"></a>페더레인션에서 관리형으로 도메인 변환

**이 시점에서 페더레이션 인증은 여전히 ​​활성 상태이며 도메인에 대해 작동합니다**. 배포를 계속하려면 각 도메인을 페더레이션 ID에서 관리 ID로 변환해야 합니다.

>[!IMPORTANT]
> 모든 도메인을 동시에 변환할 필요는 없습니다. 프로덕션 테넌트에서 테스트 도메인으로 시작하거나 사용자 수가 가장 적은 도메인으로 시작하도록 선택할 수 있습니다.

**Azure AD PowerShell 모듈을 사용하여 변환을 수행합니다.**

1. PowerShell에서 글로벌 관리자 계정을 사용하여 Azure AD에 로그인합니다.

2. 첫 번째 도메인을 변환하려면 다음 명령을 실행합니다.
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)을 참조하세요.

3. Azure AD 포털에서 **Azure Active Directory > Azure AD Connect** 를 차례로 선택합니다.

4. 다음 명령을 실행하여 도메인이 관리형으로 변환되었는지 확인합니다.
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>마이그레이션 완료

다음 작업을 완료하여 가입 방법을 확인하고 변환 프로세스를 완료합니다.

### <a name="test-the-new-sign-in-method"></a>새 로그인 방법 테스트

테넌트에서 페더레이션 ID를 사용한 경우 사용자는 Azure AD 로그인 페이지에서 AD FS 환경으로 리디렉션되었습니다. 이제 테넌트가 페더레이션 인증 대신 새 로그인 방법을 사용하도록 구성되었으므로 사용자는 AD FS로 리디렉션되지 않습니다. 

**대신, 사용자는 Azure AD 로그인 페이지에서 직접 로그인합니다.**

이 [PHS/PTA 및 Seamless SSO를 통한 로그인 유효성 검사](how-to-connect-staged-rollout.md#validation) 링크의 단계를 따릅니다(필요한 경우).

### <a name="remove-a-user-from-staged-rollout"></a>단계적 롤아웃에서 사용자 제거

단계적 롤아웃을 사용한 경우 중단이 완료되면 단계적 롤아웃 기능을 해제해야 합니다. 

**단계적 롤아웃 기능을 사용하지 않도록 설정하려면 컨트롤을 끄기로 다시 밉니다.** .

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName 업데이트 동기화

지금까지 다음 조건이 모두 충족되지 않으면 온-프레미스 환경에서 동기화 서비스를 사용하는 **UserPrincipalName** 특성에 대한 업데이트가 차단되었습니다.

   - 사용자가 관리(페더레이션되지 않음) ID 도메인에 있습니다.
   - 사용자에게 라이선스가 할당되지 않았습니다.

이 기능을 확인하거나 설정하는 방법을 알아보려면 [userPrincipalName 업데이트 동기화](how-to-connect-syncservice-features.md)를 참조하세요.

## <a name="manage-your-implementation"></a>구현 관리

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Seamless SSO Kerberos 암호 해독 키 롤오버

Active Directory 도메인 멤버가 암호 변경을 제출하는 방법에 맞게 Kerberos 암호 해독 키를 적어도 30일마다 롤오버하는 것이 좋습니다. AZUREADSSO 컴퓨터 계정 개체에 연결된 디바이스가 없으므로 롤오버를 수동으로 수행해야 합니다.

[AZUREADSSO 컴퓨터 계정의 Kerberos 암호 해독 키를 롤오버하려면 어떻게 할까요?](how-to-connect-sso.md) FAQ를 참조하세요.

### <a name="monitoring-and-logging"></a>모니터링 및 로깅

인증 에이전트를 실행하는 서버를 모니터링하여 솔루션 가용성을 유지합니다. 인증 에이전트는 일반 서버 성능 카운터 외에도 인증 통계 및 오류를 이해하는 데 도움이 되는 성능 개체를 공개합니다.

인증 에이전트는 작업을 [애플리케이션 및 서비스] 로그 아래에 있는 Windows 이벤트 로그에 기록합니다. 또한 문제 해결을 위해 로깅을 설정할 수도 있습니다.

단계적 롤아웃에서 수행된 다양한 작업을 확인하기 위해 [PHS, PTA 또는 Seamless SSO에 대한 이벤트를 감사](how-to-connect-staged-rollout.md#auditing)할 수 있습니다.

### <a name="troubleshoot"></a>문제 해결

지원 팀은 페더레이션에서 관리형으로 변경하는 동안 또는 그 후에 발생하는 인증 문제를 해결하는 방법을 이해해야 합니다. 다음 문제 해결 설명서를 사용하여 지원 팀이 문제를 가려내고 해결하는 데 도움이 될 수 있는 일반적인 문제 해결 단계와 적절한 작업을 숙지하도록 지원합니다.

-  [Azure AD PHS](tshoot-connect-password-hash-synchronization.md)
- [Azure AD PTA](tshoot-connect-pass-through-authentication.md)
- [Azure AD Seamless SSO](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>AD FS 인프라 서비스 해제

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>앱 인증을 AD FS에서 Azure AD로 마이그레이션

마이그레이션을 위해서는 온-프레미스에서 애플리케이션을 구성하는 방법을 평가하고 해당 구성을 Azure AD에 매핑해야 합니다.

SAML/WS-FED 또는 Oauth 프로토콜을 사용하여 온-프레미스 및 SaaS 애플리케이션에서 AD FS를 계속 사용하려는 경우 사용자 인증을 위해 도메인을 변환한 후에 AD FS와 Azure AD를 모두 사용합니다. 이 경우 [Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md) 또는 [Azure AD 파트너 통합](../manage-apps/secure-hybrid-access.md) 중 하나를 통해 SHA(보안 하이브리드 액세스)를 사용하여 온-프레미스 애플리케이션과 리소스를 보호할 수 있습니다. 애플리케이션 프록시 또는 Microsoft 파트너 중 하나를 사용하면 온-프레미스 애플리케이션에 대한 보안 원격 액세스를 제공할 수 있습니다. 사용자는 [Single Sign-On](../manage-apps/add-application-portal-setup-sso.md) 후에 모든 디바이스에서 애플리케이션에 쉽게 연결하여 이점을 누릴 수 있습니다.

현재 ADFS와 페더레이션된 SaaS 애플리케이션을 Azure AD로 이동할 수 있습니다. [Azure 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)의 기본 제공 커넥터를 사용하거나 [애플리케이션을 Azure AD에 등록](../develop/quickstart-register-app.md)하여 Azure AD에 인증하도록 다시 구성합니다.

자세한 내용은 다음을 참조하세요. 

- [Active Directory Federation Services에서 Azure Active Directory로 애플리케이션 인증 이동](../manage-apps/migrate-adfs-apps-to-azure.md) 및
- [개발자를 위한 AD FS에서 Azure AD로의 애플리케이션 마이그레이션 플레이북](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad)

### <a name="remove-relying-party-trust"></a>신뢰 당사자 트러스트 제거

Azure AD Connect Health가 있는 경우 Azure Portal에서 [사용량을 모니터링](how-to-connect-health-adfs.md)할 수 있습니다. 사용량에 새 인증 요청이 표시되지 않고 모든 사용자와 클라이언트에서 Azure AD를 통해 성공적으로 인증하고 있음을 확인하는 경우 Microsoft 365에 신뢰 당사자 트러스트를 제거하는 것이 안전합니다.

AD FS를 다른 용도(즉, 다른 신뢰 당사자 트러스트)로 사용하지 않는 경우 이 시점에서 AD FS를 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 마이그레이션에 대해 알아보기](../manage-apps/migration-resources.md)
- [다른 ID 기능 배포](../fundamentals/active-directory-deployment-plans.md)