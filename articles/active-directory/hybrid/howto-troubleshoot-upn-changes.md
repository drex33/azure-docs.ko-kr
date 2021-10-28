---
title: Azure UPN(사용자 계정 이름) 변경 계획 및 문제 해결
description: UPN 변경에 대한 알려진 문제 및 해결 방법 이해
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b48fa334aedce144e274b8155edda3cc7b08b9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739938"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active Directory에서 사용자 계정 이름 변경 계획 및 문제 해결

UPN(사용자 계정 이름)은 사용자 계정에 대한 인터넷 통신 표준인 특성입니다. UPN은 UPN 접두사(사용자 계정 이름) 및 UPN 접미사(DNS 도메인 이름)으로 구성됩니다. 접두사는 "\@" 기호를 사용하여 접미사를 조인합니다. 예들 들어 someone@example.com입니다. UPN은 디렉터리 포리스트 내의 모든 보안 주체 개체 사이에서 고유해야 합니다. 

**이 문서에서는 UPN을 사용자 ID로 사용하고 있다고 가정합니다. 이를 통해 UPN 변경 계획을 수립하는 방법과 UPN 변경으로 인해 발생할 수 있는 문제로부터 복구하는 방법을 설명합니다.**

> [!NOTE]
> 개발자의 경우 값이 변경될 수 있으므로 UPN 또는 메일 주소 대신 사용자 objectID를 변경 불가능한 식별자로 사용하는 것이 좋습니다.


## <a name="learn-about-upns-and-upn-changes"></a>UPN 및 UPN 변경에 대한 자세한 정보
필수 값이 실제로 UPN인 경우 로그인 페이지에서 사용자에게 메일 주소를 입력하라는 메시지를 표시하는 경우가 많습니다. 따라서 기본 메일 주소가 변경될 때마다 사용자의 UPN을 변경해야 합니다.

사용자의 기본 메일 주소는 다음과 같은 다양한 이유로 변경될 수 있습니다.

* 회사 리브랜딩

* 직원들이 다른 회사 부서로 전환 

* 인수 합병

* 직원 이름 변경

### <a name="types-of-upn-changes"></a>UPN 변경 유형

접두사, 접미사 또는 둘 다를 변경하여 UPN을 변경할 수 있습니다.

* **접두사를 변경합니다**.

   *  예를 들어 이름이 변경된 사용자의 계정 이름을 변경할 수 있습니다.  
‎BSimon@contoso.com에서 BJohnson@contoso.com으로 변경

   * 접두사에 대한 회사 표준도 변경할 수 있습니다.  
‎Bsimon@contoso.com에서 Britta.Simon@contoso.com으로 변경

* **접미사를 변경합니다**. <br>

    예를 들어 부서가 변경된 사용자의 도메인을 변경할 수 있습니다. 

   * Britta.Simon@contosolabs.com에 대한 Britta.Simon@contoso.com <br>
     또는<br>
    * Britta.Simon@labs.contoso.com에 대한 Britta.Simon@corp.contoso.com 

기본 메일 주소가 업데이트될 때마다 사용자의 UPN을 변경하는 것이 좋습니다.

Active Directory에서 Azure AD로의 초기 동기화 중에 사용자의 메일이 UPN과 동일한지 확인합니다.

### <a name="upns-in-active-directory"></a>Active Directory의 UPN

Active Directory에서 기본 UPN 접미사는 사용자 계정이 생성된 도메인의 DNS 이름입니다. 대부분의 경우 인터넷에서 엔터프라이즈 도메인으로 등록된 도메인 이름입니다. contoso.com 도메인에서 사용자 계정을 만들 경우 기본 UPN은 다음과 같습니다.

username@contoso.com

 그러나 Active Directory 도메인과 트러스트를 사용하여 [더 많은 UPN 접미사를 추가](../fundamentals/add-custom-domain.md)할 수 있습니다. 

