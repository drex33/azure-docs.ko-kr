---
title: 셀프 서비스 암호 재설정 정책 - Azure Active Directory
description: 다른 Azure Active Directory 셀프 서비스 암호 재설정 정책 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: ff0e43570b9bf30b84538cfeca841a69d025b26b
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090388"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory에서 암호 정책 및 계정 제한

Azure Active Directory(Azure AD)에는 암호 복잡성, 길이 또는 나이와 같은 설정을 정의하는 암호 정책이 있습니다. 사용자 이름에 허용되는 문자와 길이를 정의하는 정책도 있습니다.

SSPR(셀프 서비스 암호 재설정)을 사용하여 Azure AD에서 암호를 변경하거나 재설정하는 경우 암호 정책이 확인됩니다. 암호가 정책 요구 사항을 충족하지 않으면 사용자에게 다시 시도하라는 메시지가 표시됩니다. Azure 관리자에게는 일반 사용자 계정과 다른 SSPR 사용에 대한 몇 가지 제한 사항이 있습니다.

이 문서에서는 Azure AD 테넌트에서 사용자 계정과 관련된 암호 정책 설정 및 복잡성 요구 사항과 PowerShell을 사용하여 암호 만료 설정을 확인하거나 설정하는 방법을 설명합니다.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>사용자 이름 정책

Azure AD에 로그인하는 모든 계정에는 해당 계정에 연결된 고유한 UPN(사용자 계정 이름) 특성 값이 있어야 합니다. Azure AD Connect를 사용하여 AD DS(온-프레미스 Active Directory Domain Services) 환경이 Azure AD에 동기화된 하이브리드 환경에서는 기본적으로 Azure AD UPN이 온-프레미스 UPN으로 설정됩니다.

다음 표에서는 Azure AD에 동기화되는 온-프레미스 AD DS 계정과 Azure AD에서 직접 만든 클라우드 전용 사용자 계정에 적용되는 사용자 이름 정책을 간략하게 설명합니다.

| 속성 | UserPrincipalName 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul> <li>A-Z</li> <li>a-z</li><li>0-9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 |<ul> <li>도메인에서 사용자 이름을 구분하지 않는 모든 "\@\"" 문자입니다.</li> <li>"\@\"" 기호 바로 앞에는 "."(마침표) 문자를 사용할 수 없습니다.</li></ul> |
| 길이 제약 조건 |<ul> <li>총 길이는 113자를 초과할 수 없습니다.</li><li>"\@\"" 기호 앞에는 최대 64자를 사용할 수 있습니다.</li><li>"\@\"" 기호 뒤에는 최대 48자를 사용할 수 있습니다.</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD 암호 정책

암호 정책은 Azure AD에서 직접 만들고 관리하는 모든 사용자 계정에 적용됩니다. 이러한 암호 정책 설정 중 일부는 수정할 수 없지만 계정 차단 매개 변수 또는 [Azure AD 암호 보호를 위한 사용자 지정 금지 암호를 구성](tutorial-configure-custom-password-protection.md)할 수 있습니다.

기본적으로 잘못된 암호를 사용하여 로그인을 10회 시도했다가 실패하면 계정이 차단됩니다. 사용자는 1분 동안 차단됩니다. 잘못된 로그인을 더 시도하면 사용자가 잠기는 시간이 더 늘어납니다. [스마트 잠금](howto-password-smart-lockout.md) 기능은 동일한 암호에 대해 잠금 카운터가 증가하는 것을 방지하기 위해 마지막 세 개의 잘못된 암호 해시를 추적합니다. 누군가가 동일한 잘못된 암호를 여러 번 입력하는 경우에는 계정이 차단되지 않습니다. 스마트차단 임계값 및 기간을 정의할 수 있습니다.

*EnforceCloudPasswordPolicyForPasswordSyncedUsers* 를 사용하도록 설정하지 않으면 Azure AD 암호 정책이 Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 사용자 계정에 적용되지 않습니다.

다음 Azure AD 암호 정책 옵션이 정의되어 있습니다. 다음 설정은 변경이 가능하다고 명시되지 않는 한 변경할 수 없습니다.

