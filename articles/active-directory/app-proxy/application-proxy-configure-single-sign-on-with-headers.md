---
title: Azure AD 앱 프록시로 온-프레미스 앱에 대한 헤더 기반 Single Sign-On 제공
description: 헤더 기반 인증으로 보호되는 온-프레미스 애플리케이션에 대해 Single Sign-On을 제공하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 28f5cbcd124363f3e7a004bd80f254248dd80e95
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162401"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>Azure AD 앱 프록시로 온-프레미스 앱에 대한 헤더 기반 Single Sign-On 제공

Azure Active Directory(Azure AD) 애플리케이션 프록시는 기본적으로 인증에 헤더를 사용하는 애플리케이션에 대한 Single Sign-On 액세스를 지원합니다. Azure AD에서 애플리케이션에 필요한 헤더 값을 구성할 수 있습니다. 헤더 값은 애플리케이션 프록시를 통해 애플리케이션으로 전송됩니다. 애플리케이션 프록시로 헤더 기반 인증에 대한 기본 지원을 사용할 때의 이점은 다음과 같습니다.  

* **온-프레미스 앱에 대한 원격 액세스 제공 간소화** - 앱 프록시를 사용하면 기존의 원격 액세스 아키텍처를 단순화할 수 있습니다. 해당 앱에 대한 VPN 액세스를 바꿀 수 있습니다. 인증을 위해 온-프레미스 ID 솔루션에 의존하는 의존성을 제거할 수 있습니다. 사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 달라진 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다.  

* **추가 소프트웨어 또는 앱 변경의 필요성이 없음** - 기존 애플리케이션 프록시 커넥터를 사용할 수 있으며 추가 소프트웨어를 설치할 필요가 없습니다.  