예를 들어 labs.contoso.com을 추가하고 사용자의 UPN 및 메일에 이를 반영하도록 할 수 있습니다. 그러면 다음과 같이 됩니다.

username@labs.contoso.com.

>[!IMPORTANT]
> [Active Directory에서 접미사를 변경](../fundamentals/add-custom-domain.md)하는 경우 일치하는 사용자 지정 도메인 이름이 [Azure AD에서 추가되고 확인되었는지](../fundamentals/add-custom-domain.md) 확인해야 합니다. 

![확인된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory의 UPN

사용자는 userPrincipalName 특성의 값을 사용하여 Azure AD에 로그인합니다. 

온-프레미스 Active Directory와 함께 Azure AD를 사용하는 경우 사용자 계정은 Azure AD Connect 서비스를 사용하여 동기화됩니다. 기본적으로 Azure AD Connect 마법사는 온-프레미스 Active Directory의 userPrincipalName 특성을 Azure AD의 UPN으로 사용합니다. 사용자 지정 설치에서 다른 특성으로 변경할 수 있습니다.

단일 사용자의 UPN(사용자 계정 이름) 또는 전체 조직을 업데이트할 때 정의 프로세스가 있어야 합니다. 

이 문서에서 알려진 문제와 해결 방법을 참조하세요.

사용자 계정을 Active Directory에서 Azure AD로 동기화하는 경우 Active Directory의 UPN이 Azure AD의 확인된 도메인에 매핑되는지 확인합니다.

![확인된 Azure A D 도메인에 매핑된 UPN의 예제를 보여 주는 스크린샷](./media/howto-troubleshoot-upn-changes/verified-domains.png)

userPrincipalName 특성의 값이 Azure AD에서 확인된 도메인에 해당하지 않으면 동기화 프로세스는 접미사를 기본 .onmicrosoft.com 값으로 바꿉니다.


### <a name="roll-out-bulk-upn-changes"></a>대량 UPN 변경 롤아웃

대량 UPN 변경에 대한 [파일럿의 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 따르세요. 또한 신속하게 해결할 수 없는 문제를 발견한 경우 UPN을 되돌리기 위한 테스트된 롤백 계획도 있습니다. 파일럿이 실행되면 다양한 조직 역할과 특정 앱 또는 디바이스 집합을 사용하여 소규모 사용자 집합을 대상으로 시작할 수 있습니다.

사용자의 첫 번째 하위 집합을 통해 사용자는 변경의 일부로 사용자에게 필요한 작업을 파악할 수 있습니다. 사용자 통신에 이 정보를 포함합니다.

일반 작업의 일부로 개별 사용자의 UPN을 변경하는 정의된 절차를 만듭니다. 알려진 문제와 해결 방법에 대한 설명서가 포함된 테스트된 절차를 수행하는 것이 좋습니다.

다음 섹션에서는 UPN이 변경될 때 발생할 수 있는 잠재적인 알려진 문제와 해결 방법을 설명합니다.

## <a name="apps-known-issues-and-workarounds"></a>앱의 알려진 문제 및 해결 방법

[SaaS(Software as a service)](https://azure.microsoft.com/overview/what-is-saas/) 및 LoB(사업 부문) 애플리케이션은 종종 UPN을 사용하여 사용자를 찾고 역할을 비롯한 사용자 프로필 정보를 저장합니다. 사용자가 처음으로 앱에 로그인할 때 [Just in Time 프로비저닝](../app-provisioning/user-provisioning.md)을 사용하여 사용자 프로필을 만드는 애플리케이션은 UPN 변경의 영향을 받을 수 있습니다.

**알려진 문제**<br>
사용자의 UPN을 변경하면 Azure AD 사용자와 애플리케이션에서 만든 사용자 프로필 간의 관계가 손상될 수 있습니다. 애플리케이션에서 [Just in Time 프로비저닝](../app-provisioning/user-provisioning.md)을 사용하는 경우 새 사용자 프로필을 만들 수 있습니다. 이 관계를 수정하려면 애플리케이션 관리자가 이를 수동으로 변경해야 합니다.

**해결 방법**<br>
[Azure AD 자동 사용자 프로비저닝](../app-provisioning/user-provisioning.md)을 사용하면 지원되는 클라우드 애플리케이션에서 사용자 ID를 자동으로 만들고, 유지 관리하고, 제거할 수 있습니다. 애플리케이션에 자동화된 사용자 프로비저닝을 구성하면 애플리케이션의 UPN이 자동으로 업데이트됩니다. 점진적 롤아웃의 일부로 애플리케이션을 테스트하여 UPN 변경의 영향을 받지 않는지 확인합니다.
개발자인 경우 [애플리케이션에 SCIM 지원을 추가](../app-provisioning/use-scim-to-provision-users-and-groups.md)하여 Azure Active Directory에서 자동 사용자 프로비저닝을 사용하는 것이 좋습니다. 

## <a name="managed-devices-known-issues-and-workarounds"></a>관리형 디바이스의 알려진 문제 및 해결 방법

[Azure AD에 디바이스를 가져오면](../devices/overview.md) 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다.

### <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

[Azure AD 조인된](../devices/concept-azure-ad-join.md) 디바이스는 Azure AD에 직접 조인되며 이를 통해 사용자는 조직의 ID를 사용하여 디바이스에 로그인할 수 있습니다.

**알려진 문제** <br>
사용자는 인증을 위해 Azure AD를 사용하는 애플리케이션에 Single Sign-On 문제가 발생할 수 있습니다.

**해결 방법** <br>
이 섹션에서 설명하는 문제는 Windows 10 2020년 5월 업데이트(2004)에서 해결되었습니다.

**해결 방법** <br>
UPN 변경 내용을 Azure AD와 동기화하는 데 충분한 시간을 허용합니다. 새 UPN이 Azure AD 포털에 반영되었는지 확인한 후에는 사용자에게 새 UPN으로 로그인할 “다른 사용자” 타일을 선택하도록 요청합니다. [PowerShell](/powershell/module/azuread/get-azureaduser)을 통해 확인할 수도 있습니다. 새 UPN을 사용하여 로그인한 후에는 이전 UPN에 대한 참조가 “직장 또는 학교 액세스” Windows 설정에 계속 나타날 수 있습니다.

![확인된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

[하이브리드 Azure AD 조인된](../devices/concept-azure-ad-join-hybrid.md) 디바이스는 Active Directory 및 Azure AD에 조인됩니다. 사용자 환경에 온-프레미스 Active Directory 공간이 있고 Azure AD에서 제공하는 기능의 이점을 활용하려는 경우 하이브리드 Azure AD 조인을 구현할 수 있습니다.

**알려진 문제** 

Windows 10 하이브리드 Azure AD 조인된 디바이스에서 예기치 않은 다시 시작 및 액세스 문제가 발생할 수 있습니다.

사용자가 새 UPN이 Azure AD에 동기화되기 전에 Windows에 로그인하거나 기존 Windows 세션을 계속 사용하는 경우, 조건부 액세스가 하이브리드 조인 디바이스를 사용하여 리소스에 액세스하도록 구성되면 인증에 Azure AD를 사용하는 애플리케이션에 Single Sign-On 문제가 발생할 수 있습니다. 

또한 다음 메시지가 표시되고 1분 후에 강제로 다시 시작됩니다. 

“1분 후에 PC가 자동으로 다시 시작됩니다. Windows에 문제가 생겨 다시 시작해야 합니다. 이제 이 메시지를 닫고 작업을 저장해야 합니다.”

**해결 방법** <br>
이 섹션에서 설명하는 문제는 Windows 10 2020년 5월 업데이트(2004)에서 해결되었습니다.

**해결 방법** 

디바이스를 Azure AD에서 조인 해제하고 다시 시작해야 합니다. 다시 시작한 후 디바이스가 Azure AD에 자동으로 다시 조인되고 사용자는 “다른 사용자” 타일을 선택하여 새 UPN을 사용해 로그인해야 합니다. Azure AD에서 디바이스를 조인 해제하려면 명령 프롬프트에서 다음 명령을 실행합니다.

**dsregcmd /leave**

비즈니스용 Windows Hello가 사용 중인 경우 사용자는 [다시 등록](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)해야 합니다. UPN이 변경된 후에도 Windows 7 및 8.1 디바이스는 이 문제의 영향을 받지 않습니다.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator의 알려진 문제 및 해결 방법

조직에서 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 사용하여 로그인하고 조직 애플리케이션 및 데이터에 액세스하도록 요구할 수 있습니다. 사용자 이름이 앱에 나타날 수 있지만, 이 계정은 사용자가 등록 과정을 완료할 때까지는 확인 방법으로 사용되도록 설정되지 않습니다.

[Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)에는 네 가지 주요 기능이 있습니다.

* 푸시 알림 또는 확인 코드를 통한 다단계 인증

* iOS 디바이스와 Android 디바이스에서 인증 브로커 역할을 수행하여 [조정된 인증](../develop/msal-android-single-sign-on.md)을 사용하는 애플리케이션에 Single Sign-On 제공

* Intune 앱 보호 및 디바이스 등록/관리와 같은 다른 기능에 대한 요구 사항인 Azure AD에 디바이스 등록(Workplace Join이라고도 함)

* MFA 및 디바이스 등록이 필요한 휴대폰 로그인

### <a name="multi-factor-authentication-with-android-devices"></a>Android 디바이스를 사용한 Multi-Factor Authentication

Microsoft Authenticator 앱은 대역 외 확인 옵션을 제공합니다. 로그인하는 동안 사용자에게 자동으로 전화가 걸리거나 SMS가 전송되는 대신, [MFA(Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)에서 사용자의 스마트폰이나 태블릿에 있는 Microsoft Authenticator 앱에 푸시 알림을 보냅니다. 사용자는 앱에서 “승인”을 탭(또는 PIN이나 생체 인식을 입력하고 “인증”을 탭)하여 로그인을 완료합니다.

**알려진 문제** 

사용자의 UPN을 변경해도 이전 UPN이 사용자 계정에 계속 표시되고 알림이 수신되지 않을 수 있습니다. [확인 코드](https://support.microsoft.com/account-billing/common-problems-with-the-microsoft-authenticator-app-12d283d1-bcef-4875-9ae5-ac360e2945dd)는 계속 작동합니다.

**해결 방법**

알림이 수신되면 사용자에게 알림을 해제하고 Authenticator 앱을 연 다음 “알림 확인” 옵션을 탭하고 MFA 프롬프트를 승인하도록 지시합니다. 그러면 계정에 표시되는 UPN이 업데이트됩니다. 업데이트된 UPN은 새 계정으로 표시될 수 있습니다. 이는 다른 Authenticator 기능이 사용되기 때문입니다. 자세한 내용은 이 문서에서 알려진 추가 문제를 참조하세요.

### <a name="brokered-authentication"></a>조정된 인증

Android 및 iOS 브로커(예: Microsoft Authenticator)에서 다음을 사용합니다.

* SSO(Single Sign-On) - 사용자는 각 애플리케이션에 로그인할 필요가 없습니다.

* 디바이스 식별 - 브로커는 디바이스가 작업 공간에 연결되면 디바이스에서 생성된 디바이스 인증서에 액세스합니다.

* 애플리케이션 식별 확인 - 애플리케이션에서 브로커를 호출할 때 해당 리디렉션 URL을 전달하고 브로커는 이를 확인합니다.

또한 애플리케이션은 [조건부 액세스](../conditional-access/index.yml)와 같은 고급 기능에 참여할 수 있으며 [Microsoft Intune 시나리오](../develop/msal-net-use-brokers-with-xamarin-apps.md)를 지원합니다.

**알려진 문제**<br>
애플리케이션에서 전달한 login_hint와 브로커에 저장된 UPN이 일치하지 않아 브로커 지원 로그인을 사용하는 새 애플리케이션에 더 많은 대화형 인증 프롬프트가 표시됩니다.

**해결 방법** <br> 사용자는 Microsoft Authenticator에서 수동으로 계정을 제거하고 브로커 지원 애플리케이션에서 새 로그인을 시작해야 합니다. 초기 인증 후 계정이 자동으로 추가됩니다.

### <a name="device-registration"></a>디바이스 등록

Microsoft Authenticator 앱은 Azure AD에 디바이스를 등록하는 일을 담당합니다. 디바이스 등록을 통해 디바이스는 Azure AD에 인증할 수 있으며 이는 다음 시나리오의 요구 사항입니다.

* Intune 앱 보호

* Intune 디바이스 등록

* 휴대폰 로그인

**알려진 문제**<br>
UPN을 변경하면 새 UPN이 있는 새 계정이 Microsoft Authenticator 앱에 나열되고, 이전 UPN이 있는 계정은 계속 나열됩니다. 또한 앱 설정의 디바이스 등록 섹션에 이전 UPN이 표시됩니다. 디바이스 등록 또는 종속 시나리오의 정상적인 기능은 변경되지 않습니다.

**해결 방법** <br> Microsoft Authenticator 앱에서 이전 UPN에 대한 모든 참조를 제거하려면 사용자에게 이전 Microsoft Authenticator 계정과 새 Microsoft Authenticator 계정을 모두 수동으로 제거하고 MFA에 다시 등록한 후, 디바이스의 조인을 해제하도록 지시합니다.

### <a name="phone-sign-in"></a>휴대폰 로그인

휴대폰 로그인을 통해 사용자는 암호 없이 Azure AD에 로그인할 수 있습니다. 휴대폰 로그인을 사용하려면 사용자가 Authenticator 앱을 사용하여 MFA에 등록한 다음, Authenticator에서 직접 휴대폰 로그인을 사용해야 합니다. 구성의 일부로 디바이스는 Azure AD에 등록됩니다.

**알려진 문제** <br>
사용자는 알림을 수신하지 않으므로 휴대폰 로그인을 사용할 수 없습니다. 사용자가 “알림 확인”을 탭하면 오류가 발생합니다.

**해결 방법**<br>
사용자는 휴대폰 로그인이 사용된 계정에서 드롭다운 메뉴를 선택하고 “휴대폰 로그인 사용 안 함”을 선택해야 합니다. 원하는 경우 휴대폰 로그인을 다시 사용할 수 있습니다.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>FIDO2(보안 키)의 알려진 문제 및 해결 방법

**알려진 문제** <br>
여러 사용자가 동일한 키에 등록되면 이전 UPN이 표시되는 계정 선택 페이지가 로그인 화면에 표시됩니다. 보안 키를 사용하는 로그인은 UPN 변경의 영향을 받지 않습니다.  

**해결 방법**<br>
이전 UPN에 대한 참조를 제거하려면 사용자가 [보안 키를 다시 설정하고 다시 등록](../authentication/howto-authentication-passwordless-security-key.md#known-issues)해야 합니다.

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive의 알려진 문제 및 해결 방법

UPN이 변경되면 OneDrive 사용자에게 문제가 발생하는 것으로 알려져 있습니다. 자세한 내용은 [UPN 변경이 OneDrive URL 및 OneDrive 기능에 미치는 영향](/onedrive/upn-changes)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 리소스를 참조하세요.
* [Azure AD Connect: 설계 개념](./plan-connect-design-concepts.md)

* [Azure AD userPrincipalName 채우기](./plan-connect-userprincipalname.md)

* [Microsoft ID 플랫폼 ID 토큰](../develop/id-tokens.md)