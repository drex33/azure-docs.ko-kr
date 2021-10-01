---
title: 하이브리드 Azure Active Directory 조인 디바이스 문제 해결
description: 이 문서는 하이브리드 Azure Active Directory 조인 Windows&nbsp;10 및 Windows Server 2016 디바이스 문제를 해결하는 데 도움이 됩니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6d8ee6769fdffa4cb16a6a8b075c1933a2428374
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436503"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 조인 디바이스 문제 해결

이 문서에서는 Windows&nbsp;10 또는 Windows Server 2016을 실행 중인 디바이스의 잠재적인 문제를 해결하는 데 도움이 되는 문제 해결 지침을 제공합니다.

하이브리드 Azure AD(Azure Active Directory) 조인은 Windows&nbsp;10 2015년 11월 업데이트 이상을 지원합니다.

다른 Windows 클라이언트 문제를 해결하려면 [하이브리드 Azure AD 조인 하위 수준 디바이스 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)을 참조하세요.

이 문서에서는 다음 시나리오를 지원하도록 [하이브리드 Azure AD 조인 디바이스를 구성](hybrid-azuread-join-plan.md)했다고 가정합니다.

- 디바이스 기반 조건부 액세스
- [엔터프라이즈 상태 로밍](./enterprise-state-roaming-overview.md)
- [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


## <a name="troubleshoot-join-failures"></a>조인 오류 문제 해결

### <a name="step-1-retrieve-the-join-status"></a>1단계: 조인 상태 검색

1. 관리자로 명령 프롬프트 창을 엽니다.
1. `dsregcmd /status`.

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>2단계: 조인 상태 평가

다음 표의 필드를 검토하고 필드에 예상 값이 있는지 확인합니다.

| 필드 | 필요한 값 | Description |
| --- | --- | --- |
| DomainJoined | YES | 이 필드는 디바이스가 온-프레미스 Active Directory에 조인되는지 여부를 나타냅니다. <br><br>값이 *NO* 인 경우 디바이스는 하이브리드 Azure AD 조인을 수행할 수 없습니다. |
| WorkplaceJoined | 아니요 | 이 필드는 디바이스가 Azure AD에 개인 디바이스로 등록되어 있는지 여부를 나타냅니다(*작업 영역 조인* 으로 표시). 이 값은 하이브리드 Azure AD 조인된 도메인 조인 컴퓨터에 대해 *NO* 이어야 합니다. <br><br>값이 *YES* 인 경우 하이브리드 Azure AD 조인을 완료하기 전에 회사 또는 학교 계정이 추가되었습니다. 이 경우 Windows&nbsp;10 버전 1607 이상을 사용하는 경우 계정이 무시됩니다. |
| AzureAdJoined | YES | 이 필드는 디바이스가 조인되어 있는지 여부를 나타냅니다. 디바이스가 Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스인 경우에는 값이 *YES* 입니다. <br><br>값이 *NO* 인 경우 Azure AD에 대한 조인은 아직 완료되지 않았습니다. |
|  |  |

추가적인 문제 해결을 위해 다음 단계를 진행합니다.

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>3단계: 조인이 실패한 단계 및 오류 코드 찾기

**Windows&nbsp;10 버전 1803 이상의 경우**

조인 상태 출력의 “Diagnostic Data” 섹션에서 “Previous Registration” 하위 섹션을 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.

“Error Phase” 필드는 조인 실패 단계를 나타내고 “Client ErrorCode”는 조인 작업의 오류 코드를 나타냅니다.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

**이전 Windows&nbsp;10개 버전의 경우**

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. 이 로그는 **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 에 저장되어 있습니다.
1. 이벤트 ID가 304, 305, 307인 이벤트를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="이벤트 ID 304가 선택되고, 해당 정보가 표시되고, 오류 코드와 단계가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="이벤트 ID 305가 선택되고, 해당 정보가 표시되고, 오류 코드가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>4단계: 가능한 원인 및 해결 방법 확인

#### <a name="pre-check-phase"></a>사전 검사 단계

가능한 실패 원인:

- 디바이스가 도메인 컨트롤러에 연결되어 있지 않습니다.
   - 디바이스는 조직의 내부 네트워크에 있거나 온-프레미스 Active Directory 도메인 컨트롤러에 대한 네트워크 연결을 통해 가상 사설망에 있어야 합니다.

#### <a name="discover-phase"></a>검색 단계

가능한 실패 원인:

-  서비스 연결점 개체가 잘못 구성되거나 도메인 컨트롤러에서 읽을 수 없습니다.
   - Azure AD에서 확인된 도메인 이름을 가리키고 디바이스가 속하는 AD 포리스트에 유효한 서비스 연결점 개체가 있어야 합니다.
   - 자세한 내용은 [자습서: 페더레이션된 도메인용 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)의 “서비스 연결점 구성” 섹션을 참조하세요.
- 검색 엔드포인트에 연결하여 검색 메타데이터를 가져오지 못했습니다.
   - 등록 및 권한 부여 엔드포인트를 검색하려면 디바이스가 시스템 컨텍스트에서 `https://enterpriseregistration.windows.net`에 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 컴퓨터 계정이 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.
- 사용자 영역 엔드포인트에 연결하고 영역 검색을 수행하지 못했습니다(Windows&nbsp;10 버전 1809 이상에만 해당).
   - 디바이스는 확인된 도메인에 대해 영역 검색을 수행하고 도메인 유형(관리형 또는 페더레이션)을 결정할 수 있도록 시스템 컨텍스트에서 `https://login.microsoftonline.com`에 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 시스템 컨텍스트가 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.

**일반적인 오류 코드:**

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED**(0x801c001d/-2145648611) | SCP(서비스 연결점) 개체를 읽고 Azure AD 테넌트 정보를 가져올 수 없습니다. | [서비스 연결점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join) 섹션을 참조하세요. |
| **DSREG_AUTOJOIN_DISC_FAILED**(0x801c0021/-2145648607) | 일반 검색에 실패했습니다. DRS(데이터 복제 서비스)에서 검색 메타데이터를 가져오기 못했습니다. | 자세히 조사하려면 다음 섹션에서 하위 오류를 찾습니다. |
| **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**(0x801c001f/-2145648609) | 검색을 수행하는 동안 작업 시간이 초과되었습니다. | 시스템 컨텍스트에서 `https://enterpriseregistration.windows.net`에 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites) 섹션을 참조하세요. |
| **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED**(0x801c003d/-2145648579) | 일반 영역 검색에 실패했습니다. STS에서 도메인 유형(관리/페더레이션)을 확인하지 못했습니다. | 자세히 조사하려면 다음 섹션에서 하위 오류를 찾습니다. |
| | |

