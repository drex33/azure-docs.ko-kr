---
title: Azure AD Connect 동기화로 암호 해시 동기화 구현 | Microsoft Docs
description: 암호 해시 동기화 작동 방식 및 설정 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/01/2021
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9eb9843e9ce0d59c28b2011164611e2a54e2b68
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215374"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현
이 문서에서는 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure Active Directory(Azure AD) 인스턴스로 사용자 암호를 동기화하는 데 필요한 정보를 제공합니다.

## <a name="how-password-hash-synchronization-works"></a>암호 해시 동기화 작동 방식
Active Directory 도메인 서비스는 실제 사용자 암호의 해시 값 표현 형태로 암호를 저장합니다. 해시 값은 단방향 수학 함수(*해시 알고리즘*)의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 

암호를 동기화 하기 위해, Azure AD Connect 동기화는 온-프레미스 Active Directory 인스턴스에서 암호 해시를 추출합니다. Azure Active Directory 인증 서비스로 동기화 되기 전에 암호 해시에 추가적인 보안 처리가 적용됩니다. 암호는 각 사용자 기본별로 동기화되고 시간 순서대로 동기화됩니다.

암호 해시 동기화 과정의 실제 데이터 흐름은 사용자 데이터의 동기화와 비슷합니다. 그러나 암호는 다른 특성에 대한 표준 디렉터리 동기화 창보다 더 자주 동기화됩니다. 암호 해시 동기화 프로세스는 2분마다 실행됩니다. 이 프로세스의 빈도를 수정할 수 없습니다. 암호를 동기화할 경우 기존 클라우드 암호를 덮어씁니다.

암호 해시 동기화 기능을 사용하도록 처음으로 설정하면 범위 내 모든 사용자 암호에 대한 초기 동기화를 수행합니다. 동기화할 사용자 암호의 하위 집합을 명시적으로 정의할 수 없습니다. 그러나 커넥터가 여러 개인 경우 일부 커넥터에 대해서는 암호 해시 동기화를 사용하지 않도록 설정할 수 있지만, [Set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) cmdlet을 사용하는 다른 커넥터에는 암호 해시 동기화를 사용하지 않도록 설정할 수 없습니다.

온-프레미스 암호를 변경하면, 업데이트된 암호는 대개 몇 분 내에 동기화됩니다.
암호 해시 동기화 기능은 실패한 사용자 암호 동기화를 자동으로 다시 시도합니다. 암호를 동기화하는 동안 오류가 발생하면 이벤트 뷰어에 오류가 기록됩니다.

암호 동기화는 현재 로그인된 사용자에게 아무런 영향도 미치지 않습니다.
클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 서비스 세션이 즉시 영향을 받지는 않습니다. 하지만, 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

사용자는 회사 네트워크에 로그인되어 있는지 여부에 관계없이 Azure AD에 인증하려면 회사 자격 증명을 다시 입력해야 합니다. 하지만 사용자가 로그인 시 KMSI(로그인 유지) 확인란을 선택하면 이 패턴을 최소화할 수 있습니다. 이 확인란을 선택하면 180일 동안 인증을 우회하는 세션 쿠키가 설정됩니다. KMSI 동작은 Azure AD 관리자가 설정 또는 해제할 수 있습니다. 또한 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자가 자동으로 로그인되는 [Seamless SSO](how-to-connect-sso.md)를 켜서 암호 프롬프트를 줄일 수 있습니다.

> [!NOTE]
> 암호 동기화는 Active Directory의 개체 형식 사용자에만 지원됩니다. iNetOrgPerson 개체 형식에 대해 지원되지 않습니다.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>암호 해시 동기화의 작동 방식에 대한 자세한 설명

다음 섹션에서는 Active Directory와 Azure AD 간에 암호 해시 동기화가 작동하는 방식에 대해 자세히 설명합니다.

