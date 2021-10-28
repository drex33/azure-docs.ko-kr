---
title: NDES 서버에서 Azure Active Directory 애플리케이션 프록시와 통합
description: NDES 서버를 보호하기 위한 Azure Active Directory 애플리케이션 프록시 배포에 대한 지침입니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.openlocfilehash: 675708c2212d76d1f10bf20a9b5568d466030aa8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990371"
---
# <a name="integrate-with-azure-active-directory-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>NDES(네트워크 디바이스 등록 서비스) 서버에서 Azure Active Directory 애플리케이션 프록시와 통합

AD(Azure Active Directory) 애플리케이션 프록시를 통해 네트워크 내부에 애플리케이션을 게시할 수 있습니다. 이러한 애플리케이션은 SharePoint 사이트, Microsoft Outlook Web App 및 기타 웹 애플리케이션과 같은 애플리케이션입니다. 또한 Azure를 통해 네트워크 외부의 사용자에게 보안 액세스를 제공합니다.

Azure AD 애플리케이션 프록시를 처음 사용하고 자세히 알아보려면 [Azure AD 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격 액세스](application-proxy.md)를 참조하세요.

Azure AD 애플리케이션 프록시는 Azure를 기반으로 합니다. DDOS(배포된 서비스 거부) 공격과 뛰어난 가용성에 대한 보호를 강화하기 위해 대량의 네트워크 대역폭과 서버 인프라를 제공합니다. 또한 온-프레미스 네트워크에 대한 외부 방화벽 포트를 열 필요가 없으며 DMZ 서버가 필요하지 않습니다. 모든 트래픽은 인바운드로 시작됩니다. 아웃바운드 포트의 전체 목록은 [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)를 참조하세요.

> Azure AD 애플리케이션 프록시는 Premium 또는 Basic 버전의 Azure Active Directory를 사용하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요. 
> EMS(Enterprise Mobility Suite) 라이선스가 있는 경우 이 솔루션을 사용할 수 있습니다.
> Azure AD 애플리케이션 프록시 커넥터는 Windows Server 2012 R2 이상에만 설치됩니다. 이는 NDES 서버의 요구 사항이기도 합니다.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>NDES 서버에 커넥터 설치 및 등록

