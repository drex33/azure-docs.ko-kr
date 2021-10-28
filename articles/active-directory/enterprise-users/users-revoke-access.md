---
title: 긴급 상황 시 Azure Active Directory에서 사용자의 액세스 권한 취소 | Microsoft Docs
description: Azure Active Directory에서 사용자의 모든 액세스 권한을 취소하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: KarenH444
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fdff97b2b0325d8c965000a309fb6c3232aee7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985184"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Azure Active Directory에서 사용자의 액세스 권한 취소

관리자가 사용자의 모든 액세스 권한을 취소해야 하는 시나리오에는 계정 훼손, 직원 퇴사 및 기타 내부자 위협 등이 있습니다. 관리자는 해당 환경의 복잡성에 따라 다양한 단계를 수행하여 액세스 권한이 취소되었는지 확인할 수 있습니다. 일부 시나리오에서는 액세스 권한을 취소한 시점과 액세스 권한이 실제로 취소된 시점 사이에 간격이 발생할 수 있습니다.

위험 완화를 위해 토큰의 작동 방식을 이해해야 합니다. 토큰에는 여러 종류가 있으며 아래 섹션에 언급된 패턴 중 하나에 해당하게 됩니다.

## <a name="access-tokens-and-refresh-tokens"></a>액세스 토큰과 새로 고침 토큰

액세스 토큰과 새로 고침 토큰은 씩 클라이언트 애플리케이션에서 자주 사용되며 단일 페이지 앱과 같은 브라우저 기반 애플리케이션에서도 사용됩니다.

- 사용자가 Azure AD에 인증하면 권한 부여 정책을 평가하여 사용자에게 특정 리소스에 대한 액세스 권한을 부여할 수 있는지 확인합니다.  

- 권한이 부여된 경우 Azure AD는 리소스에 대한 액세스 토큰과 새로 고침 토큰을 발급합니다.  

- 기본적으로 Azure AD에서 발급한 액세스 토큰의 수명은 1시간입니다. 인증 프로토콜에서 허용하는 경우, 앱은 액세스 토큰이 만료될 때 Azure AD에 새로 고침 토큰을 전달하여 사용자를 자동으로 다시 인증할 수 있습니다.

그러면 Azure AD가 권한 부여 정책을 다시 평가합니다. 사용자에게 여전히 권한이 부여된 경우 Azure AD는 새 액세스 토큰을 발급하고 토큰을 새로 고칩니다.

일반적으로 약 1시간 정도인 토큰의 수명보다 짧은 시간 내에 액세스 권한을 취소해야 하는 경우에는 액세스 토큰이 보안 문제가 될 수 있습니다. 이러한 이유로 Microsoft는 거의 실시간으로 액세스 토큰의 무효화를 보장하는 [지속적인 액세스 권한 평가](../conditional-access/concept-continuous-access-evaluation.md)를 Office 365 애플리케이션에 제공하기 위해 적극적으로 노력하고 있습니다.  

## <a name="session-tokens-cookies"></a>세션 토큰(쿠키)

대부분의 브라우저 기반 애플리케이션은 액세스 토큰과 새로 고침 토큰 대신 세션 토큰을 사용합니다.  

- 사용자가 브라우저를 열고 Azure AD를 통해 애플리케이션에 인증하면 두 개의 세션 토큰을 받습니다. 이러한 토큰은 하나는 Azure AD에서, 다른 하나는 애플리케이션에서 제공됩니다.  

- 애플리케이션이 자체 세션 토큰을 발급하면 애플리케이션에 대한 액세스 권한은 애플리케이션의 세션에 의해 관리됩니다. 이 시점에서 사용자는 애플리케이션이 인식하는 권한 부여 정책에만 영향을 받습니다.

- Azure AD의 권한 부여 정책은 애플리케이션이 사용자를 Azure AD로 다시 보낼 때마다 다시 평가됩니다. 일반적으로 재평가는 자동으로 이루어지지만 애플리케이션 구성 방법에 따라 빈도가 달라집니다. 세션 토큰이 유효한 한 앱이 사용자를 Azure AD로 다시 보내지 않을 수 있습니다.

- 세션 토큰을 취소하려면 애플리케이션이 자체 권한 부여 정책에 따라 액세스 권한을 취소해야 합니다. Azure AD는 애플리케이션에서 발급한 세션 토큰을 직접 취소할 수 없습니다.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>하이브리드 환경에서 사용자의 액세스 권한 취소

