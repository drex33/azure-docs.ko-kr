---
title: 메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인
description: 사용자가 메일을 대체 로그인 ID로 사용하여 Azure Active Directory에 로그인하도록 설정하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/07/2021
ms.author: justinha
author: calui
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 0a4ad5d9aaa9bb851a651ddc77bd1acb773b6019
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113485710"
---
# <a name="sign-in-to-azure-ad-with-email-as-an-alternate-login-id-preview"></a>메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인(미리 보기)

> [!NOTE]
> 메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인하는 것은 Azure Active Directory의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

여러 조직에서 사용자가 온-프레미스 디렉터리 환경과 동일한 자격 증명을 사용하여 Azure AD(Azure Active Directory)에 로그인하도록 지원하고자 합니다. 하이브리드 인증이라고 하는 이 방법을 사용하면 사용자는 한 세트의 자격 증명만 기억하면 됩니다.

일부 조직에서는 다음과 같은 이유로 하이브리드 인증으로 전환하지 않습니다.

* 기본적으로 Azure AD UPN(사용자 주체 이름)은 온-프레미스 UPN과 동일한 값으로 설정됩니다.
* Azure AD UPN을 변경하면 온-프레미스 환경과 Azure AD 환경 사이에 불일치가 발생하게 되며, 이로 인해 특정 애플리케이션과 서비스에서 문제가 생길 수 있습니다.
* 비즈니스 또는 규정 준수로 인해 조직에서는 온-프레미스 UPN을 사용하여 Azure AD에 로그인하는 방법을 사용하지 않으려고 합니다.

하이브리드 인증으로 전환하는 데 도움이 될 수 있도록, 사용자가 메일을 대체 로그인 ID로 사용하여 로그인하도록 Azure AD를 구성할 수 있습니다. 예를 들어 *Contoso* 가 레거시 `balas@contoso.com` UPN을 사용한 로그인을 지원하는 대신 *Fabrikam* 으로 리브랜딩되었다면 메일을 대체 로그인 ID로 사용할 수 있습니다. 애플리케이션 또는 서비스에 액세스하려면 사용자는 비 UPN 메일(예: `balas@fabrikam.com`)을 사용하여 Azure AD에 로그인하게 됩니다.

이 문서에서는 대체 로그인 ID로 메일을 사용하도록 설정하고 사용하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

다음은 대체 로그인 ID로 사용되는 메일에 대해 알아야 하는 사항입니다.

