---
title: 클레임 인식 앱 - Azure Active Directory 애플리케이션 프록시
description: 사용자의 안전한 원격 액세스를 위해 AD FS 클레임을 허용하는 온-프레미스 ASP.NET 애플리케이션을 게시하는 방법입니다.
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
ms.openlocfilehash: 50f0f4ead9b44da13eb2a45a96db1caed81a5471
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990010"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>애플리케이션 프록시에서 클레임 인식 앱으로 작업
[클레임 인식 앱](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85))은 STS(보안 토큰 서비스)에 대한 리디렉션을 수행합니다. STS는 토큰의 교환으로 사용자의 자격 증명을 요청한 다음 애플리케이션에 사용자를 리디렉션합니다. 애플리케이션 프록시를 이러한 리디렉션과 함께 작동하도록 하는 몇 가지 방법이 있습니다. 이 문서를 사용하여 클레임 인식 앱에 대한 배포를 구성합니다. 

## <a name="prerequisites"></a>사전 요구 사항
클레임 인식 앱이 리디렉션되는 STS가 온-프레미스 네트워크 외부에서 사용 가능한지 확인합니다. 프록시를 통해 노출하거나 외부 연결을 허용하여 STS를 사용할 수 있도록 할 수 있습니다. 

## <a name="publish-your-application"></a>애플리케이션 게시

1. [애플리케이션 프록시로 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)에 설명된 지침에 따라 애플리케이션을 게시합니다.
2. 포털에서 애플리케이션 페이지로 이동하고 **Single Sign-On** 을 선택합니다.
3. **Azure Active Directory** 를 **사전 인증 방법** 으로 선택했다면 **Azure AD Single Sign-On 비활성화** 를 **내부 인증 방법** 으로 선택합니다. **사전 인증 방법** 으로 **통과** 를 선택하면 아무 것도 변경할 필요가 없습니다.

## <a name="configure-adfs"></a>ADFS 구성

두 가지 방법 중 하나에서 클레임 인식 앱에 대한 ADFS를 구성할 수 있습니다. 첫 번째는 사용자 지정 도메인을 사용하는 것입니다. 두 번째는 WS-Federation을 사용하는 것입니다. 

### <a name="option-1-custom-domains"></a>옵션 1: 사용자 지정 도메인

애플리케이션에 대한 모든 내부 URL이 FQDN(정규화된 도메인 이름)인 경우 애플리케이션에 대한 [사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 구성할 수 있습니다. 사용자 지정 도메인을 사용하여 내부 URL과 동일한 외부 URL을 만듭니다. 외부 URL이 내부 URL과 일치하는 경우 사용자가 온-프레미스 또는 원격인지 관계 없이 STS 리디렉션이 작동합니다. 

### <a name="option-2-ws-federation"></a>옵션 2: WS-Federation

1. ADFS 관리를 엽니다.
2. **신뢰 당사자 트러스트** 로 이동하여 애플리케이션 프록시로 게시하려는 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.  

   ![신뢰 당사자 트러스트 앱 이름을 마우스 오른쪽 단추로 클릭 - 스크린샷](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. **엔드포인트** 탭에 있는 **엔드포인트 유형** 아래에서 **WS-Federation** 을 선택합니다.
4. **신뢰할 수 있는 URL** 아래에 **외부 URL** 아래에 있는 애플리케이션 프록시에 입력한 URL을 입력하고 **확인** 을 클릭합니다.  

   ![엔드포인트 추가 - 신뢰할 수 있는 URL 값 설정 - 스크린샷](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>다음 단계
* [네이티브 클라이언트 앱을 사용하여 프록시 애플리케이션과 상호 작용](application-proxy-configure-native-client-application.md)