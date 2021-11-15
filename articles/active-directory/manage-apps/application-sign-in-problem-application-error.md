---
title: 로그인한 후 앱 페이지에 오류 메시지가 표시됩니다.
titleSuffix: Azure AD
description: 앱에서 오류를 내보내는 경우 Azure AD 로그인에서 발생한 문제를 해결하는 방법
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 499ce4c7875a1805b5845614221ffef0641816d6
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617780"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in-azure-active-directory"></a>사용자가 Azure Active Directory에 로그인하면 앱 페이지에 오류 메시지가 표시됩니다.

이 시나리오에서 Azure Active Directory (Azure AD)는 사용자를 로그인시킵니다. 그러나 애플리케이션은 오류 메시지를 표시하고 사용자가 로그인 흐름을 완료하지 않도록 합니다. 문제는 앱이 Azure AD에서 발급한 응답을 수락하지 않았다는 것입니다.

앱이 Azure AD의 응답을 수락하지 않은 데에는 몇 가지 가능한 이유가 있습니다. 오류 메시지가 응답에서 누락된 항목을 명확하게 식별하지 못하는 경우 다음을 시도합니다.

- 앱이 Azure AD 갤러리라면 [Azure AD의 애플리케이션에 대한 SAML 기반 Single Sign-On을 디버그하는 방법](./debug-saml-sso-issues.md)의 단계를 수행했는지 확인합니다.

- [Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 SAML 요청, 응답 및 토큰을 캡처합니다.

- 앱 공급 업체에 SAML 응답을 보내고 누락된 항목을 요청합니다.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 응답에 특성이 없습니다.

Azure AD 응답으로 보낼 Azure AD 구성의 특성을 추가하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인합니다.

2. 왼쪽의 탐색 창 상단에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **모든 애플리케이션** 을 선택하여 앱 목록을 표시합니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션 목록** 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 앱이 로드된 후 왼쪽의 탐색 창에서 **Single Sign-On** 을 선택합니다.

8. **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집** 을 선택합니다. 여기에서 사용자가 로그인 할 때 SAML 토큰에서 앱에 보낼 특성을 변경할 수 있습니다.

   특성을 추가하려면:

   1. **특성 추가** 를 선택합니다. **이름을** 입력하고 드롭다운 목록에서 **값** 을 선택합니다.

   1. **저장** 을 선택합니다. 테이블에 새 특성이 표시됩니다.

9. 구성을 저장합니다.

   다음 번에 사용자가 앱에 로그인하면, Azure AD는 SAML 응답으로 새 특성을 보냅니다.

## <a name="the-app-doesnt-identify-the-user"></a>앱에서 사용자를 식별하지 않습니다

SAML 응답에 역할 같은 특성이 없으므로 앱에 로그인하지 못합니다. 또는 앱에 **NameID** (사용자 식별자) 특성에 대해 다른 형식 또는 값이 필요하기 때문에 실패합니다.

앱에서 사용자를 만들어 유지 관리하고 제거하기 위해 [Azure AD 자동 사용자 프로비전](../app-provisioning/user-provisioning.md)을 사용하는 경우 사용자가 SaaS 앱에 프로비전 되었는지 확인합니다. 자세한 내용은 [Azure AD Gallery 애플리케이션에 프로비저닝되는 사용자가 없음](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)을 참조하십시오.

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD 애플리케이션 구성에 특성을 추가합니다.

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인합니다.

2. 왼쪽의 탐색 창 상단에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **모든 애플리케이션** 을 선택하여 앱 목록을 표시합니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션 목록** 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.

6. SSO에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 왼쪽의 탐색 창에서 **Single Sign-On** 을 선택합니다.

8. **사용자 특성** 의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

## <a name="change-the-nameid-format"></a>NameID 형식 변경

애플리케이션에서 **NameID** (사용자 식별자) 특성에 다른 형식을 사용해야 하는 경우 [NameID를 편집](../develop/active-directory-saml-claims-customization.md#editing-nameid)하여 NameID 형식을 변경하는 것을 참조하세요.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 앱이 요청한 형식을 기반으로 **NameID** 특성(사용자 ID)의 형식을 선택합니다. 자세한 내용은 [Single sign-on SAML 프로토콜](../develop/single-sign-on-saml-protocol.md#nameidpolicy)의 "NameIDPolicy" 섹션을 참조하세요.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>앱은 SAML 응답에 대해 다른 시그니처 메서드를 필요로 합니다.

Azure AD에서 디지털 서명하는 SAML 토큰의 일부를 변경하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인합니다.

2. 왼쪽의 탐색 창 상단에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **모든 애플리케이션** 을 선택하여 앱 목록을 표시합니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션 목록** 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 앱이 로드된 후 왼쪽의 탐색 창에서 **Single sign-on** 을 선택합니다.

8. **SAML 서명 인증서** 섹션에서 **고급 인증서 서명 설정 표시** 를 클릭합니다.

9. 다음 옵션 중에서 앱에 필요한 **서명 옵션** 을 선택합니다.

   - **SAML 응답 서명**
   - **SAML 응답 및 어설션 서명**
   - **SAML 어설션 서명**

   다음 번에 사용자가 앱에 로그인하면 Azure AD는 선택한 SAML 응답의 일부를 서명합니다.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>앱에 SHA-1 서명 알고리즘이 필요합니다.

기본적으로 Azure AD는 대부분의 보안 알고리즘을 사용하여 SAML 토큰을 서명합니다. 앱에 SHA-1이 필요한 경우를 제외하고 서명 알고리즘을 *SHA-1* 로 변경하지 않는 것이 좋습니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인합니다.

2. 왼쪽의 탐색 창 상단에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션 목록** 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.

6. Single Sign-On을 구성하려는 앱을 선택합니다.

7. 앱이 로드된 후 앱 왼쪽의 탐색 창에서 **Single Sign-On** 을 선택합니다.

8. **SAML 서명 인증서** 에서 **고급 인증서 서명 설정 표시** 를 클릭합니다.

9. **서명 알고리즘** 으로 **SHA-1** 을 선택합니다.

   다음에 사용자가 앱에 로그인 할 때 Azure AD는 SHA-1 알고리즘을 사용하여 SAML 토큰에 서명합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](./debug-saml-sso-issues.md)