* **광범위한 특성 및 변환 목록 제공** - 사용 가능한 모든 헤더 값은 Azure AD에서 발급한 표준 클레임을 기반으로 합니다. [SAML 또는 OIDC 애플리케이션에 대한 클레임을 구성](../develop/active-directory-saml-claims-customization.md#attributes)하는 데 사용할 수 있는 모든 속성 및 변환을 헤더 값으로도 사용할 수 있습니다. 

## <a name="pre-requisites"></a>필수 구성 요소
헤더 기반 인증 앱에 대한 Single Sign-On을 시작하기 전에 내 환경이 다음 설정 및 구성을 갖추고 준비되었는지 확인합니다.
- 애플리케이션 프록시를 사용 설정하고 애플리케이션과 곧바로 연결되는 커넥터를 설치해야 합니다. 온-프레미스 환경을 준비하고 커넥터를 설치 및 등록하고 커넥터를 테스트하는 방법에 대한 자세한 내용은 [애플리케이션 프록시를 통해 원격 액세스를 위한 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 자습서를 참조하세요. 

## <a name="supported-capabilities"></a>지원되는 기능

다음 표에서는 애플리케이션 프록시로 지원되는 헤더 기반 인증 애플리케이션에 필요한 일반적인 기능이 나열되어 있습니다. 

|요구 사항   |Description|
|----------|-----------|
|페더레이션 SSO |사전 인증 모드에서는 모든 애플리케이션이 Azure AD 인증으로 보호되며 사용자는 Single Sign-On을 사용할 수 있습니다. |
|원격 액세스 |애플리케이션 프록시는 앱에 원격으로 액세스할 수 있게 해 줍니다. 사용자는 외부 URL을 사용하여 어느 브라우저에서든 인터넷을 통해 애플리케이션에 액세스할 수 있습니다. 애플리케이션 프록시는 회사 액세스에 사용하기 위한 것이 아닙니다. |
|헤더 기반 통합 |애플리케이션 프록시는 Azure AD와의 SSO 통합을 수행한 다음 ID 또는 기타 애플리케이션 데이터를 HTTP 헤더로 애플리케이션에 전달합니다. |
|애플리케이션 권한 부여 |액세스되는 애플리케이션, 사용자의 그룹 멤버 자격 및 기타 정책에 따라 일반 정책을 지정할 수 있습니다. Azure AD에서 정책은 [조건부 액세스](../conditional-access/overview.md)를 사용하여 구현됩니다. 애플리케이션 권한 부여 정책은 최초 인증 요청에만 적용됩니다. |
|인증 강화 |예를 들어 중요한 리소스에 대한 액세스 권한을 얻기 위해서는 추가 인증을 강제하도록 정책을 정의할 수 있습니다. |
|세분화된 권한 부여 |URL 수준에서 액세스 제어를 제공합니다. 액세스되는 URL에 따라 추가 정책을 적용할 수 있습니다. 앱에 대해 구성된 내부 URL은 정책이 적용되는 앱의 범위를 정의합니다. 가장 세분화된 경로에 대해 구성된 정책이 적용됩니다.  |

> [!NOTE] 
> 이 문서에서는 애플리케이션 프록시를 사용하여 헤더 기반 인증 애플리케이션을 Azure AD에 연결(권장 패턴)하는 기능을 설명합니다. 또한 대체 방법으로 PingAccess를 Azure AD와 함께 사용하여 헤더 기반 인증을 가능하게 하는 통합 패턴이 있습니다. 자세한 내용은 [애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증](application-proxy-ping-access-publishing-guide.md)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works-updated.png" alt-text="애플리케이션 프록시를 사용해 헤더 기반 Single Sign-On이 작동하는 방법입니다." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works-updated.png":::

1. 관리자는 Azure AD 포털에서 애플리케이션에 필요한 특성 매핑을 사용자 지정합니다. 
2. 사용자가 앱에 액세스하면 애플리케이션 프록시는 사용자가 Azure AD에서 인증되었는지 확인합니다. 
3. 애플리케이션 프록시 클라우드 서비스는 필요한 특성을 인지합니다. 따라서 서비스는 인증 중에 받은 ID 토큰에서 해당 클레임을 페치합니다. 그런 다음 서비스는 해당 값을 필요한 HTTP 헤더로 커넥터에 대한 요청의 일부로 변환합니다. 
4. 그런 다음 요청은 커넥터로 전달되고 커넥터는 백 엔드 애플리케이션으로 전달됩니다. 
5. 애플리케이션은 헤더를 수신하고 필요에 따라 해당 헤더를 사용할 수 있습니다. 

## <a name="publish-the-application-with-application-proxy"></a>애플리케이션 프록시를 사용하여 애플리케이션 게시

1.  [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)에 설명된 지침에 따라 애플리케이션을 게시합니다.  
    - 내부 URL 값에 따라 애플리케이션의 범위가 결정됩니다. 애플리케이션의 루트 경로에서 내부 URL 값을 구성하는 경우 루트 아래의 모든 하위 경로에 동일한 헤더 구성과 기타 애플리케이션 구성이 들어갑니다. 
    - 새 애플리케이션을 만들어 이전에 구성한 애플리케이션보다 더 세분화된 경로에 다른 헤더 구성 또는 사용자 할당을 설정합니다. 필요한 특정 경로로 새 애플리케이션의 내부 URL을 구성한 다음 이 URL에 필요한 특정 헤더를 구성합니다. 애플리케이션 프록시는 항상 내 구성 설정을 애플리케이션에 대해 설정된 가장 세분화된 경로에 일치시킵니다. 

2.  **사전 인증 방법** 으로  **Azure Active Directory** 를 선택합니다. 
3. **사용자 및 그룹** 으로 이동한 다음 적절한 사용자 및 그룹을 할당하여 테스트 사용자를 할당합니다. 
4. 브라우저를 열고 애플리케이션 프록시 설정에서  **외부 URL** 로 이동합니다. 
5. 애플리케이션에 연결할 수 있는지 확인합니다. 연결할 수는 있지만 헤더가 구성되지 않았기 때문에 앱에 액세스할 수 없습니다. 

## <a name="configure-single-sign-on"></a>Single Sign-On 구성 
헤더 기반 애플리케이션에 대한 Single Sign-On을 시작하기 전에 애플리케이션 프록시 커넥터가 이미 설치되어 있고 커넥터가 대상 애플리케이션에 액세스할 수 있어야 합니다. 그렇지 않은 경우  [자습서: Azure AD 애플리케이션 프록시](application-proxy-add-on-premises-application.md) 에 있는 단계를 수행한 다음 여기로 돌아오세요. 

1. 애플리케이션이 엔터프라이즈 애플리케이션 목록에 나타나면 이를 선택하고  **Single Sign-On** 을 선택합니다. 
2. Single Sign-On 모드를 **헤더 기반** 으로 설정합니다. 
3. 기본 구성에서는 **Azure Active Directory** 가 기본값으로 선택됩니다. 
4. 헤더에서 편집 연필을 선택하여 애플리케이션에 전송할 헤더를 구성합니다. 
5. **새 헤더 추가** 를 선택합니다. 헤더에 사용할 **이름** 을 제공하고 **특성** 또는 **변환** 중 하나를 선택한 다음 드롭 다운에서 애플리케이션에 필요한 헤더를 선택합니다.  
    - 사용할 수 있는 특성의 목록에 대한 자세한 내용은 [클레임 사용자 지정 - 특성](../develop/active-directory-saml-claims-customization.md#attributes)을 참조하세요. 
    - 사용할 수 있는 특성의 목록에 대한 자세한 내용은 [클레임 사용자 지정 - 클레임 변환](../develop/active-directory-saml-claims-customization.md#claim-transformations)을 참조하세요. 
    - **그룹 헤더** 를 추가하여 사용자가 속한 모든 그룹 또는 애플리케이션에 헤더로 할당된 그룹을 전송할 수도 있습니다. 그룹을 값으로 구성하는 방법에 대한 자세한 내용은 [애플리케이션에 대한 그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)을 참조하세요. 
6. [저장]을 선택합니다. 

## <a name="test-your-app"></a>앱 테스트 

이 단계를 모두 완료하면 앱이 실행되고 사용 가능한 상태가 되어야 합니다. 앱을 테스트하는 방법은 다음과 같습니다. 
1. 새 브라우저 또는 프라이빗 브라우저 창을 열어 이전에 캐시된 헤더가 지워졌는지 확인합니다. 그런 다음 애플리케이션 프록시 설정에서  **외부 URL** 로 이동합니다.
2. 앱에 할당한 테스트 계정으로 로그인합니다. SSO를 사용하여 애플리케이션을 로드하고 로그인할 수 있으면 된 것입니다. 

## <a name="considerations"></a>고려 사항

- 애플리케이션 프록시는 온-프레미스 또는 프라이빗 클라우드에서 앱에 원격으로 액세스하는 데 사용됩니다. 애플리케이션 프록시는 회사 네트워크에서 내부적으로 발생하는 트래픽을 처리하는 용도로는 권장되지 않습니다.
- 헤더 기반 인증 애플리케이션에 대한 액세스는 커넥터 또는 다른 허용된 헤더 기반 인증 솔루션에서 들어오는 트래픽에만 허용되어야 합니다. 이는 일반적으로 애플리케이션 서버에서 방화벽 또는 IP 제한을 사용하여 애플리케이션에 대한 네트워크 액세스를 제한하는 방식으로 수행됩니다.

## <a name="next-steps"></a>다음 단계

- [Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)
- [애플리케이션 프록시란?](what-is-application-proxy.md)
- [애플리케이션 관리에 대한 빠른 시작 시리즈](../manage-apps/view-applications-portal.md)
