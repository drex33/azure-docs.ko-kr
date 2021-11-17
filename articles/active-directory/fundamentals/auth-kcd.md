---
title: Azure Active Directory를 사용하는 Kerberos 제한 위임
description: Azure Active Directory를 사용하여 Kerberos 제한 위임을 구현하는 아키텍처 지침입니다.
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
ms.openlocfilehash: 2c12ab8421b578a0c1bf60f5d901d5d9223fc685
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231276"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 인증 - Azure Active Directory를 사용하는 Kerberos 제한 위임

KCD(Kerberos 제한 위임)는 리소스 간의 제한된 위임을 제공하며 서비스 사용자 이름을 기반으로 합니다. 도메인 관리자가 위임을 만들어야 하며 단일 도메인으로 제한됩니다. 리소스 기반 KCD는 Active Directory 포리스트 내에서 여러 도메인의 사용자가 있는 웹 애플리케이션에 대한 Kerberos 인증을 제공하는 방법으로 사용되는 경우가 많습니다.

Azure Active Directory 애플리케이션 프록시는 액세스 및 KCD(Kerberos 제한 위임)를 위해 Kerberos 티켓이 필요한 KCD 기반 애플리케이션에 대한 SSO(Single Sign-On) 및 원격 액세스를 지원할 수 있습니다.

IWA(Windows 통합 인증)를 사용하는 온-프레미스 KCD 애플리케이션에 SSO를 사용하도록 설정합니다. 애플리케이션 프록시 커넥터에는 Active Directory에서 사용자를 가장할 수 있는 권한이 부여됩니다. 애플리케이션 프록시 커넥터는 이 권한을 사용하여 사용자를 대신해 토큰을 보내고 받습니다.

## <a name="use-when"></a>적용 가능한 상황

원격 액세스를 제공하고, 사전 인증을 사용하여 보호하고, 온-프레미스 IWA 애플리케이션에 SSO를 제공해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 애플리케이션 프록시에서 제공하는 레거시 애플리케이션에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **Azure AD**: 사용자를 인증합니다. 

* **애플리케이션 프록시 서비스**: 사용자에게서 온-프레미스 애플리케이션으로 요청을 보내는 역방향 프록시로 작동합니다. Azure AD에서 가능합니다. 애플리케이션 프록시는 모든 조건부 액세스 정책을 적용할 수도 있습니다.

* **애플리케이션 프록시 커넥터**: 애플리케이션에 대한 연결을 제공하기 위해 Windows 서버의 온-프레미스에 설치됩니다. Azure AD에 대한 응답을 반환합니다. Active Directory와 함께 KCD 협상을 수행하고, 사용자를 가장하여 애플리케이션에 대한 Kerberos 토큰을 가져옵니다.

* **Active Directory**: 애플리케이션에 대한 Kerberos 토큰을 애플리케이션 프록시 커넥터로 보냅니다.

* **레거시 애플리케이션**: 애플리케이션 프록시에서 사용자 요청을 수신하는 애플리케이션입니다. 레거시 애플리케이션은 애플리케이션 프록시 커넥터에 대한 응답을 반환합니다.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Azure AD를 사용하여 Windows 인증 (KCD) 구현

* [애플리케이션 프록시를 사용하여 앱에 Single Sign-On에 대한 Kerberos 제한된 위임](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)