[![자세한 암호 흐름](./media/how-to-connect-password-hash-synchronization/arch3d.png)](./media/how-to-connect-password-hash-synchronization/arch3d.png#lightbox)

1. 2분마다 AD Connect 서버의 암호 해시 동기화 에이전트는 DC에서 저장된 암호 해시(unicodePwd 특성)를 요청합니다.  이 요청은 DC 간 데이터를 동기화하는 데 사용된 표준 [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) 복제 프로토콜을 통해 전송됩니다. 암호 해시를 얻으려면 서비스 계정에 디렉터리 변경 내용 복제 및 모든 디렉터리 변경 내용 복제 AD 권한(설치 시 기본적으로 부여)이 있어야 합니다.
2. 전송하기 전에 DC는 RPC 세션 키의 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) 해시인 키와 솔트를 사용하여 MD4 암호 해시를 암호화합니다. 그런 다음, RPC를 통해 암호 해시 동기화 에이전트에 결과를 전송합니다. 또한 DC는 DC 복제 프로토콜을 사용하여 동기화 에이전트에 솔트를 전달하고, 따라서 에이전트는 봉투(Envelope)를 해독할 수 있게 됩니다.
3. 암호 해시 동기화 에이전트는 봉투(Envelope)를 암호화한 후 [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider)와 솔트를 사용하여 수신한 데이터를 원래 MD4 형식으로 해독하는 키를 생성합니다. 암호 해시 동기화 에이전트는 절대 일반 텍스트 암호에 액세스할 수 없습니다. 암호 해시 동기화 에이전트의 MD5 사용은 DC와의 복제 프로토콜 호환성으로 그 용도가 엄격하게 제한되며 DC와 암호 해시 동기화 에이전트 간의 온-프레미스에서만 사용됩니다.
4. 암호 해시 동기화 에이전트는 해시를 32바이트 16진수 문자열로 변환한 후 UTF-16 인코딩을 사용하여 이 문자열을 다시 이진으로 변환하는 방법으로 16바이트 이진 암호 해시를 64바이트로 확장합니다.
5. 암호 해시 동기화 에이전트는 10바이트 길이 솔트로 구성된 사용자별 솔트를 64비트 이진 파일에 추가하여 원래 해시의 보안을 강화합니다.
6. 그런 다음, 암호 해시 동기화 에이전트는 MD4 해시와 사용자별 솔트를 결합하고 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 함수에 입력합니다. [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) 키 지정 해시 알고리즘이 1000번 반복됩니다. 
7. 암호 해시 동기화 에이전트는 결과 32바이트 해시를 가져오고, 사용자별 솔트와 SHA256 반복 횟수를 해시에 연결하고(Azure AD가 사용할 수 있도록), TLS를 통해 Azure AD Connect에서 Azure AD로 문자열을 전송합니다.</br> 
8. 사용자가 Azure AD에 로그인을 시도하고 암호를 입력하면 암호가 동일한 MD4+salt+PBKDF2+HMAC-SHA256 프로세스를 통해 실행됩니다. 결과 해시가 Azure AD에 저장된 해시와 일치하고 사용자가 올바른 암호를 입력하면 해당 사용자가 인증됩니다.

> [!NOTE]
> 원래 MD4 해시는 Azure AD로 전송되지 않습니다. 대신 원래 MD4 해시의 SHA256 해시가 전송됩니다. 결과적으로 Azure AD에 저장된 해시를 습득하더라도 온-프레미스 pass-the-hash 공격에 사용할 수 없습니다.

### <a name="security-considerations"></a>보안 고려 사항

암호를 동기화할 때, 사용자 암호의 일반 텍스트 버전은 암호 해시 동기화 기능, Azure AD 혹은 다른 어떤 관련 서비스에 노출되지 않습니다.