**일반적인 하위 오류 코드:**

검색 오류 코드의 하위 오류 코드를 찾으려면 다음 방법 중 하나를 사용합니다.

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 버전 1803 이상

조인 상태 출력의 “Diagnostic Data” 섹션에서 “DRS Discovery Test”를 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="earlier-windowsnbsp10-versions"></a>이전 Windows&nbsp;10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. 이 로그는 **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 에 저장되어 있습니다.
1. 이벤트 ID 201을 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="이벤트 ID 201이 선택되고, 해당 정보가 표시되고, 오류 코드가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

**네트워크 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **WININET_E_CANNOT_CONNECT**(0x80072efd/-2147012867) | 서버에 대한 연결을 설정할 수 없습니다. | 필요한 Microsoft 리소스에 대한 네트워크 연결을 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요. |
| **WININET_E_TIMEOUT**(0x80072ee2/-2147012894) | 일반 네트워크 시간 제한입니다. | 필요한 Microsoft 리소스에 대한 네트워크 연결을 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요. |
| **WININET_E_DECODING_FAILED**(0x80072f8f/-2147012721) | 네트워크 스택이 서버의 응답을 디코딩할 수 없습니다. | 네트워크 프록시가 서버 응답을 방해하고 수정하지 않는지 확인합니다. |
| | |


**HTTP 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **DSREG_DISCOVERY_TENANT_NOT_FOUND**(0x801c003a/-2145648582) | 서비스 연결점 개체가 잘못된 테넌트 ID로 구성되었거나 테넌트에서 활성 구독을 찾을 수 없습니다. | 서비스 연결점 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되었는지 또는 서비스가 테넌트에 있는지 확인합니다. |
| **DSREG_SERVER_BUSY**(0x801c0025/-2145648603) | DRS 서버의 HTTP 503입니다. | 현재 서버를 사용할 수 없습니다. 이후 조인 시도는 서버가 다시 온라인으로 전환된 후 성공할 수 있습니다. |
| | |


