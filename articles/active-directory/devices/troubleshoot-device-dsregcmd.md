---
title: dsregcmd 명령을 사용하여 문제 해결 - Azure Active Directory
description: dsregcmd의 출력을 사용하여 Azure AD의 디바이스 상태 이해
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff1c0d1e552ad26832b2c142f5ca1506654a9a0c
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219541"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>dsregcmd 명령을 사용하여 디바이스 문제 해결

dsregcmd/status 유틸리티는 도메인 사용자 계정으로 실행해야 합니다.

## <a name="device-state"></a>디바이스 상태

이 섹션에는 디바이스 조인 상태 매개 변수가 나열됩니다. 다음 표에서는 다양한 조인 상태에 있는 디바이스에 대한 조건을 나열합니다.

| AzureAdJoined | EnterpriseJoined | DomainJoined | 디바이스 상태 |
| ---   | ---   | ---   | ---   |
| YES | 아니요 | 아니요 | Azure AD 조인됨 |
| 아니요 | 아니요 | YES | 도메인 조인 |
| YES | 아니요 | YES | Hybrid AD 조인됨 |
| 아니요 | YES | YES | 온-프레미스 DRS 조인됨 |

> [!NOTE]
> “사용자 상태” 섹션에 Workplace Join(Azure AD 등록됨) 상태가 표시됩니다.

- **AzureAdJoined:** 디바이스가 Azure AD에 조인되어 있는 경우 “YES”로 설정합니다. 그렇지 않은 경우 “NO”입니다.
- **EnterpriseJoined:** 디바이스가 온-프레미스 DRS에 조인되어 있는 경우 “YES”로 설정합니다. 디바이스는 EnterpriseJoined와 AzureAdJoined 둘 다일 수는 없습니다.
- **DomainJoined:** 디바이스가 도메인(AD)에 조인되어 있는 경우 “YES”로 설정합니다.
- **DomainName:** 디바이스가 도메인에 조인되어 있는 경우 도메인의 이름으로 설정합니다.

### <a name="sample-device-state-output"></a>샘플 디바이스 상태 출력

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>디바이스 세부 정보

디바이스가 Azure AD에 조인된 경우 또는 하이브리드 Azure AD에 조인된 경우에만 표시됩니다(Azure AD 등록됨이 아님). 이 섹션에는 Azure AD에 저장된 장치 식별 세부 정보가 나열됩니다.

- **DeviceId:** Azure AD 테넌트에서 디바이스의 고유 ID입니다.
- **Thumbprint:** 디바이스 인증서의 지문입니다.
- **DeviceCertificateValidity:** 디바이스 인증서의 유효성을 검사합니다.
- **KeyContainerId:** - 디바이스 인증서와 연결된 디바이스 프라이빗 키의 ContainerId입니다.
- **KeyProvider:** 디바이스 프라이빗 키를 저장하는 데 사용되는 KeyProvider(하드웨어/소프트웨어)입니다.
- **TpmProtected:** 디바이스 프라이빗 키가 하드웨어 TPM에 저장되는 경우 “YES”입니다.

> [!NOTE]
> **DeviceAuthStatus** 필드가 **Windows 10 2021년 5월 업데이트(버전 21H1)** 에 추가되었습니다.

- **DeviceAuthStatus:** Azure AD에서 기기의 상태를 확인하기 위해 검사를 수행합니다.  
장치가 있고 Azure AD에서 활성화된 경우 ‘성공’입니다.  
‘실패. 기기가 비활성화되었거나 삭제되었습니다"라는 메시지가 표시되며 기기가 비활성화되거나 삭제된 경우 [추가 정보](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)를 참조하세요.  
‘실패. 오류" - 테스트를 실행할 수 없는 경우 이 테스트에는 Azure AD에 대한 네트워크 연결이 필요합니다.  

### <a name="sample-device-details-output"></a>샘플 디바이스 세부 정보 출력

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
          DeviceAuthStatus : SUCCESS
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>테넌트 세부 정보