사용자 인증은 조직의 고유한 Active Directory 인스턴스가 아닌 Azure AD에 대해 이루어집니다. Azure AD에 저장된 SHA256 암호 데이터(원래 MD4 해시의 해시)는 Active Directory에 저장된 것보다 안전합니다. 뿐만 아니라 이 SHA256 해시는 해독이 불가능하기 때문에 조직의 Active Directory 환경으로 가져와서 pass-the-hash 공격에서 유효한 사용자 암호로 표시할 수 없습니다.

### <a name="password-policy-considerations"></a>암호 정책 고려 사항

암호 해시 동기화를 사용할 경우, 영향을 받는 두 가지 정책이 있습니다.

* 암호 복잡성 정책
* 암호 만료 정책

#### <a name="password-complexity-policy"></a>암호 복잡성 정책

암호 해시 동기화를 사용하는 경우 온-프레미스 Active Directory 인스턴스의 암호 복잡성 정책이 동기화된 사용자에 대한 클라우드의 복잡성 정책을 재정의합니다. 온-프레미스 Active Directory 인스턴스의 유효한 모든 암호를 사용하여 Azure AD 서비스에 액세스할 수 있습니다.

> [!NOTE]
> 클라우드에서 직접 만든 사용자의 암호는 클라우드 내에서 정의된 암호 정책을 계속 따릅니다.

#### <a name="password-expiration-policy"></a>암호 만료 정책

사용자가 암호 해시 동기화 범위 내에 있을 경우 클라우드 계정 암호는 기본적으로 *사용 기간 제한 없음* 으로 설정됩니다.

온-프레미스 환경에서 만료된 동기화된 암호를 사용하여 클라우드 서비스에 계속 로그인할 수 있습니다. 클라우드 암호는 온-프레미스 환경에서 다음에 암호를 변경할 때 업데이트됩니다.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Azure AD 통합 서비스에만 상호 작용하고 암호 만료 정책도 준수해야 하는 동기화된 사용자가 있는 경우 *EnforceCloudPasswordPolicyForPasswordSyncedUsers* 기능을 사용 설정하여 사용자가 Azure AD 암호 만료 정책을 준수하도록 강제할 수 있습니다.

*EnforceCloudPasswordPolicyForPasswordSyncedUsers* 가 사용하지 않도록 설정된 경우(기본 설정) Azure AD Connect는 동기화된 사용자의 PasswordPolicies 특성을 "DisablePasswordExpiration"으로 설정합니다. 이 작업은 사용자의 암호가 동기화될 때마다 수행되며 해당 사용자에 대해 클라우드 암호 만료 정책을 무시하도록 Azure AD에 지시합니다. 이 특성 값은 Azure AD PowerShell 모듈에서 다음 명령을 사용하여 확인할 수 있습니다.

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

EnforceCloudPasswordPolicyForPasswordSyncedUsers 기능을 사용하도록 설정하려면 아래와 같이 MSOnline PowerShell 모듈에서 다음 명령을 실행합니다. 아래와 같이 Enable 매개 변수에 yes를 입력해야 합니다.

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

사용하도록 설정되면 Azure AD가 PasswordPolicies 특성에서 `DisablePasswordExpiration` 값을 제거하기 위해 동기화된 각 사용자로 이동하지 않습니다. 대신 온-프레미스 AD에서 다음 번에 암호를 변경할 때 각 사용자에 대한 다음 번 암호 해시 동기화 중에 PasswordPolicies에서 `DisablePasswordExpiration` 값을 제거합니다.

암호 해시 동기화를 사용하도록 설정하기 전에 EnforceCloudPasswordPolicyForPasswordSyncedUsers를 사용하도록 설정하여 암호 해시의 최초 동기화 시 사용자의 PasswordPolicies 특성에 `DisablePasswordExpiration` 값을 추가하지 않도록 하는 것이 좋습니다.

기본 Azure AD 암호 정책에서는 사용자가 90일마다 암호를 변경해야 합니다. AD의 정책에서도 90일이면 두 정책이 일치해야 합니다. 하지만 AD 정책에서 90일이 아니라면 Set-MsolPasswordPolicy PowerShell 명령을 사용하여 일치하도록 Azure AD 암호 정책을 업데이트하면 됩니다.