**기타 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **E_INVALIDDATA**(0x8007000d/-2147024883) | 서버 응답 JSON을 구문 분석할 수 없습니다. 프록시가 HTML 권한 부여 페이지와 함께 HTTP 200을 반환하기 때문일 수 있습니다. | 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 시스템 컨텍스트가 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다. |
| | |


#### <a name="authentication-phase"></a>인증 단계

이 콘텐츠는 페더레이션된 도메인 계정에만 적용됩니다.

실패 원인:

- DRS 리소스의 액세스 토큰을 자동으로 가져올 수 없습니다.
   - Windows&nbsp;10 디바이스는 활성 WS-Trust 엔드포인트에 통합된 Windows 인증을 사용하여 페더레이션 서비스에서 인증 토큰을 획득합니다. 자세한 내용은 [페더레이션 서비스 구성](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)을 참조하세요.

**일반적인 오류 코드**:

이벤트 뷰어 로그를 사용하여 오류 코드, 하위 오류 코드, 서버 오류 코드, 서버 오류 메시지를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. 이 로그는 **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 에 저장되어 있습니다.
1. 이벤트 ID 305를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="이벤트 ID 305가 선택되고, 해당 정보가 표시되고, ADAL 오류 코드와 상태가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

**구성 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED**(0xcaa90017/-894894057) | ADAL(Azure AD 인증 라이브러리) 인증 프로토콜이 WS-Trust가 아닙니다. | 온-프레미스 ID 공급자가 WS-Trust를 지원해야 합니다. |
| **ERROR_ADAL_FAILED_TO_PARSE_XML**(0xcaa9002c/-894894036) | 온-프레미스 페더레이션 서비스가 XML 응답을 반환하지 않았습니다. | MEX(Metadata Exchange) 엔드포인트가 유효한 XML을 반환하는지 확인합니다. 프록시가 비 XML 응답을 방해하고 반환하지 않는지 확인합니다. |
| **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT**(0xcaa90023/-894894045) | 사용자 이름/암호 인증을 위한 엔드포인트를 검색할 수 없습니다. | 온-프레미스 ID 공급자 설정을 확인합니다. WS-Trust 엔드포인트를 사용하도록 설정하고 MEX 응답에 이러한 올바른 엔드포인트가 포함되어 있는지 확인합니다. |
| | |


**네트워크 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **ERROR_ADAL_INTERNET_TIMEOUT**(0xcaa82ee2/-894947614) | 일반 네트워크 시간 제한입니다. | 시스템 컨텍스트에서 `https://login.microsoftonline.com`에 액세스할 수 있는지 확인합니다. 시스템 컨텍스트에서 온-프레미스 ID 공급자에 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요. |
| **ERROR_ADAL_INTERNET_CONNECTION_ABORTED**(0xcaa82efe/-894947586) | 권한 부여 엔드포인트에 대한 연결이 중단되었습니다. | 잠시 후 조인을 다시 시도하거나 또 다른 안정적인 네트워크 위치에서 조인을 시도합니다. |
| **ERROR_ADAL_INTERNET_SECURE_FAILURE**(0xcaa82f8f/-894947441) | 서버에서 전송한 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안) 인증서의 유효성을 검사할 수 없습니다. | 클라이언트 시간차를 확인합니다. 잠시 후 조인을 다시 시도하거나 또 다른 안정적인 네트워크 위치에서 조인을 시도합니다. |
| **ERROR_ADAL_INTERNET_CANNOT_CONNECT**(0xcaa82efd/-894947587) | `https://login.microsoftonline.com`에 연결하려는 시도가 실패했습니다. | `https://login.microsoftonline.com`에 대한 네트워크 연결을 확인합니다. |
| | |


**기타 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT**(0xcaa20003/-895352829) | 온-프레미스 ID 공급자의 SAML 토큰을 Azure AD에서 수락하지 않았습니다. | 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다. |
| **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED**(0xcaa90014/-894894060) | 서버 WS-Trust 응답이 오류 예외를 보고했으며 어설션을 가져오지 못했습니다. | 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다. |
| **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL**(0xcaa90006/-894894074) | 토큰 엔드포인트에서 액세스 토큰을 가져오는 동안 오류가 발생했습니다. | ADAL 로그에서 기본 오류를 찾습니다. |
| **ERROR_ADAL_OPERATION_PENDING**(0xcaa1002d/-895418323) | 일반 ADAL 실패입니다. | 인증 로그에서 하위 오류 코드 또는 서버 오류 코드를 찾습니다. |
| | |


