---
title: Azure Active Directory 애플리케이션 프록시를 사용하여 Power BI에 대한 원격 액세스 설정
description: 온-프레미스 Power BI를 Azure Active Directory 애플리케이션 프록시와 통합하는 방법에 대한 기본 사항을 다룹니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.custom: has-adal-ref
ms.openlocfilehash: 7199e973aa24ea93e85798c6b5a8645a84adb024
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989345"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 Power BI Mobile에 대한 원격 액세스 설정

이 문서에서는 Azure AD 애플리케이션 프록시를 사용하여 Power BI Mobile 앱을 사용하도록 설정함으로써 PBIRS(Power BI Report Server) 및 SSRS(SQL Server Reporting Services) 2016 이상에 연결하는 방법을 알아봅니다. 회사 네트워크에서 멀리 떨어진 사용자는 이 통합을 통해 Power BI Mobile 앱의 보고서에 액세스하고 Azure AD 인증으로 보호될 수 있습니다. 이 보호에는 조건부 액세스 및 다단계 인증과 같은 [보안 혜택](application-proxy-security.md#security-benefits)이 포함됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 이미 보고서 서비스를 배포하고 [애플리케이션 프록시를 사용하도록 설정](../app-proxy/application-proxy-add-on-premises-application.md)했다고 가정합니다.

- 애플리케이션 프록시를 사용하도록 설정하려면 Windows 서버에 커넥터를 설치하고 커넥터에서 Azure AD 서비스와 통신할 수 있도록 [필수 구성 요소](../app-proxy/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)를 완료해야 합니다.
- Power BI를 게시하는 경우 동일한 내부 및 외부 도메인을 사용하는 것이 좋습니다. 사용자 지정 도메인에 대한 자세한 내용은 [애플리케이션 프록시에서 사용자 지정 도메인 작업](./application-proxy-configure-custom-domain.md)을 참조하세요.
- 이 통합은 **Power BI Mobile iOS 및 Android** 애플리케이션에 사용할 수 있습니다.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1단계: KCD(Kerberos 제한 위임) 구성

Windows 인증을 사용하는 온-프레미스 애플리케이션에 대해 Kerberos 인증 프로토콜 및 KCD(Kerberos 제한 위임)라는 기능을 사용하여 SSO(Single Sign-On)를 획득할 수 있습니다. 구성된 경우, KCD를 통해 애플리케이션 프록시 커넥터는 사용자가 Windows에 직접 로그인하지 않더라도 사용자에 대한 Windows 토큰을 얻을 수 있습니다. KCD에 대한 자세한 내용은 [Kerberos 제한 위임 개요](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) 및 [애플리케이션 프록시를 사용한 앱에 Single Sign-On을 위한 Kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조하세요.

Reporting Services 쪽에서 구성할 항목이 많지 않습니다. 적절한 Kerberos 인증을 수행할 수 있도록 유효한 SPN(서비스 주체 이름)이 있어야 합니다. 또한 Reporting Services 서버에서 Negotiate 인증을 사용할 수 있는지 확인합니다.

Reporting Services에 대한 KCD를 설정하려면 다음 단계를 따라 계속 진행합니다.

### <a name="configure-the-service-principal-name-spn"></a>SPN(서비스 주체 이름) 구성

SPN은 Kerberos 인증을 사용하는 서비스에 대한 고유한 식별자입니다. 보고서 서버에 대한 적절한 HTTP SPN이 있는지 확인해야 합니다. 보고서 서버에 대한 적절한 SPN(서비스 주체 이름)을 구성하는 방법에 대한 자세한 내용은 [보고서 서버에 SPN(서비스 주체 이름) 등록](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server)을 참조하세요.
-L 옵션과 함께 Setspn 명령을 실행하여 SPN이 추가되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)을 참조하세요.

### <a name="enable-negotiate-authentication"></a>Negotiate 인증 사용

보고서 서버에서 Kerberos 인증을 사용하도록 설정하려면 보고서 서버의 인증 유형이 RSWindowsNegotiate가 되도록 구성합니다. rsreportserver.config 파일을 사용하여 이 설정을 구성합니다.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

자세한 내용은 [Reporting Services 구성 파일 수정](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) 및 [보고서 서버에서 Windows 인증 구성](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server)을 참조하세요.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>커넥터가 Reporting Services 애플리케이션 풀 계정에 추가된 SPN에 위임되도록 신뢰할 수 있는지 확인합니다.
Azure AD 애플리케이션 프록시 서비스가 사용자 ID를 Reporting Services 애플리케이션 풀 계정에 위임할 수 있도록 KCD를 구성합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음 해당 서버에서 컨텍스트를 대상 애플리케이션(이 경우 Reporting Services)에 전달합니다.

KCD를 구성하려면 각 커넥터 컴퓨터에 대해 다음 단계를 반복합니다.

1. 도메인 관리자로 도메인 컨트롤러에 로그인하고 **Active Directory 사용자 및 컴퓨터** 를 엽니다.
2. 커넥터가 실행 중인 컴퓨터를 찾습니다.
3. 컴퓨터를 두 번 클릭한 후 **위임** 탭을 선택합니다.
4. 위임 설정이 **지정된 서비스에 대한 위임용으로만 이 컴퓨터 트러스트** 로 설정되어 있는지 확인합니다. 그런 다음 **모든 인증 프로토콜 사용** 을 선택합니다.
5. **추가** 를 선택하고 **사용자 또는 컴퓨터** 를 선택합니다.
6. Reporting Services에 사용할 서비스 계정을 입력합니다. Reporting Services 구성 내에서 SPN을 추가할 계정입니다.
7. **확인** 을 클릭합니다. 변경 내용을 저장하려면 **확인** 을 다시 클릭합니다.

자세한 내용은 [애플리케이션 프록시를 사용하여 앱에 Single Sign-On에 대한 Kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조하세요.

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2단계: Azure AD 애플리케이션 프록시를 통해 Report Services 게시

이제 Azure AD 애플리케이션 프록시를 구성할 준비가 되었습니다.

1. 다음 설정을 사용하여 애플리케이션 프록시를 통해 Report Services를 게시합니다. 애플리케이션 프록시를 통해 애플리케이션을 게시하는 방법에 대한 단계별 지침은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요.
   - **내부 URL**: 커넥터가 회사 네트워크에서 연결할 수 있는 보고서 서버의 URL을 입력합니다. 커넥터가 설치된 서버에서 이 URL에 연결할 수 있는지 확인합니다. 애플리케이션 프록시를 통해 게시된 하위 경로 문제를 방지하기 위해 `https://servername/`과 같은 최상위 도메인을 사용하는 것이 가장 좋습니다. 예를 들어, `https://servername/`을 사용하고, `https://servername/reports/` 또는 `https://servername/reportserver/`는 사용하지 않습니다.
     > [!NOTE]
     > 보고서 서버에 대한 보안 HTTPS 연결을 사용하는 것이 좋습니다. 자세한 내용은 [기본 모드 보고서 서버에서 SSL 연결 구성](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server)을 참조하세요.
   - **외부 URL**: Power BI Mobile 앱에서 연결할 퍼블릭 URL을 입력합니다. 예를 들어, 사용자 지정 도메인을 사용하는 경우 `https://reports.contoso.com`처럼 보일 수 있습니다. 사용자 지정 도메인을 사용하려면 해당 도메인에 대한 인증서를 업로드하고, DNS 레코드로 애플리케이션의 기본 msappproxy.net 도메인을 가리킵니다. 자세한 내용은 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조하세요.

   - **사전 인증 방법**: Azure Active Directory

2. 앱이 게시된 후에는 다음 단계에 따라 Single Sign-On 설정을 구성합니다.

   a. 포털의 애플리케이션 페이지에서 **Single Sign-On** 을 선택합니다.

   b. **Single Sign-On 모드** 로 **Windows 통합 인증** 을 선택합니다.

   다. **내부 애플리케이션 SPN** 을 이전에 설정한 값으로 설정합니다.

   d. 커넥터에 대한 **위임된 로그인 ID** 를 선택하여 사용자를 대신하여 사용합니다. 자세한 내용은 [다른 온-프레미스 및 클라우드 ID로 작업](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)을 참조하세요.

   e. **저장** 을 클릭하여 변경 내용을 저장합니다.

애플리케이션 설정을 완료하려면 **사용자 및 그룹** 섹션으로 이동하고 이 애플리케이션에 액세스할 사용자를 할당합니다.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3단계: 애플리케이션에 대한 회신 URI 수정

Power BI Mobile 앱이 Report Services에 연결되고 액세스할 수 있으려면 먼저 2단계에서 자동으로 만들어진 애플리케이션 등록을 구성해야 합니다.

1. Azure Active Directory **개요** 페이지에서 **앱 등록** 을 선택합니다.
2. **모든 애플리케이션** 탭 아래에서 2단계에서 만든 애플리케이션을 검색합니다.
3. 해당 애플리케이션을 선택한 다음, **인증** 을 선택합니다.
4. 사용 중인 플랫폼에 따라 다음 리디렉션 URI를 추가합니다.

   Power BI Mobile **iOS** 용 앱을 구성하는 경우 퍼블릭 클라이언트(모바일 및 데스크톱) 형식의 다음 리디렉션 URI를 추가합니다.
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Power BI Mobile **Android** 용 앱을 구성하는 경우 퍼블릭 클라이언트 유형(모바일 및 데스크톱)의 다음 리디렉션 URI를 추가합니다.
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 애플리케이션이 올바르게 작동하려면 리디렉션 URI를 추가해야 합니다. Power BI Mobile iOS 및 Android용 앱을 구성하는 경우, iOS에 대해 구성된 리디렉션 URI 목록에 퍼블릭 클라이언트 유형(모바일 및 데스크톱)의 다음 리디렉션 URI를 추가합니다(`urn:ietf:wg:oauth:2.0:oob`).

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4단계: Power BI Mobile 앱에서 연결

1. Power BI Mobile 앱에서 Reporting Services 인스턴스에 연결합니다. 이렇게 하려면 애플리케이션 프록시를 통해 게시한 애플리케이션의 **외부 URL** 을 입력합니다.

   ![외부 URL을 사용하는 Power BI Mobile 앱](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **연결** 을 선택합니다. Azure Active Directory 로그인 페이지로 이동됩니다.

3. 사용자에 대한 올바른 자격 증명을 입력하고 **로그인** 을 선택합니다. Reporting Services 서버의 요소가 표시됩니다.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5단계: 관리되는 디바이스에 대한 Intune 정책 구성(선택 사항)

Microsoft Intune을 사용하여 회사 인력이 사용하는 클라이언트 앱을 관리할 수 있습니다. Intune을 사용하면 데이터 암호화 및 추가 액세스 요구 사항과 같은 기능을 사용할 수 있습니다. Intune을 통한 앱 관리에 대해 자세히 알아보려면 Intune 앱 관리를 참조하세요. Power BI Mobile 애플리케이션이 Intune 정책에서 작동하도록 설정하려면 다음 단계를 사용합니다.

1. **Azure Active Directory** 로 이동한 다음 **앱 등록** 으로 이동합니다.
2. 원시 클라이언트 애플리케이션을 등록할 때는 3단계에서 구성된 애플리케이션을 선택합니다.
3. 애플리케이션 페이지에서 **API 사용 권한** 을 선택합니다.
4. **권한 추가** 를 클릭합니다.
5. **내 조직에서 사용하는 API** 에서 "Microsoft 모바일 애플리케이션 관리"를 검색하고 선택합니다.
6. 애플리케이션에 **DeviceManagementManagedApps** 사용 권한 추가
7. **관리자 동의 부여** 를 클릭하여 애플리케이션에 대한 사용 권한을 부여합니다.
8. [앱 보호 정책을 만들고 할당하는 방법](/intune/app-protection-policies)을 참조하여 원하는 Intune 정책을 구성합니다.

## <a name="troubleshooting"></a>문제 해결

몇 분이 지나도록 보고서 로드를 시도한 후 애플리케이션에서 오류 페이지가 반환되는 경우 제한 시간 설정을 변경해야 할 수 있습니다. 기본적으로 애플리케이션 프록시는 요청에 응답하는 데 최대 85초를 소요하는 애플리케이션을 지원합니다. 이 설정을 180초로 늘리려면 애플리케이션의 앱 프록시 설정 페이지에서 백 엔드 제한 시간을 **길게** 로 선택합니다. 빠르고 신뢰할 수 있는 보고서를 만드는 방법에 대한 팁은 [Power BI 보고서 모범 사례](/power-bi/power-bi-reports-performance)를 참조하세요.

온-프레미스 Power BI Report Server에 연결하기 위해 Azure AD 애플리케이션 프록시를 사용하여 Power BI Mobile 앱을 사용하도록 설정하는 것은 승인된 클라이언트 앱으로 사용 가능한 Microsoft Power BI 앱을 요구하는 조건부 액세스 정책으로 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [네이티브 클라이언트 앱이 프록시 애플리케이션과 상호 작용할 수 있도록 설정](application-proxy-configure-native-client-application.md).
- [Power BI 모바일 앱에서 온-프레미스 보고서 서버 보고서 및 KPI 보기](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
