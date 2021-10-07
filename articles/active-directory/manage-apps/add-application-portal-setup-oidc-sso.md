---
title: OpenID Connect 기반 Single Sign-On 애플리케이션 추가
description: Azure Active Directory에서 OpenID Connect 기반 Single Sign-On 애플리케이션을 추가하는 방법을 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 24d30dc81b2b2a3da2ac5f172dabe77cf5b01f9c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058247"
---
# <a name="add-an-openid-connect-based-single-sign-on-application-in-azure-active-directory"></a>Azure Active Directory에서 OpenID Connect 기반 Single Sign-On 애플리케이션 추가

[OIDC(OpenID Connect)](../develop/active-directory-v2-protocols.md) 기반 SSO(Single Sign-On)를 지원하는 애플리케이션을 Azure AD(Azure Active Directory) 테넌트에 추가합니다.

이 페이지의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

OIDC 기반 SSO를 구성하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.

## <a name="add-the-application"></a>애플리케이션 추가

OIDC 표준을 SSO에 사용하는 엔터프라이즈 애플리케이션을 추가하는 경우 설정 단추를 선택합니다. 단추를 선택하면 애플리케이션에 대한 가입 프로세스가 완료됩니다.

애플리케이션에 대한 OIDC 기반 SSO를 구성하려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다. 
1. **엔터프라이즈 애플리케이션** 창에서 **새 애플리케이션** 을 선택합니다.
1. **Azure AD 갤러리 찾아보기** 창이 열리고 클라우드 플랫폼, 온-프레미스 애플리케이션 및 추천 애플리케이션에 대한 타일이 표시됩니다. **추천 애플리케이션** 섹션에 나열된 애플리케이션에는 페더레이션된 SSO 및 프로비전을 지원하는지 여부를 나타내는 아이콘이 있습니다. 애플리케이션을 검색하여 선택합니다. 이 예에서는 **SmartSheet** 가 사용됩니다.
1. **가입** 을 선택합니다. Azure Active Directory의 사용자 계정 자격 증명으로 로그인합니다. 애플리케이션에 대한 구독이 이미 있는 경우 사용자 세부 정보 및 테넌트 정보의 유효성이 검사됩니다. 애플리케이션에서 사용자를 확인할 수 없는 경우 애플리케이션 서비스에 가입하도록 리디렉션됩니다.

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="애플리케이션에 대한 동의 화면 완료":::

1. **조직 대신 동의** 를 선택하고 **수락** 을 선택합니다. 애플리케이션이 테넌트에 추가되고 애플리케이션 홈 페이지가 표시됩니다. 사용자 및 관리자 동의에 대한 자세한 내용은 [사용자 및 관리자 동의 이해](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Single Sign-On 배포 계획에 대해 자세히 알아봅니다.
> [!div class="nextstepaction"]
> [Single Sign-On 배포 계획](plan-sso-deployment.md)