#### <a name="join-phase"></a>조인 단계

실패 원인:

사용 중인 Windows 10 버전에 따라 다음 표에서 등록 유형 및 오류 코드를 찾습니다.

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 버전 1803 이상

조인 상태 출력의 “Diagnostic Data” 섹션에서 “Previous Registration” 하위 섹션을 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.

“Registration Type” 필드는 수행된 조인 유형을 나타냅니다.

```
+----------------------------------------------------------------------+
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

#### <a name="earlier-windowsnbsp10-versions"></a>이전 Windows&nbsp;10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. 이 로그는 **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 에 저장되어 있습니다.
1. 이벤트 ID 204를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="이벤트 ID 204가 선택되고 오류 코드, HTTP 상태, 메시지가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

**DRS 서버에서 반환된 HTTP 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **DSREG_E_DIRECTORY_FAILURE**(0x801c03f2/-2145647630) | DRS에서 오류 응답을 받았습니다. ErrorCode: “DirectoryError” | 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요. |
| **DSREG_E_DEVICE_AUTHENTICATION_ERROR**(0x801c0002/-2145648638) | DRS에서 오류 응답을 받았습니다. ErrorCode는 “AuthenticationError”이고 ErrorSubCode는 “DeviceNotFound”가 ‘아닙니다’. | 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요. |
| **DSREG_E_DEVICE_INTERNALSERVICE_ERROR**(0x801c0006/-2145648634) | DRS에서 오류 응답을 받았습니다. ErrorCode: “DirectoryError” | 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요. |
| | |


**TPM 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **NTE_BAD_KEYSET**(0x80090016/-2146893802) | TPM(신뢰할 수 있는 플랫폼 모듈) 작업이 실패하거나 잘못되었습니다. | 잘못된 sysprep 이미지로 인해 실패했을 수 있습니다. sysprep 이미지가 생성된 머신이 Azure AD 조인, 하이브리드 Azure AD 조인 또는 Azure AD 등록되어 있지 않은지 확인합니다. |
| **TPM_E_PCP_INTERNAL_ERROR**(0x80290407/-2144795641) | 일반 TPM 오류입니다. | 이 오류가 발생한 디바이스에서 TPM을 사용하지 않도록 설정합니다. Windows&nbsp;10 버전 1809 이상에서는 TPM 실패를 자동으로 검색하며 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다. |
| **TPM_E_NOTFIPS**(0x80280036/-2144862154) | FIPS 모드에서는 현재 TPM이 지원되지 않습니다. | 이 오류가 발생한 디바이스에서 TPM을 사용하지 않도록 설정합니다. Windows 10 버전 1809 이상에서는 TPM 실패를 자동으로 검색하며 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다. |
| **NTE_AUTHENTICATION_IGNORED**(0x80090031/-2146893775) | TPM이 잠겼습니다. | 일시적인 오류입니다. 휴지 기간 동안 기다리세요. 잠시 후에 조인 시도가 성공해야 합니다. 자세한 내용은 [TPM 기본 사항](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)을 참조하세요. |
| | |


**네트워크 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **WININET_E_TIMEOUT**(0x80072ee2/-2147012894) | DRS에서 디바이스를 등록하는 동안 일반적인 네트워크 시간 초과가 발생했습니다. | `https://enterpriseregistration.windows.net`에 대한 네트워크 연결을 확인합니다. |
| **WININET_E_NAME_NOT_RESOLVED**(0x80072ee7/-2147012889) | 서버 이름이나 주소를 확인할 수 없습니다. | `https://enterpriseregistration.windows.net`에 대한 네트워크 연결을 확인합니다. |
| **WININET_E_CONNECTION_ABORTED**(0x80072efe/-2147012866) | 서버와 연결이 비정상적으로 종료되었습니다. | 잠시 후 조인을 다시 시도하거나 또 다른 안정적인 네트워크 위치에서 조인을 시도합니다. |
| | |