디바이스가 Azure AD에 조인된 경우 또는 하이브리드 Azure AD에 조인된 경우에만 표시됩니다(Azure AD 등록됨이 아님). 이 섹션에서는 디바이스가 Azure AD에 조인된 경우 일반적인 테넌트 세부 정보를 나열합니다.

> [!NOTE]
> 이 섹션에서 MDM URL이 비어 있으면 MDM이 구성되지 않았거나 현재 사용자가 MDM 등록 범위에 있지 않음을 나타냅니다. Azure AD의 모바일 설정을 확인하여 MDM 구성을 검토합니다.

> [!NOTE]
> MDM URL이 표시되는 경우라도 디바이스가 MDM에서 관리되는 것을 의미하지는 않습니다. 디바이스 자체가 관리되지 않더라도 테넌트에 자동 등록에 대한 MDM 구성이 있으면 정보가 표시됩니다.

### <a name="sample-tenant-details-output"></a>샘플 테넌트 세부 정보 출력

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>사용자 상태

이 섹션에는 현재 디바이스에 로그인한 사용자에 대한 다양한 특성의 상태가 나열됩니다.

> [!NOTE]
> 유효한 상태를 검색하려면 명령을 사용자 컨텍스트에서 실행해야 합니다.

- **NgcSet:** 현재 로그온한 사용자에 대해 Windows Hello 키가 설정된 경우 “YES”로 설정합니다.
- **NgcKeyId:** 현재 로그온한 사용자에 대해 설정된 경우 Windows Hello 키의 ID입니다.
- **CanReset:** 사용자가 Windows Hello 키를 다시 설정할 수 있는지 여부를 나타냅니다.
- **가능한 값:** 오류가 발생한 경우 DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive 또는 Unknown입니다.
- **WorkplaceJoined:** Azure AD 등록된 계정이 현재 NTUSER 컨텍스트의 디바이스에 추가된 경우 “YES”로 설정합니다.
- **WamDefaultSet:** 로그인한 사용자에 대해 WAM default WebAccount가 생성되면 “YES”로 설정합니다. dsregcmd /status가 관리자 권한 명령 프롬프트에서 실행되는 경우 이 필드에 오류가 표시될 수 있습니다.
- **WamDefaultAuthority:** Azure AD의 “조직”으로 설정합니다.
- **WamDefaultId:** Azure AD의 경우 항상 “https://login.microsoft.com”입니다.
- **WamDefaultGUID:** 기본 WAM WebAccount에 대한 WAM 공급자(Azure AD/Microsoft 계정)의 GUID입니다.

### <a name="sample-user-state-output"></a>샘플 사용자 상태 출력

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 상태

이 섹션은 Azure AD 등록 디바이스에 대해 무시할 수 있습니다.

> [!NOTE]
> 해당 사용자에 대해 유효한 상태를 검색하려면 명령을 사용자 컨텍스트에서 실행해야 합니다.

- **AzureAdPrt:** 로그온한 사용자의 디바이스에 PRT가 있는 경우 “YES”로 설정합니다.
- **AzureAdPrtUpdateTime:** PRT가 마지막으로 업데이트된 시간을 UTC로 설정합니다.
- **AzureAdPrtExpiryTime:** 갱신되지 않은 경우 PRT가 만료되는 시간을 UTC로 설정합니다.
- **AzureAdPrtAuthority:** Azure AD 기관 URL입니다.
- **EnterprisePrt:** 디바이스에 온-프레미스 ADFS의 PRT가 있는 경우 “YES”로 설정합니다. 하이브리드 Azure AD 조인 디바이스의 경우 디바이스는 Azure AD와 온-프레미스 AD의 PRT를 동시에 가질 수 있습니다. 온-프레미스 조인 디바이스는 Enterprise PRT만 갖습니다.
- **EnterprisePrtUpdateTime:** Enterprise PRT가 마지막으로 업데이트된 시간을 UTC로 설정합니다.
- **EnterprisePrtExpiryTime:** 갱신되지 않은 경우 PRT가 만료되는 시간을 UTC로 설정합니다.
- **EnterprisePrtAuthority:** ADFS 기관 URL입니다.

