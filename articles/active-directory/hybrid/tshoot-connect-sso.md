---
title: 'Azure Active Directory Connect: Seamless Single Sign-On 문제 해결 | Microsoft Docs'
description: 이 항목에서는 Azure Active Directory Seamless Single Sign-On 문제를 해결하는 방법을 설명합니다.
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b1947d9b78ef77644dd5df8aabe4298de90f9e8
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754276"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign-On 문제 해결

이 문서에서는 Azure AD(Azure Active Directory) Seamless SSO(Seamless Single Sign-On)와 관련된 일반적인 문제에 대한 문제 해결 정보를 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제

- 경우에 따라 Seamless SSO를 활성화하는 데 최대 30분이 소요될 수 있습니다.
- 테넌트에서 Seamless SSO를 사용하지 않도록 설정했다가 다시 사용하도록 설정하면, 일반적으로 10시간 동안 유효한 캐시된 Kerberos 티켓이 만료될 때까지 Single Sign-On 환경을 사용할 수 없게 됩니다.
- Seamless SSO가 성공하면 사용자에게 **로그인 유지** 를 선택하는 기회가 제공되지 않습니다. 이 동작으로 인해 [SharePoint 및 OneDrive 매핑 시나리오](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec)가 작동하지 않습니다.
- 버전 16.0.8730.xxxx 이상의 Microsoft 365 Win32 클라이언트(Outlook, Word, Excel 등)는 비대화형 흐름을 사용하여 지원됩니다. 다른 버전이 지원되지 않습니다. 해당 버전에서 사용자는 암호가 아닌 해당 사용자 이름을 입력하여 로그인합니다. OneDrive의 경우 자동 로그온 환경에 대해 [OneDrive 자동 구성 기능](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894)을 활성화해야 합니다.
- Firefox의 프라이빗 검색 모드에서는 Seamless SSO가 작동하지 않습니다.
- [향상된 보호] 모드가 설정되어 있는 경우 Internet Explorer에서 Seamless SSO가 작동하지 않습니다.
- Microsoft Edge(레거시)는 더 이상 지원되지 않습니다.
- iOS 및 Android의 모바일 브라우저에서는 Seamless SSO가 작동하지 않습니다.
- 사용자가 Active Directory에 있는 여러 그룹의 일부인 경우 사용자의 Kerberos 티켓은 처리하기에 너무 크기 때문에 원활한 SSO에 실패하게 됩니다. Azure AD HTTPS 요청에는 최대 50KB 크기인 헤더가 포함될 수 있습니다. Kerberos 티켓은 쿠키 등 다른 Azure AD 아티팩트(일반적으로 2~5KB)를 수용하는 제한보다 작아야 합니다. 사용자의 그룹 멤버 자격 수를 줄이고 다시 시도하는 것이 좋습니다.
- 30개 이상의 Active Directory 포리스트를 동기화하는 경우 Azure AD Connect를 통해 Seamless SSO를 활성화할 수 없습니다. 이 경우 테넌트에서 이 기능을 [수동으로 활성화](#manual-reset-of-the-feature)하여 해결할 수 있습니다.
- 로컬 인트라넷 영역 대신 신뢰할 수 있는 사이트 영역에 Azure AD 서비스 URL(`https://autologon.microsoftazuread-sso.com`)을 추가하면 *사용자가 로그인하지 못하도록 차단* 됩니다.
- Seamless SSO는 Kerberos에 대한 AES256_HMAC_SHA1, AES128_HMAC_SHA1 및 RC4_HMAC_MD5 암호화 유형을 지원합니다. AzureADSSOAcc$ 계정의 암호화 유형은 AES256_HMAC_SHA1 또는 추가 보안을 위해 AES 및 RC4 유형 중 하나로 설정하는 것이 좋습니다. 암호화 유형은 Active Directory에 있는 계정의 msDS-SupportedEncryptionTypes 특성에 저장됩니다.  AzureADSSOAcc $ account encryption 유형을 RC4_HMAC_MD5로 설정 하고 AES 암호화 유형 중 하나로 변경하려는 경우 [FAQ 문서](how-to-connect-sso-faq.yml)에 설명된 대로 먼저 AzureADSSOAcc $ 계정의 Kerberos 암호 해독 키를 롤오버하는지 확인하세요. 관련 질문에서, 그렇지 않으면 원활한 SSO가 발생하지 않습니다.
-  포리스트 트러스트가 있는 포리스트가 둘 이상인 경우 포리스트 중 하나에서 SSO를 사용하도록 설정하면 트러스트된 모든 포리스트에서 SSO를 사용할 수 있습니다. 이미 SSO를 사용하도록 설정된 포리스트에서 SSO를 사용하도록 설정하면 포리스트에서 SSO가 이미 사용하도록 설정되었다는 오류가 표시됩니다.
-  Seamless SSO를 사용하도록 설정하는 정책에는 25600자 제한이 있습니다. 이 제한은 Seamless SSO를 사용하도록 설정하려는 포리스트 이름을 포함하여 정책에 포함된 모든 것에 적용됩니다. 환경에 포리스트 수가 많은 경우 문자 제한에 도달할 수 있습니다. 포리스트 간에 트러스트가 있는 경우 하나의 포리스트에서만 Seamless SSO를 사용하도록 설정하는 것으로 충분합니다. 예를 들어 contoso.com 및 fabrikam.com이 있고 둘 간에 트러스트가 있는 경우 contoso.com에서만 Seamless SSO를 사용하도록 설정할 수 있으며 이는 fabrikam.com에도 적용됩니다. 이러한 방식으로 정책에서 사용하도록 설정된 포리스트 수를 줄이고 정책 char 제한에 도달하지 않도록 할 수 있습니다.

## <a name="check-status-of-feature"></a>기능의 상태 확인

테넌트에서 Seamless SSO 기능이 여전히 **활성화** 되어 있는지 확인합니다. [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)의 **Azure AD Connect** 창으로 이동하여 상태를 확인할 수 있습니다.

![Azure Active Directory 관리 센터: Azure AD Connect 창](./media/tshoot-connect-sso/sso10.png)

클릭하면서 Seamless SSO에 대해 설정된 모든 AD 포리스트를 확인합니다.

![Azure Active Directory 관리 센터: Seamless SSO 창](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Azure Active Directory 관리 센터에서 로그인이 실패한 이유(프리미엄 라이선스 필요)

테넌트에 연결된 Azure AD Premium 라이선스가 있으면 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 [로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)를 볼 수도 있습니다.

![Azure Active Directory 관리 센터: 로그인 보고서](./media/tshoot-connect-sso/sso9.png)

[Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 **Azure Active Directory** > **로그인** 으로 차례로 이동한 다음, 특정 사용자의 로그인 활동을 선택합니다. **로그인 오류 코드** 필드를 찾습니다. 다음 표를 사용하여 해당 필드의 값을 실패 이유 및 해결에 매핑합니다.

|로그인 오류 코드|로그인 실패 이유|해결 방법
| --- | --- | ---
| 81001 | 사용자의 Kerberos 티켓이 너무 큽니다. | 사용자의 그룹 멤버 자격 수를 줄이고 다시 시도합니다.
| 81002 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist) 참조
| 81003 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist) 참조
| 81004 | Kerberos 인증 시도가 실패했습니다. | [문제 해결 검사 목록](#troubleshooting-checklist) 참조
| 81008 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist) 참조
| 81009 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist) 참조
| 81010 | 사용자의 Kerberos 티켓이 만료되었거나 잘못되었으므로 Seamless SSO가 실패했습니다. | 사용자는 회사 네트워크 내부의 도메인 조인 디바이스에서 로그인해야 합니다.
| 81011 | 사용자의 Kerberos 티켓 정보에 기반하여 사용자 개체를 찾을 수 없습니다. | Azure AD Connect를 사용하여 사용자 정보를 Azure AD에 동기화합니다.
| 81012 | Azure AD에 로그인하려는 사용자가 디바이스에 로그인한 사용자와 다릅니다. | 사용자가 다른 디바이스에서 로그인해야 합니다.
| 81013 | 사용자의 Kerberos 티켓 정보에 기반하여 사용자 개체를 찾을 수 없습니다. |Azure AD Connect를 사용하여 사용자 정보를 Azure AD에 동기화합니다. 

