---
title: Azure Active Directory 애플리케이션 프록시 및 Tableau
description: Azure AD(Azure Active Directory) 애플리케이션 프록시를 사용하여 Tableau 배포에 대해 원격 액세스를 제공하는 방법을 알아봅니다.
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
ms.openlocfilehash: d7634858c1cc44d2a04847b7fef39eed588280ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989250"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory 애플리케이션 프록시 및 Tableau 

Azure Active Directory 애플리케이션 프록시 및 Tableau를 사용하여 Tableau 배포에 대한 원격 액세스를 제공하기 위해 애플리케이션 프록시를 쉽게 사용할 수 있도록 합니다. 이 아티클에서는 이 시나리오를 구성하는 방법에 대해 설명합니다.  

## <a name="prerequisites"></a>사전 요구 사항 

이 아티클의 시나리오에서는 다음을 가정합니다.

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure)가 구성됨 

- [애플리케이션 프록시 커넥터](../app-proxy/application-proxy-add-on-premises-application.md)가 설치됨 

 
## <a name="enabling-application-proxy-for-tableau"></a>Tableau에 대해 애플리케이션 프록시 사용 

애플리케이션 프록시는 Tableau가 제대로 작동하는 데 필요한 OAuth 2.0 Grant Flow를 지원합니다. 즉, 아래의 게시 단계를 따라 구성하기만 하면 더 이상 특별한 단계를 수행할 필요 없이 이 애플리케이션을 활성화할 수 있습니다.


## <a name="publish-your-applications-in-azure"></a>Azure에 애플리케이션 게시 

Tableau를 게시하려면 Azure Portal에서 애플리케이션을 게시해야 합니다.

다음의 경우:

- 1~8단계의 자세한 지침은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)를 참조하세요. 
- 앱 프록시 필드의 Tableau 값을 확인하는 방법에 대한 정보는 Tableau 설명서를 참조하세요.  

**앱을 게시하려면**: 


1. 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory &gt; 엔터프라이즈 애플리케이션** 을 선택합니다. 

3. 블레이드의 위쪽에서 **추가** 를 선택합니다. 

4. **온-프레미스 애플리케이션** 을 선택합니다. 

5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다. 

    - **내부 URL**: 이 애플리케이션에는 자체 Tableau URL인 내부 URL이 있어야 합니다. 예들 들어 `https://adventure-works.tableau.com`입니다. 

    - **사전 인증 방법**: Azure Active Directory(권장되지만 필수 아님)입니다. 

6. 블레이드의 위쪽에서 **추가** 를 선택합니다. 애플리케이션이 추가되고 빠른 시작 메뉴가 열립니다. 

7. 빠른 시작 메뉴에서 **테스트할 사용자 지정** 을 선택하고 애플리케이션에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다. 

8. **할당** 을 선택하여 테스트 사용자 할당을 저장합니다. 

9. (선택 사항) 앱 관리 페이지에서 **Single Sign-On** 을 선택합니다. 드롭다운 메뉴에서 **Windows 통합 인증** 을 선택하고 Tableau 구성에 따라 필수 필드를 입력합니다. **저장** 을 선택합니다. 

 

## <a name="testing"></a>테스트 

애플리케이션을 테스트할 준비가 되었습니다. Tableau를 게시하는 데 사용되는 외부 URL에 액세스하고 두 애플리케이션에 할당된 사용자 권한으로 로그인합니다.



## <a name="next-steps"></a>다음 단계

Azure AD 애플리케이션 프록시에 대한 자세한 정보는 [온-프레미스 애플리케이션에 보안 원격 액세스를 제공하는 방법](application-proxy.md)을 참조하세요.

