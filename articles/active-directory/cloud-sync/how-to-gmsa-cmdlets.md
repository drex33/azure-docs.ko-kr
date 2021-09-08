---
title: Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets
description: Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets를 사용하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fc33aaa51a687cc6f9df8aea494d5790dad22a2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306160"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets

본 문서의 목적은 Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets에 대해 설명하는 것입니다. 이 cmdlets를 사용함으로써 서비스 계정(gMSA)에 적용되는 권한에 대한 세분성을 더욱 확보할 수 있습니다. 기본적으로 Azure AD Connect 클라우드 동기화는 Azure AD Connect 기본 gMSA 또는 사용자 지정 gMSA의 Azure AD Connect와 유사한 모든 권한을 적용합니다.

본 문서에서 다루는 cmdlets는 다음과 같습니다.

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions`

## <a name="how-to-use-the-cmdlets"></a>cmdlets 사용 방법

이러한 cmdlets 사용을 위해서는 다음 필수 구성 요소가 필요합니다.

1. 프로비저닝 에이전트를 설치합니다.

2. 프로비저닝 에이전트 PS 모듈을 PowerShell 세션에 가져옵니다.

   ```powershell
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
   ```

3. 기존 권한을 제거합니다.  서비스 계정에 대해 기존의 모든 권한을 제거하고 SELF 사용만 남기려면 `Set-AADCloudSyncRestrictedPermission`을 이용합니다.

   해당 cmdlet에는 통과할 수 있는 `Credential`이라는 매개 변수가 필요하며, 해당 매개 변수 없이 호출하면 프롬프트가 표시됩니다.

   변수를 만들려면 다음을 사용합니다.

   `$credential = Get-Credential`

   사용자에게 사용자 이름과 암호를 입력하라는 프롬프트가 표시됩니다. 자격 증명은 최소 해당 에이전트가 설치된 도메인의 관리자여야 하며 엔터프라이즈 관리자도 됩니다.

4. 그런 다음 해당 cmdlet을 호출해 추가 권한을 제거합니다.

   ```powershell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```

5. 또는 다음을 호출하기만 하면 됩니다.

   자격 증명 관련 프롬프트를 표시하도록 `Set-AADCloudSyncRestrictedPermissions`를 호출할 수도 있습니다.

6. 특정 권한 유형을 추가합니다. 추가한 권한은 Azure AD Connect와 동일합니다. 권한 설정 예시에 대해서는 아래의 [Set-AADCloudSyncPermissions 사용](#using-set-aadcloudsyncpermissions)을 참고하세요.

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions 사용

`Set-AADCloudSyncPermissions`는 Azure AD Connect에서 사용하는 권한과 동일한 다음 권한 유형을 지원합니다. 다음 권한 유형이 지원됩니다.

|사용 권한 유형|Description|
|-----|-----|
|BasicRead| Azure AD Connect에 대한 [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) 권한을 참조하세요.|
|PasswordHashSync|Azure AD Connect에 대한 [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) 권한을 참조하세요.|
|PasswordWriteBack|Azure AD Connect에 대한 [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) 권한을 참조하세요.|
|HybridExchangePermissions|Azure AD Connect에 대한 [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) 권한을 참조하세요.|
|ExchangeMailPublicFolderPermissions| Azure AD Connect에 대한 [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders) 권한을 참조하세요.|
|CloudHR| '이 개체 및 모든 하위 개체'에 '사용자 개체 만들기/삭제' 적용|
|모두|위의 모든 권한을 추가합니다.|

다음 두 가지 중 하나를 선택하면 AADCloudSyncPermissions를 사용할 수 있습니다.
- [구성된 모든 도메인에 특정 사용 권한 부여](#grant-a-certain-permission-to-all-configured-domains)
- [특정 도메인에 특정 사용 권한 부여](#grant-a-certain-permission-to-a-specific-domain)

## <a name="grant-a-certain-permission-to-all-configured-domains"></a>구성된 모든 도메인에 특정 사용 권한 부여

구성된 모든 도메인에 특정 사용 권한을 부여하려면 엔터프라이즈 관리자 계정을 사용해야 합니다.

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>특정 도메인에 특정 사용 권한 부여

특정 도메인에 특정 사용 권한을 부여하려면 최소한 추가하려는 도메인의 도메인 관리자 계정을 사용해야 합니다.

```powershell
Set-AADCloudSyncPermissions -PermissionType "Any mentioned above" -TargetDomain "FQDN of domain" (has to be already configured through wizard) -TargetDomainCredential $credential(same as above) 
```

참고: 1의 경우 자격 증명은 최소한 엔터프라이즈 관리자여야 합니다.

2의 경우 자격 증명은 도메인 관리자 또는 엔터프라이즈 관리자 중 하나일 수 있습니다.
