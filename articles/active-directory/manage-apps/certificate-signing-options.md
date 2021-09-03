---
title: SAML 토큰의 고급 인증서 서명 옵션
titleSuffix: Azure AD
description: Azure Active Directory에서 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션을 사용하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: davidmu
ms.reviewer: saumadan
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d2a346dcd77352715b63937e5bad5274f2d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536244"
---
# <a name="advanced-certificate-signing-options-in-a-saml-token"></a>SAML 토큰의 고급 인증서 서명 옵션

현재 Azure AD(Azure Active Directory)는 Azure Active Directory 앱 갤러리에서 수천 개의 사전 통합 애플리케이션을 지원합니다. [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) 애플리케이션과 같은 500개가 넘는 애플리케이션에서 [SAML(Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0 프로토콜을 사용하여 Single Sign-On을 지원합니다. 고객이 SAML을 사용하여 Azure AD를 통해 애플리케이션을 인증하면 Azure AD는 애플리케이션에 토큰을 보냅니다(HTTP POST를 통해). 그런 다음, 애플리케이션은 사용자 이름과 암호를 묻는 대신 토큰의 유효성을 검사하고 사용하여 사용자를 로그인합니다. 이러한 SAML 토큰은 Azure AD 및 특정 표준 알고리즘에서 생성된 고유한 인증서로 서명됩니다.

Azure AD는 갤러리 애플리케이션의 기본 설정 중 일부를 사용합니다. 애플리케이션 요구 사항에 따라 기본값이 설정됩니다.

Azure AD에서 인증서 서명 옵션과 인증서 서명 알고리즘을 설정할 수 있습니다.

## <a name="certificate-signing-options"></a>인증서 서명 옵션

Azure AD는 다음과 같이 세 가지 인증서 서명 옵션을 지원합니다.

* **SAML 어설션 서명**. 대부분의 갤러리 애플리케이션에 이 기본 옵션이 설정됩니다. 이 옵션을 선택하면 IdP(ID 공급자)와 같은 Azure AD에서 애플리케이션의 [X.509](https://wikipedia.org/wiki/X.509) 인증서로 SAML 어설션 및 인증서에 서명합니다.

* **SAML 응답 서명**. 이 옵션을 선택하면 IdP인 Azure AD에서 애플리케이션의 X509 인증서로 SAML 응답에 서명합니다.

* **SAML 응답 및 어설션 서명**. 이 옵션을 선택하면 IdP인 Azure AD에서 애플리케이션의 X509 인증서로 전체 SAML 토큰에 서명합니다.

## <a name="certificate-signing-algorithms"></a>인증서 서명 알고리즘

Azure AD는 SAML 응답에 서명하기 위해 두 서명 알고리즘 또는 SHA(보안 해시 알고리즘)를 지원합니다.

* **SHA-256**. Azure AD가 SAML 응답에 서명하기 위해 이 기본 알고리즘을 사용합니다. 최신 알고리즘이며, SHA-1보다 더 안전합니다. 대부분의 애플리케이션에서 SHA-256 알고리즘을 지원합니다. 애플리케이션이 서명 알고리즘으로 SHA-1만 지원하는 경우 이를 변경할 수 있습니다. 그렇지 않으면 SAML 응답에 서명하는 데 SHA-256 알고리즘을 사용하는 것이 좋습니다.

* **SHA-1**. 이것은 이전 알고리즘이며, SHA-256보다 덜 안전하다고 여겨집니다. 애플리케이션에서 이 서명 알고리즘만 지원하는 경우 **서명 알고리즘** 드롭다운 목록에서 이 옵션을 선택할 수 있습니다. 그러면 Azure AD에서 SHA-1 알고리즘으로 SAML 응답에 서명합니다.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>인증서 서명 옵션 및 서명 알고리즘 변경

애플리케이션의 SAML 인증서 서명 옵션과 인증서 서명 알고리즘을 변경하려면 해당 애플리케이션을 선택합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에서 계정에 로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다. 계정의 엔터프라이즈 애플리케이션 목록이 표시됩니다.
1. 애플리케이션을 선택합니다. 애플리케이션에 대한 개요 페이지가 표시됩니다. 이 예에서는 Salesforce 애플리케이션이 사용됩니다.

   ![예제: 애플리케이션 개요 페이지](./media/certificate-signing-options/application-overview-page.png)

그런 다음, 해당 애플리케이션에 대한 SAML 토큰에서 인증서 서명 옵션을 변경합니다.

1. 애플리케이션 개요 페이지의 왼쪽 창에서 **Single Sign-On** 을 선택합니다.
1. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지가 나타나면 5단계로 이동합니다.
1. **Single Sign-On 방법 선택** 페이지가 표시되지 않으면 **Single Sign-On 모드 변경** 을 선택하여 해당 페이지를 표시합니다.
1. 사용 가능한 경우 **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다. (**SAML** 을 사용할 수 없는 경우 애플리케이션은 SAML을 지원하지 않으며, 이 절차와 문서의 나머지 부분을 무시해도 됩니다.)
1. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지에서 **SAML 서명 인증서** 제목을 찾고 **편집** 아이콘(연필)을 선택합니다. **SAML 서명 인증서** 페이지가 나타납니다.

   ![예제: SAML 서명 인증서 페이지](./media/certificate-signing-options/saml-signing-page.png)

1. **서명 옵션** 드롭다운 목록에서 **SAML 응답 서명**, **SAML 어설션 서명** 또는 **SAML 응답 및 어설션 서명** 을 선택합니다. 이러한 옵션에 대한 설명은 이 문서의 앞부분에 나오는 [인증서 서명 옵션](#certificate-signing-options)에 나와 있습니다.
1. **서명 알고리즘** 드롭다운 목록에서 **SHA-1** 또는 **SHA-256** 을 선택합니다. 이러한 옵션에 대한 설명은 이 문서의 앞부분에 나오는 [인증서 서명 알고리즘](#certificate-signing-algorithms)에 나와 있습니다.
1. 선택 항목이 만족스러운 경우 **저장** 을 선택하여 새 SAML 서명 인증서 설정을 적용합니다. 그렇지 않으면 **X** 를 선택하여 변경 내용을 취소합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 앱 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](./configure-saml-single-sign-on.md)
* [SAML 기반 Single Sign-On 문제 해결](./debug-saml-sso-issues.md)
