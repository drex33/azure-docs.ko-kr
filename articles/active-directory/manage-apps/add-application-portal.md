---
title: '빠른 시작: 엔터프라이즈 애플리케이션 추가'
description: Azure Active Directory에서 엔터프라이즈 애플리케이션을 추가합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: f3d9cd36b821b1dca5311c266b84e259eab005c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546959"
---
# <a name="quickstart-add-an-enterprise-application"></a>빠른 시작: 엔터프라이즈 애플리케이션 추가

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 엔터프라이즈 애플리케이션을 Azure AD(Azure Active Directory) 테넌트에 추가합니다. Azure AD에는 수천 개의 사전 통합된 엔터프라이즈 애플리케이션이 포함된 갤러리가 있습니다. 조직에서 사용하는 대부분의 애플리케이션은 이미 갤러리에 있을 수 있습니다. 이 빠른 시작에서는 **Azure AD SAML Toolkit** 이라는 애플리케이션을 예로 사용하지만, 개념은 [갤러리에 있는 대부분의 엔터프라이즈 애플리케이션](../saas-apps/tutorial-list.md)에 적용됩니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

엔터프라이즈 애플리케이션을 Azure AD 테넌트에 추가하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.

## <a name="add-an-enterprise-application"></a>엔터프라이즈 애플리케이션 추가

엔터프라이즈 애플리케이션을 테넌트에 추가하려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다.
1. **엔터프라이즈 애플리케이션** 창에서 **새 애플리케이션** 을 선택합니다.
1. **Azure AD 갤러리 찾아보기** 창이 열리고 클라우드 플랫폼, 온-프레미스 애플리케이션 및 추천 애플리케이션에 대한 타일이 표시됩니다. **추천 애플리케이션** 섹션에 나열된 애플리케이션에는 페더레이션된 SSO(Single Sign-On) 및 프로비저닝을 지원하는지 여부를 나타내는 아이콘이 있습니다. 애플리케이션을 검색하여 선택합니다. 이 빠른 시작에서는 **Azure AD SAML Toolkit** 가 사용됩니다.

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="엔터프라이즈 애플리케이션 갤러리에서 추가하려는 애플리케이션 찾아보기":::

1. 애플리케이션의 인스턴스를 인식하는 데 사용하려는 이름을 입력합니다. `Azure AD SAML Toolkit 1`)을 입력합니다.
1. **만들기** 를 선택합니다.

OpenID Connect 기반 SSO를 사용하는 애플리케이션을 설치하도록 선택하면 **만들기** 단추 대신 이미 계정이 있는지 여부에 따라 애플리케이션 로그인 또는 가입 페이지로 리디렉션하는 단추가 표시됩니다. 자세한 내용은 [OpenID Connect 기반 Single Sign-On 애플리케이션 추가](add-application-portal-setup-oidc-sso.md)를 참조하세요. 로그인하면 애플리케이션이 테넌트에 추가됩니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 완료하려는 경우 여기서 만든 엔터프라이즈 애플리케이션을 유지합니다. 그렇지 않으면 해당 애플리케이션을 삭제하여 테넌트를 정리하는 것이 좋습니다. 자세한 내용은 [애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자 계정을 만들고 추가한 엔터프라이즈 애플리케이션에 할당하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용자 계정 만들기 및 할당](add-application-portal-assign-users.md)
