---
title: Azure Active Directory에 Connect Health 에이전트 설치
description: 이 Azure AD Connect Health 문서에서는 AD FS(Active Directory Federation Services) 및 동기화를 위한 에이전트 설치에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31dd21a7c525bd1e5dc659b611f72c2c33c6f043
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535632"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health Agent 설치

이 문서에서는 Azure AD(Azure Active Directory) Connect Health 에이전트를 설치하고 구성하는 방법에 대해 설명합니다. 에이전트를 다운로드하려면 [관련 지침](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)을 참조하세요.

## <a name="requirements"></a>요구 사항

다음 표는 Azure AD Connect Health를 사용하기 위한 요구 사항 목록입니다.

| 요구 사항 | Description |
| --- | --- |
| Azure AD Premium(P1 또는 P2) 구독이 있습니다.  |Azure AD Connect Health는 Azure AD Premium(P1 또는 P2) 기능입니다. 자세한 내용은 [Azure AD Premium 가입](../fundamentals/active-directory-get-started-premium.md)을 참조하세요. <br /><br />30일 평가판을 시작하려면 [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조하세요. |
| Azure AD의 전역 관리자입니다. |기본적으로 전역 관리자만 Health Agent를 설치 및 구성하고 포털에 액세스하고 Azure AD Connect Health 내에서 작업을 수행할 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 관리](../fundamentals/active-directory-whatis.md)를 참조하세요. <br /><br /> Azure RBAC(역할 기반 액세스 제어)를 사용하여 조직의 다른 사용자가 Azure AD Connect Health에 액세스하는 것을 허용할 수 있습니다. 자세한 내용은 [Azure AD Connect Health를 위한 Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 참조하세요. <br /><br />**중요**: 회사 또는 학교 계정을 사용하여 에이전트를 설치하세요. Microsoft 계정은 사용할 수 없습니다. 자세한 내용은 [조직으로 Azure 가입](../fundamentals/sign-up-organization.md)을 참조하세요. |
| Azure AD Connect Health Agent가 각 대상 서버에 설치되어 있습니다. | Health 에이전트는 데이터를 수신하고 모니터링 및 분석 기능을 제공할 수 있도록 대상 서버에 설치되고 구성되어야 합니다. <br /><br />예를 들어 AD FS(Active Directory Federation Services) 인프라에서 데이터를 가져오려면 AD FS 서버와 웹 애플리케이션 프록시 서버에 에이전트를 설치해야 합니다. 마찬가지로 온-프레미스 Azure AD DS(Azure AD Domain Services) 인프라에서 데이터를 가져오려면 도메인 컨트롤러에 에이전트를 설치해야 합니다.  |
| Azure 서비스 엔드포인트에 아웃바운드 연결이 있습니다. | 에이전트는 설치 및 런타임 중에 Azure AD Connect Health 서비스 엔드포인트에 연결되어야 합니다. 방화벽이 아웃바운드 연결을 차단하는 경우에는 [아웃바운드 연결 엔드포인트](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)를 허용 목록에 추가합니다. |
|아웃바운드 연결은 IP 주소를 기반으로 합니다. | IP 주소를 기반으로 하는 방화벽 필터링에 대한 자세한 내용은 [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요.|
| 아웃바운드 트래픽에 대한 TLS 검사를 필터링하거나 사용하지 않도록 설정합니다. | 네트워크 계층에서 아웃바운드 트래픽에 대해 TLS 검사 또는 종료가 이루어지는 경우 에이전트 등록 단계 또는 데이터 업로드 작업이 실패할 수 있습니다. 자세한 내용은 [TLS 검사 설정](/previous-versions/tn-archive/ee796230(v=technet.10))을 참조하세요. |
| 서버의 방화벽 포트에서 에이전트를 실행 중입니다. |에이전트는 Azure AD Connect Health 서비스 엔드포인트와 통신할 수 있도록 다음 방화벽 포트가 열려 있어야 합니다. <br /><li>TCP 포트 443</li><li>TCP 포트 5671</li> <br />최신 버전의 에이전트에는 포트 5671이 필요하지 않습니다. 포트 443만 필요하도록 최신 버전으로 업그레이드합니다. 자세한 내용은 [포트 및 프로토콜이 필요한 하이브리드 ID](./reference-connect-ports.md)를 참조하세요. |
| Internet Explorer 보안 강화가 사용하도록 설정된 경우 지정된 웹 사이트를 허용합니다.  |Internet Explorer 보안 강화가 사용하도록 설정된 경우 에이전트를 설치한 서버에서 다음 웹 사이트를 허용합니다.<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https:\//aadcdn.msftauth.net</li><li>Azure AD에서 신뢰할 수 있는 조직의 페더레이션 서버(예: https:\//sts.contoso.com)</li> <br />자세한 내용은 [Internet Explorer를 구성하는 방법](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)을 참조하세요. 네트워크에 프록시가 있는 경우 이 표 끝에 나오는 정보를 참조하세요.|
| PowerShell 버전 5.0 이상이 설치되어 있습니다. | Windows Server 2016에는 PowerShell 버전 5.0이 포함되어 있습니다. 
|FIPS(Federal Information Processing Standard)가 사용하지 않도록 설정되어 있습니다.|Azure AD Connect Health 에이전트는 FIPS를 지원하지 않습니다.|

> [!IMPORTANT]
> Windows Server Core는 Azure AD Connect Health 에이전트 설치를 지원하지 않습니다.


> [!NOTE]
> 매우 폐쇄적이고 제약이 많은 환경을 사용하는 경우에는 Internet Explorer 보안 강화 표에 나와 있는 것보다 많은 URL을 추가해야 합니다. 또한 다음 섹션의 표에 나와 있는 URL을 추가합니다.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure 서비스 엔드포인트에 대한 아웃바운드 연결

에이전트는 설치 및 런타임 중에 Azure AD Connect Health 서비스 엔드포인트에 연결되어야 합니다. 방화벽이 아웃바운드 연결을 차단하는 경우에는 다음 표에 나와 있는 URL이 기본적으로 차단되지 않아야 합니다. 

해당 URL에 대해 보안 모니터링 또는 검사를 사용하지 않도록 하지 마세요. 대신 다른 인터넷 트래픽을 허용하는 것처럼 허용하세요. 

해당 URL은 Azure AD Connect Health 서비스 엔드포인트와의 통신을 허용합니다. 이 문서의 뒷부분에서는 `Test-AzureADConnectHealthConnectivity`를 사용하여 [아웃바운드 연결을 확인](#test-connectivity-to-azure-ad-connect-health-service)하는 방법을 알아봅니다.

| 도메인 환경 | 필수 Azure 서비스 엔드포인트 |
| --- | --- |
| 일반 공개 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - 포트: 5671(이 엔드포인트는 최신 버전의 에이전트에서 필요하지 않습니다.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com(이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure 독일 | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de(이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com(이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>에이전트 설치

Azure AD Connect Health 에이전트를 다운로드하여 설치하려면 다음을 수행합니다. 

* Azure AD Connect Health에 대한 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 충족해야 합니다.
* 다음과 같이 AD FS용 Azure AD Connect Health 사용을 시작합니다.
    * [AD FS용 Azure AD Connect Health 에이전트를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=518973).
    * [설치 지침](#install-the-agent-for-ad-fs)을 참조합니다.
* 다음과 같이 동기화용 Azure AD Connect Health 사용을 시작합니다.
    * [최신 버전의 Azure AD Connect 다운로드 및 설치](https://go.microsoft.com/fwlink/?linkid=615771). 동기화용 Health 에이전트는 Azure AD Connect의 일부로 설치됩니다(버전 1.0.9125.0 이상).
* 다음과 같이 Azure AD DS용 Azure AD Connect Health 사용을 시작합니다.
    * [Azure AD DS용 Azure AD Connect Health 에이전트를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [설치 지침](#install-the-agent-for-azure-ad-ds)을 참조합니다.

## <a name="install-the-agent-for-ad-fs"></a>AD FS용 에이전트를 설치

> [!NOTE]
> AD FS 서버는 동기화 서버와 다릅니다. 동기화 서버에 AD FS 에이전트를 설치하지 마세요.
>

에이전트를 설치하기 전에 AD FS 서버 호스트 이름이 고유하고 AD FS 서비스에 없는지 확인합니다.
에이전트 설치를 시작하려면 다운로드한 *.exe* 파일을 두 번 클릭합니다. 첫 번째 창에서 **설치** 를 선택합니다.

![Azure AD Connect Health AD FS 에이전트의 설치 창을 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/install1.png)

설치가 완료되면 **지금 구성** 을 선택합니다.

![Azure AD Connect Health AD FS 에이전트 설치에 대한 확인 메시지를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/install2.png)

PowerShell 창이 열리고 에이전트 등록 프로세스가 시작됩니다. 메시지가 표시되면 에이전트를 등록할 권한이 있는 Azure AD 계정을 사용하여 로그인합니다. 기본적으로 전역 관리자 계정에 권한이 있습니다.

![Azure AD Connect Health AD FS에 대한 로그인 창을 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/install3.png)

로그인하면 PowerShell이 계속 진행됩니다. 완료되면 PowerShell을 닫을 수 있습니다. 구성이 완료되었습니다.

이 시점에서 에이전트가 필요한 데이터를 클라우드 서비스에 안전하게 업로드할 수 있도록 에이전트 서비스가 자동으로 시작됩니다.

모든 필수 구성 요소를 충족하지 않은 경우에는 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 완료해야 합니다. 다음 스크린샷에서는 이러한 경고의 예를 보여 줍니다.

![Azure AD Connect Health AD FS 구성 스크립트를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/install4.png)

에이전트가 설치되었는지 확인하려면 서버에서 다음 서비스를 찾습니다. 구성을 완료했다면 해당 서비스가 실행 중이어야 합니다. 구성을 완료하지 않은 경우 구성이 완료될 때까지 해당 서비스가 중지됩니다.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health AD FS 서비스를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>AD FS 감사 사용

> [!NOTE]
> 이 섹션은 AD FS 서버에만 해당됩니다. 웹 애플리케이션 프록시 서버에서는 이 단계를 수행할 필요가 없습니다.
>

사용량 현황 분석 기능을 사용하려면 데이터를 수집하고 분석해야 합니다. 따라서 Azure AD Connect Health 에이전트에 AD FS 감사 로그의 정보가 필요합니다. 해당 로그는 기본적으로 사용되지 않습니다. AD FS 서버에서 AD FS 감사를 사용하도록 설정하고 AD FS 감사 로그를 찾으려면 다음 절차에 따르세요.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자** 를 연 다음 **로컬 보안 정책** 을 엽니다. 또는 작업 표시줄에서 **서버 관리자** 를 연 다음 **도구/로컬 보안 정책** 을 선택합니다.
2. *Security Settings\Local Policies\User Rights Assignment* 폴더로 이동합니다. 그런 다음 **보안 감사 생성** 을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 나열되지 않으면 **사용자 또는 그룹 추가** 를 선택하여 목록에 추가합니다. 그런 다음, **확인** 을 선택합니다.
4. 감사를 사용하려면 상승된 권한으로 명령 프롬프트 창을 엽니다. 그런 후 다음 명령을 실행합니다. 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **로컬 보안 정책** 을 닫습니다.
    >[!Important]
    >다음 단계는 기본 AD FS 서버에만 필요합니다. 
1. **AD FS 관리** 스냅인을 엽니다. (**서버 관리자** 에서 **도구** > **AD FS 관리** 를 선택합니다.)
1. **작업** 창에서 **페더레이션 서비스 속성 편집** 을 선택합니다.
1. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 선택합니다.
1. **성공 감사 및 실패 감사** 확인란을 선택한 다음 **확인** 을 선택합니다.
1. PowerShell을 통해 자세한 정보 로깅을 사용하려면 다음 명령을 사용합니다. 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자** 를 연 다음 **로컬 보안 정책** 을 엽니다. 또는 작업 표시줄에서 **서버 관리자** 를 연 다음 **도구/로컬 보안 정책** 을 선택합니다.
2. *Security Settings\Local Policies\User Rights Assignment* 폴더로 이동한 다음 **보안 감사 생성** 을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 나열되지 않으면 **사용자 또는 그룹 추가** 를 선택하여 AD FS 서비스 계정을 목록에 추가합니다. 그런 다음, **확인** 을 선택합니다.
4. 감사를 사용하려면 상승된 권한으로 명령 프롬프트 창을 엽니다. 그런 후 다음 명령을 실행합니다. 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **로컬 보안 정책** 을 닫습니다.
    >[!Important]
    >다음 단계는 기본 AD FS 서버에만 필요합니다.
1. **AD FS 관리** 스냅인을 엽니다. (**서버 관리자** 에서 **도구** > **AD FS 관리** 를 선택합니다.)
1. **작업** 창에서 **페더레이션 서비스 속성 편집** 을 선택합니다.
1. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 선택합니다.
1. **성공 감사 및 실패 감사** 확인란을 선택한 다음 **확인** 을 선택합니다. 성공 감사 및 실패 감사는 기본적으로 사용하도록 설정되어야 합니다.
1. PowerShell 창을 열고 다음 명령을 실행합니다. 

    `Set-AdfsProperties -AuditLevel Verbose`

기본적으로 “기본” 감사 수준이 사용됩니다. 자세한 내용은 [Windows Server 2016의 AD FS 감사 기능 향상](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)을 참조하세요.


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS 감사 로그를 찾으려면

1. **이벤트 뷰어** 를 엽니다.
2. **Windows 로그** 로 이동한 다음 **보안** 을 선택합니다.
3. 오른쪽에서 **현재 로그 필터링** 을 선택합니다.
4. **이벤트 원본** 에 **AD FS 감사** 를 선택합니다.

    감사 로그에 대한 자세한 내용은 [작업 질문](/azure/active-directory/hybrid/reference-connect-health-faq#operations-questions)을 참조하세요.

    ![현재 로그 필터링 창을 보여 주는 스크린샷. “이벤트 원본” 필드에 “AD FS 감사”가 선택되었습니다.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 그룹 정책은 AD FS 감사를 사용하지 않도록 설정할 수 있습니다. AD FS 감사를 사용할 수 없는 경우 로그인 활동에 대한 사용 현황 분석을 사용할 수 없습니다. AD FS 감사를 사용하지 않는 그룹 정책이 없는지 확인해야 합니다.
>


## <a name="install-the-agent-for-sync"></a>동기화용 에이전트 설치

동기화용 Azure AD Connect Health Agent는 최신 버전의 Azure AD Connect에 자동으로 설치됩니다. 동기화용 Azure AD Connect를 사용하려면 [최신 버전의 Azure AD Connect를 다운로드](https://www.microsoft.com/download/details.aspx?id=47594)하여 설치합니다.

에이전트가 설치되었는지 확인하려면 서버에서 다음 서비스를 살펴봅니다. 구성을 완료했다면 서비스가 이미 실행 중입니다. 구성을 완료하지 않은 경우 구성이 완료될 때까지 해당 서비스가 중지됩니다.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![서버에서 실행 중인 동기화용 Azure AD Connect Health 서비스를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health를 사용하려면 Azure AD Premium(P1 또는 P2)이 있어야 합니다. Azure AD Premium이 없으면 Azure Portal에서 구성을 완료할 수 없습니다. 자세한 내용은 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 참조하세요.
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>동기화용 Azure AD Connect Health 수동 등록

Azure AD Connect를 성공적으로 설치한 후 동기화용 Azure AD Connect Health 등록이 실패할 경우 PowerShell 명령을 사용하여 수동으로 에이전트를 등록할 수 있습니다.

> [!IMPORTANT]
> Azure AD Connect를 설치한 후 에이전트 등록에 실패하는 경우에만 이 PowerShell 명령을 사용합니다.
>
>

다음 PowerShell 명령을 사용하여 동기화용 Azure AD Connect Health 에이전트를 수동으로 등록합니다. Azure AD Connect Health 서비스는 에이전트가 성공적으로 등록된 후에 시작됩니다.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $true -StagingMode $false`

이 명령은 다음 매개 변수를 사용합니다.

* **AttributeFiltering**: Azure AD Connect가 기본 특성 집합을 동기화하지 않으며 필터링된 특성 집합을 사용하도록 사용자 지정된 경우에는 `$true`(기본값)입니다. 그렇지 않으면 `$false`를 사용합니다.
* **StagingMode**: Azure AD Connect 서버가 준비 모드에 있지 ‘않은’ 경우에는 `$false`(기본값)입니다. 서버가 준비 모드에 있도록 구성된 경우 `$true`를 사용합니다.

인증을 요구하는 메시지가 표시되면 Azure AD Connect 구성에 사용한 것과 동일한 전역 관리자 계정(예: admin@domain.onmicrosoft.com)을 사용합니다.

## <a name="install-the-agent-for-azure-ad-ds"></a>Azure AD DS용 에이전트 설치

에이전트 설치를 시작하려면 다운로드한 *.exe* 파일을 두 번 클릭합니다. 첫 번째 창에서 **설치** 를 선택합니다.

![AD DS 설치 창의 Azure AD Connect Health 에이전트를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

설치가 완료되면 **지금 구성** 을 선택합니다.

![Azure AD DS용 Azure AD Connect Health 에이전트의 설치를 완료하는 창을 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

명령 프롬프트 창이 열립니다. PowerShell에서 `Register-AzureADConnectHealthADDSAgent`를 실행합니다. 메시지가 표시되면 Azure에 로그인합니다.

![Azure AD DS용 Azure AD Connect Health 에이전트의 로그인 창을 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

로그인하면 PowerShell이 계속 진행됩니다. 완료되면 PowerShell을 닫을 수 있습니다. 구성이 완료되었습니다.

이때 서비스가 자동으로 시작되면서 에이전트에 모니터링과 데이터 수집이 허용됩니다. 이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 완료해야 합니다. 다음 스크린샷에서는 이러한 경고의 예를 보여 줍니다.

![Azure AD DS 구성 시 Azure AD Connect Health 에이전트에 대한 경고를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

에이전트가 설치되었는지 확인하려면 도메인 컨트롤러에서 다음 서비스를 살펴봅니다.

* Azure AD Connect Health AD DS Insights 서비스
* Azure AD Connect Health AD DS 모니터링 서비스

구성을 완료했다면 이러한 서비스가 이미 실행 중이어야 합니다. 구성을 완료하지 않은 경우 구성이 완료될 때까지 해당 서비스가 중지됩니다.

![도메인 컨트롤러에서 실행 중인 서비스를 보여 주는 스크린샷.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>여러 서버에 에이전트를 빠르게 설치

1. Azure AD에서 사용자 계정을 만듭니다. 암호를 사용하여 이를 보호합니다.
2. 포털을 사용하여 이 로컬 Azure AD 계정에 대한 **소유자** 역할을 Azure AD Connect Health에 할당합니다. [이러한 단계](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 수행합니다. 모든 서비스 인스턴스에 역할을 할당합니다. 
3. 설치를 위해 로컬 도메인 컨트롤러에서 *.exe* MSI 파일을 다운로드합니다.
4. 다음 스크립트를 실행합니다. 매개 변수를 새 사용자 계정 및 암호로 바꿉니다. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

완료되면 다음 작업 중 하나 이상을 수행하여 로컬 계정에 대한 액세스 권한을 제거할 수 있습니다. 
* Azure AD Connect Health용 로컬 계정의 역할 할당을 제거합니다.
* 로컬 계정의 암호를 회전합니다. 
* Azure AD 로컬 계정을 사용하지 않습니다.
* Azure AD 로컬 계정을 삭제합니다.  

## <a name="register-the-agent-by-using-powershell"></a>PowerShell을 사용하여 에이전트 등록

적절한 에이전트 *setup.exe* 파일을 설치한 후에는 역할에 따라 다음 PowerShell 명령을 사용하여 에이전트를 등록할 수 있습니다. PowerShell 창을 열고 적합한 명령을 실행합니다.

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> 소버린 클라우드에 등록하려면 다음 명령줄을 사용합니다.
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


해당 명령은 등록을 비 대화형으로 완료하거나 Server Core를 실행하는 머신에서 등록을 완료하는 매개 변수로 `Credential`을 받아들입니다. 다음 사항에 유의하세요.
* 매개 변수로 전달된 PowerShell 변수에서 `Credential`을 캡처할 수 있습니다.
* 에이전트를 등록할 권한이 있고 다단계 인증을 사용하도록 설정되어 있지 ‘않은’ Azure AD ID를 제공할 수 있습니다.
* 기본적으로 전역 관리자는 에이전트를 등록할 권한을 가집니다. 낮은 권한의 ID가 이 단계를 수행하도록 허용할 수도 있습니다. 자세한 내용은 [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 참조하세요.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>HTTP 프록시를 사용하도록 Azure AD Connect Health 에이전트 구성

HTTP 프록시를 사용하도록 Azure AD Connect Health 에이전트를 구성할 수 있습니다.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress`은 지원되지 않습니다. 에이전트는 Windows HTTP 서비스 대신 System.Net을 사용하여 웹 요청을 만듭니다.
> * 구성된 HTTP 프록시 주소는 암호화된 HTTPS 메시지를 통과하는 데 사용됩니다.
> * 인증된 프록시(HTTPBasic 사용)는 지원되지 않습니다.
>
>

### <a name="change-the-agent-proxy-configuration"></a>에이전트 프록시 구성 변경

HTTP 프록시를 사용하도록 Azure AD Connect Health 에이전트를 구성하기 위해 다음과 같은 작업을 수행할 수 있습니다.
* 기존 프록시 설정을 가져옵니다.
* 수동으로 프록시 주소를 지정합니다.
* 기존 프록시 구성을 지웁니다.

> [!NOTE]
> 프록시 설정을 업데이트하려면 모든 Azure AD Connect Health 에이전트 서비스를 다시 시작해야 합니다. 다음 명령 실행:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>기존 프록시 설정 가져오기

Azure AD Connect Health 에이전트가 설정을 사용할 수 있도록 Internet Explorer HTTP 프록시 설정을 가져올 수 있습니다. Health 에이전트를 실행하는 각 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Azure AD Connect Health 에이전트에서 사용할 수 있도록 WinHTTP 프록시 설정을 가져올 수 있습니다. Health 에이전트를 실행하는 각 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>수동으로 프록시 주소 지정

프록시 서버를 수동으로 지정할 수 있습니다. Health 에이전트를 실행하는 각 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

예를 들면 다음과 같습니다. 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

이 예제에서는 다음이 적용됩니다.
* `address` 설정은 DNS 확인 가능 서버 이름 또는 IPv4 주소일 수 있습니다.
* 이때 `port`는 생략할 수 있습니다. 생략하는 경우 443이 기본 포트가 됩니다.

#### <a name="clear-the-existing-proxy-configuration"></a>기존 프록시 구성 지우기

다음 명령을 실행하여 기존 프록시 구성을 지울 수 있습니다.

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>현재 프록시 설정 읽기

다음 명령을 실행하여 현재 프록시 설정을 읽을 수 있습니다.

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에 대한 테스트 연결

Azure AD Connect Health 에이전트로 인해 Azure AD Connect Health 서비스와의 연결이 끊어질 수 있습니다. 연결 손실의 원인에는 네트워크 문제, 권한 문제 및 기타 여러 가지 문제가 있을 수 있습니다.

에이전트에서 2시간 넘게 Azure AD Connect Health 서비스에 데이터를 보낼 수 없으면 “상태 서비스 데이터가 최신 상태가 아닙니다”라는 경고가 포털에 표시됩니다. 

다음 PowerShell 명령을 실행하면 영향을 받는 Azure AD Connect Health Agent가 Azure AD Connect Health 서비스에 데이터를 업로드할 수 있는지 확인할 수 있습니다.

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

역할 매개 변수가 현재 다음 값을 사용합니다.

* ADFS
* 동기화
* ADDS

> [!NOTE]
> 연결 도구를 사용하려면 먼저 에이전트를 등록해야 합니다. 에이전트 등록을 완료할 수 없는 경우 Azure AD Connect Health에 대한 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 모두 충족했는지 확인합니다. 기본적으로 연결은 에이전트를 등록하는 과정에서 테스트됩니다.
>
>

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 확인합니다.

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](how-to-connect-health-sync.md)
* [Azure AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.yml)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)