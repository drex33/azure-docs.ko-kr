---
title: Single Sign-On이란?
description: Azure Active Directory의 엔터프라이즈 애플리케이션에 대한 Single Sign-On에 대해 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q1
ms.openlocfilehash: c3b2cc9f3fa6656cf4d5e493f725c17b64f4d7d8
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233581"
---
# <a name="what-is-single-sign-on-in-azure-active-directory"></a>Azure Active Directory의 Single Sign-On이란?

이 문서에서는 사용자에게 제공되는 SSO(Single Sign-On) 옵션에 대한 정보를 제공하고, Azure AD(Azure Active Directory)를 사용할 때 Single Sign-On 배포를 계획하는 방법을 소개합니다. Single Sign-On은 사용자가 하나의 자격 증명 세트를 사용하여 여러 독립 소프트웨어 시스템에 로그인 할 수 있는 인증 방법입니다. SSO를 사용하면 사용자는 사용하는 모든 애플리케이션에 로그인할 필요가 없습니다. SSO를 사용하면 사용자는 다른 자격 증명으로 인증할 필요 없이 원하는 모든 애플리케이션에 액세스할 수 있습니다. 

SSO를 사용할 수 있는 많은 애플리케이션이 이미 Azure AD에 있습니다. 애플리케이션의 요구 사항과 구현 방법에 따라 여러 가지 SSO 옵션이 있습니다. Azure AD에서 애플리케이션을 만들기 전에 시간을 갖고 SSO 배포 계획을 수립하세요. [내 앱] 포털을 사용하면 애플리케이션을 더 쉽게 관리할 수 있습니다.

## <a name="single-sign-on-options"></a>Single Sign-On 옵션

애플리케이션의 인증이 구성된 방식에 따라 선택해야 하는 SSO가 달라집니다. 클라우드 애플리케이션은 OpenID Connect, OAuth, SAML 등의 페더레이션 기반 옵션을 사용할 수 있습니다. 이 애플리케이션은 암호 기반 SSO, 연결 기반 SSO를 사용할 수도 있고 SSO를 사용하지 않을 수도 있습니다.

- **페더레이션** - 여러 ID 공급자 간에 작업하도록 SSO를 설정하는 경우 이를 페더레이션이라고 합니다. 페더레이션된 프로토콜을 기반으로 SSO를 구현하면 보안, 안정성, 최종 사용자 환경 및 구현이 개선됩니다. 

    페더레이션 Single Sign-On을 사용하면 Azure AD는 사용자의 Azure AD 계정을 사용하여 사용자를 애플리케이션에 인증합니다. 이 방법은 [SAML 2.0](../develop/single-sign-on-saml-protocol.md), WS-Federation 또는 [OpenID Connect](../develop/active-directory-v2-protocols.md) 애플리케이션을 지원합니다. 페더레이션된 SSO는 가장 풍부한 SSO 모드입니다. 애플리케이션에서 지원하는 경우 암호 기반 SSO 및 AD FS(Active Directory Federation Services) 대신 페더레이션된 SSO를 Azure AD와 함께 사용하세요.

    엔터프라이즈 애플리케이션을 위한 SSO 옵션이 없는 몇 가지 시나리오가 있습니다. 포털에서 **앱 등록** 을 사용하여 애플리케이션을 등록하면 기본적으로 OpenID Connect 및 OAuth를 사용하도록 Single Sign-On 기능이 구성됩니다. 이 경우 엔터프라이즈 애플리케이션에서 Single Sign-On 옵션을 탐색해도 표시되지 않습니다.

    애플리케이션이 다른 테넌트에 호스트되는 경우 Single Sign-On을 사용할 수 없습니다. 계정에 필요한 권한(전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자)이 없는 경우 Single Sign-On을 사용할 수 없습니다. 권한 때문에 Single Sign-On을 열 수는 있지만 저장할 수 없는 경우가 발생할 수도 있습니다.

    > [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]

- **암호** - 온-프레미스 애플리케이션은 암호 기반, Windows 인증, 헤더 기반, 연결 기반 방법을 SSO에 사용할 수 있습니다. 온-프레미스 선택은 [애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)에 대해 애플리케이션을 구성할 때 작동합니다.

    암호 기반 SSO를 사용하는 경우 최종 사용자는 애플리케이션에 처음 액세스할 때 사용자 이름 및 암호를 사용하여 애플리케이션에 로그인합니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 암호 기반 SSO를 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 스토리지하고 재생할 수 있습니다. 이 옵션은 애플리케이션에서 제공하는 기존 로그인 프로세스를 사용하지만, 관리자가 암호를 관리할 수 있으므로 사용자는 암호를 몰라도 됩니다.