* 이 기능은 Azure AD Free 버전 이상에서 사용할 수 있습니다.
* 이 기능을 통해 클라우드 인증 Azure AD 사용자에 대해 확인된 도메인 *ProxyAddresses* 로 로그인하도록 설정할 수 있습니다.
* 사용자가 비 UPN 이메일을 사용하여 로그인하면 [ID 토큰](../develop/id-tokens.md)의 `unique_name` 및 `preferred_username` 클레임(있는 경우)이 비 UPN 이메일을 반환합니다.
* 이 기능은 PHS(암호 해시 동기화) 또는 PTA(통과 인증)로 관리되는 인증을 지원합니다.
* 이 기능을 구성하는 데는 다음 두 가지 옵션을 사용할 수 있습니다.
    * [HRD(홈 영역 검색) 정책](#enable-user-sign-in-with-an-email-address) - 전체 테넌트에 대해 해당 기능을 사용하도록 설정하려면 이 옵션을 사용합니다. 전역 관리자 권한이 필요합니다.
    * [단계적 출시 정책](#enable-staged-rollout-to-test-user-sign-in-with-an-email-address) - 특정 Azure AD 그룹에서 기능을 테스트하려면 이 옵션을 사용합니다. 전역 관리자 권한이 필요합니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

현재 미리 보기 상태에서 다음과 같은 제한 사항이 대체 로그인 ID로 사용되는 메일에 적용됩니다.

* **사용자 환경** - 비 UPN 이메일을 사용하여 로그인한 경우에도 사용자에게 UPN이 표시될 수 있습니다. 다음과 같은 동작이 관찰될 수 있습니다.
    * `login_hint=<non-UPN email>`을 사용하는 Azure AD 로그인으로 이동될 때 사용자에게 UPN을 사용하여 로그인하라는 메시지가 표시됩니다.
    * 사용자가 비 UPN 메일을 사용하여 로그인하고 잘못된 암호를 입력하면 "암호 입력" 페이지가 변경되어 UPN이 표시됩니다.
    * Microsoft Office 같은 일부 Microsoft 사이트 및 앱에서, 일반적으로 오른쪽 위에 표시되는 *계정 관리자* 컨트롤에 로그인하는 데 사용된 비 UPN 메일 대신 사용자의 UPN이 표시될 수 있습니다.

* **지원되지 않는 흐름** - 일부 흐름은 현재 다음과 같은 비 UPN 이메일과 호환되지 않습니다.
    * ID 보호는 비 UPN 메일을 *유출된 자격 증명* 위험 검색과 일치시키지 않습니다. 이 위험 검색은 UPN을 사용하여 유출된 자격 증명을 일치시킵니다. 자세한 내용은 [Azure AD ID 보호 위험 검색 및 수정][identity-protection]을 참조하세요.
    * 비 UPN 메일에 전송된 B2B 초대가 완전히 지원되지는 않습니다. 비 UPN 메일에 전송된 초대를 수락하면 리소스 테넌트 엔드포인트의 게스트 사용자에 대해 비 UPN 메일을 사용한 로그인이 작동하지 않을 수 있습니다.
    * 사용자가 비 UPN 메일을 사용하여 로그인한 경우에는 암호를 변경할 수 없습니다. Azure AD SSPR(셀프 서비스 암호 재설정)가 예상대로 작동해야 합니다. SSPR 동안 사용자는 대체 메일을 통해 자신의 ID를 확인하는 경우 UPN을 볼 수 있습니다.

* **지원되지 않는 시나리오** - 다음 시나리오는 지원되지 않습니다. 다음에 대해 비 UPN 이메일을 사용하여 로그인
    * [하이브리드 Azure AD 가입 디바이스](../devices/concept-azure-ad-join-hybrid.md)
    * [Azure AD 조인 디바이스](../devices/concept-azure-ad-join.md)
    * [Azure AD 등록 디바이스](../devices/concept-azure-ad-register.md)
    * [매끄러운 SSO](../hybrid/how-to-connect-sso.md)
    * [ROPC(리소스 소유자 암호 자격 증명)를 사용하는 애플리케이션](../develop/v2-oauth-ropc.md)
    * POP3 및 SMTP와 같은 레거시 인증을 사용하는 애플리케이션
    * 비즈니스용 Skype
    * macOS의 Microsoft Office
    * 웹상의 Microsoft Teams
    * OneDrive(로그인 흐름에 Multi-Factor Authentication이 포함되지 않는 경우)

* **지원되지 않는 앱** - `unique_name` 또는 `preferred_username` 클레임이 불변하거나 항상 UPN과 같은 특정 사용자 특성과 일치한다고 가정하는 경우 일부 타사 애플리케이션이 예상대로 작동하지 않을 수 있습니다.

* **로깅** - HRD 정책에서 수행한 기능 구성 변경 내용은 감사 로그에 명시적으로 표시되지 않습니다. 또한 로그인 로그의 *로그인 식별자 유형* 필드가 항상 정확하지 않을 수 있으며 기능이 로그인에 사용되었는지 여부를 확인하는 데 사용하면 안 됩니다.

* **단계적 롤아웃 정책** - 다음 제한은 단계적 롤아웃 정책을 사용하여 기능을 사용하도록 설정한 경우에만 적용됩니다.
    * 여러 단계적 롤아웃 정책에 포함된 사용자의 경우 기능이 예상대로 작동하지 않습니다.
    * 단계적 롤아웃 정책은 기능당 최대 10개의 그룹을 지원합니다.
    * 단계적 롤아웃 정책은 중첩된 그룹을 지원하지 않습니다.
    * 단계적 롤아웃 정책은 동적 그룹을 지원하지 않습니다.
    * 그룹 내의 연락처 개체는 그룹이 단계적 롤아웃 정책에 추가되는 것을 차단합니다.

* **중복 값** - 테넌트 내에서 클라우드 전용 사용자의 UPN은 온-프레미스 디렉터리에서 동기화된 다른 사용자의 프록시 주소와 같은 값일 수 있습니다. 이 시나리오에서 해당 기능을 사용하도록 설정하면 클라우드 전용 사용자가 UPN으로 로그인할 수 없습니다. 이 문제는 [문제 해결](#troubleshoot) 섹션에 자세히 설명되어 있습니다.

## <a name="overview-of-alternate-login-id-options"></a>대체 로그인 ID 옵션 개요
Azure AD에 로그인하려면 사용자가 계정을 고유하게 식별하는 값을 입력합니다. 이전에는 Azure AD UPN만 로그인 식별자로 사용할 수 있었습니다.

온-프레미스 UPN이 사용자의 기본 설정 로그인 메일인 조직의 경우 이 접근 방식을 사용하는 것이 좋습니다. 이러한 조직은 Azure AD UPN을 온-프레미스 UPN과 정확히 동일한 값으로 설정하고 사용자는 일관된 로그인 환경을 사용합니다.

### <a name="alternate-login-id-for-ad-fs"></a>AD FS에 대한 대체 로그인 ID

그러나 일부 조직에서는 온-프레미스 UPN을 로그인 식별자로 사용하지 않습니다. 온-프레미스 환경에서는 대체 로그인 ID를 사용하여 로그인할 수 있도록 로컬 AD DS를 구성합니다. Azure AD UPN을 온-프레미스 UPN과 동일한 값으로 설정하는 것은 Azure AD에서 사용자가 해당 값을 사용하여 로그인해야 하기 때문에 선택 사항이 아닙니다.

### <a name="alternate-login-id-in-azure-ad-connect"></a>Azure AD Connect의 대체 로그인 ID

이 문제에 대한 일반적인 해결 방법은 Azure AD UPN을 사용자가 로그인에 사용할 메일 주소로 설정하는 것입니다. 이 방법은 유효하지만 온-프레미스 AD와 Azure AD 간에 UPN이 달라지며, 이 구성이 모든 Microsoft 365 워크로드와 호환되지는 않습니다.

### <a name="email-as-an-alternate-login-id"></a>메일을 대체 로그인 ID로 사용

다른 방법은 Azure AD와 온-프레미스 UPN을 동일한 값으로 동기화한 다음 사용자가 확인된 메일을 사용하여 Azure AD에 로그인할 수 있도록 Azure AD를 구성하는 것입니다. 이 기능을 제공하려면 온-프레미스 디렉터리에서 사용자의 *ProxyAddresses* 특성에 하나 이상의 메일 주소를 정의합니다. 그러면 *ProxyAddresses* 가 Azure AD Connect를 사용하여 자동으로 Azure AD에 동기화됩니다.


| 옵션 | Description |
|---|---|
| [AD FS에 대한 대체 로그인 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) | AD FS 사용자에 대해 대체 특성(예: 메일)으로 로그인하도록 설정합니다. |
| [Azure AD Connect의 대체 로그인 ID](../hybrid/plan-connect-userprincipalname.md#alternate-login-id) | 대체 특성(예: 메일)을 Azure AD UPN으로 동기화합니다. |
| 메일을 대체 로그인 ID로 사용 | Azure AD 사용자에 대해 확인된 도메인 *ProxyAddresses* 으로 로그인하도록 설정합니다. |

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>로그인 메일 주소를 Azure AD에 동기화

기존 AD DS(Active Directory Domain Services) 또는 AD FS(Active Directory Federation Services) 인증은 네트워크에서 직접 수행되며 AD DS 인프라에서 처리됩니다. 하이브리드 인증을 사용하면 사용자가 대신 Azure AD에 직접 로그인할 수 있습니다.

이 하이브리드 인증 방법을 지원하려면 [Azure AD Connect][azure-ad-connect]를 사용하여 온-프레미스 AD DS 환경을 Azure AD에 동기화하고 PHS 또는 PTA를 사용하도록 구성해야 합니다. 자세한 내용은 [Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택][hybrid-auth-methods]을 참조하세요.

두 가지 구성 옵션에서 모두 사용자가 사용자 이름과 암호를 Azure AD에 제출하면 Azure AD가 자격 증명의 유효성을 검사하고 티켓을 발급합니다. 사용자가 Azure AD에 로그인하면 조직에서 AD FS 인프라를 호스트하고 관리할 필요가 없어집니다.

Azure AD Connect에 의해 자동으로 동기화되는 사용자 특성 중 하나는 *ProxyAddresses* 입니다. 사용자의 메일 주소가 *ProxyAddresses* 특성의 일부로 온-프레미스 AD DS 환경에 정의된 경우 이 특성이 Azure AD에 자동으로 동기화됩니다. 이 메일 주소는 Azure AD 로그인 프로세스에서 대체 로그인 ID로 직접 사용될 수 있습니다.

> [!IMPORTANT]
> 테넌트에 대해 확인된 도메인의 메일만 Azure AD에 동기화됩니다. 각 Azure AD 테넌트에는 소유권이 입증되어 테넌트에 고유하게 바인딩된 확인된 도메인이 하나 이상 있습니다.
>
> 자세한 내용은 [Azure AD에서 사용자 지정 도메인 이름 추가 및 확인][verify-domain]을 참조하세요.

## <a name="enable-user-sign-in-with-an-email-address"></a>메일 주소를 사용한 사용자 로그인 사용

> [!NOTE]
> 이 구성 옵션은 HRD 정책을 사용합니다. 자세한 내용은 [homeRealmDiscoveryPolicy 리소스 종류](/graph/api/resources/homeRealmDiscoveryPolicy?view=graph-rest-1.0&preserve-view=true)를 참조 하세요.

*ProxyAddresses* 특성이 적용된 사용자가 Azure AD Connect를 사용하여 Azure AD와 동기화되면 사용자가 테넌트의 대체 로그인 ID로 메일을 사용하여 로그인하는 기능을 사용하도록 설정해야 합니다. 이 기능은 Azure AD 로그인 서버에 UPN 값을 기준으로 로그인 식별자를 확인하고 *ProxyAddresses* 값을 기준으로 메일 주소를 확인하라고 명령합니다.

미리 보기 단계에서는 오직 PowerShell만 사용하여 대체 로그인 ID 기능으로 메일을 사용한 로그인을 사용하도록 설정할 수 있습니다. 다음 단계를 완료하려면 *전역 관리자* 권한이 필요합니다.

1. 관리자 권한으로 PowerShell 세션을 연 다음 [Install-Module][Install-Module] cmdlet을 사용하여 *AzureADPreview* 모듈을 설치합니다.

    ```powershell
    Install-Module AzureADPreview
    ```

    메시지가 표시되면 **Y** 를 선택하여 NuGet을 설치하거나 신뢰할 수 없는 리포지토리에서 설치합니다.

1. [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 *전역 관리자* 로 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

    이 명령은 계정, 환경 및 테넌트 ID에 대한 정보를 반환합니다.

1. 다음과 같이 [Get-AzureADPolicy][Get-AzureADPolicy] cmdlet을 사용하여 *HomeRealmDiscoveryPolicy* 가 테넌트에 이미 있는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. 현재 구성된 정책이 없는 경우 이 명령은 아무것도 반환하지 않습니다. 정책이 반환되면 이 단계를 건너뛰고 다음 단계로 넘어가서 기존 정책을 업데이트합니다.

    테넌트에 *HomeRealmDiscoveryPolicy* 정책을 추가하려면 다음 예에서와 같이 [New-AzureADPolicy][New-AzureADPolicy] cmdlet을 사용하여 *AlternateIdLogin* 특성을 *“Enabled”: true* 로 설정합니다.

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    정책을 성공적으로 만들면 다음 예제 출력에서와 같이 명령이 정책 ID를 반환합니다.

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 구성된 정책이 이미 있는 경우 다음 예제의 정책 출력에서와 같이  *AlternateIdLogin*  특성을 사용하도록 설정했는지 확인합니다.

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    정책이 있지만 *AlternateIdLogin* 특성이 없거나 사용하도록 설정되지 않은 경우, 또는 정책에 유지하고 싶은 다른 특성이 존재하는 경우 [Set-AzureADPolicy][Set-AzureADPolicy] cmdlet을 사용하여 기존 정책을 업데이트합니다.

    > [!IMPORTANT]
    > 정책을 업데이트할 때 이전 설정과 새로운  *AlternateIdLogin* 특성이 포함되었는지 확인하세요.

    다음 예제에서는  *AlternateIdLogin* 특성을 추가하고  *AllowCloudPasswordValidation* 특성을 유지합니다(이 특성은 이미 설정되었을 수 있습니다).

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    업데이트된 정책에 변경 내용이 표시되며 *AlternateIdLogin* 특성이 이제 사용하도록 설정되었는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

정책이 적용되면, 사용자가 대체 로그인 ID를 사용하여 로그인할 수 있도록 전파하는 데 최대 1시간이 걸릴 수 있습니다.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>메일 주소를 사용하는 사용자 로그인을 테스트하기 위해 단계적 롤아웃 사용  

> [!NOTE]
>이 구성 옵션은 단계적 출시 정책을 사용합니다. 자세한 내용은 [featureRolloutPolicy 리소스 종류](/graph/api/resources/featurerolloutpolicy?preserve-view=true&view=graph-rest-1.0)를 참조하세요.

단계적 롤아웃 정책을 사용하면 테넌트 관리자가 특정 Azure AD 그룹에 대해 기능을 사용하도록 설정할 수 있습니다. 테넌트 관리자는 단계적 롤아웃을 사용하여 메일 주소를 사용하는 사용자 로그인을 테스트하는 것이 좋습니다. 관리자가 전체 테넌트에 이 기능을 배포할 준비가 되면 [HRD 정책](#enable-user-sign-in-with-an-email-address)을 사용해야 합니다.  


다음 단계를 완료하려면 *전역 관리자* 권한이 필요합니다.

1. 관리자 권한으로 PowerShell 세션을 연 다음 [Install-Module][Install-Module] cmdlet을 사용하여 *AzureADPreview* 모듈을 설치합니다.

    ```powershell
    Install-Module AzureADPreview
    ```

    메시지가 표시되면 **Y** 를 선택하여 NuGet을 설치하거나 신뢰할 수 없는 리포지토리에서 설치합니다.

1. [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 *전역 관리자* 로 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

    이 명령은 계정, 환경 및 테넌트 ID에 대한 정보를 반환합니다.

1. 다음 cmdlet을 사용하여 기존의 모든 단계적 롤아웃 정책을 나열합니다.
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

1. 이 기능에 대한 기존의 단계적 롤아웃 정책이 없으면 새 단계적 롤아웃 정책을 만들고 정책 ID를 기록해 둡니다.

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

1. 단계적 롤아웃 정책에 추가할 그룹의 directoryObject ID를 찾습니다. *Id* 매개 변수에 반환된 값을 기록해 둡니다. 이 값은 다음 단계에서 사용됩니다.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

1. 다음 예제에 표시된 대로 단계적 롤아웃 정책을 그룹에 추가합니다. *-Id* 매개 변수의 값을 4단계에서 정책 Id에 반환된 값으로 바꾸고 *-RefObjectId* 매개 변수의 값을 5단계에서 기록한 *Id* 로 바꿉니다. 그룹의 사용자가 메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인하는 데 최대 1시간이 걸릴 수 있습니다.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
그룹에 추가된 새 멤버의 경우 메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인하는 데 최대 24시간이 걸릴 수 있습니다.

### <a name="removing-groups"></a>그룹 제거

단계적 롤아웃 정책에서 그룹을 제거하려면 다음 명령을 실행합니다.

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>정책 제거

단계적 롤아웃 정책을 제거하려면 먼저 정책을 사용하지 않도록 설정한 다음 시스템에서 제거합니다.

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="test-user-sign-in-with-an-email-address"></a>메일 주소를 사용한 사용자 로그인 테스트

사용자가 메일로 로그인할 수 있는지 테스트하려면 [https://myprofile.microsoft.com][my-profile]으로 이동한 후 비 UPN 메일(예: `balas@fabrikam.com` )로 로그인합니다. 로그인 환경은 UPN으로 로그인할 때와 모양과 느낌이 동일합니다.

## <a name="troubleshoot"></a>문제 해결

메일 주소를 사용하여 로그인하는 데 문제가 있는 경우 다음과 같은 문제 해결 단계를 검토하세요.

1. 메일을 대체 로그인 ID로 사용하도록 설정한 후 1시간 이상 경과했는지 확인합니다. 사용자가 최근에 단계적 출시 정책을 위해 그룹에 추가된 경우 그룹에 추가된 후 24시간 이상 경과했는지 확인합니다.
1. HRD 정책을 사용하는 경우 Azure AD *HomeRealmDiscoveryPolicy* 에서 *AlternateIdLogin* 정의 속성이 *"Enabled": true* 로 설정되고 *IsOrganizationDefault* 속성이 *True* 로 설정되어 있는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```
    단계적 출시 정책을 사용하는 경우 Azure AD *FeatureRolloutPolicy* 의 *IsEnabled* 속성이 *True* 로 설정되어 있는지 확인합니다.

    ```powershell
    Get-AzureADMSFeatureRolloutPolicy
    ```
1. 사용자 계정에서 Azure AD의 *ProxyAddresses* 특성에 메일 주소가 설정되었는지 확인합니다.

### <a name="conflicting-values-between-cloud-only-and-synced-users"></a>클라우드 전용 사용자와 동기화된 사용자 간에 값 충돌

테넌트 내에서 클라우드 전용 사용자의 UPN은 온-프레미스 디렉터리에서 동기화된 다른 사용자의 프록시 주소와 같은 값일 수 있습니다. 이 시나리오에서 해당 기능을 사용하도록 설정하면 클라우드 전용 사용자가 UPN으로 로그인할 수 없습니다. 이 이슈 발생 경우를 감지하기 위한 단계는 다음과 같습니다.

1. 관리자 권한으로 PowerShell 세션을 연 다음 [Install-Module][Install-Module] cmdlet을 사용하여 *AzureADPreview* 모듈을 설치합니다.

    ```powershell
    Install-Module AzureADPreview
    ```

    메시지가 표시되면 **Y** 를 선택하여 NuGet을 설치하거나 신뢰할 수 없는 리포지토리에서 설치합니다.

1. [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 *전역 관리자* 로 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

1. 영향을 받는 사용자를 확인합니다.

    ```powershell
    # Get all users
    $allUsers = Get-AzureADUser -All $true
    
    # Get list of proxy addresses from all synced users
    $syncedProxyAddresses = $allUsers |
        Where-Object {$_.ImmutableId} |
        Select-Object -ExpandProperty ProxyAddresses |
        ForEach-Object {$_ -Replace "smtp:", ""}
    
    # Get list of user principal names from all cloud-only users
    $cloudOnlyUserPrincipalNames = $allUsers |
        Where-Object {!$_.ImmutableId} |
        Select-Object -ExpandProperty UserPrincipalName
    
    # Get intersection of two lists
    $duplicateValues = $syncedProxyAddresses |
        Where-Object {$cloudOnlyUserPrincipalNames -Contains $_}
    ``` 

1. 영향을 받는 사용자를 출력하려면 다음을 수행합니다.

    ```powershell
    # Output affected synced users
    $allUsers |
        Where-Object {$_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    
    # Output affected cloud-only users
    $allUsers |
        Where-Object {!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    ```

1. 영향을 받는 사용자를 CSV로 출력하려면 다음을 수행합니다.

    ```powershell
    # Output affected users to CSV
    $allUsers |
        Where-Object {
            ($_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0) -Or
            (!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName)
        } |
        Select-Object ObjectId, DisplayName, UserPrincipalName, @{n="ProxyAddresses"; e={$_.ProxyAddresses -Join ','}}, @{n="IsSyncedUser"; e={$_.ImmutableId.Length -GT 0}}, UserType |
        Export-Csv -Path .\AffectedUsers.csv -NoTypeInformation
    ```

## <a name="next-steps"></a>다음 단계

Azure AD 앱 프록시 또는 Azure AD Domain Services와 같은 하이브리드 ID에 대해 자세히 알아보려면 [온-프레미스 워크로드의 액세스 및 관리에 Azure AD 하이브리드 ID 사용][hybrid-overview]을 참조하세요.

하이브리드 ID 작업에 대한 자세한 내용은 [암호 해시 동기화][phs-overview] 또는 [통과 인증][pta-overview] 동기화가 작동하는 방식을 참조하세요.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com