**기타 오류**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED**(0x801c001d/-2145648611) | 이벤트 ID 220이 사용자 디바이스 등록 이벤트 로그에 표시됩니다. Windows가 Active Directory의 컴퓨터 개체에 액세스할 수 없습니다. Windows 오류 코드가 이벤트에 포함될 수 있습니다. 오류 코드 ERROR_NO_SUCH_LOGON_SESSION(1312) 및 ERROR_NO_SUCH_USER(1317)는 온-프레미스 Active Directory의 복제 문제와 관련이 있습니다. | Active Directory의 복제 문제를 해결합니다. 이러한 복제 문제는 일시적일 수 있으며 잠시 후에도 사라질 수 있습니다. |
| | |


**페더레이션된 조인 서버 오류**:

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 이유 | 해상도 |
| --- | --- | --- | --- |
| DirectoryError | 요청이 일시적으로 제한되었습니다. 300초 후에 시도하십시오. | 이 오류는 예상되는 오류이며 여러 등록 요청이 빠르게 연속으로 수행되었기 때문일 수 있습니다. | 휴지 기간 후 조인 다시 시도 |
| | |

**동기화 조인 서버 오류**:

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 이유 | 해상도 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: 테넌트 `UUID`를 찾을 수 없습니다. 이 오류는 테넌트에 대한 활성 구독이 없는 경우 발생할 수 있습니다. 구독 관리자와 함께 확인하세요. | 서비스 연결점 개체의 테넌트 ID가 잘못되었습니다. | 서비스 연결점 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되었는지 또는 서비스가 테넌트에 있는지 확인합니다. |
| DirectoryError | 지정된 ID의 디바이스 개체를 찾을 수 없습니다. | 이는 동기화 조인에서 예상되는 오류입니다. 디바이스 개체가 AD에서 Azure AD로 동기화되지 않았습니다. | Azure AD Connect 동기화가 완료될 때까지 기다리면 동기화 완료 후 다음 조인 시도에서 문제가 해결됩니다. |
| AuthenticationError | 대상 컴퓨터의 SID 확인 | Azure AD 디바이스의 인증서가 동기화 조인 중 Blob에 로그인하는 데 사용된 인증서와 일치하지 않습니다. 이 오류는 일반적으로 동기화가 아직 완료되지 않았음을 의미합니다. |  Azure AD Connect 동기화가 완료될 때까지 기다리면 동기화 완료 후 다음 조인 시도에서 문제가 해결됩니다. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5단계: 로그 수집 및 Microsoft 지원에 연락

1. [*Auth.zip* 파일을 다운로드](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip)합니다.

1. *c:\temp* 와 같은 폴더에 파일의 압축을 푼 다음, 해당 폴더로 이동합니다.
1. 관리자 권한 Azure PowerShell 세션에서 `.\start-auth.ps1 -v -accepteula`를 실행합니다.
1. **계정 전환** 을 사용하여 문제가 있는 사용자의 다른 세션으로 전환합니다.
1. 문제를 재현합니다.
1. **계정 전환** 을 선택하여 추적을 실행하는 관리자 세션으로 다시 전환합니다.
1. 관리자 권한 PowerShell 세션에서 `.\stop-auth.ps1`을 실행합니다.
1. 스크립트가 실행된 폴더에서 *Authlogs* 폴더를 압축(zip)하여 보냅니다.
    
## <a name="troubleshoot-post-join-authentication-issues"></a>사후 조인 인증 문제 해결

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>1단계: `dsregcmd /status`를 사용하여 PRT 상태 검색

1. 명령 프롬프트 창을 엽니다. 
   > [!NOTE] 
   > PRT(기본 새로 고침 토큰) 상태를 얻으려면 로그인한 사용자의 컨텍스트에서 명령 프롬프트 창을 엽니다. 

1. `dsregcmd /status`를 실행합니다. 

   “SSO state” 섹션은 현재 PRT 상태를 제공합니다. 

   AzureAdPrt 필드가 *NO* 로 설정된 경우 Azure AD에서 PRT 상태를 가져오는 동안 오류가 발생했습니다. 

1. AzureAdPrtUpdateTime이 4시간을 초과하면 PRT를 새로 고치는 데 문제가 있을 수 있습니다. 디바이스를 잠그고 잠금 해제하여 PRT를 강제로 새로 고친 다음, 시간이 업데이트되었는지 확인합니다.

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>2단계: 오류 코드 찾기 