Azure AD는 등록된 도메인별로 별도의 암호 만료 정책을 지원합니다.

주의: Azure AD에서 만료되지 않는 암호를 포함해야 하는 동기화된 계정이 있는 경우, Azure AD에서 사용자 개체의 PasswordPolicies 특성에 `DisablePasswordExpiration` 값을 명시적으로 추가해야 합니다.  이 작업은 다음 명령을 실행하여 수행할 수 있습니다.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> 페더레이션된 도메인에는 Set-MsolPasswordPolicy PowerShell 명령이 작동하지 않습니다. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>임시 암호 동기화 및 "다음 번 로그온 시 암호 강제 변경 적용"

특히 관리자 암호가 재설정된 후에 사용자가 처음 로그온하면 암호를 변경하도록 강제하는 것이 일반적입니다.  이는 일반적으로 "임시" 암호 설정으로 알려져 있으며 AD(Active Directory)에서 사용자 개체에 "다음 번 로그온 시 사용자가 암호를 변경해야 합니다" 플래그를 체크하여 수행합니다.
  
임시 암호 기능은 두 명 이상의 개인이 해당 자격 증명을 알고 있는 시간을 최소화하기 위해 처음 사용할 때 자격 증명의 소유권 이전을 완료하도록 만드는 데 도움이 됩니다.

Azure AD에서 동기화된 사용자에 대해 임시 암호를 지원하려면 Azure AD Connect 서버에서 다음 명령을 실행하여 *ForcePasswordChangeOnLogOn* 기능을 사용하도록 설정하면 됩니다.

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> 사용자가 다음 번에 로그온할 때 암호를 변경하도록 강제하면 동시에 암호 변경이 필요합니다.  Azure AD Connect는 자체적으로 암호 강제 변경 플래그를 선택하지 않으며, 암호 해시 동기화 중에 발생하는 감지된 암호 변경을 보완합니다.

> [!CAUTION]
> 테넌트에서 SSPR 및 암호 쓰기 저장이 사용 설정된 경우에만 이 기능을 사용할 수 있습니다.  이는 사용자가 SSPR을 통해 암호를 변경한 경우에 Active Directory와 동기화되도록 하기 위한 것입니다.

#### <a name="account-expiration"></a>계정 만료

조직에서 사용자 계정 관리의 일부로 accountExpires 특성을 사용하는 경우 이 특성은 Azure AD와 동기화되지 않습니다. 결과적으로 암호 해시 동기화를 위해 구성된 환경의 만료된 Active Directory 계정은 Azure AD에서 계속 활성 상태입니다. 계정이 만료되면 워크플로 작업에서 사용자의 Azure AD 계정을 비활성화하는 PowerShell 스크립트를 트리거하는([Set-AzureADUser](/powershell/module/azuread/set-azureaduser) cmdlet를 사용하는) 것이 좋습니다. 반대로 계정이 켜지면 Azure AD 인스턴스도 켜져야 합니다.

### <a name="overwrite-synchronized-passwords"></a>동기화된 암호 덮어쓰기

관리자는 Windows PowerShell을 사용하여 사용자 암호를 수동으로 재설정할 수 있습니다.

이런 경우, 새 암호는 사용자의 동기화된 암호를 재정의하고 클라우드 내에 정의된 모든 암호 정책이 새 암호에 적용됩니다.

사용자가 온-프레미스 암호를 다시 변경하면, 새 암호는 클라우드에 동기화되며, 수동으로 업데이트한 암호를 재정의합니다.

암호 동기화는 로그인된 Azure 사용자에게 아무런 영향도 미치지 않습니다. 클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 세션이 즉시 영향을 받지는 않습니다. KMSI는 이런 차이가 나는 기간을 연장합니다. 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