>[!NOTE]
> 다음 PRT 진단 필드가 **Windows 10 2021년 5월 업데이트(버전 21H1)** 에 추가되었습니다.

>[!NOTE]
> **AzureAdPrt** 필드에 표시되는 진단 정보는 AzureAD PRT 획득/새로 고침에 대한 정보이고 **EnterprisePrt** 에 표시되는 진단 정보는 Enterprise PRT 획득/새로고침에 대한 정보입니다.

>[!NOTE]
>진단 정보는 마지막으로 성공한 PRT 업데이트 시간(AzureAdPrtUpdateTime/EnterprisePrtUpdateTime) 이후에 획득/새로 고침 실패가 발생한 경우에만 표시됩니다.  
>공유 장치에서 이 진단 정보는 다른 사용자의 로그온 시도를 형성할 수 있습니다.

- **AcquirePrtDiagnostics:** PRT 진단 정보 획득이 로그에 있는 경우 ‘표시’로 설정합니다.  
진단 정보를 사용할 수 없는 경우 이 필드를 건너뜁니다.
- **이전 PRT 시도:** 실패한 PRT 시도가 발생한 UTC의 현지 시간입니다.  
- **Client ErrorCode:** 클라이언트 오류 코드가 반환되었습니다(HRESULT).
- **사용자 ID:** PRT 시도가 발생한 사용자의 UPN입니다.
- **자격 증명 유형:** PRT를 획득/새로고침하는 데 사용되는 자격 증명입니다. 일반적인 자격 증명 유형은 암호 및 NGC(Windows Hello)입니다.
- **상관 관계 ID:** 실패한 PRT 시도에 대해 서버에서 보낸 상관 관계 ID입니다.
- **엔드포인트 URI:** 실패 전에 액세스한 마지막 엔드포인트입니다.
- **HTTP 메서드:** 엔드포인트에 액세스하는 데 사용되는 HTTP 메서드입니다.
- **HTTP 오류:** WinHttp 전송 오류 코드입니다. WinHttp 오류는 [여기에서](/windows/win32/winhttp/error-messages)찾을 수 있습니다.
- **HTTP 상태:** 엔드포인트에서 반환된 HTTP 상태
- **서버 오류 코드:** 서버의 오류 코드입니다.  
- **서버 오류 설명:** 서버의 오류 메시지입니다.
- **AcquirePrtDiagnostics:** PRT 진단 정보 획득이 로그에 있는 경우 ‘표시’로 설정합니다.  
진단 정보를 사용할 수 없는 경우 이 필드를 건너뜁니다.
진단 정보 필드는 **AcquirePrtDiagnostics와 동일합니다.**


### <a name="sample-sso-state-output"></a>샘플 SSO 상태 출력

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>진단 데이터

### <a name="pre-join-diagnostics"></a>사전 조인 진단

이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.

이 섹션에서는 조인 실패를 진단하는 데 유용한 다양한 테스트를 수행합니다. 또한 이 섹션에는 이전 (?)의 세부 정보가 포함되어 있습니다. 이 정보에는 오류 단계, 오류 코드, 서버 요청 ID, 서버 응답 http 상태, 서버 응답 오류 메시지가 포함됩니다.

- **사용자 컨텍스트:** 진단이 실행되는 컨텍스트입니다. 가능한 값: SYSTEM, UN-ELEVATED User, ELEVATED User입니다.

   > [!NOTE]
   > 실제 조인은 SYSTEM 컨텍스트에서 수행되므로 SYSTEM 컨텍스트에서 진단 실행은 실제 조인 시나리오와 가장 가깝습니다. SYSTEM 컨텍스트에서 진단을 실행하려면 관리자 권한 명령 프롬프트에서 dsregcmd/status 명령을 실행해야 합니다.

