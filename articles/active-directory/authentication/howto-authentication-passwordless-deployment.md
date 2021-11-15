---
title: Azure Active Directory에서 암호 없는 인증 배포 계획
description: 암호 없는 인증 배포 지침
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3427384d41b4ebdd5f65d9c7e9ecdde911b5f08e
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807648"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Azure Active Directory에서 암호 없는 인증 배포 계획

암호는 주요 공격 벡터입니다. 악의적인 행위자는 소셜 엔지니어링, 피싱 및 스프레이 공격을 사용하여 암호를 손상시킵니다. 암호 없는 인증 전략은 이러한 공격의 위험을 완화합니다.

Microsoft는 Azure Active Directory(Azure AD)와 통합되는 [세 가지 암호 없는 인증 옵션](concept-authentication-passwordless.md)을 다음과 같이 제공합니다.

* [Microsoft Authenticator 앱](./concept-authentication-passwordless.md#microsoft-authenticator-app) - 사용자가 모든 플랫폼 또는 브라우저에 로그인할 수 있도록 허용하여 iOS 또는 Android 휴대폰을 강력하고 암호 없는 자격 증명으로 전환합니다.

* [FIDO2 준수 보안 키](./concept-authentication-passwordless.md#fido2-security-keys) - 휴대폰 사용이 제한되는 경우에 키오스크와 같은 공유 머신에 로그인하는 사용자 및 높은 권한이 있는 ID에 유용합니다. 

* [비즈니스용 Windows Hello](./concept-authentication-passwordless.md#windows-hello-for-business) - 전용 Windows 컴퓨터의 사용자에게 가장 적합합니다. 

> [!NOTE]
> 모든 링크가 포함된 이 계획의 오프라인 버전을 만들려면 브라우저에서 PDF로 인쇄 기능을 사용합니다.

## <a name="use-the-passwordless-methods-wizard"></a>암호 없는 방법 마법사 사용

이제 [Azure Portal](https://portal.azure.com/)에 각 잠재 고객에게 적합한 방법을 선택하는 데 도움이 되는 암호 없는 방법 마법사가 있습니다. 적절한 방법을 아직 결정하지 않은 경우 [https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard)를 참조한 다음 이 문서로 돌아와 선택한 방법에 대한 계획을 계속합니다. **이 마법사에 액세스하려면 관리자 권한이 필요합니다.**

## <a name="passwordless-authentication-scenarios"></a>암호 없는 인증 시나리오

Microsoft의 암호 없는 인증 방법으로 다양한 시나리오가 가능해집니다. 사용자의 조직 요구 사항, 필수 구성 요소 및 각 인증 방법의 기능을 고려하여 암호 없는 인증 전략을 선택합니다. 

다음 표에는 디바이스 유형별 암호 없는 인증 방법이 나와 있습니다. 권장 사항은 **굵게** 표시되어 있습니다.

| 디바이스 유형| 암호 없는 인증 방법 |
| - | - |
| Windows가 아닌 전용 디바이스| <li> **Microsoft Authenticator 앱** <li> 보안 키 |
| 전용 Windows 10 컴퓨터(버전 1703 이상)| <li> **비즈니스용 Windows Hello** <li> 보안 키 |
| 전용 Windows 10 컴퓨터(1703 이전 버전)| <li> **비즈니스용 Windows Hello** <li> Microsoft Authenticator 앱 |
| 공유 디바이스: 태블릿 및 모바일 디바이스| <li> **Microsoft Authenticator 앱** <li> 일회용 암호 로그인 |
| 키오스크(레거시)| **Microsoft Authenticator 앱** |
| 키오스크 및 공유 컴퓨터 ‎(Windows 10)| <li> **보안 키** <li> Microsoft Authenticator 앱 |


## <a name="prerequisites"></a>필수 구성 요소 

암호 없는 배포를 시작하기 전에 필수 구성 요소를 충족하는지 확인합니다.

### <a name="required-roles"></a>필요한 역할

이 배포에 필요한 최소 권한 역할은 다음과 같습니다.
<p>

| Azure AD 역할| Description |
| - | -|
| 전역 관리자| 결합된 등록 환경을 구현합니다. |
| 인증 관리자| 인증 방법을 구현하고 관리합니다. |
| 사용자| 디바이스에서 Authenticator 앱을 구성하거나 웹 또는 Windows 10 로그인을 위해 보안 키 디바이스를 등록합니다. |

이 배포 계획의 일부로 모든 [권한 있는 계정](../privileged-identity-management/pim-configure.md)에 대해 암호 없는 인증을 사용하도록 설정하는 것이 좋습니다.

### <a name="microsoft-authenticator-app-and-security-keys"></a>Microsoft Authenticator 앱 및 보안 키

필수 조건은 선택한 암호 없는 인증 방법에 따라 결정됩니다.

| 필수 조건| Microsoft Authenticator 앱| FIDO2 보안 키|
| - | -|-|
| [결합된 Azure AD MFA(Multi-Factor Authenticaiton) 및 SSPR(셀프 서비스 암호 재설정 등록)](howto-registration-mfa-sspr-combined.md)이 사용하도록 설정됩니다.| √| √|
| [사용자가 Azure AD MFA를 수행할 수 있습니다.](howto-mfa-getstarted.md)| √| √|
| [사용자가 Azure AD MFA 및 SSPR에 등록했습니다.](howto-registration-mfa-sspr-combined.md)| √| √|
| [사용자가 모바일 디바이스를 Azure Active Directory에 등록했습니다.](../devices/overview.md)| √| |
| Microsoft Edge 또는 Mozilla Firefox(버전 67 이상)와 같은 지원되는 브라우저를 사용하는 Windows 10 버전 1809 이상 네이티브 지원을 위해 버전 1903 이상이 권장됩니다.| | √|
| 호환되는 보안 키. [Microsoft에서 테스트하고 확인한 FIDO2 보안 키](concept-authentication-passwordless.md) 또는 기타 호환되는 FIDO2 보안 키를 사용하고 있는지 확인합니다.| | √|


### <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

비즈니스용 Windows Hello에 대한 필수 구성 요소 및 배포 경로는 온-프레미스, 하이브리드 또는 클라우드 전용 구성에서 배포하는지에 따라 달라집니다. 디바이스 조인 전략에 따라서도 다릅니다. 

비즈니스용 Windows Hello를 선택하고 [마법사를 완료](https://aka.ms/passwordlesswizard)하여 조직에 적합한 필수 조건 및 배포를 결정합니다.

![마법사에서 비즈니스용 Windows Hello 선택](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


마법사는 사용자의 입력을 사용하여 수행할 단계별 계획을 세웁니다.

## <a name="plan-the-project"></a>프로젝트 계획

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md)시키고 프로젝트의 관련자 역할이 명확하게 이해되도록 해야 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

암호 없는 인증을 배포하는 경우 먼저 하나 이상의 파일럿 그룹을 사용하도록 설정해야 합니다. 이 용도로만 사용되는 그룹을 만들 수 있습니다. 파일럿에 참여할 사용자를 그룹에 추가합니다. 그런 다음, 선택한 그룹에 대해 새 암호 없는 인증 방법을 사용하도록 설정합니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

### <a name="plan-communications"></a>통신 계획

최종 사용자에 대한 통신은 다음 정보를 포함해야 합니다.

* [Azure AD MFA 및 SSPR의 결합 등록 지침](howto-registration-mfa-sspr-combined.md)

* [Microsoft Authenticator 앱 다운로드](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [Microsoft Authenticator 앱에 등록](howto-authentication-passwordless-phone.md)

* [휴대폰으로 로그인](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft는 최종 사용자를 위한 커뮤니케이션 템플릿을 제공합니다. 커뮤니케이션 초안을 작성하는 데 도움이 되는 [인증 롤아웃 자료](https://aka.ms/MFAtemplates)를 다운로드합니다. 롤아웃 자료에는 조직의 향후 암호 없는 인증 옵션에 대해 사용자에게 알리는 데 사용할 수 있는 사용자 지정 가능한 포스터와 이메일 템플릿이 포함되어 있습니다.

## <a name="plan-user-registration"></a>사용자 등록 계획

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)에서 **결합된 보안 정보 워크플로** 의 일부로 암호 없는 방법을 등록합니다. Azure AD는 보안 키 및 Microsoft Authenticator 앱의 등록과 인증 방법에 대한 기타 변경 사항을 기록합니다. 

암호가 없는 최초 사용자의 경우 관리자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md)에 보안 정보를 등록하기 위한 [임시 액세스 암호](howto-authentication-temporary-access-pass.md)를 제공할 수 있습니다. 이 암호는 시간이 제한된 암호이며 강력한 인증 요구 사항을 충족합니다. **임시 액세스 패스는 사용자별 프로세스** 입니다.

이 방법을 사용하면 사용자가 보안 키 또는 Microsoft Authenticator 앱과 같은 인증 요소를 분실했거나 잊어버린 경우에도 쉽게 복구할 수 있지만 **새로운 강력한 인증 방법을 등록** 하려면 로그인해야 합니다. 

>[!NOTE] 
> 일부 시나리오에서 보안 키 또는 Microsoft Authenticator 앱을 사용할 수 없는 경우 다른 등록 방법과 함께 사용자 이름 및 암호를 사용한 다단계 인증을 대체 옵션으로 사용할 수 있습니다.

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 계획 및 배포

[Microsoft Authenticator 앱](concept-authentication-passwordless.md)으로 iOS 또는 Android 휴대폰이 강력한 암호 없는 자격 증명으로 바뀝니다. Google Play 또는 Apple App Store에서 무료로 다운로드할 수 있습니다. 사용자가 [Microsoft Authenticator 앱을 다운로드](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)하고 지침에 따라 휴대폰 로그인을 사용 설정하도록 합니다.

### <a name="technical-considerations"></a>기술 고려 사항

**AD FS(Active Directory Federation Services) 통합** - 사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 하이브리드 테넌트의 사용자는 "암호를 대신 사용합니다."를 선택하지 않는 한, 로그인을 위해 AD FS로 이동되지 않습니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 PTA(통과 인증) 흐름을 무시합니다. 그러나 login_hint가 지정된 경우 사용자가 AD FS로 전달되고 암호 없는 자격 증명을 사용하는 옵션이 바이패스됩니다.

**Azure MFA 서버** - 조직의 온-프레미스 Azure MFA 서버를 통한 다단계 인증을 사용하는 최종 사용자는 단일 암호 없는 휴대폰 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator 앱의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.

> [!IMPORTANT]
> 2019년 7월 1일부터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공하지 않습니다. 로그인 이벤트 중에 MFA(다단계 인증)를 요구하려는 신규 고객은 클라우드 기반 Azure AD Multi-Factor Authentication을 사용해야 합니다. 2019년 7월 1일 이전에 MFA 서버를 활성화한 기존 고객은 종전과 같이 최신 버전 및 이후 업데이트를 다운로드하고 활성화 자격 증명을 생성할 수 있습니다. Azure MFA 서버에서 Azure Active Directory MFA로 이동하는 것이 좋습니다.

**디바이스 등록** - 암호 없는 인증을 위해 Authenticator 앱을 사용하려면 디바이스가 Azure AD 테넌트에 등록되어 있어야 하며 공유 디바이스일 수 없습니다. 디바이스는 단일 테넌트에만 등록될 수 있습니다. 이 제한은 Microsoft Authenticator 앱을 사용하는 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 지원됨을 의미합니다.

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하여 휴대폰 로그인 배포

[Microsoft Authenticator 앱에서 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md) 문서의 단계를 수행하여 조직에서 Microsoft Authenticator 앱을 암호 없는 인증 방법으로 사용하도록 설정합니다.

### <a name="testing-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 테스트

다음은 Microsoft Authenticator 앱을 사용한 암호 없는 인증에 대한 샘플 테스트 사례입니다.

| 시나리오| 예상 결과 |
| - |-|
| 사용자가 Microsoft Authenticator 앱을 등록할 수 있습니다.| 사용자는 https://aka.ms/mysecurityinfo 에서 앱을 등록할 수 있습니다. |
| 사용자가 휴대폰 로그인을 사용하도록 설정할 수 있습니다.| 회사 계정에 대해 휴대폰 로그인을 구성했습니다. |
| 사용자가 휴대폰 로그인을 사용하여 앱에 액세스할 수 있습니다.| 사용자가 휴대폰 로그인 흐름을 통해 이동하고 애플리케이션에 연결합니다. |
| Microsoft Authenticator 암호 없는 로그인을 해제하여 전화 로그인 등록 롤백을 테스트합니다. Azure AD 포털의 인증 방법 화면 내에서 이 작업을 수행합니다.| 이전에 사용하도록 설정된 사용자는 Microsoft Authenticator에서 암호 없는 로그인을 사용할 수 없습니다. |
| Microsoft Authenticator 앱에서 휴대폰 로그인을 제거합니다.| Microsoft Authenticator에서 회사 계정을 더 이상 사용할 수 없습니다. |


### <a name="troubleshoot-phone-sign-in"></a>휴대폰 로그인 문제 해결


| 시나리오| 솔루션 |
| - |-|
| 사용자가 결합된 등록을 수행할 수 없습니다.| [결합된 등록](concept-registration-mfa-sspr-combined.md)이 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 휴대폰 로그인 인증자 앱을 사용하도록 설정할 수 없습니다.| 사용자가 배포 범위에 있는지 확인하세요. |
| 사용자가 암호 없는 인증에 대한 범위 내에 있지 않지만, 암호 없는 로그인 옵션으로 제공되며, 이 옵션은 완료할 수 없습니다.| 사용자가 정책이 만들어지기 전에 애플리케이션에서 휴대폰 로그인을 사용하도록 설정한 경우에 발생합니다. 로그인을 사용하도록 설정하려면 암호 없는 로그인이 사용하도록 설정된 사용자 그룹에 사용자를 추가합니다. 로그인을 차단하려면: 사용자가 해당 애플리케이션에서 자격 증명을 제거하도록 합니다. |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>FIDO2 호환 보안 키 계획 및 배포

호환되는 보안 키를 사용하도록 설정합니다. 다음은 암호 없는 환경과 호환되는 것으로 알려진 키를 제공하는 [FIDO2 보안 키 제공업체](concept-authentication-passwordless.md) 목록입니다.

### <a name="plan-security-key-lifecycle"></a>보안 키 수명 주기 계획

키 수명 주기를 준비하고 계획합니다.

**키 배포** - 조직에 키를 프로비저닝하는 방법을 계획합니다. 중앙 집중식 프로비저닝 프로세스가 있거나 최종 사용자가 FIDO 2.0 호환 키를 구매하도록 할 수 있습니다.

 **키 활성화** - 최종 사용자는 보안 키를 직접 활성화해야 합니다. 최종 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)에 보안 키를 등록하고 처음 사용할 때 두 번째 요소(PIN 또는 생체 인식)를 사용하도록 설정합니다. 처음 사용자는 TAP를 사용하여 보안 정보를 등록할 수 있습니다.

 **키 사용 안 함** - 관리자가 사용자 계정과 연결된 FIDO2 키를 제거하려는 경우 아래와 같이 사용자 인증 방법에서 키를 삭제하면 됩니다. 자세한 내용은 [키 사용 안 함](howto-authentication-passwordless-security-key.md#disable-a-key)을 참조하세요.

 

**새 키 발급**: 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동하여 새 FIDO2 키를 등록할 수 있습니다. 

### <a name="technical-considerations"></a>기술 고려 사항

보안 키에는 다음과 같은 세 가지 유형의 암호 없는 로그인 배포를 사용할 수 있습니다.

* 지원되는 브라우저의 Azure AD 웹앱

* Azure AD 조인 Windows 10 디바이스

* 하이브리드 Azure AD 조인 Windows 10 디바이스 

  * 클라우드 기반 및 온-프레미스 리소스 모두에 대한 액세스를 제공합니다. 온-프레미스 리소스에 액세스하는 방법에 대한 자세한 내용은 [FIDO2 키를 사용하여 온-프레미스 리소스에 SSO](howto-authentication-passwordless-security-key-on-premises.md)를 참조하세요.

**Azure AD 웹앱 및 Azure AD 결합 Windows 디바이스의 경우** 다음을 사용합니다.

* Microsoft Edge 또는 Mozilla Firefox(버전 67 이상)와 같은 지원되는 브라우저를 사용하는 Windows 10 버전 1809 이상

* Windows 10 버전 1809는 FIDO2 로그인을 지원하며, FIDO2 키 제조업체의 소프트웨어를 배포해야 할 수 있습니다. 버전 1903 이상을 사용하는 것이 좋습니다.

**하이브리드 Azure AD 도메인 가입 디바이스의 경우** 다음을 사용합니다. 

* Windows 10 버전 2004 이상. 

* Windows Server 2016 또는 2019를 실행하는 완전히 패치된 도메인 서버입니다. 

* 최신 버전의 Azure AD Connect.

#### <a name="enable-windows-10-support"></a>Windows 10 지원 사용

FIDO2 보안 키를 사용하여 Windows 10 로그인을 사용하도록 설정하려면 Windows 10에서 자격 증명 공급자 기능을 사용하도록 설정해야 합니다. 다음 중 하나를 선택합니다.

* [Intune에서 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md)

  * Intune 배포를 권장합니다.

* [프로비저닝 패키지를 사용하여 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md)

  * Intune 배포를 수행할 수 없는 경우 관리자는 자격 증명 공급자 기능을 사용하도록 설정하기 위해 각 머신에 패키지를 배포해야 합니다. 다음 옵션 중 하나를 수행하여 패키지를 설치할 수 있습니다.
    * 그룹 정책 또는 Configuration Manager
    * Windows 10 머신에서 로컬 설치

* [그룹 정책에서 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md)

   * 하이브리드 Azure AD 조인 디바이스에 대해서만 지원됩니다.

#### <a name="enable-on-premises-integration"></a>온-프레미스 통합 사용

[온-프레미스 리소스에 암호 없는 보안 키 로그인 사용(미리 보기)](howto-authentication-passwordless-security-key-on-premises.md) 문서의 단계를 따릅니다.

> [!IMPORTANT] 
> Windows 10 로그인에 대한 FIDO2 보안 키를 활용하려면 하이브리드 Azure AD 조인 디바이스에서 이러한 단계도 완료해야 합니다.


### <a name="key-restrictions-policy"></a>키 제한 정책

보안 키를 배포할 때 조직에서 승인한 특정 제조업체로만 FIDO2 키 사용을 선택적으로 제한할 수 있습니다. 키를 제한하려면 AAGUID(인증자 증명 GUID)가 필요합니다. [AAGUID를 얻는 방법은 두 가지입니다](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid).

![키 제한을 적용하는 방법](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


보안 키가 제한된 상태에서 사용자가 FIDO2 보안 키를 등록하려고 하면 다음 오류가 발생합니다.

![키 제한 시 보안 키 오류](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


사용자가 보안 키를 등록한 후 AAGUID가 제한된 경우 다음 메시지가 표시됩니다.

![AAGUID가 제한된 경우 사용자에 대한 보기](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*키 제한 정책에 의해 차단된 FIDO2 키

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 보안 키 로그인 배포

[암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md) 문서의 단계를 수행하여 조직에서 FIDO2 보안 키를 암호 없는 인증 방법으로 사용하도록 설정합니다. 

### <a name="testing-security-keys"></a>보안 키 테스트

다음은 보안 키를 사용하는 암호 없는 인증에 대한 샘플 테스트 사례입니다.

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>Azure Active Directory 조인 Windows 10 디바이스에 대한 암호 없는 FIDO 로그인


| 시나리오(Windows 빌드)| 예상 결과 |
| - |-|
| 사용자가 FIDO2 디바이스에 등록할 수 있습니다(1809).| 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키를 사용하여 FIDO2 디바이스를 등록할 수 있습니다. |
| 사용자가 FIDO2 디바이스를 다시 설정할 수 있습니다(1809).| 사용자가 제조업체 소프트웨어를 사용하여 FIDO2 디바이스를 다시 설정할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 로그인할 수 있습니다(1809).| 사용자가 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 등록할 수 있습니다(1903).| 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키에서 FIDO2 디바이스를 등록할 수 있습니다. |
| 사용자가 FIDO2 디바이스를 다시 설정할 수 있습니다(1903).| 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키에서 FIDO2 디바이스를 다시 설정할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 로그인할 수 있습니다(1903).| 사용자가 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>Azure AD 웹앱에 대한 암호 없는 FIDO 로그인


| 시나리오| 예상 결과 |
| - |-|
| 사용자가 Microsoft Edge를 사용하여 aka.ms/mysecurityinfo에서 FIDO2 디바이스를 등록할 수 있음| 등록 성공 |
| 사용자가 Firefox를 사용하여 aka.ms/mysecurityinfo에서 FIDO2 디바이스를 등록할 수 있음| 등록 성공 |
| 사용자가 Microsoft Edge를 사용하여 FIDO2 디바이스에서 온라인으로 OneDrive에 로그인할 수 있음| 로그인 성공 |
| 사용자가 Firefox를 사용하여 FIDO2 디바이스에서 온라인으로 OneDrive에 로그인할 수 있음| 로그인 성공 |
| Azure Active Directory 포털의 인증 방법 창 내에서 FIDO2 보안 키를 해제하여 FIDO2 디바이스 등록 롤백을 테스트| 사용자: <li> 보안 키를 사용하여 로그인하라는 메시지가 표시됩니다. <li> 성공적으로 로그인되고 “회사 정책에 따라 다른 방법으로 로그인해야 합니다.”라는 오류가 표시됩니다. <li>다른 방법을 선택하여 성공적으로 로그인할 수 있습니다. 창을 닫은 후 다시 로그인하여 동일한 오류 메시지가 표시되지 않는지 확인합니다. |


### <a name="troubleshoot-security-key-sign-in"></a>보안 키 로그인 문제 해결

| 시나리오| 솔루션 |
| - | -|
| 사용자가 결합된 등록을 수행할 수 없습니다.| [결합된 등록](concept-registration-mfa-sspr-combined.md)이 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 [보안 설정](https://aka.ms/mysecurityinfo)에서 보안 키를 추가할 수 없습니다.| [보안 키](howto-authentication-passwordless-security-key.md)가 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 Windows 10 로그인 옵션에서 보안 키를 추가할 수 없습니다.| [Windows 로그인용 보안 키가 사용하도록 설정되어 있는지 확인합니다](concept-authentication-passwordless.md). |
| **오류 메시지**: 이 브라우저 또는 OS가 FIDO2 보안 키를 지원하지 않는 것으로 확인되었습니다.| 암호 없는 FIDO2 보안 디바이스는 Windows 10 버전 1809 이상에서 지원되는 브라우저(Microsoft Edge, Firefox 버전 67)에만 등록할 수 있습니다. |
| **오류 메시지**: 회사 정책에 따라 다른 방법으로 로그인해야 합니다.| 테넌트에서 보안 키를 사용하도록 설정했는지 확인합니다. |
| 사용자가 Windows 10 버전 1809에서 내 보안 키를 관리할 수 없습니다.| 버전 1809에서는 FIDO2 키 공급업체에서 제공하는 보안 키 관리 소프트웨어를 사용해야 합니다. 공급업체에 지원을 문의하세요. |
| FIDO2 보안 키에 결함이 있는 것 같습니다. 어떻게 테스트할 수 있나요?| [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)으로 이동하고, 테스트 계정에 대한 자격 증명을 입력하고, 의심스러운 보안 키를 연결하고, 화면 오른쪽 위에 있는 + 단추를 선택하고, 만들기를 선택하고, 만들기 프로세스를 진행합니다. 이 시나리오에서 오류가 발생하면 디바이스에 결함이 있을 수 있습니다. |

## <a name="manage-passwordless-authentication"></a>암호 없는 인증 관리

[Azure Portal](https://portal.azure.com/)에서 사용자의 암호 없는 인증 방법을 관리하려면 사용자 계정을 선택한 다음, 인증 방법을 선택합니다.

### <a name="microsoft-graph-apis"></a>Microsoft Graph API 

Microsoft Graph의 인증 방법 API를 사용하여 암호 없는 인증 방법을 관리할 수도 있습니다. 예를 들면 다음과 같습니다.

* 사용자의 FIDO2 보안 키 세부 정보를 검색하고 사용자가 키를 분실한 경우 삭제할 수 있습니다.

* 사용자의 Microsoft Authenticator 등록 세부 정보를 검색하고 사용자가 전화를 분실한 경우 삭제할 수 있습니다.

* 보안 키 및 Microsoft Authenticator 앱에 대한 인증 방법 정책을 관리합니다.

Microsoft Graph에서 관리할 수 있는 인증 방법에 대한 자세한 내용은 [Azure AD 인증 방법 API 개요](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)를 참조하세요.

### <a name="rollback"></a>롤백

암호 없는 인증은 최종 사용자에게 미치는 영향을 최소화하는 간단한 기능이지만 롤백해야 할 수 있습니다.

롤백하려면 관리자가 Azure Portal에 로그인하고, 원하는 강력한 인증 방법을 선택한 다음, 사용 옵션을 아니요로 변경해야 합니다. 이 프로세스는 모든 사용자에 대해 암호 없는 기능을 해제합니다.

![암호 없는 롤백](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

FIDO2 보안 디바이스를 이미 등록한 사용자에게 다음 로그인 시 보안 디바이스를 사용하라는 메시지가 표시되고 다음 오류가 표시됩니다.

![암호 롤백 오류 창](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD에는 기술 및 비즈니스 인사이트를 제공하는 보고서가 있습니다. 비즈니스 및 기술 애플리케이션 소유자에게 조직의 요구 사항에 따라 이러한 보고서를 소유하고 사용하도록 합니다.

다음 표에서는 일반적인 보고 시나리오의 몇 가지 예를 제공합니다.

| 위험 관리| 생산성 향상| 거버넌스 및 규정 준수| other|
|-|-|-|-|
| 보고서 유형| 인증 방법 - 사용자가 결합된 보안 등록에 등록함| 인증 방법 – 사용자가 앱 알림에 등록함| 로그인: 테넌트에 액세스하는 사용자 및 방법 검토 |
| 잠재적 작업| 대상 사용자가 아직 등록되지 않음| Microsoft Authenticator 앱 또는 보안 키의 도입 주도| 관리자에 대해 액세스 해지 또는 추가 보안 정책 적용 |


#### <a name="track-usage-and-insights"></a>사용량 및 인사이트 추적

Azure AD는 다음과 같은 경우 감사 로그에 항목을 추가합니다.

* 관리자가 인증 방법 섹션에서 변경을 수행합니다.

* 사용자는 Azure AD 내에서 자격 증명을 다각도로 변경합니다.

* 사용자가 보안 키에서 계정을 사용 또는 사용하지 않도록 설정하거나 Win 10 컴퓨터에서 보안 키에 대한 두 번째 요소를 다시 설정합니다. 이벤트 ID: 4670 및 5382를 참조하세요.

**Azure AD는 대부분의 감사 데이터를 30일 동안 유지** 하고 데이터를 분석 시스템으로 다운로드하기 위해 Azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있도록 합니다. 좀 더 긴 보존이 필요한 경우 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk 또는 Sumo Logic과 같은 SIEM 도구에서 로그를 내보내고 사용합니다. 해당되는 경우 감사, 추세 분석 및 기타 비즈니스 요구 사항을 위해 더 오래 보존하는 것이 좋습니다.

인증 방법 작업 대시보드에는 등록 및 사용량이라는 두 개의 탭이 있습니다.

[등록 탭](https://portal.azure.com/)에는 암호 없이 인증할 수 있는 사용자 수와 기타 인증 방법이 표시됩니다. 이 탭에는 두 개의 그래프가 표시됩니다.

* 인증 방법별 등록된 사용자.

* 인증 방법별 최근 등록.

![인증 방법을 볼 수 있는 등록 탭](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

[사용량 탭](https://portal.azure.com/)에는 인증 방법별 로그인이 표시됩니다.

![인증 방법을 볼 수 있는 사용량 탭](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

자세한 내용은 [Azure AD 조직 전체에서 등록된 인증 방법 및 사용량 추적](howto-authentication-methods-activity.md)을 참조하세요.

#### <a name="sign-in-activity-reports"></a>로그인 활동 보고서

[로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)를 사용하여 다양한 애플리케이션에 로그인하는 데 사용된 인증 방법을 추적합니다. 

사용자 행을 선택한 다음 **인증 세부 정보** 탭을 선택하여 어떤 로그인 활동에 어떤 인증 방법이 사용되었는지 확인합니다.

![로그인 활동 보고](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>다음 단계

* [암호 없는 인증의 작동 원리 알아보기](concept-authentication-passwordless.md)

* [다른 ID 기능 배포](../fundamentals/active-directory-deployment-plans.md)