### <a name="additional-advantages"></a>추가적인 이점

- 일반적으로 암호 해시 동기화는 페더레이션 서비스보다 더 쉽게 구현할 수 있습니다. 추가 서버가 필요 없으며, 사용자를 인증하기 위해 고가용성의 페더레이션 서비스에 의존하지 않아도 됩니다.
- 페더레이션 외에 암호 해시 동기화를 사용하도록 설정할 수도 있습니다. 페더레이션 서비스에 중단이 발생하는 경우 대체 서비스로 사용될 수 있습니다.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Azure AD Domain Services에 대한 암호 해시 동기화 프로세스

Azure AD Domain Services를 사용하여 Kerberos, LDAP 또는 NTLM을 사용해야 하는 애플리케이션 및 서비스에 레거시 인증을 제공하는 경우, 일부 프로세스가 암호 해시 동기화 흐름의 일부로 추가됩니다. Azure AD Connect는 Azure AD Domain Services에서 사용할 수 있도록 다음의 추가 프로세스를 사용하여 암호 해시를 Azure AD에 동기화합니다.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.
>
> Azure AD Connect는 Azure AD 테넌트에 대해 Azure AD DS를 사용하도록 설정한 경우에만 레거시 암호 해시를 동기화합니다. Azure AD Connect만을 사용하여 온-프레미스 AD DS 환경을 Azure AD와 동기화하는 경우에는 다음 단계가 사용되지 않습니다.
>
> 레거시 애플리케이션에서 NTLM 인증 또는 LDAP 단순 바인딩을 사용하지 않는 경우, Azure AD DS에 NTLM 암호 해시 동기화를 사용하지 않는 것이 좋습니다. 자세한 내용은 [취약한 암호화 제품군 비활성화 및 NTLM 자격 증명 해시 동기화](../../active-directory-domain-services/secure-your-domain.md)를 참조하세요.

1. Azure AD Connect는 테넌트의 Azure AD Domain Services 인스턴스에 대한 공개 키를 검색합니다.
1. 사용자가 암호를 변경하면 온-프레미스 도메인 컨트롤러는 암호 변경 결과(해시)를 다음 두 가지 특성에 저장합니다.
    * NTLM 암호 해시를 *unicodePwd* 에 저장.
    * Kerberos 암호 해시를 *supplementalCredentials* 에 저장.
1. Azure AD Connect는 디렉터리 복제 채널을 통해 암호 변경을 감지합니다(다른 도메인 컨트롤러에 복제해야 하는 특성 변경).
1. 암호가 변경된 각 사용자에 대해 Azure AD Connect는 다음 단계를 수행합니다.
    * 임의의 AES 256비트 대칭 키를 생성합니다.
    * 첫 번째 암호화 라운드에 필요한 임의의 초기화 벡터를 생성합니다.
    * *supplementalCredentials* 특성에서 Kerberos 암호 해시를 추출합니다.
    * Azure AD Domain Services 보안 구성 *SyncNtlmPasswords* 설정을 확인합니다.
        * 이 설정을 사용하지 않도록 설정된 경우, 임의의 높은 엔트로피 NTLM 해시를 생성합니다(사용자 암호와는 다름). 그런 다음, 이 해시를 *supplementalCrendetials* 특성에서 추출된 Kerberos 암호 해시와 하나의 데이터 구조로 결합합니다.
        * 이 설정을 사용하도록 설정된 경우, *unicodePwd* 특성 값을 *supplementalCredentials* 특성에서 추출된 Kerberos 암호 해시와 하나의 데이터 구조로 결합합니다.
    * AES 대칭 키를 사용하여 단일 데이터 구조를 암호화합니다.
    * 테넌트의 Azure AD Domain Services 공개 키를 사용하여 AES 대칭 키를 암호화합니다.
