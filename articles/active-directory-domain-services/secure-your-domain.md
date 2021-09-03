---
title: Azure AD Domain Services 보호 | Microsoft Docs
description: Azure Active Directory Domain Services 관리되는 도메인에 대해 약한 암호화, 오래된 프로토콜, NTLM 암호 해시 동기화를 사용하지 않도록 설정하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/21/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe8f41c2ecf92034f81f2332aabee5a55df66a92
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439257"
---
# <a name="harden-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인 확정

기본적으로 Azure Active Directory Domain Services(Azure AD DS)는 NTLM v1및 TLS v1과 같은 암호를 사용할 수 있도록 합니다. 이러한 암호화는 일부 레거시 애플리케이션에 필요할 수 있지만 약한 암호화로 간주되며 필요하지 않은 경우 사용하지 않도록 설정할 수 있습니다. Azure AD Connect를 사용하여 온-프레미스 하이브리드 연결을 사용하는 경우 NTLM 암호 해시의 동기화를 사용하지 않도록 설정할 수도 있습니다.

이 문서에서는 다음과 같은 설정을 사용하여 관리되는 도메인을 강화하는 방법을 보여 줍니다. 

- NTLM v1 및 TLS v1 암호화 사용 안 함
- NTLM 암호 해시 동기화 사용 안 함
- RC4 암호화를 사용하여 암호를 변경하는 기능 사용 안 함
- Kerberos 아머링(armoring) 사용

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="use-security-settings-to-harden-your-domain"></a>보안 설정을 사용하여 도메인 강화

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure AD Domain Services** 를 검색하여 선택합니다.
1. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽에서 **보안 설정** 을 선택합니다.
1. 다음 설정에 대해 **사용** 또는 **사용 안 함** 을 클릭합니다.
   - **TLS 1.2 전용 모드**
   - **NTLM 인증**
   - **온-프레미스에서 암호 동기화**
   - **온-프레미스에서 NTLM 암호 동기화**
   - **RC4 암호화**
   - **Kerberos armoring**

   ![약한 암호화 및 NTLM 암호 해시 동기화를 사용하지 않도록 설정하는 보안 설정의 스크린샷](media/secure-your-domain/security-settings.png)

## <a name="assign-azure-policy-compliance-for-tls-12-usage"></a>TLS 1.2 사용에 대한 Azure Policy 규정 준수 할당

**보안 설정** 외에도 Microsoft Azure Policy에는 TLS 1.2 사용을 적용하는 **규정 준수** 설정이 있습니다. 정책은 할당될 때까지 영향을 주지 않습니다. 정책이 할당되면 **규정 준수** 에 나타납니다.

- 할당이 **감사** 인 경우 규정 준수는 Azure AD DS 인스턴스가 규정을 준수하는지 보고합니다.
- 할당이 **거부** 인 경우 규정 준수는 TLS 1.2가 필요하지 않으면 Azure AD DS 인스턴스가 생성되지 않도록 하고 TLS 1.2가 필요할 때까지 Azure AD DS 인스턴스에 대한 업데이트를 방지합니다.

![규정 준수 설정 스크린샷](media/secure-your-domain/policy-tls.png)

## <a name="audit-ntlm-failures"></a>NTLM 오류 감사

NTLM 암호 동기화를 사용하지 않도록 설정하면 보안은 향상되지만 많은 애플리케이션과 서비스가 작동하지 않습니다. 예를 들어 DNS 서버 관리나 RDP와 같은 리소스에 해당 IP 주소로 연결하면 액세스가 거부되어 실패합니다. NTLM 암호 동기화를 사용하지 않도록 설정한 후 애플리케이션이나 서비스가 예상대로 작동하지 않는 경우, 이벤트 세부 정보에 NTLM이 **인증 패키지** 로 지정된 **로그온/로그오프** > **로그온 감사** 이벤트 범주에 대해 보안 감사를 사용하도록 설정하여 NTLM 인증 오류를 확인할 수 있습니다. 자세한 내용은 [Azure Active Directory Domain Services에 보안 감사 사용](security-audit-events.md)을 참조하세요.

## <a name="use-powershell-to-harden-your-domain"></a>PowerShell을 사용하여 도메인 강화

필요한 경우 [Azure PowerShell을 설치 및 구성](/powershell/azure/install-az-ps)하세요. [Connect-AzAccount][Connect-AzAccount] cmdlet을 사용하여 Azure 구독에 로그인합니다. 

또한 필요한 경우 [Azure AD PowerShell을 설치 및 구성](/powershell/azure/active-directory/install-adv2)하세요. [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인합니다.

약한 암호 그룹 및 NTLM 자격 증명 해시 동기화를 사용하지 않도록 설정하려면 Azure 계정에 로그인한 다음 [AzResource][Get-AzResource] cmdlet을 사용하여 Azure AD DS 리소스를 가져옵니다.

> [!TIP]
> [AzResource][Get-AzResource] 명령을 사용하여 *Microsoft.AAD/DomainServices* 리소스가 존재하지 않는 오류가 발생하면 [액세스 권한을 높여 모든 Azure 구독 및 관리 그룹을 관리해야][global-admin]합니다.

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

다음으로 *DomainSecuritySettings* 를 정의하여 다음 보안 옵션을 구성합니다.

1. NTLM v1 지원 사용 안 함.
2. 온-프레미스 AD에서 NTLM 암호 해시 동기화를 사용하지 않도록 설정합니다.
3. TLS v1을 사용하지 않도록 설정합니다.

> [!IMPORTANT]
> Azure AD DS 관리되는 도메인에서 NTLM 암호 해시 동기화를 사용하지 않도록 설정한 경우 사용자 및 서비스 계정은 LDAP 단순 바인딩을 수행할 수 없습니다. LDAP 단순 바인딩을 수행해야하는 경우 다음 명령에서 *"SyncNtlmPasswords"="Disabled";* 보안 구성옵션을 설정하지 마십시오.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled";"KerberosRc4Encryption"="Disabled";"KerberosArmoring"="Disabled"}}
```

마지막으로 [Set-AzResource][Set-AzResource] cmdlet을 사용하여 관리되는 도메인에 정의된 보안 설정을 적용합니다. 첫 번째 단계에서 Azure AD DS 리소스를 지정하고 이전 단계의 보안 설정을 지정합니다.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -ApiVersion “2021-03-01” -Verbose -Force
```

보안 설정이 관리되는 도메인에 적용되는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> NTLM을 사용하지 않도록 설정한 후 Azure AD Connect에서 전체 암호 해시 동기화를 수행하여 관리되는 도메인에서 모든 암호 해시를 제거합니다. NTLM을 사용하지 않는 경우 암호 해시 동기화를 적용하지 않으면 사용자 계정에 대한 NTLM 암호 해시는 다음 암호 변경시에만 제거됩니다. 이 동작을 통해 NTLM이 인증 방법으로 사용되는 시스템에 캐시된 자격 증명이 있는 경우 사용자가 계속 로그인 할 수 있습니다.
>
> NTLM 암호 해시가 Kerberos 암호 해시와 다르면 NTLM 대체 연결이 작동하지 않습니다. 또한 VM이 관리되는 도메인 컨트롤러에 연결되어 있으면 캐시된 자격 증명은 더 이상 작동하지 않습니다.  

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대한 자세한 내용은 [관리되는 도메인에서 개체 및 자격 증명을 동기화하는 방법][synchronization]을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