Azure Active Directory와 동기화되는 온-프레미스 Active Directory가 있는 하이브리드 환경에서는 IT 관리자가 다음 작업을 수행하는 것이 좋습니다. **Azure AD 전용 환경** 을 사용하는 경우 [Azure Active Directory 환경](#azure-active-directory-environment) 섹션으로 건너뜁니다.


### <a name="on-premises-active-directory-environment"></a>온-프레미스 Active Directory 환경

Active Directory의 관리자 권한으로 온-프레미스 네트워크에 연결하여 PowerShell을 열고, 다음 작업을 수행합니다.

1. Active Directory에서 사용자를 비활성화합니다. [Disable-ADAccount](/powershell/module/activedirectory/disable-adaccount)를 참조하세요.

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Active Directory에서 사용자 암호를 두 번 재설정합니다. [Set-ADAccountPassword](/powershell/module/activedirectory/set-adaccountpassword)를 참조하세요.

    > [!NOTE]
    > 사용자 암호를 두 번 변경하는 이유는 특히 온-프레미스 암호 복제가 지연될 때 Pass-the-Hash 위험을 완화하기 위한 것입니다. 이 계정이 훼손되지 않았다고 안전하게 가정할 수 있는 경우에는 암호를 한 번만 재설정해도 됩니다.

    > [!IMPORTANT]
    > 다음 cmdlets에서 예제 암호를 사용하지 마세요. 암호를 임의의 문자열로 변경해야 합니다.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory 환경

Azure Active Directory의 관리자 권한으로 PowerShell을 열고, ``Connect-AzureAD``를 실행한 후 다음 작업을 수행합니다.

1. Azure AD에서 사용자를 비활성화합니다. [Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser)를 참조하세요.

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. 사용자의 Azure AD 새로 고침 토큰을 철회합니다. [Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)을 참조하세요.

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. 사용자의 디바이스를 사용하지 않도록 설정합니다. [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice)를 참조하세요.

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>액세스가 취소된 경우

관리자가 위 단계를 수행하면 사용자는 Azure Active Directory에 연결된 어떤 애플리케이션에 대해서도 새 토큰을 얻을 수 없습니다. 취소 후 사용자가 액세스 권한을 잃을 때까지의 경과 시간은 애플리케이션에서 액세스 권한을 부여하는 방법에 따라 달라집니다.

- **액세스 토큰을 사용하는 애플리케이션** 의 경우 액세스 토큰이 만료되면 사용자가 액세스 권한을 잃게 됩니다.

- **세션 토큰을 사용하는 애플리케이션** 의 경우 토큰이 만료되는 즉시 기존 세션이 종료됩니다. 사용자의 사용 안 함 상태가 애플리케이션에 동기화되면 애플리케이션은 구성되어 있는 경우 사용자의 기존 세션을 자동으로 철회할 수 있습니다.  이때 걸리는 시간은 애플리케이션과 Azure AD 간의 동기화 빈도에 따라 달라집니다.

## <a name="best-practices"></a>모범 사례

- 자동화된 프로비저닝 및 프로비저닝 해제 솔루션을 배포합니다. 애플리케이션에서 사용자를 프로비전을 해제하면 특히 세션 토큰을 사용하는 애플리케이션의 경우 액세스 권한을 효과적으로 취소할 수 있습니다. 자동 프로비저닝 및 프로비전 해제를 지원하지 않는 앱에 대한 사용자 프로비전을 해제하는 프로세스를 개발합니다. 애플리케이션이 자체 세션 토큰을 철회하고 여전히 유효한 경우라도 Azure AD 액세스 토큰 수락을 중지하도록 합니다.

  - [Azure AD SaaS 앱 프로비전](../app-provisioning/user-provisioning.md)을 사용합니다. Azure AD SaaS 앱 프로비전은 일반적으로 20~40분마다 자동으로 실행됩니다. 애플리케이션에서 비활성화된 사용자 프로비전을 해제하거나 비활성화하도록 [Azure AD 프로비전을 구성](../saas-apps/tutorial-list.md)합니다.
  
  - Azure AD SaaS 앱 프로비저닝을 사용하지 않는 애플리케이션의 경우 [MIM(Identity Manager)](/microsoft-identity-manager/mim-how-provision-users-adds) 또는 타사 솔루션을 사용하여 사용자의 프로비전 해제를 자동화합니다.  
  - 수동 프로비전 해제가 필요한 애플리케이션을 위한 프로세스를 파악하고 개발합니다. 관리자가 필요한 수동 작업을 신속하게 실행하여 필요할 때 이러한 앱에서 사용자의 프로비전을 해제할 수 있도록 합니다.
  
- [Microsoft Intune으로 디바이스 및 애플리케이션을 관리합니다](/mem/intune/remote-actions/device-management). Intune 관리형 [디바이스를 초기 설정으로 재설정할 수 있습니다](/mem/intune/remote-actions/devices-wipe). 디바이스가 관리형이 아니라면 [관리형 앱에서 회사 데이터를 지울](/mem/intune/apps/apps-selective-wipe) 수 있습니다. 이 프로세스는 최종 사용자의 디바이스에서 잠재적으로 중요한 데이터를 제거하는 데 효과적입니다. 그러나 두 프로세스 중 하나를 트리거하려면 디바이스가 인터넷에 연결되어 있어야 합니다. 디바이스가 오프라인 상태인 경우에도 디바이스는 로컬에 저장된 데이터에 계속 액세스할 수 있습니다.

> [!NOTE]
> 디바이스의 데이터는 한 번 지운 후에는 복구할 수 없습니다.

- 필요한 경우 [MCAS(Microsoft Cloud App Security)를 사용하여 데이터 다운로드를 차단](/cloud-app-security/use-case-proxy-block-session-aad)합니다. 데이터에 온라인으로만 액세스할 수 있는 경우, 조직은 세션을 모니터링하고 실시간 정책 시행을 달성할 수 있습니다.

- [Azure AD에서 CAE(지속적인 액세스 권한 평가)](../conditional-access/concept-continuous-access-evaluation.md)를 사용하도록 설정합니다. CAE를 사용하면 관리자가 CAE를 지원하는 애플리케이션에 대한 세션 토큰과 액세스 토큰을 취소할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리자를 위한 보안 액세스 사례](../roles/security-planning.md)
- [사용자 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)
- [이전 직원 제거 또는 삭제](/microsoft-365/admin/add-users/remove-former-employee)