- **연결 기반** - 연결된 로그온은 일정 기간 동안 애플리케이션을 마이그레이션하면서 일관된 사용자 경험을 제공할 수 있습니다. 애플리케이션을 Azure AD로 마이그레이션하는 경우 연결 기반 SSO를 사용하면 마이그레이션하려는 모든 애플리케이션에 대한 링크를 신속하게 게시할 수 있습니다. 사용자는 [내 앱] 포털 또는 Microsoft 365 포털에서 모든 링크를 찾을 수 있습니다.

    사용자가 연결된 애플리케이션을 사용하여 인증한 후에는 계정을 만들어야만 최종 사용자에게 Single Sign-On 액세스가 제공됩니다. 이 계정은 자동으로 프로비저닝될 수도 있고, 관리자가 수동으로 프로비저닝할 수도 있습니다. 연결된 애플리케이션은 Azure AD를 통해 Single Sign-On 기능을 제공하지 않으므로 연결된 애플리케이션에 조건부 액세스 정책이나 다단계 인증을 적용할 수 없습니다. 연결된 애플리케이션을 구성하는 경우 애플리케이션을 시작하기 위해 표시되는 링크만 추가하면 됩니다.

- **사용 안 함** - SSO를 사용하지 않도록 설정하면 애플리케이션에서 사용할 수 없습니다. Single Sign-On이 사용하지 않도록 설정되면 사용자는 두 번 인증해야 할 수도 있습니다. 먼저 사용자는 Azure AD에 인증한 다음, 애플리케이션에 로그인합니다.

    다음과 같은 경우 SSO를 사용하지 않습니다.

    - 애플리케이션을 Azure AD Single Sign-On과 통합할 준비가 되지 않은 경우
    - 애플리케이션의 다른 측면을 테스트 중인 경우
    - 온-프레미스 애플리케이션에서 사용자 인증을 요구하지 않지만 관리자는 요구하고 싶은 경우. SSO를 사용하지 않으면 사용자는 인증해야 합니다.

    SP 시작 SAML 기반 SSO에 맞게 애플리케이션을 구성하고 SSO 모드를 [사용 안 함]으로 변경하면 사용자가 [내 앱] 포털 외부의 애플리케이션에 로그인하는 것을 막을 수 없습니다. 이를 위해 [사용자의 로그인 기능을 비활성화](disable-user-sign-in-portal.md)해야 합니다.

## <a name="plan-sso-deployment"></a>SSO 배포 계획

웹 애플리케이션은 다양한 회사에서 호스팅되며 서비스로 제공됩니다. 몇 가지 인기 있는 웹 애플리케이션으로 Microsoft 365, GitHub 및 Salesforce가 있으며, 그 외에도 수천 개가 있습니다. 사람들은 자신의 컴퓨터에서 웹 브라우저를 사용하여 웹 애플리케이션에 액세스합니다. Single Sign-On을 사용하면 사람들이 여러 번 로그인하지 않고도 다양한 웹 애플리케이션 간에 이동할 수 있습니다.

SSO를 구현하는 방법은 애플리케이션이 호스트되는 위치에 따라 달라집니다. 애플리케이션에 액세스하기 위해 네트워크 트래픽이 라우팅되는 방식 때문에 호스팅이 중요합니다. 사용자는 인터넷을 사용하여 온-프레미스 애플리케이션(로컬 네트워크에 호스트되는)에 액세스할 필요가 없습니다. 애플리케이션이 클라우드에 호스트되는 경우 사용자가 이 애플리케이션을 사용하려면 인터넷 연결이 필요합니다. 클라우드 호스팅 애플리케이션을 SaaS(Software as a Service) 애플리케이션이라고도 합니다.

클라우드 애플리케이션에는 페더레이션 프로토콜이 사용됩니다. 온-프레미스 애플리케이션에 Single Sign-On을 사용할 수도 있습니다. 애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션에 대한 액세스를 구성할 수 있습니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격으로 액세스](../app-proxy/application-proxy.md)를 참조하세요.

## <a name="my-apps"></a>내 앱

애플리케이션 사용자는 아마도 SSO 세부 정보에 대해 크게 신경 쓰지 않을 것입니다. 암호를 자주 입력할 필요 없이 생산성을 높일 수 있는 애플리케이션을 원할 뿐입니다. [내 앱 포털](https://myapps.microsoft.com)에서 애플리케이션을 찾고 관리할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에서 Single Sign-On 배포 계획](plan-sso-deployment.md)