1. 애플리케이션 프록시를 사용하는 디렉터리의 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 예를 들어, 테넌트 도메인이 contoso.com이면 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 관리자 별칭이어야 합니다.
1. 오른쪽 위 모서리에서 사용자 이름을 선택합니다. 애플리케이션 프록시를 사용하는 디렉터리에 로그인했는지 확인합니다. 디렉터리를 변경해야 할 경우 **디렉터리 전환** 을 선택하고 애플리케이션 프록시를 사용하는 디렉터리를 선택합니다.
1. 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.
1. **관리** 아래에서 **애플리케이션 프록시** 를 선택합니다.
1. **커넥터 서비스 다운로드** 를 선택합니다.

    ![커넥터 서비스를 다운로드하여 서비스 약관 확인](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 서비스 약관을 참고하세요. 준비되면 **사용 약관 동의 및 다운로드** 를 선택합니다.
1. Azure AD 애플리케이션 프록시 커넥터 설치 파일을 NDES 서버에 복사합니다. 
   > NDES에 대한 액세스 권한이 있는 회사 네트워크 내의 모든 서버에 커넥터를 설치할 수 있습니다. NDES 서버 자체에 설치할 필요는 없습니다.
1. 설정 파일(예: *AADApplicationProxyConnectorInstaller.exe*)을 실행합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치하는 동안 Azure AD Directory의 애플리케이션 프록시를 사용하여 커넥터를 등록하라는 메시지가 표시됩니다.
   * Azure AD 디렉터리의 전역 또는 애플리케이션 관리자에 대한 자격 증명을 제공합니다. Azure AD 전역 관리자 또는 애플리케이션 관리자 자격 증명은 포털의 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록하는 데 사용되는 전역 또는 애플리케이션 관리자 계정은 애플리케이션 프록시 서비스를 사용할 수 있는 동일한 디렉터리에 있어야 합니다.
        >
        > 예를 들어, Azure AD 도메인이 *contoso.com* 인 경우, 전역/애플리케이션 관리자는 `admin@contoso.com`이거나 해당 도메인에서 다른 유효한 별칭이어야 합니다.

   * 커넥터를 설치하는 서버에 Internet Explorer 보안 강화 구성이 켜져 있으면 등록 화면이 차단될 수 있습니다. 액세스를 허용하려면 오류 메시지의 지침에 따라 설치하는 동안 Internet Explorer 보안 강화를 끕니다.
   * 커넥터 등록에 실패한 경우 [애플리케이션 프록시 문제 해결](application-proxy-troubleshoot.md)을 참조하세요.
1. 설정 종료 시 아웃바운드 프록시를 사용하는 환경에 대한 노트가 표시됩니다. 아웃바운드 프록시를 통해 작동하도록 Azure AD 애플리케이션 프록시 커넥터를 구성하려면 제공되는 스크립트(예: `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`)를 실행하세요.
1. 다음 예제와 같이 Azure Portal의 애플리케이션 프록시 페이지에 새 커넥터가 *활성* 상태로 나열됩니다.

    ![Azure Portal에서 활성으로 표시된 새 Azure AD 애플리케이션 프록시 커넥터](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Azure AD 애플리케이션 프록시를 통해 인증하는 애플리케이션에 고가용성을 제공하기 위해 여러 VM에 커넥터를 설치할 수 있습니다. Azure AD DS 관리되는 도메인에 조인된 다른 서버에 커넥터를 설치하려면 이전 섹션에 나열된 것과 동일한 단계를 반복합니다.

1. 설치가 완료되면 Azure Portal로 돌아갑니다.

1. **Enterprise 애플리케이션** 을 선택합니다.

   ![올바른 관련자가 참여하는지 확인](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **+새 애플리케이션** 을 선택한 다음 **온-프레미스 애플리케이션** 을 선택합니다. 

1. **사용자 고유의 온-프레미스 애플리케이션 추가** 에서 다음 필드를 구성합니다.

   * **이름**: 애플리케이션의 이름을 입력합니다.
   * **내부 URL**: 커넥터를 설치한 NDES 서버의 내부 URL/FQDN을 입력합니다.
   * **사전 인증**: **통과** 를 선택합니다. 어떤 형태의 사전 인증도 사용할 수 없습니다. 인증서 요청(SCEP)에 사용되는 프로토콜은 이러한 옵션을 제공하지 않습니다.
   * 제공된 **외부 URL** 을 클립보드에 복사합니다.

1. **+추가** 를 선택하여 애플리케이션을 저장합니다.

1. 15단계에서 복사한 링크를 브라우저에 붙여넣어 Azure AD 애플리케이션 프록시를 통해 NDES 서버에 액세스할 수 있는지 테스트합니다. 기본 IIS 시작 페이지가 표시됩니다.

1. 최종 테스트로 이전 단계에서 붙여넣은 기존 URL에 *mscep.dll* 경로를 추가합니다.

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. **HTTP 오류 403 - 사용할 수 없음** 응답이 표시됩니다.

1. Microsoft Intune을 통해 제공된 NDES URL을 디바이스로 변경합니다. 이러한 변경은 Microsoft Endpoint Configuration Manager 또는 Microsoft Endpoint Manager 관리 센터에 있을 수 있습니다.

   * Configuration Manager의 경우 인증서 등록 지점으로 이동하여 URL을 조정합니다. 이 URL은 문제를 호출하고 표시하는 디바이스입니다.
   * Intune 독립 실행형의 경우 새 SCEP 정책을 편집하거나 만들고 새 URL을 추가합니다.

## <a name="next-steps"></a>다음 단계

NDES와 통합된 Azure AD 애플리케이션 프록시를 사용하여 사용자가 액세스할 수 있도록 애플리케이션을 게시합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](./application-proxy-add-on-premises-application.md)를 참조하세요.
