---
title: Azure Active Directory를 사용한 SAML 인증
description: Azure Active Directory를 사용하여 SAML 인증을 얻기 위한 아키텍처 참고 자료
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1173a6dbb47bfad61c0987fcb409731bc9f28c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451695"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용한 SAML 인증

SAML(Security Assertion Markup Language)은 ID 공급자와 서비스 공급자 간에 인증 및 권한 부여 데이터를 교환하기 위한 개방형 표준입니다. SAML은 서비스 공급자가 액세스 제어 결정을 내리는 데 사용하는 문인 보안 어설션에 대한 XML 기반 생성 언어입니다. 

SAML 사양은 다음 세 가지 역할을 정의합니다.

* 보안 주체, 일반적으로 사용자
* IdP(ID 공급자)
* SP(서비스 공급자)


## <a name="use-when"></a>적용 가능한 상황

엔터프라이즈 SAML 애플리케이션에 SSO(Single Sign-On) 환경을 제공해야 합니다.

SAML 주소가 가장 중요한 사용 사례 중 하나는 SSO이지만, 특히 보안 도메인 간에 SSO를 확장하면 기타 사용 사례(프로필이라고 함)도 있습니다. 

![SAML에 대한 아키텍처 다이어그램](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 애플리케이션에서 서비스를 요청합니다.

* **웹 브라우저**: 사용자가 상호 작용하는 구성 요소입니다.

* **웹앱**: SAML을 지원하고 Azure AD를 IdP로 사용하는 엔터프라이즈 애플리케이션입니다.

* **토큰**: IdP에서 보안 주체(사용자)에 대해 만든 클레임 집합을 전달하는 SAML 어설션(SAML 토큰이라고도 함)입니다. 여기에는 인증 정보, 특성 및 권한 부여 의사 결정문이 포함됩니다.

* **Azure AD**: SAML 앱에 대한 SSO 및 다단계 인증을 제공하는 엔터프라이즈 클라우드 IdP입니다. 신뢰 애플리케이션에 인증 서비스를 제공하면서 사용자의 ID 정보를 동기화, 유지 관리 및 관리합니다. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Azure AD를 사용하여 SAML 인증 구현

* [SaaS 애플리케이션과 Azure Active Directory를 통합하는 방법에 대한 자습서](../saas-apps/tutorial-list.md) 

* [비 갤러리 애플리케이션에 대한 SAML 기반 Single Sign-On 구성](../manage-apps/add-application-portal.md) 

* [Azure AD에서 SAML 프로토콜을 사용하는 방법](../develop/active-directory-saml-protocol-reference.md)