- **클라이언트 시간:** UTC로 나타낸 시스템 시간입니다.
- **AD 연결 테스트:** 테스트는 도메인 컨트롤러에 대한 연결 테스트를 수행합니다. 이 테스트에 오류가 발생하면 사전 검사 단계에서 조인 오류가 발생할 수 있습니다.
- **AD 구성 테스트:** 테스트는 SCP 개체가 온-프레미스 AD 포리스트에서 올바르게 구성되었는지 여부를 읽고 확인합니다. 이 테스트의 오류는 검색 단계에서 오류 코드 0x801c001d와 함께 조인 오류가 발생할 수 있습니다.
- **DRS 검색 테스트:** 테스트는 검색 메타데이터 엔드포인트에서 DRS 엔드포인트를 가져오고 사용자 영역 요청을 수행합니다. 이 테스트의 오류는 검색 단계에서 조인 오류가 발생할 수 있습니다.
- **DRS 연결 테스트:** 테스트는 DRS 엔드포인트에 대한 기본 연결 테스트를 수행합니다.
- **토큰 획득 테스트:** 테스트는 사용자 테넌트가 페더레이션된 경우 Azure AD 인증 토큰을 가져오려고 시도합니다. 이 테스트의 오류는 인증 단계에서 조인 오류가 발생할 수 있습니다. 인증에 실패한 경우 아래 레지스트리 키 설정에서 대체(fallback)를 명시적으로 사용하지 않도록 설정하지 않으면 대체(fallback)로 동기화 조인이 시도됩니다.

```
Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
Value: FallbackToSyncJoin
Type:  REG_DWORD
Value: 0x0 -> Disabled
Value: 0x1 -> Enabled
Default (No Key): Enabled
```

- **동기화 조인으로 대체(fallback):** 대체(fallback)가 인증 실패로 동기화 조인되지 않도록 하기 위해 위의 레지스트리 키가 존재하지 않는 경우 “사용”으로 설정합니다. 이 옵션은 Windows 10 1803 이상에서 사용할 수 있습니다.
- **이전 등록:** 이전 조인 시도가 발생한 시간입니다. 실패한 조인 시도만 로깅됩니다.
- **오류 단계:** 중단된 조인의 단계입니다. 가능한 값은 사전 검사, 검색, 인증, 조인입니다.
- **Client ErrorCode:** 클라이언트 오류 코드가 반환되었습니다(HRESULT).
- **Server ErrorCode:** 서버에 요청을 보내고 서버에서 오류 코드와 함께 응답한 경우 서버 오류 코드입니다.
- **Server Message:** 오류 코드와 함께 반환되는 서버 메시지입니다.
- **Https Status:** 서버에서 반환된 Http 상태입니다.
- **Request ID:** 서버에 전송되는 클라이언트 requestId입니다. 서버쪽 로그와의 상관 관계를 파악하는 데 유용합니다.

### <a name="sample-pre-join-diagnostics-output"></a>샘플 사전 조인 진단 출력

다음 예제에서는 검색 오류로 인해 진단 테스트가 실패하는 것을 보여 줍니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

다음 예제에서는 진단 테스트가 통과하지만 동기화 조인에 필요한 디렉터리 오류로 인해 등록 시도가 실패한 경우를 보여 줍니다. Azure AD Connect 동기화 작업이 완료되면 디바이스를 조인할 수 있습니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>사후 조인 진단

이 섹션에는 클라우드에 조인된 디바이스에서 수행된 온전성 검사의 출력이 표시됩니다.