1. Azure AD Connect는 암호화된 AES 대칭 키, 암호 해시가 포함된 암호화된 데이터 구조 및 초기화 벡터를 Azure AD로 전송합니다.
1. Azure AD는 암호화된 AES 대칭 키, 암호화된 데이터 구조 및 사용자에 대한 초기화 벡터를 저장합니다.
1. Azure AD는 암호화된 HTTP 세션을 통해 내부 동기화 메커니즘을 사용하여 암호화된 AES 대칭 키, 암호화된 데이터 구조 및 초기화 벡터를 Azure AD Domain Services로 푸시합니다.
1. Azure AD Domain Services는 Azure Key Vault에서 테넌트의 인스턴스에 대한 프라이빗 키를 검색합니다.
1. 단일 사용자의 암호 변경을 나타내는 암호화된 각 데이터 세트에 대해 Azure AD Domain Services는 다음 단계를 수행합니다.
    * 프라이빗 키를 사용하여 AES 대칭 키를 해독합니다.
    * 초기화 벡터와 함께 AES 대칭 키를 사용하여 암호 해시가 포함된 암호화된 데이터 구조를 해독합니다.
    * 수신한 Kerberos 암호 해시를 Azure AD Domain Services 도메인 컨트롤러에 씁니다. 이 해시는 Azure AD Domain Services 도메인 컨트롤러의 공개 키로 암호화된 사용자 개체의 *supplementalCredentials* 특성에 저장됩니다.
    * Azure AD Domain Services는 수신한 NTLM 암호 해시를 Azure AD Domain Services 도메인 컨트롤러에 씁니다. 이 해시는 Azure AD Domain Services 도메인 컨트롤러의 공개 키로 암호화된 사용자 개체의 *unicodePwd* 특성에 저장됩니다.

## <a name="enable-password-hash-synchronization"></a>암호 해시 동기화 사용

>[!IMPORTANT]
>AD FS(또는 기타 페더레이션 기술)에서 암호 해시 동기화로 마이그레이션하는 경우 [여기](https://aka.ms/adfstophsdpdownload)에 게시된 자세한 배포 가이드를 따르는 것이 좋습니다.

**기본 설정** 옵션을 사용하여 Azure AD Connect를 설치할 경우, 암호 해시 동기화를 사용하도록 자동으로 설정됩니다. 자세한 내용은 [기본 설정을 사용하여 Azure AD Connect 시작](how-to-connect-install-express.md)을 참조하세요.

Azure AD Connect를 설치할 때 사용자 지정 설정을 사용하는 경우 사용자 로그인 페이지에서 암호 해시 동기화를 사용할 수 있습니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](how-to-connect-install-custom.md)를 참조하세요.

![암호 해시 동기화 사용](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>암호 해시 동기화 및 FIPS
서버가 FIPS(Federal Information Processing Standard)에 따라 잠긴 다음 MD5가 비활성화됩니다.

**암호 해시 동기화에 MD5를 사용하려면 다음 단계를 수행합니다.**

1. %programfiles%\Microsoft Azure AD Sync\Bin으로 이동합니다.
2. miiserver.exe.config를 엽니다.
3. 파일의 끝에서 구성/런타임 노드로 이동합니다.
4. 다음 노드를 추가 합니다. `<enforceFIPSPolicy enabled="false"/>`
5. 변경 내용을 저장합니다.

참조용으로 다음 조각처럼 보여야 합니다.

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

보안 및 FIPS에 대한 자세한 내용은 [Azure AD 암호 동기화, 암호화 및 FIPS 규정 준수](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)를 참조하세요.

## <a name="troubleshoot-password-hash-synchronization"></a>암호 해시 동기화 문제 해결
암호 해시 동기화에 문제가 있으면 [암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect 동기화: 사용자 지정 동기화 옵션](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
* [ADFS에서 암호 해시 동기화로 마이그레이션하기 위한 단계별 배포 계획 가져오기](https://aka.ms/authenticationDeploymentPlan)