| 속성 | 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul><li>A-Z</li><li>a-z</li><li>0-9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ></li> <li>공백</li></ul> |
| 허용되지 않는 문자 | 유니코드 문자 |
| 암호 제한 |<ul><li>최소 8자, 최대 256자</li><li>다음 4개 중 3개가 필요합니다.<ul><li>소문자</li><li>대문자</li><li>숫자(0-9)</li><li>기호(이전 암호 제한 참조)</li></ul></li></ul> |
| 암호 만료 기간(최대 암호 사용 기간) |<ul><li>기본값: **90** 일</li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림(사용자에게 암호 만료 알림이 제공됨) |<ul><li>기본값: **14** 일(암호 만료 이전)</li><li>값은 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료(암호가 만료되지 않도록 함) |<ul><li>기본값: **false**(암호의 만료 날짜가 있음을 나타냄).</li><li>`Set-MsolUser` cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다.</li></ul> |
| 암호 변경 기록 | 사용자 암호를 변경할 때 마지막 암호를 다시 사용할 수 *없습니다*. |
| 암호 재설정 기록 | 사용자가 잊어버린 암호를 재설정할 때 마지막 암호를 다시 사용할 수 *있습니다*. |

## <a name="administrator-reset-policy-differences"></a>관리자 재설정 정책의 차이점

기본적으로 관리자 계정은 셀프 서비스 암호 재설정을 사용하도록 설정되며 강력한 기본 ‘두 게이트’ 암호 재설정 정책이 적용됩니다. 이 정책은 사용자에 대해 정의한 정책과 다를 수 있으며 변경할 수 없습니다. 항상 Azure 관리자 역할이 할당되지 않은 사용자로 암호 재설정 기능을 테스트해야 합니다.

두 게이트 정책을 사용할 경우 관리자는 보안 질문을 사용할 수 없습니다.

두 게이트 정책은 이메일 주소, 인증자 앱 또는 전화 번호와 같은 두 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 두 게이트 정책이 적용됩니다.

* 다음과 같은 모든 Azure 관리자 역할이 영향을 받습니다.
  * 애플리케이션 관리자
  * 애플리케이션 프록시 서비스 관리자
  * 인증 관리자
  * Azure AD 조인 디바이스 로컬 관리자
  * 대금 청구 관리자
  * 규정 준수 관리자
  * 디바이스 관리자
  * 디렉터리 동기화 계정
  * 디렉터리 작성자
  * Dynamics 365 관리자
  * Exchange 관리자
  * 전역 관리자 또는 회사 관리자
  * 기술 지원팀 관리자
  * Intune 관리자
  * 사서함 관리자
  * 파트너 계층1 지원
  * 파트너 계층2 지원
  * 암호 관리자
  * Power BI 서비스 관리자
  * 권한 있는 인증 관리자
  * 권한 있는 역할 관리자
  * SharePoint 관리자
  * 보안 관리자
  * 서비스 지원 관리자
  * 비즈니스용 Skype 관리자
  * 사용자 관리자

* 평가판 구독에서 30일이 경과한 경우 또는
* Azure AD 테넌트에 대해 사용자 지정 도메인이 구성되었습니다(예: *contoso.com*).
* Azure AD Connect가 온-프레미스 디렉터리에서 ID를 동기화하는 경우

[Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) PowerShell cmdlet을 사용하여 관리자 계정에 SSPR를 사용하지 않도록 설정할 수 있습니다. `-SelfServePasswordResetEnabled $False` 매개 변수는 관리자에 대해 SSPR를 사용하지 않도록 설정합니다.

### <a name="exceptions"></a>예외

한 게이트 정책은 전자 메일 주소 또는 전화 번호 등, 한 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 한 게이트 정책이 적용됩니다.

* 평가판 구독의 처음 30일 이내인 경우 또는
* Azure AD 테넌트에 대해 사용자 지정 도메인이 구성되지 않았으므로 기본 * *.onmicrosoft.com* 을 사용합니다. 프로덕션에는 기본 * *. onmicrosoft.com* 도메인을 사용하지 않는 것이 좋습니다.
* Azure AD Connect가 ID를 동기화하지 않는 경우

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>암호 만료 정책

‘전역 관리자’ 또는 ‘사용자 관리자’는 [Windows PowerShell용 Microsoft Azure AD 모듈](/powershell/module/Azuread/)을 사용하여 사용자 암호가 만료되지 않도록 설정할 수 있습니다. 