- **AadRecoveryEnabled:** “YES”인 경우 디바이스에 저장된 키를 사용할 수 없으며 디바이스는 복구를 위해 표시됩니다. 다음에 로그인하면 복구 흐름이 트리거되고 디바이스를 다시 등록합니다.
- **KeySignTest:** “PASSED”인 경우 디바이스 키의 상태가 정상입니다. KeySignTest가 실패하면 디바이스는 일반적으로 복구를 위해 표시됩니다. 다음에 로그인하면 복구 흐름이 트리거되고 디바이스를 다시 등록합니다. 하이브리드 Azure AD 조인 디바이스의 경우 복구는 자동입니다. Azure AD 조인 또는 Azure AD 등록되는 동안 디바이스는 필요한 경우 디바이스를 복구하고 다시 등록하기 위해 사용자 인증을 요청하는 메시지를 표시합니다. **KeySignTest를 수행하려면 상승된 권한이 필요합니다.**

#### <a name="sample-post-join-diagnostics-output"></a>샘플 사후 조인 진단 출력

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 필수 구성 요소 확인

이 섹션에서는 WHFB(비즈니스용 Windows Hello)를 프로비전하기 위한 필수 구성 요소 확인을 수행합니다.

> [!NOTE]
> 사용자가 이미 WHFB를 구성한 경우 dsregcmd/status에서 NGC 필수 구성 요소 확인 세부 정보를 볼 수 없습니다.

- **IsDeviceJoined:** 디바이스가 Azure AD에 조인되어 있는 경우 “YES”로 설정합니다.
- **IsUserAzureAD:** 로그인한 사용자가 Azure AD에 있는 경우 "YES"로 설정합니다.
- **PolicyEnabled:** WHFB 정책이 디바이스에서 사용되도록 설정된 경우 “YES”로 설정합니다.
- **PostLogonEnabled:** WHFB 등록이 플랫폼에 의해 기본적으로 트리거되는 경우 “YES”로 설정합니다. “NO”로 설정된 경우 비즈니스용 Windows Hello 등록이 사용자 지정 메커니즘에 의해 트리거됨을 나타냅니다.
- **DeviceEligible:** 디바이스가 WHFB를 사용하여 등록하기 위한 하드웨어 요구 사항을 충족하는 경우 “YES”로 설정합니다.
- **SessionIsNotRemote:** 현재 사용자가 원격이 아닌 디바이스에 직접 로그인한 경우 “YES”로 설정합니다.
- **CertEnrollment:** WHFB 인증서 신뢰 배포와 관련하여 WHFB에 대한 인증서 등록 기관을 나타냅니다. WHFB 정책의 원본이 그룹 정책 경우 “enrollment authority”로 설정하고, 원본이 MDM인 경우 “mobile device management”로 설정합니다. 그렇지 않으면 “none”입니다.
- **AdfsRefreshToken:** WHFB 인증서 신뢰 배포에만 적용됩니다. CertEnrollment가 “enrollment authority”인 경우에만 표시됩니다. 디바이스에 사용자에 대한 엔터프라이즈 PRT가 있는지 여부를 나타냅니다.
- **AdfsRaIsReady:** WHFB 인증서 신뢰 배포에 적용됩니다.  CertEnrollment가 “enrollment authority”인 경우에만 표시됩니다. ADFS가 검색 메타데이터에서 WHFB를 지원한다고 표시되고, *그리고* 로그온 인증서 템플릿을 사용할 수 있는 경우 “YES”로 설정합니다.
- **LogonCertTemplateReady:** WHFB 인증서 신뢰 배포에 적용됩니다. CertEnrollment가 “enrollment authority”인 경우에만 표시됩니다. 로그온 인증서 템플릿의 상태가 유효하고 ADFS RA 문제를 해결하는 데 도움이 되는 경우 “YES”로 설정합니다.
- **PreReqResult:** 모든 WHFB 필수 구성 요소 평가의 결과를 제공합니다. 사용자가 다음에 로그인할 때 WHFB 등록을 사후 로그온 작업으로 시작하는 경우 “Will Provision”으로 설정합니다.

### <a name="sample-ngc-prerequisite-check-output"></a>샘플 NGC 필수 구성 요소 검사 출력

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>다음 단계

- [Microsoft 오류 조회 도구](/windows/win32/debug/system-error-code-lookup-tool)
