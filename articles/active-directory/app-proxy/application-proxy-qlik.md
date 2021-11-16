---
title: Azure Active Directory 애플리케이션 프록시 및 Qlik Sense
description: Azure Active Directory 애플리케이션 프록시를 Qlik Sense와 통합합니다.
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
ms.openlocfilehash: 825ec4927a1db3011560a221868816a1e6089a35
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988889"
---
# <a name="azure-active-directory-application-proxy-and-qlik-sense"></a>Azure Active Directory 애플리케이션 프록시 및 Qlik Sense 
Azure Active Directory 애플리케이션 프록시 및 Qlik Sense를 함께 사용하여 Qlik Sense 배포에 대한 원격 액세스를 제공하기 위해 애플리케이션 프록시를 쉽게 사용할 수 있도록 합니다.  

## <a name="prerequisites"></a>사전 요구 사항 
이 시나리오의 나머지 부분에서는 다음 작업을 완료했다고 가정합니다.
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) 구성 
- [애플리케이션 프록시 커넥터 설치](../app-proxy/application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Azure에 애플리케이션 게시 
QlikSense를 게시하려면 Azure에서 두 개의 애플리케이션을 게시해야 합니다.  

### <a name="application-1"></a>애플리케이션 #1: 
앱을 게시하려면 다음 단계를 수행합니다. 1 ~ 8 단계에 대한 자세한 연습은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)를 참조하세요. 


1. 전역 관리자 권한으로 Azure Portal에 로그인합니다. 
2. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다. 
3. 블레이드의 위쪽에서 **추가** 를 선택합니다. 
4. **온-프레미스 애플리케이션** 을 선택합니다. 
5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다. 
   - **내부 URL**: 이 애플리케이션에는 자체 QlikSense URL인 내부 URL이 있어야 합니다. 예: **https&#58;//demo.qlikemm.com:4244** 
   - **사전 인증 방법**: Azure Active Directory(권장되지만 필수 아님) 
1. 블레이드의 아래쪽에서 **추가** 를 선택합니다. 애플리케이션이 추가되고 빠른 시작 메뉴가 열립니다. 
2. 빠른 시작 메뉴에서 **테스트할 사용자 지정** 을 선택하고 애플리케이션에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다. 
3. **할당** 을 선택하여 테스트 사용자 할당을 저장합니다. 
4. (선택 사항) 앱 관리 블레이드에서 Single Sign-On을 선택합니다. 드롭다운 메뉴에서 **Kerberos 제한된 위임** 을 선택하고 Qlik 구성에 따라 필수 필드를 입력합니다. **저장** 을 선택합니다. 

### <a name="application-2"></a>애플리케이션 #2: 
다음과 같은 예외가 포함된 애플리케이션 #1과 동일한 단계를 따릅니다. 

**5단계**: 내부 URL은 애플리케이션에서 사용하는 인증 포트를 사용하는 QlikSense URL이어야 합니다. 기본값은 HTTPS의 경우 **4244** 이고, 2018년 4월 이전 릴리스된 QlikSense용 HTTP의 경우 **4248** 입니다. 2018년 4월 이전의 QlikSense 릴리스에 대한 기본값은 HTTPS의 경우 **443**, HTTP의 경우 **80** 입니다.  예: **https&#58;//demo.qlik.com:4244**</br></br>
**10단계:** SSO를 설정하지 않고 **Single Sign-On을 설정하지 않고** 그대로 둡니다.
 
 
## <a name="testing"></a>테스트 
애플리케이션을 테스트할 준비가 되었습니다. 애플리케이션 #1에 QlikSense를 게시하는 데 사용되는 외부 URL에 액세스하고 두 애플리케이션에 할당된 사용자 권한으로 로그인합니다.  

## <a name="additional-references"></a>추가 참조
애플리케이션 프록시를 사용하여 Qlik Sense를 게시하는 방법에 대한 자세한 내용은 다음의 Qlik 커뮤니티 문서를 참조하세요. 
- [Qlik Sense가 포함된 Kerberos 제한 위임을 통해 Windows 통합 인증을 사용한 Azure AD](https://community.qlik.com/docs/DOC-20183)
- [Azure AD 애플리케이션 프록시와 Qlik Sense 통합](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 커넥터 작업](../app-proxy/application-proxy-connector-groups.md)
