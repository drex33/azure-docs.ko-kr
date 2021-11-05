---
title: Azure Active Directory를 사용한 헤더 기반 인증
description: Azure Active Directory를 사용한 헤더 기반 인증에 대한 아키텍처 지침.
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
ms.openlocfilehash: 99cf28c88b3c94fad5e9abe7eaee1e11bcfacbe6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052373"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용한 헤더 기반 인증

레거시 애플리케이션은 일반적으로 헤더 기반 인증을 사용합니다. 이 시나리오에서는 사용자(또는 메시지 보낸 사람)가 중간 ID 솔루션을 인증합니다. 중간 솔루션은 사용자를 인증하고 필요한 HTTP(Hypertext Transfer Protocol) 헤더를 대상 웹 서비스로 전파합니다. Azure AD(Active Directory)는 애플리케이션 프록시 서비스를 통해 이 패턴을 지원하고 다른 네트워크 컨트롤러 솔루션과의 통합을 지원합니다.

이 솔루션에서 애플리케이션 프록시는 애플리케이션에 대한 원격 액세스를 제공하고, 사용자를 인증하고, 애플리케이션에 필요한 헤더를 전달합니다. 

## <a name="use-when"></a>적용 가능한 상황

원격 사용자는 헤더 기반 인증이 필요한 온-프레미스 애플리케이션에 안전하게 SSO(Single Sign-On)해야 합니다.

![아키텍처 이미지 헤더 기반 인증](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 애플리케이션 프록시에서 제공하는 레거시 애플리케이션에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **Azure AD**: 사용자를 인증합니다. 

* **애플리케이션 프록시 서비스**: 사용자에게서 온-프레미스 애플리케이션으로 요청을 보내는 역방향 프록시로 작동합니다. Azure AD에 상주하며 모든 조건부 액세스 정책을 적용할 수도 있습니다.

* **애플리케이션 프록시 커넥터**: 애플리케이션에 대한 연결을 제공하기 위해 Windows 서버의 온-프레미스에 설치됩니다. 아웃바운드 연결만 사용하고 Azure AD에 대한 응답을 반환합니다.

* **레거시 애플리케이션**: 애플리케이션 프록시에서 사용자 요청을 수신하는 애플리케이션입니다. 레거시 애플리케이션은 세션을 설정하고 응답을 반환하는 데 필요한 HTTP 헤더를 수신합니다. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Azure AD를 사용한 헤더 기반 인증 구현

* [Azure AD에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)  

* [애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md) 

* [앱 제공 컨트롤러 및 네트워크를 사용하여 레거시 앱 보호](../manage-apps/secure-hybrid-access.md)
