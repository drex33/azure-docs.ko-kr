---
title: Azure AD 앱을 사용자 집합으로 제한 | Azure
titleSuffix: Microsoft identity platform
description: Azure AD에 등록된 앱에 대한 액세스를 선택한 사용자 집합으로 제한하는 방법을 알아봅니다.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/18/2021
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5e5c06174201fd1ef426a3b9fde3d4a54971eb19
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058168"
---
# <a name="restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Azure AD 테넌트에서 Azure AD 앱을 사용자 세트로 제한

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다.

마찬가지로, [다중 테넌트](howto-convert-app-to-be-multi-tenant.md) 앱의 경우 이 애플리케이션이 프로비전된 Azure AD 테넌트의 모든 사용자는 각 테넌트에서 성공적으로 인증되면 이 애플리케이션에 액세스할 수 있게 됩니다.

테넌트 관리자 및 개발자에게는 종종 애플리케이션을 특정 사용자 집합으로 제한해야 하는 요구 사항이 있습니다. 애플리케이션을 특정 사용자 또는 보안 그룹 집합으로 제한하는 방법에는 두 가지가 있습니다.

- 개발자는 [Azure RBAC(Azure 역할 기반 액세스 제어)](howto-implement-rbac-for-apps.md)와 같은 인기 있는 권한 부여 패턴을 사용할 수 있습니다.
- 테넌트 관리자 및 개발자는 Azure AD의 기본 제공 기능을 사용할 수 있습니다.

## <a name="supported-app-configurations"></a>지원되는 앱 구성

앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한하는 옵션은 다음과 같은 애플리케이션 형식으로 작동합니다.

- SAML 기반 인증을 사용하여 페더레이션된 Single Sign-On에 대해 구성된 애플리케이션
- Azure AD 사전 인증을 사용하는 애플리케이션 프록시 애플리케이션
- 애플리케이션은 사용자 또는 관리자가 해당 애플리케이션에 동의한 후에 OAuth 2.0/OpenID Connect 인증을 사용하는 Azure AD 애플리케이션 플랫폼에서 직접 빌드됩니다.

## <a name="update-the-app-to-require-user-assignment"></a>사용자 할당을 요구하도록 앱 업데이트

사용자 할당을 요구하도록 애플리케이션을 업데이트하려면 Enterprise 앱에서 애플리케이션의 소유자이거나 **전역 관리자**, **애플리케이션 관리자** 또는 **클라우드 애플리케이션 관리자** 디렉터리 역할 중 하나를 할당받아야 합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.
1. 할당을 요구하도록 구성하려는 애플리케이션을 선택합니다. 창 상단에 있는 필터를 사용하여 특정 애플리케이션을 검색합니다.
1. 애플리케이션 **개요** 페이지의 **관리** 에서 **속성** 을 선택합니다.
1. **사용자 할당이 필요합니까?** 설정을 찾아 **예** 로 설정합니다. 이 옵션을 **예** 로 설정하면 애플리케이션 또는 서비스에 액세스하려는 사용자 및 서비스를 먼저 이 애플리케이션에 할당해야 합니다. 그렇지 않으면 로그인하거나 액세스 토큰을 가져올 수 없습니다.
1. **저장** 을 선택합니다.

애플리케이션에 할당이 필요한 경우 해당 애플리케이션에 대한 사용자 동의가 허용되지 않습니다. 이는 해당 앱에 대한 사용자 동의가 다른 방식으로 허용된 경우에도 마찬가지입니다. 할당이 필요한 앱에 대해 [테넌트 전체 관리자 동의를 부여](../manage-apps/grant-admin-consent.md)해야 합니다.

## <a name="assign-the-app-to-users-and-groups"></a>앱에 사용자 및 그룹 할당

사용자 할당을 사용하도록 앱을 구성하고 나면 앱에 사용자 및 그룹을 할당할 수 있습니다.

1. **관리** 에서 **사용자 및 그룹** > **사용자/그룹 추가** 를 선택합니다.
1. **사용자** 선택기를 선택합니다.

   특정 사용자 및 그룹을 검색하고 찾는 텍스트 상자와 함께 사용자 및 보안 그룹의 목록이 표시됩니다. 이 화면에서는 여러 사용자 및 그룹을 한 번에 선택할 수 있습니다.

1. 사용자 및 그룹을 선택한 후 **선택** 을 선택합니다.
1. (선택 사항) 애플리케이션에서 앱 역할을 정의한 경우 **역할 선택** 옵션을 사용하여 선택한 사용자 및 그룹에 앱 역할을 할당할 수 있습니다.
1. **할당** 을 선택하여 사용자 및 그룹에 대한 앱 할당을 완료합니다.
1. 추가한 사용자 및 그룹이 업데이트된 **사용자 및 그룹** 목록에 보이는지 확인합니다.

## <a name="more-information"></a>자세한 정보

역할 및 보안 그룹에 대한 자세한 내용은 다음을 참조하세요.

- [방법: 애플리케이션에 앱 역할 추가](./howto-add-app-roles-in-azure-ad-apps.md)
- [앱에서 보안 그룹 및 애플리케이션 역할 사용(비디오)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory 앱 매니페스트](./reference-app-manifest.md)