또한 PowerShell cmdlet을 사용하여 만료되지 않는 구성을 제거하거나 어떤 사용자 암호가 만료되지 않도록 설정되어 있는지 확인할 수 있습니다.

이 지침은 ID 및 디렉터리 서비스로 Azure AD를 사용하는 Intune, Microsoft 365와 같은 다른 공급자에 제공됩니다. 암호 만료는 정책에서 변경할 수 있는 유일한 부분입니다.

> [!NOTE]
> 기본적으로 Azure AD Connect를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [Azure AD와 AD 연결](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy)을 참조하세요.

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell을 사용하여 암호 정책 설정 또는 확인

시작하려면 [Azure AD PowerShell 모듈을 다운로드 및 설치](/powershell/module/Azuread/)하고 [Azure AD 테넌트에 연결](/powershell/module/azuread/connect-azuread#examples)합니다.

모듈을 설치한 후 다음 단계를 사용하여 필요에 따라 각 작업을 완료합니다.

### <a name="check-the-expiration-policy-for-a-password"></a>암호에 대한 만료 정책 확인

1. PowerShell 프롬프트를 열고 ‘전역 관리자’ 또는 ‘사용자 관리자’ 계정을 사용하여 [Azure AD 테넌트에 연결](/powershell/module/azuread/connect-azuread#examples)합니다. 
1. 개별 사용자 또는 모든 사용자에 대해 다음 명령 중 하나를 실행합니다.

   * 단일 사용자의 암호가 만료되지 않도록 설정되어 있는지 확인하려면 다음 cmdlet을 실행합니다. `<user ID>`를 확인하려는 사용자의 사용자 ID(예: *driley\@contoso.onmicrosoft.com*)로 바꿉니다.

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * 모든 사용자에 대한 **암호 사용 기간 제한 없음** 설정을 보려면  cmdlet을 실행합니다.

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>암호가 만료되도록 설정

1. PowerShell 프롬프트를 열고 ‘전역 관리자’ 또는 ‘사용자 관리자’ 계정을 사용하여 [Azure AD 테넌트에 연결](/powershell/module/azuread/connect-azuread#examples)합니다. 
1. 개별 사용자 또는 모든 사용자에 대해 다음 명령 중 하나를 실행합니다.

   * 특정 사용자의 암호가 만료되도록 설정하려면 다음 cmdlet을 실행합니다. `<user ID>` 를 확인하려는 사용자의 사용자 ID(예: *driley\@ contoso.onmicrosoft.com*)로 바꿉니다.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * 조직의 모든 사용자 암호가 만료되도록 설정하려면 다음 cmdlet을 사용합니다. 

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>암호가 만료되지 않도록 설정

1. PowerShell 프롬프트를 열고 ‘전역 관리자’ 또는 ‘사용자 관리자’ 계정을 사용하여 [Azure AD 테넌트에 연결](/powershell/module/azuread/connect-azuread#examples)합니다. 
1. 개별 사용자 또는 모든 사용자에 대해 다음 명령 중 하나를 실행합니다.

   * 특정 사용자의 암호가 만료되지 않도록 설정하려면 다음 cmdlet을 실행합니다. `<user ID>` 를 확인하려는 사용자의 사용자 ID(예: *driley\@ contoso.onmicrosoft.com*)로 바꿉니다.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * 조직 내 모든 사용자의 암호를 기간 제한 없음으로 설정하려면 다음 cmdlet을 실행합니다.

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > `-PasswordPolicies DisablePasswordExpiration`로 설정된 암호는 계속해서 `pwdLastSet` 특성에 따라 사용 기간이 계산됩니다. `pwdLastSet` 특성에 따라, 만료를 `-PasswordPolicies None`로 변경한 경우 `pwdLastSet`이 90일보다 오래된 모든 암호는 다음 번에 로그인할 때 변경해야 합니다. 이 변경으로 많은 사용자가 영향을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계

SSPR을 시작하려면 [자습서: 사용자가 Azure Active Directory 셀프 서비스 암호 재설정을 사용하여 계정의 잠금을 해제하거나 암호를 다시 설정할 수 있도록 설정](tutorial-enable-sspr.md)을 참조하세요.

사용자의 SSPR에 문제가 발생하는 경우 [셀프 서비스 암호 재설정 문제 해결](./troubleshoot-sspr.md)을 참조하세요.