**`dsregcmd` 출력에서**

> [!NOTE]
>  이 출력은 Windows&nbsp;10 2021년 5월 업데이트(버전 21H1)에서 사용할 수 있습니다.

“AzureAdPrt” 필드 아래 “Attempt Status” 필드는 다른 필수 디버그 정보와 함께 이전 PRT 시도의 상태를 제공합니다. 이전 Windows 버전의 경우 Azure AD 분석 및 작업 로그에서 정보를 추출합니다.

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
```

**Azure AD 분석 및 작업 로그에서**

이벤트 뷰어를 사용하여 PRT 획득 중에 Azure AD CloudAP 플러그 인에 의해 로그되는 로그 항목을 찾습니다. 

1. 이벤트 뷰어에서 Azure AD 이벤트 로그를 엽니다. 이 로그는 **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 에 저장되어 있습니다. 

   > [!NOTE]
   > CloudAP 플러그 인은 작업 로그에 오류 이벤트를 로그하고 분석 로그에 정보 이벤트를 로그합니다. 분석 및 작업 로그 이벤트는 모두 문제를 해결하는 데 필요합니다. 

1. 분석 로그의 이벤트 1006은 PRT 획득 흐름의 시작을 나타내고 분석 로그의 이벤트 1007은 PRT 획득 흐름의 끝을 나타냅니다. 이벤트 1006과 1007 사이에 로그된 Azure AD 로그(분석 및 작업)의 모든 이벤트는 PRT 획득 흐름의 일부로 로그되었습니다. 

1. 이벤트 1007은 최종 오류 코드를 기록합니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="이벤트 ID 1006과 1007이 선택되고 최종 오류 코드가 강조 표시된 이벤트 뷰어 스크린샷" border="false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>3단계: 찾은 오류 코드에 따라 추가로 문제 해결

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)<br>**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a) | <li>디바이스가 Azure AD 인증 서비스에 연결할 수 없습니다.<li>Azure AD 인증 서비스 또는 WS-Trust 엔드포인트에서 오류 응답(HTTP 400)을 받았습니다.<br>**참고**: 페더레이션 인증에는 WS-Trust가 필요합니다. | <li>온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 컴퓨터 계정이 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.<li>이벤트 1081 및 1088(Azure AD 작업 로그)에는 Azure AD 인증 서비스에서 발생한 오류의 서버 오류 코드와 WS-Trust 엔드포인트에서 발생한 오류의 오류 설명이 포함됩니다. 일반적인 서버 오류 코드 및 해결 방법은 다음 섹션에 나열되어 있습니다. 이벤트 1081 또는 1088 이전의 이벤트 1022(Azure AD 분석 로그)의 첫 번째 인스턴스에는 액세스 중인 URL이 포함됩니다. |
| **STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0) | Azure AD 인증 서비스 또는 WS-Trust 엔드포인트에서 오류 응답(HTTP 400)을 받았습니다.<br>**참고**: 페더레이션 인증에는 WS-Trust가 필요합니다. | 이벤트 1081 및 1088(Azure AD 작업 로그)에는 각각 Azure AD 인증 서비스 및 WS-Trust 엔드포인트에서 발생하는 오류에 대한 서버 오류 코드와 오류 설명이 포함됩니다. 일반적인 서버 오류 코드 및 해결 방법은 다음 섹션에 나열되어 있습니다. 이벤트 1081 또는 1088 이전의 이벤트 1022(Azure AD 분석 로그)의 첫 번째 인스턴스에는 액세스 중인 URL이 포함됩니다. |
| **STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)<br>**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)<br>**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4) | <li>Azure AD 인증 서비스 또는 WS-Trust 엔드포인트에서 오류 응답(HTTP > 400)을 받았습니다.<br>**참고**: 페더레이션 인증에는 WS-Trust가 필요합니다.<li>필수 엔드포인트에 대한 네트워크 연결 문제 | <li>서버 오류의 경우 이벤트 1081 및 1088(Azure AD 작업 로그)에는 Azure AD 인증 서비스의 오류 코드와 WS-Trust 엔드포인트의 오류 설명이 포함됩니다. 일반적인 서버 오류 코드 및 해결 방법은 다음 섹션에 나열되어 있습니다.<li>연결 문제의 경우 이벤트 1022(Azure AD 분석 로그)에는 액세스 중인 URL이 포함되고 이벤트 1084(Azure AD 작업 로그)에는 네트워크 스택의 하위 오류 코드가 포함됩니다. |
| **STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f) | Azure AD 인증 서비스가 사용자 도메인을 찾을 수 없어 사용자 영역 검색에 실패했습니다. | <li>사용자 UPN의 도메인은 Azure AD에서 사용자 지정 도메인으로 추가되어야 합니다. 이벤트 1144(Azure AD 분석 로그)에는 제공된 UPN이 포함됩니다.<li>온-프레미스 도메인 이름이 라우팅할 수 없는 경우(jdoe@contoso.local) AltID(대체 로그인 ID)를 구성합니다. 참조: [필수 조건](hybrid-azuread-join-plan.md), [대체 로그인 ID 구성](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) |
| **AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c) | 사용자 UPN이 예상 형식이 아닙니다.<br>**참고**:<li>Azure AD 조인 디바이스의 경우 UPN은 사용자가 LoginUI에 입력한 텍스트입니다. <li>하이브리드 Azure AD 조인 디바이스의 경우 UPN은 로그인 프로세스 중에 도메인 컨트롤러에서 반환됩니다. | <li>사용자 UPN은 인터넷 표준 [RFC 822](https://www.ietf.org/rfc/rfc0822.txt)를 기반으로 하는 인터넷 스타일 로그인 이름이어야 합니다. 이벤트 1144(Azure AD 분석 로그)에는 제공된 UPN이 포함됩니다.<li>하이브리드 조인 디바이스의 경우 도메인 컨트롤러가 UPN을 올바른 형식으로 반환하도록 구성되어 있는지 확인합니다. 도메인 컨트롤러에서 `whoami /upn`은 구성된 UPN을 표시해야 합니다.<li>온-프레미스 도메인 이름이 라우팅할 수 없는 경우(jdoe@contoso.local) AltID(대체 로그인 ID)를 구성합니다. 참조: [필수 조건](hybrid-azuread-join-plan.md), [대체 로그인 ID 구성](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) |
| **AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442) | Azure AD 인증 서비스에서 반환하는 ID 토큰에 사용자 SID가 없습니다. | 네트워크 프록시가 서버 응답을 방해하고 수정하지 않는지 확인합니다. |
| **AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1) | WS-Trust 엔드포인트에서 오류를 받았습니다.<br>**참고**: 페더레이션 인증에는 WS-Trust가 필요합니다. | <li>네트워크 프록시가 WS-Trust 응답을 방해하고 수정하지 않는지 확인합니다.<li>이벤트 1088(Azure AD 작업 로그)에는 WS-Trust 엔드포인트의 서버 오류 코드 및 오류 설명이 포함됩니다. 일반적인 서버 오류 코드 및 해결 방법은 다음 섹션에 나열되어 있습니다. |
| **AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b) | MEX 엔드포인트가 잘못 구성되었습니다. MEX 응답에 암호 URL이 포함되어 있지 않습니다. | <li>네트워크 프록시가 서버 응답을 방해하고 수정하지 않는지 확인합니다.<li>응답으로 유효한 URL을 반환하도록 MEX 구성을 수정합니다. |
| **WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f) | WS-Trust 엔드포인트의 XML 응답에 DTD(문서 종류 정의)가 포함되었습니다. DTD는 XML 응답에서 필요하지 않으며 DTD가 포함된 경우 응답 구문 분석에 실패합니다.<br>**참고**: 페더레이션 인증에는 WS-Trust가 필요합니다. | <li>XML 응답에서 DTD를 보내지 않도록 ID 공급자의 구성을 수정합니다.<li>이벤트 1022(Azure AD 분석 로그)에는 DTD를 사용하여 XML 응답을 반환할 액세스되는 URL이 포함됩니다. |
| | |


**일반적인 서버 오류 코드:**

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **AADSTS50155: 디바이스 인증에 실패했습니다.** | <li>Azure AD는 PRT를 발급하기 위해 디바이스를 인증할 수 없습니다.<li>Azure Portal에서 디바이스가 삭제되거나 사용하지 않도록 설정되지 않았는지 확인합니다. 이 문제에 관한 자세한 내용은 [Azure Active Directory 디바이스 관리 FAQ](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)를 참조하세요. | [Azure Active Directory 디바이스 관리 FAQ](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do)에서 이 문제에 대한 지침에 따라 디바이스 조인 유형을 기준으로 디바이스를 다시 등록합니다. |
| **AADSTS50034: `Account` 사용자 계정이 `tenant id` 디렉터리에 없습니다.** | Azure AD가 테넌트에서 사용자 계정을 찾을 수 없습니다. | <li>사용자가 올바른 UPN을 입력하고 있는지 확인합니다.<li>온-프레미스 사용자 계정이 Azure AD와 동기화되는지 확인합니다.<li>이벤트 1144(Azure AD 분석 로그)에는 제공된 UPN이 포함됩니다. |
| **AADSTS50126: 잘못된 사용자 이름 또는 암호로 인해 자격 증명 유효성 검사 오류가 발생했습니다.** | <li>Windows LoginUI에서 사용자가 입력한 사용자 이름과 암호가 올바르지 않습니다.<li>테넌트에 암호 해시 동기화가 사용하도록 설정되어 있고 디바이스가 하이브리드 조인되고 사용자가 방금 암호를 변경한 경우 새 암호가 Azure AD에 동기화되지 않았을 수 있습니다. | 새 자격 증명을 사용하여 새 PRT를 획득하려면 Azure AD 암호 동기화가 완료될 때까지 기다립니다. |
| | |


**일반적인 네트워크 오류 코드**:

| 오류 코드 | 이유 | 해결 방법 |
| --- | --- | --- |
| **ERROR_WINHTTP_TIMEOUT** (12002)<br>**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)<br>**ERROR_WINHTTP_CANNOT_CONNECT** (12029)<br>**ERROR_WINHTTP_CONNECTION_ERROR** (12030) | 일반적인 네트워크 관련 문제입니다. | <li>이벤트 1022(Azure AD 분석 로그) 및 1084(Azure AD 작업 로그)에는 액세스되는 URL이 포함됩니다.<li>온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 컴퓨터 계정이 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.<br><br>추가 [네트워크 오류 코드](/windows/win32/winhttp/error-messages)를 가져옵니다. |
| | |


### <a name="step-4-collect-logs"></a>4단계: 로그 수집

**일반 로그**

1. https://aka.ms/icesdptool 로 이동하여 진단 도구가 포함된 *.cab* 파일을 자동으로 다운로드합니다.
1. 도구를 실행하고 시나리오를 재현합니다.
1. Fiddler 추적의 경우 표시되는 인증서 요청을 수락합니다.
1. 마법사는 추적 파일을 보호하기 위해 암호를 묻는 메시지를 표시합니다. 암호를 입력합니다.
1. 마지막으로 *%LOCALAPPDATA%\ElevatedDiagnostics\numbers* 와 같이 수집된 로그가 모두 저장된 폴더를 엽니다.
1. 최신 *.cab* 파일의 콘텐츠를 사용하여 고객 지원팀에 문의하세요.

**네트워크 추적**

> [!NOTE]
> 네트워크 추적을 수집하는 경우 재현 중에 Fiddler를 사용하지 ‘않는’ 것이 중요합니다.

1.  `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes`를 실행합니다.
1. 디바이스를 잠그고 잠금 해제합니다. 하이브리드 조인 디바이스의 경우 PRT 획득 작업이 완료될 때까지 잠시 기다립니다.
1. `netsh trace stop`를 실행합니다.
1. *nettrace.cab* 파일을 지원과 공유합니다.

---

## <a name="known-issues"></a>알려진 문제
- 모바일 핫스폿이나 외부 Wi-Fi 네트워크에 연결되어 있고 **설정** > **계정** > **회사 또는 학교 액세스** 로 이동하면 하이브리드 Azure AD 조인 디바이스는 Azure AD 및 온-프레미스 AD에 대해 하나씩 두 개의 다른 계정을 표시할 수 있습니다. 이는 UI 문제일 뿐이며 기능에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [`dsregcmd` 명령을 사용하여 디바이스 문제를 해결](troubleshoot-device-dsregcmd.md)합니다.
- [Microsoft 오류 조회 도구](/windows/win32/debug/system-error-code-lookup-tool)로 이동합니다.