## <a name="troubleshooting-checklist"></a>문제 해결 검사 목록

다음 검사 목록을 사용하여 Seamless SSO 문제를 해결합니다.

- Azure AD Connect에서 Seamless SSO 기능을 사용할 수 있는지 확인합니다. 차단된 포트 등과 같은 이유로 기능을 사용할 수 없으면 모든 [필수 구성 요소](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)가 제대로 갖추어져 있는지 확인합니다.
- 테넌트에서 [Azure AD Join](../devices/overview.md) 및 Seamless SSO를 둘 다 사용하도록 설정한 경우에는 Azure AD Join의 문제는 아닌지 확인합니다. 디바이스가 Azure AD에 등록되고 도메인에 가입된 경우 Azure AD Join의 SSO가 Seamless SSO보다 우선합니다. Azure AD Join의 SSO를 사용하면 "Windows에 연결됨"이라는 로그인 타일이 표시됩니다.
- Azure AD URL(`https://autologon.microsoftazuread-sso.com`)이 사용자의 인트라넷 영역 설정에 속하는지 확인합니다.
- 회사 디바이스가 Active Directory 도메인에 조인되어 있는지 확인합니다. Seamless SSO가 작동하려면 디바이스가 [Azure AD에 조인](../devices/overview.md)될 필요는 _없습니다_.
- 사용자가 Active Directory 도메인 계정을 통해 디바이스에 로그온되어 있는지 확인합니다.
- Seamless SSO가 설정된 Active Directory 포리스트에 사용자의 계정이 있는지 확인합니다.
- 디바이스가 회사 네트워크에 연결되어 있는지 확인합니다.
- 디바이스의 시간이 Active Directory 및 도메인 컨트롤러의 시간과 동기화되고 서로 5분 이내에 있는지 확인합니다.
- `AZUREADSSOACC` 컴퓨터 계정이 있으며, Seamless SSO를 설정하려는 각 AD 포리스트에서 사용되도록 설정되어 있는지 확인합니다. 컴퓨터 계정이 삭제되었거나 누락된 경우 [PowerShell cmdlet](#manual-reset-of-the-feature)을 사용하여 다시 만들 수 있습니다.
- 명령 프롬프트에서 `klist` 명령을 사용하여 디바이스의 기존 Kerberos 티켓을 나열합니다. `AZUREADSSOACC` 컴퓨터 계정에 대해 발급된 티켓이 있는지 확인합니다. 사용자의 Kerberos 티켓은 일반적으로 10시간 동안 유효합니다. Active Directory에 다른 설정이 있을 수 있습니다.
- 테넌트에서 Seamless SSO를 사용하지 않도록 설정했다가 다시 사용하도록 설정하면, 캐시된 Kerberos 티켓이 만료될 때까지 Single Sign-On 환경을 사용할 수 없게 됩니다.
- `klist purge` 명령을 사용하여 디바이스에서 기존 Kerberos 티켓을 제거한 다음 다시 시도합니다.
- JavaScript 관련 문제가 있는지 확인하려면 브라우저의 콘솔 로그(**개발자 도구** 아래)를 검토합니다.
- [도메인 컨트롤러 로그](#domain-controller-logs)를 검토합니다.

### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

도메인 컨트롤러에서 성공 감사가 사용되면 사용자가 Seamless SSO를 통해 로그인할 때마다 보안 항목이 이벤트 로그에 기록됩니다. 다음 쿼리를 사용하여 이러한 보안 이벤트를 찾을 수 있습니다. (**AzureADSSOAcc$** 컴퓨터 계정과 연결된 **4769** 이벤트를 찾습니다.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>기능의 수동 다시 설정

문제 해결이 도움이 되지 않으면 테넌트에서 해당 기능을 수동으로 다시 설정할 수 있습니다. Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 수행합니다.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1단계: Seamless SSO PowerShell 모듈 가져오기

1. 먼저 [Azure AD PowerShell](/powershell/azure/active-directory/overview)을 다운로드한 후 설치합니다.
2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
3. `Import-Module .\AzureADSSO.psd1` 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2단계: Seamless SSO를 사용하도록 설정된 Active Directory 포리스트 목록 가져오기

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
2. `Get-AzureADSSOStatus`을 호출합니다. 이 명령은 이 기능을 사용하도록 설정된 Active Directory 포리스트 목록("도메인" 목록에 표시됨)을 제공합니다.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3단계: 기능을 설정한 각 Active Directory 포리스트에 대한 Seamless SSO 사용 해제

1. `$creds = Get-Credential`을 호출합니다. 메시지가 표시되면 원하는 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   >도메인 관리자 자격 증명 사용자 이름은 SAM 계정 이름 형식(contoso\johndoe 또는 contoso.com\johndoe)으로 입력해야 합니다. 사용자 이름의 도메인 부분을 사용하여 DNS를 사용하는 도메인 관리자의 도메인 컨트롤러를 찾습니다.

   >[!NOTE]
   >사용되는 도메인 관리자 계정은 보호된 사용자 그룹의 구성원이 아니어야 합니다. 이 경우 작업이 실패합니다.

2. `Disable-AzureADSSOForest -OnPremCredentials $creds`을 호출합니다. 이 명령은 이 특정 Active Directory 포리스트에 대한 온-프레미스 도메인 컨트롤러에서 `AZUREADSSOACC` 컴퓨터 계정을 제거합니다.

   >[!NOTE]
   >어떤 이유로든 AD 온-프레미스에 액세스할 수 없는 경우 **3.1단계** 및 **3.2단계** 를 건너뛰고 대신 `Disable-AzureADSSOForest -DomainFqdn <Domain name from the output list in step 2>`을 호출할 수 있습니다. 
   
3. 기능을 설정한 각 Active Directory 포리스트에 대해 앞의 단계를 반복합니다.
 
### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4단계: 각 Active Directory 포리스트에 대한 Seamless SSO 사용 설정

1. `Enable-AzureADSSOForest`을 호출합니다. 메시지가 표시되면 원하는 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   >도메인 관리자 자격 증명 사용자 이름은 SAM 계정 이름 형식(contoso\johndoe 또는 contoso.com\johndoe)으로 입력해야 합니다. 사용자 이름의 도메인 부분을 사용하여 DNS를 사용하는 도메인 관리자의 도메인 컨트롤러를 찾습니다.

   >[!NOTE]
   >사용되는 도메인 관리자 계정은 보호된 사용자 그룹의 구성원이 아니어야 합니다. 이 경우 작업이 실패합니다.

2. 기능을 설정하려는 각 Active Directory 포리스트에 대해 앞의 단계를 반복합니다.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5단계. 테넌트에서 기능 활성화

테넌트에서 기능을 켜려면 `Enable-AzureADSSO -Enable $true`를 호출하세요.
