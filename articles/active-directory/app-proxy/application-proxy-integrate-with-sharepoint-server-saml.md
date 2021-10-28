---
title: Azure Active Directory 애플리케이션 프록시를 사용하여 온-프레미스 SharePoint 팜 게시
description: 온-프레미스 SharePoint 팜을 SAML용 Azure Active Directory 애플리케이션 프록시와 통합하는 방법에 대한 기본 사항을 다룹니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2821cfeb2fc3df05d0b8862ad67adf9dd719ebd8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989288"
---
# <a name="integrate-azure-active-directory-application-proxy-with-sharepoint-saml"></a>Azure Active Directory 애플리케이션 프록시를 SharePoint(SAML)와 통합

이 단계별 가이드에서는 조직(Azure AD, B2B)의 사용자가 인터넷을 통해 SharePoint에 연결하는 Azure AD 애플리케이션 프록시를 사용하여 [Azure Active Directory에 통합된 온-프레미스 SharePoint(SAML)](../saas-apps/sharepoint-on-premises-tutorial.md)에 대한 액세스를 보호하는 방법을 설명합니다.

> [!NOTE]
> Azure AD 애플리케이션 프록시를 처음 사용하고 자세히 알아보려면 [Azure AD 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격 액세스](./application-proxy.md)를 참조하세요.

이 설정에는 세 가지 주요 이점이 있습니다.

- Azure AD 애플리케이션 프록시는 인증된 트래픽이 내부 네트워크와 SharePoint에 연결할 수 있는지 확인합니다.
- 사용자는 VPN을 사용하지 않고 일반적인 방법으로 SharePoint 사이트에 액세스할 수 있습니다.
- Azure AD 애플리케이션 프록시 수준에서 사용자 할당을 통해 액세스를 제어할 수 있으며, 조건부 액세스 및 MFA(Multi-Factor Authentication)와 같은 Azure AD 기능을 사용하여 보안을 강화할 수 있습니다.

이 프로세스에는 두 가지 엔터프라이즈 애플리케이션이 필요합니다. 첫 번째는 갤러리에서 관리되는 SaaS 앱 목록에 게시하는 SharePoint 온-프레미스 인스턴스입니다. 두 번째는 첫 번째 엔터프라이즈 갤러리 애플리케이션을 게시하는 데 사용할 온-프레미스 애플리케이션 (비갤러리 애플리케이션)입니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음 리소스가 필요합니다.
 - SharePoint 2013 팜 이상 SharePoint 팜은 [Azure AD와 통합](../saas-apps/sharepoint-on-premises-tutorial.md)되어야 합니다.
 - 애플리케이션 프록시를 포함하는 계획이 있는 Azure AD 테넌트. [Azure AD 요금제 및 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)에 대해 자세히 알아보세요.
 - Azure AD 테넌트에서 [확인된 사용자 지정 도메인](../fundamentals/add-custom-domain.md)입니다. 확인된 도메인은 SharePoint URL 접미사와 일치해야 합니다.
 - SSL 인증서가 필요합니다. [사용자 지정 도메인 게시](./application-proxy-configure-custom-domain.md)의 세부 정보를 참조하세요.
 - 온-프레미스 Active Directory 사용자는 Azure AD Connect와 동기화해야 하며 [Azure에 로그인](../hybrid/plan-connect-user-signin.md)하도록 구성해야 합니다. 
 - 클라우드 전용 및 B2B 게스트 사용자의 경우 [Azure Portal에서 SharePoint 온-프레미스의 게스트 계정에 대한 액세스 권한을 부여](../saas-apps/sharepoint-on-premises-tutorial.md#manage-guest-users-access)해야 합니다.
 - 회사 도메인 내의 컴퓨터에 설치되어 실행되는 애플리케이션 프록시 커넥터입니다.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>1단계: Azure AD와 온-프레미스에서 SharePoint 통합

1. SharePoint 온-프레미스 앱을 구성합니다. 자세한 내용은 [자습서: SharePoint 온-프레미스와 Azure Active Directory Single Sign-On 통합](../saas-apps/sharepoint-on-premises-tutorial.md)을 참조하세요.
2. 다음 단계로 이동하기 전에 구성의 유효성을 검사합니다. 유효성을 검사하려면 내부 네트워크에서 SharePoint 온-프레미스에 액세스를 시도하고 내부적으로 액세스할 수 있는지 확인합니다.


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>2단계: 애플리케이션 프록시를 사용하여 SharePoint 온-프레미스 애플리케이션 게시

이 단계에서 애플리케이션 프록시를 사용하는 Azure AD 테넌트에서 애플리케이션을 만듭니다. 외부 URL을 설정하고 내부 URL을 지정합니다. 둘 다 SharePoint에서 나중에 사용됩니다.

> [!NOTE]
> 내부 및 외부 URL은 1단계의 SAML 기반 애플리케이션 구성에 있는 **로그온 URL** 과 일치해야 합니다.

   ![로그온 URL 값을 보여 주는 스크린샷](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. 사용자 지정 도메인을 사용하여 새 Azure AD 애플리케이션 프록시 애플리케이션을 만듭니다. 단계별 지침은 [Azure AD 애플리케이션 프록시의 사용자 지정 도메인](./application-proxy-configure-custom-domain.md)을 참조하세요.

    - 내부 URL: 'https://portal.contoso.com/ '
    - 외부 URL: 'https://portal.contoso.com/ '
    - 사전 인증: Azure Active Directory
    - 헤더의 URL 변환: 아니요
    - 애플리케이션 본문에서 URL 변환: 아니요

        ![앱을 만드는 데 사용하는 옵션을 보여 주는 스크린샷](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. 온-프레미스 SharePoint 갤러리 애플리케이션에 할당한 [동일한 그룹](../saas-apps/sharepoint-on-premises-tutorial.md#grant-permissions-to-a-security-group)을 할당합니다.

3. 마지막으로 **속성** 섹션으로 이동하여 **사용자에게 표시** 를 **아니요** 로 설정합니다. 이 옵션은 첫 번째 애플리케이션의 아이콘만 내 앱 포털(https://myapplications.microsoft.com) )에 표시되도록 합니다.

   ![사용자 옵션에 표시로 설정하는 위치를 보여 주는 스크린샷](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>3단계: 애플리케이션 테스트

외부 네트워크의 컴퓨터에서 브라우저를 사용하여 게시 단계 중에 구성한 링크로 이동합니다. 설정한 테스트 계정으로 로그인할 수 있는지 확인합니다.
