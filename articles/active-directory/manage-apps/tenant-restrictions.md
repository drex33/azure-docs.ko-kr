---
title: 테넌트 제한을 사용하여 SaaS 앱에 대한 액세스 관리
description: 테넌트 제한을 사용하여 Azure AD 테넌트를 기준으로 앱에 액세스할 수 있는 사용자를 관리하는 방법입니다.
titleSuffix: Azure AD
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2838c6be64718669fb03b3a04dbbea41e5de8c19
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841831"
---
# <a name="restrict-access-to-a-tenant-in-azure-active-directory"></a>Azure Active Directory에서 테넌트에 대한 액세스 제한

보안을 강조하는 대규모 조직에서는 Microsoft 365와 같은 클라우드 서비스로 이동하려는 상황에서 사용자가 승인된 리소스에만 액세스할 수 있다는 사실을 인식하고 있어야 합니다. 일반적으로 회사에서는 액세스를 관리하려는 경우에 도메인 이름 또는 IP 주소를 제한합니다. 이 방법은 SaaS(Software as a Service) 앱이 퍼블릭 클라우드에서 호스트되고 [outlook.office.com](https://outlook.office.com/) 및 [login.microsoftonline.com](https://login.microsoftonline.com/)과 같은 공유 도메인 이름으로 실행되는 환경에서는 실패합니다. 이러한 주소를 차단하면 사용자가 단순히 승인된 ID 및 리소스로만 제한되는 것이 아니라 웹상의 Outlook에 완전히 액세스할 수 없게 됩니다.

이러한 경우에 대한 Azure AD(Azure Active Directory) 해결 방법은 테넌트 제한이라는 기능입니다. 테넌트 제한을 사용하면 조직은 애플리케이션이 Single Sign-On에 대해 사용하는 사용할 Azure AD 테넌트에 따라, SaaS 클라우드 애플리케이션에 대한 액세스를 제어할 수 있습니다. 예를 들어, 동일한 애플리케이션의 다른 조직 인스턴스에 대한 액세스를 차단하면서 조직의 Microsoft 365 애플리케이션에 대한 액세스를 허용하려고 할 수 있습니다.  

테넌트 제한을 사용하면 조직은 사용자에게 액세스가 허용된 테넌트 목록을 지정할 수 있습니다. 그러면 Azure AD는 이러한 허용된 테넌트에 대해서만 액세스를 허가합니다.

이 문서에서는 Microsoft 365의 테넌트 제한 사항에 중점을 두고 있지만, 이 기능은 Single Sign-On을 위해 Azure AD로 사용자를 전송하는 모든 앱을 보호합니다. Microsoft 365에서 사용하는 테넌트와는 다른 Azure AD 테넌트에서 SaaS 앱을 사용하는 경우 필요한 모든 테넌트가 허용되는지 확인합니다(예: B2B 협업 시나리오). 클라우드 SaaS 앱에 대한 자세한 내용은 [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)를 참조하세요.

또한 테넌트 제한 기능은 OneDrive, Hotmail 및 Xbox.com 같은 [모든 MSA 앱의 사용 차단](#blocking-consumer-applications)(Microsoft 소비자 애플리케이션)을 지원합니다.  `login.live.com` 엔드포인트에 별도의 헤더를 사용하며, 이에 관한 내용은 문서 끝에 자세히 나옵니다.

## <a name="how-it-works"></a>작동 방법

전체 솔루션은 다음 구성 요소로 구성됩니다.

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` 헤더가 있는 경우 Azure AD는 허용되는 테넌트의 보안 토큰만 발급합니다.

2. **온-프레미스 프록시 서버 인프라**: 이 인프라는 TLS(전송 계층 보안) 검사를 수행할 수 있는 프록시 디바이스입니다. 허용되는 테넌트 목록을 포함하는 헤더를 Azure AD로 향하는 트래픽에 삽입하도록 프록시를 구성해야 합니다.

3. **클라이언트 소프트웨어**: 테넌트 제한을 지원하기 위해 클라이언트 소프트웨어는 Azure AD에서 직접 토큰을 요청해야 하므로 프록시 인프라에서 트래픽을 가로챌 수 있습니다. 최신 인증(예: OAuth 2.0)을 사용하는 Office 클라이언트와 마찬가지로 브라우저 기반 Microsoft 365 애플리케이션도 현재 테넌트 제한을 지원합니다.

4. **최신 인증**: 클라우드 서비스는 테넌트 제한을 사용하고 허용되지 않는 모든 테넌트로의 액세스를 차단하기 위해 최신 인증을 사용해야 합니다. Microsoft 365 클라우드 서비스는 기본적으로 최신 인증 프로토콜을 사용하도록 구성해야 합니다. 최신 인증에 대한 Microsoft 365 지원과 관련된 최신 정보는 [업데이트된 Office 365 최신 인증](/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016)을 참조하세요.

다음 다이어그램은 고급 트래픽 흐름을 보여 줍니다. 테넌트 제한에서는 Microsoft 365 클라우드 서비스에 대한 트래픽이 아닌 Azure AD에 대한 트래픽에만 TLS 검사가 필요합니다. Azure AD에서 인증을 받으려는 트래픽 볼륨이 일반적으로 Exchange Online 및 SharePoint Online과 같은 SaaS 애플리케이션에 대한 트래픽 볼륨보다 훨씬 작기 때문에 이러한 구분이 필요합니다.

![테넌트 제한 트래픽 흐름 - 다이어그램](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>테넌트 제한 설정

테넌트 제한을 시작하는 방법은 두 단계로 구성됩니다. 첫째, 클라이언트가 올바른 주소에 연결할 수 있도록 합니다. 둘째, 프록시 인프라를 구성합니다.

### <a name="urls-and-ip-addresses"></a>URL 및 IP 주소

테넌트 제한을 사용하려면 클라이언트는 인증을 받기 위해 Azure AD URL [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) 및 [login.windows.net](https://login.windows.net/)에 연결할 수 있어야 합니다. 또한 Office 365에 액세스하려면 클라이언트는 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에 정의된 FQDN(정규화된 도메인 이름)/URL에 연결할 수 있어야 합니다.

### <a name="proxy-configuration-and-requirements"></a>프록시 구성 및 요구 사항

프록시 인프라를 통해 테넌트 제한을 적용하려면 다음과 같은 구성이 필요합니다. 이 지침은 일반적인 지침이므로 구체적인 구현 단계는 프록시 공급업체의 설명서를 참조하세요.

#### <a name="prerequisites"></a>사전 요구 사항

- 프록시는 FQDN/URL을 사용하여 TLS 가로채기, HTTP 헤더 삽입 및 대상 필터링을 수행할 수 있어야 합니다.

- 클라이언트는 TLS 통신을 위해 프록시에서 제공한 인증서 체인을 신뢰해야 합니다. 예를 들어 내부 [PKI(퍼블릭 키 인프라)](/windows/desktop/seccertenroll/public-key-infrastructure)에서 발급한 인증서가 사용될 경우 내부 발급 루트 인증 기관 인증서를 신뢰할 수 있어야 합니다.

- 테넌트 제한을 사용하려면 Azure AD Premium 1 라이선스가 필요합니다.

#### <a name="configuration"></a>구성

Login.microsoftonline.com, login.microsoft.com 및 login.windows.net으로 나가는 각 요청에 대해 두 개의 HTTP 헤더인 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context* 를 삽입합니다.

> [!NOTE]
> 프록시 구성의 `*.login.microsoftonline.com`에 하위 도메인을 포함하지 마세요. 이렇게 하면 device.login.microsoftonline.com이 포함되며, 디바이스 등록 및 디바이스 기반 조건부 액세스 시나리오에서 사용되는 클라이언트 인증서 인증이 방해를 받을 수 있습니다. TLS 중단 및 검사와 헤더 삽입에서 device.login.microsoftonline.com을 제외하도록 프록시 서버를 구성합니다.

헤더에는 다음 요소가 포함되어야 합니다.

- *Restrict-Access-To-Tenants* 의 경우 사용자 액세스를 허용하려는 쉼표로 구분된 테넌트 목록인 \<permitted tenant list\> 값을 사용합니다. 테넌트에 등록된 모든 도메인은 해당 목록의 테넌트 및 디렉터리 ID 자체를 식별하는 데 사용할 수 있습니다. 테넌트를 설명하는 세 가지 방법의 예제에서 Contoso, Fabrikam 및 Microsoft를 허용하는 이름/값 쌍은 `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47` 같이 표시됩니다.

- *Restrict-Access-Context* 의 경우 테넌트 제한을 설정하는 테넌트를 선언하는 단일 디렉터리 ID 값을 사용합니다. 예를 들어, Contoso를 테넌트 제한 정책을 설정하는 테넌트로 선언하기 위해 이름/값 쌍은 `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` 같이 표시됩니다. 이러한 인증을 위해 로그를 가져오려면 여기에서 사용자의 디렉터리 ID를 *사용해야* 합니다. 사용자 자신의 것이 아닌 디렉터리 ID를 사용하는 경우 해당 로그인 로그는 모든 개인 정보가 제거된 상태로 다른 사람의 테넌트에 *나타납니다*. 자세한 내용은 [관리자 환경](#admin-experience)을 참조하세요.

> [!TIP]
> [Azure Active Directory 포털](https://aad.portal.azure.com/)에서 디렉터리 ID를 찾을 수 있습니다. 관리자로 로그인한 후 **Azure Active Directory** 를 선택하고 **속성** 을 선택합니다.
>
> 디렉터리 ID 또는 도메인 이름이 동일한 테넌트를 참조하는지 확인하려면 이 URL`https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration`에서 \<tenant\> 대신 해당 ID나 도메인을 사용합니다.  도메인 및 ID를 포함하는 결과가 동일한 경우 동일한 테넌트를 나타내는 것입니다.

사용자가 승인되지 않은 테넌트에 와 자체 HTTP 헤더를 삽입하지 못하도록 하기 위해 프록시는 *Restrict-Access-To-Tenants* 헤더가 들어오는 요청에 이미 있는 경우 바꾸어야 합니다.

클라이언트는 Login.microsoftonline.com, login.microsoft.com 및 login.windows.net에 대한 모든 요청에 강제로 해당 프록시를 사용하도록 지정되어야 합니다. 예를 들어 PAC 파일을 사용하여 클라이언트가 프록시를 사용하도록 하면 최종 사용자는 PAC 파일을 편집하거나 사용하지 않도록 설정할 수 없습니다.

## <a name="the-user-experience"></a>사용자 환경

이 섹션에서는 최종 사용자와 관리자를 위한 환경을 설명합니다.

### <a name="end-user-experience"></a>최종 사용자 환경

예제 사용자는 Contoso 네트워크에 있지만 Outlook 온라인과 같은 공유 SaaS 애플리케이션의 Fabrikam 인스턴스에 액세스하려고 합니다. Fabrikam이 Contoso 인스턴스에 대해 허용되지 않는 테넌트인 경우 사용자에게 IT 부서에서 승인하지 않은 조직에 속한 리소스에 액세스하려고 한다는 액세스 거부 메시지가 표시됩니다.

![2021년 4월 이후 테넌트 제한 사항 오류 메시지](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>관리자 환경

기업 프록시 인프라에서 테넌트 제한 구성이 완료되면 관리자는 Azure Portal의 테넌트 제한 보고서에 직접 액세스할 수 있습니다. 보고서를 보려면

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인합니다. **Azure Active Directory 관리 센터** 대시보드가 표시됩니다.

2. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. Azure Active Directory 개요 페이지가 나타납니다.

3. 개요 페이지에서 **테넌트 제한** 을 선택합니다.

Restricted-Access-Context 테넌트로 지정된 테넌트의 관리자는 이 보고서를 사용하여 사용된 ID 및 대상 디렉터리 ID를 비롯하여 테넌트 제한 정책으로 인해 차단된 로그인을 확인할 수 있습니다. 이러한 제한을 설정하는 테넌트가 로그인에 대해 사용자 테넌트이거나 리소스 테넌트인 경우 로그인이 포함됩니다.

Restricted-Access-Context 테넌트 이외의 테넌트에 있는 사용자가 로그인하는 경우 보고서에는 대상 디렉터리 ID와 같은 제한된 정보가 포함될 수 있습니다. 이 경우 이름과 사용자 계정 이름 같은 사용자 식별 가능 정보는 마스킹되어 다른 테넌트의 사용자 데이터를 보호합니다(사용자 이름과 개체 ID 대신 "{PII Removed}@domain.com" 또는 00000000-0000-0000-0000-000000000000).

Azure Portal의 다른 보고서와 마찬가지로, 필터를 사용하여 보고서의 범위를 지정할 수 있습니다. 특정 시간 간격, 사용자, 애플리케이션, 클라이언트 또는 상태에 따라 필터링할 수 있습니다. **열** 단추를 선택하는 경우 다음 필드를 조합하여 데이터를 표시하도록 선택할 수 있습니다.

- **사용자** - 이 필드는 개인 데이터를 삭제할 수 있으며, 이 경우 `00000000-0000-0000-0000-000000000000`로 설정됩니다.
- **애플리케이션**
- **상태**
- **Date**
- **날짜(UTC)** - UTC는 협정 세계시임
- **IP 주소**
- **클라이언트**
- **사용자 이름** -이 필드는 개인 데이터를 제거할 수 있으며,이 경우에는 `{PII Removed}@domain.com`로 설정됩니다.
- **위치**
- **대상 테넌트 ID**

## <a name="microsoft-365-support"></a>Microsoft 365 지원

Microsoft 365 애플리케이션은 테넌트 제한을 완전히 지원하기 위해 다음과 같은 두 가지 조건을 충족해야 합니다.

1. 사용되는 클라이언트에서 최신 인증을 지원해야 합니다.
2. 최신 인증이 클라우드 서비스에 대한 기본 인증 프로토콜로 사용되어야 합니다.

현재 최신 인증을 지원하는 Office 클라이언트에 대한 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)을 참조하세요. 해당 페이지에는 특정 Exchange Online 및 비즈니스용 Skype Online 테넌트에 대해 최신 인증을 사용하도록 설정하기 위한 지침으로 연결되는 링크도 포함되어 있습니다. SharePoint Online에서는 이미 최신 인증이 사용하도록 설정되어 있습니다. Teams는 최신 인증만 지원하고 레거시 인증은 지원하지 않으므로 이 바이패스 문제는 Teams에 적용되지 않습니다. 

현재 Microsoft 365 브라우저 기반 애플리케이션(Office 포털, Yammer, SharePoint 사이트, 웹용 Outlook 등)에서 테넌트 제한을 지원합니다. 씩 클라이언트(Outlook, 비즈니스용 Skype, Word, Excel, PowerPoint 등)는 최신 인증을 사용하는 경우에만 테넌트 제한을 적용할 수 있습니다.  

최신 인증을 지원하는 Outlook 및 비즈니스용 Skype 클라이언트는 최신 인증이 사용되도록 설정되지 않은 테넌트에 대해 레거시 프로토콜을 사용하여 테넌트 제한을 효과적으로 우회할 수 있습니다. 레거시 프로토콜을 사용하는 애플리케이션은 인증하는 동안 login.microsoftonline.com, login.microsoft.com 또는 login.windows.net에 접속하는 경우 테넌트 제한으로 차단될 수 있습니다.

Windows의 Outlook에서 고객은 최종 사용자가 프로필에 승인되지 않은 메일 계정을 추가하는 것을 방지하는 제한을 구현하도록 선택할 수 있습니다. 예제를 보려면 [비기본 Exchange 계정 추가 금지](https://gpsearch.azurewebsites.net/default.aspx?ref=1) 그룹 정책 설정을 참조하세요.

## <a name="testing"></a>테스트

전체 조직을 위해 구현하기 전에 테넌트 제한을 시험해보려면 Fiddler와 같은 도구를 사용한 호스트 기반 접근 방법과 프록시 설정의 단계별 롤아웃 방법 중에서 선택할 수 있습니다.

### <a name="fiddler-for-a-host-based-approach"></a>호스트 기반 접근 방식을 위한 Fiddler

Fiddler는 HTTP 헤더 삽입을 비롯하여 HTTP/HTTPS 트래픽을 캡처하고 수정하는 데 사용할 수 있는 무료 웹 디버깅 프록시입니다. 테넌트 제한을 테스트하도록 Fiddler를 구성하려면 다음 단계를 수행합니다.

1. [Fiddler 다운로드하고 설치합니다](https://www.telerik.com/fiddler).

2. [Fiddler 도움말 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)에 따라 HTTPS 트래픽 암호를 해독하도록 Fiddler를 구성합니다.

3. 사용자 지정 규칙을 사용하여 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context* 머리글을 삽입하도록 Fiddler를 구성합니다.

   1. Fiddler Web Debugger 도구에서 **Rules** 메뉴를 선택하고 **Customize Rules...** 를 선택하여 CustomRules 파일을 엽니다.

   2. `OnBeforeRequest` 함수의 시작 부분에 다음 줄을 추가합니다. \<List of tenant identifiers\>를 테넌트에 등록된 도메인(예: `contoso.onmicrosoft.com`)으로 바꿉니다. \<directory ID\>를 테넌트의 Azure AD GUID 식별자로 바꿉니다.  로고를 테넌트에 표시하려면 정확한 GUID 식별자를 **포함시켜야** 합니다.

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   여러 테넌트를 허용해야 하는 경우 쉼표로 테넌트 이름을 구분합니다. 다음은 그 예입니다.

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. CustomRules 파일을 저장한 후 닫습니다.

Fiddler를 구성한 후 **파일** 메뉴로 이동하고 **트래픽 캡처** 를 선택하여 트래픽을 캡처할 수 있습니다.

### <a name="staged-rollout-of-proxy-settings"></a>프록시 설정의 단계별 롤아웃

프록시 인프라의 기능에 따라 사용자에게 설정을 단계별로 롤아웃할 수 있습니다. 다음은 고려해야 할 몇 가지 고급 옵션입니다.

1. 일반 사용자는 프로덕션 프록시 인프라를 계속 사용할 수 있지만 PAC 파일을 사용하여 테스트 사용자는 테스트 프록시 인프라를 가리키도록 합니다.
2. 일부 프록시 서버는 그룹을 사용하여 다양한 구성을 지원할 수 있습니다.

특정 세부 정보에 대해서는 프록시 서버 설명서를 참조하세요.

## <a name="blocking-consumer-applications"></a>소비자 애플리케이션 차단

[OneDrive](https://onedrive.live.com/) 또는 [Microsoft Learn](/learn/) 같은 소비자 계정 및 조직 계정을 모두 지원하는 Microsfot 애플리케이션은 동일 URL에 호스팅할 수 있는 경우가 있습니다.  즉, 업무용으로 해당 URL에 액세스해야 하는 사용자는 개인 용도로도 액세스할 수 있습니다. 이는 사용자의 운영 지침에 따라 허용되지 않을 수 있습니다.

일부 조직에서는 개인 계정이 인증되지 않도록 `login.live.com`을 차단하여 이 문제를 해결하려고 합니다.  여기에는 몇 가지 단점이 있습니다.

1. `login.live.com`을 차단하면 B2B 게스트 시나리오에서 개인 계정의 사용이 차단되어 방문자와 협업이 어려울 수 있습니다.
1. [Autopilot을 배포하려면 다음 `login.live.com`](/mem/autopilot/networking-requirements)을 사용해야 합니다. `login.live.com`이 차단되면 Intune과 Autopilot 시나리오에 실패할 수 있습니다.
1. 디바이스 ID에 login.live.com 서비스를 사용하는 조직 원격 분석 및 Windows 업데이트의 [작동이 중단됩니다](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>소비자 앱 구성

`Restrict-Access-To-Tenants`헤더가 allowlist 기능을 수행하는 동안에 MSA(Microsoft 계정) 블록이 deny 신호로 작동하여 사용자가 소비자 애플리케이션에 로그인할 수 없도록 Microsoft 계정 플랫폼에 지시합니다. 이 신호를 보내기 위해 [위](#proxy-configuration-and-requirements)와 동일한 회사 프록시 또는 방화벽을 사용해 `login.live.com`을 방문하는 트래픽으로 `sec-Restrict-Tenant-Access-Policy` 헤더가 삽입됩니다. 이 헤더의 값은 `restrict-msa`로 설정되어야 합니다. 헤더가 있고 소비자 앱이 사용자 직접 로그인을 시도하면 해당 로그인이 차단됩니다.

이때 소비자 애플리케이션에 대한 인증이 [관리자 로그](#admin-experience)에 표시되지 않는데 그 이유는 login.live.com이 Azure AD와 별도로 호스팅되기 때문입니다.

### <a name="what-the-header-does-and-does-not-block"></a>헤더가 차단하는 것과 차단하지 않는 것

`restrict-msa` 정책은 소비자 애플리케이션 사용을 차단하지만 다음과 같은 몇 가지 유형의 트래픽 및 인증을 통해 허용합니다.

1. 디바이스의 사용자 없는 트래픽.  여기에는 Autopilot, Windows 업데이트 및 조직 원격 분석에 대한 트래픽이 포함됩니다.
1. 소비자 계정의 B2B 인증. [테넌트와 협업하도록 초대된](../external-identities/redemption-experience.md#invitation-redemption-flow)Microsoft 계정이 있는 사용자들은 login.live.com으로 인증하여 리소스 테넌트에 액세스합니다.
    1. 이 액세스는 `Restrict-Access-To-Tenants` 헤더를 사용해 해당 리소스 테넌트에 대한 액세스를 허용 또는 거부하도록 제어됩니다.
1. 여러 Azure 앱과 Office.com에 사용되는 "통과" 인증. 이 경우 각 앱이 Azure AD를 사용하여 소비자 컨텍스트에서 소비자 사용자를 로그인합니다.
    1. 이 액세스는 `Restrict-Access-To-Tenants` 헤더를 사용해 특정 “통과” 테넌트(`f8cdef31-a31e-4b4a-93e4-5f571e91255a`)에 대한 액세스를 허용 또는 거부하도록 제어됩니다.  이 테넌가 허용된 도메인의 `Restrict-Access-To-Tenants` 목록에 표시되지 않으면 Azure AD에서 이러한 앱에 로그인하지 못하도록 소비자 계정이 차단됩니다.

## <a name="next-steps"></a>다음 단계

- [업데이트된 Office 365 최신 인증](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/) 참조
- [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 검토
