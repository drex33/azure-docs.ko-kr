---
title: Azure Active Directory 앱에 대한 암호 기반 SSO(Single Sign-On) 이해
description: Azure Active Directory 앱에 대한 암호 기반 SSO(Single Sign-On) 이해
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 40c2e4ae14e2b50a26ed3ff25846d15b129e2916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566890"
---
# <a name="understand-password-based-single-sign-on"></a>암호 기반 Single Sign-On 이해

애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)에서 애플리케이션용 IdP(ID 공급자)로 Azure AD를 사용하는 방법을 알아보았습니다. 빠른 시작 가이드에서는 SAML 기반 SSO 또는 OIDC 기반 SSO를 구성합니다. 또 다른 옵션은 암호 기반 SSO입니다. 이 문서에서는 암호 기반 SSO 옵션에 대해 자세히 설명합니다.

이 옵션은 HTML 로그인 페이지가 있는 모든 웹 사이트에 사용할 수 있습니다. 암호 기반 SSO는 암호 보관이라고도 합니다. 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 경우 유용합니다.

암호 기반 SSO는 애플리케이션을 Azure AD로 신속하게 통합하기 위한 좋은 방법이며 다음을 가능하게 합니다.

- 사용자 이름 및 암호를 안전하게 저장하고 재생하여 사용자의 Single Sign-On 사용

- 로그인하는 데 사용자 이름 및 암호 필드 이상을 필요로 하는 애플리케이션에 대해 여러 로그인 필드를 필요로 하는 애플리케이션 지원

- 사용자가 자격 증명을 입력할 때 [내 앱](../user-help/my-apps-portal-end-user-access.md)에서 사용자에게 표시되는 사용자 이름 및 암호 필드의 레이블 사용자 지정

- 사용자가 수동으로 입력하는 모든 기존 애플리케이션 계정에 대한 자신의 사용자 이름 및 암호를 제공하도록 허용

- 비즈니스 그룹의 멤버가 [셀프 서비스 애플리케이션 액세스](./manage-self-service-access.md) 기능을 사용하여 사용자에게 할당된 사용자 이름 및 암호를 지정하도록 허용

- 관리자가 자격 증명 업데이트 기능을 사용하여 애플리케이션에 로그인할 때 개인 또는 그룹에서 사용할 사용자 이름 및 암호를 지정할 수 있도록 허용

## <a name="before-you-begin"></a>시작하기 전에

IdP(ID 공급자)로 Azure AD를 사용하고 SSO(Single Sign-On)를 구성하는 것은 사용 중인 애플리케이션에 따라 간단할 수도 있고 복잡할 수도 있습니다. 몇 가지 작업만 거치면 구성할 수 있는 애플리케이션이 있는 반면 면밀하게 구성해야 하는 애플리케이션이 있습니다. 이 정보를 간단히 확인하려면 애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)를 살펴보세요. 간단히 구성할 수 있는 애플리케이션을 추가하려면 이 문서를 읽지 않아도 됩니다. 추가하려는 애플리케이션에 사용자 지정 구성이 필요하고 암호 기반 SSO를 사용해야 하는 경우 이 문서를 참조하면 됩니다.

> [!IMPORTANT]
> **엔터프라이즈 애플리케이션** 에서 애플리케이션을 탐색할 때 **Single Sign-On** 옵션이 표시되지 않는 일부 경우가 있습니다.
>
> **앱 등록** 을 사용하여 애플리케이션을 등록하면 Single Sign-On 기능은 OIDC OAuth를 기본값으로 사용하도록 구성됩니다. 이 경우 **엔터프라이즈 애플리케이션** 에서 **Single Sign-On** 옵션이 탐색에 표시되지 않습니다. **앱 등록** 을 사용하여 사용자 지정 앱을 추가하면 매니페스트 파일의 옵션을 구성하게 됩니다. 매니페스트 파일에 대한 자세한 내용은 [Azure Active Directory 앱 매니페스트](../develop/reference-app-manifest.md)를 참조하세요. SSO 표준에 대한 자세한 내용은 [Microsoft ID 플랫폼을 사용한 인증 및 권한 부여](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)를 참조하세요.
>
> 애플리케이션이 다른 테넌트에 호스트되거나 사용자 계정에 필요한 권한(전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자)이 없는 경우에는 탐색에서 **Single Sign-On** 이 표시되지 않을 수 있습니다. 사용 권한으로 **Single Sign-On** 을 열 수는 있지만 저장할 수 없는 경우가 발생할 수도 있습니다. Azure AD 관리자 역할에 대한 자세한 내용은 (https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 를 참조하세요.

## <a name="basic-configuration"></a>기본 구성

[빠른 시작 시리즈](view-applications-portal.md)에서는 Azure AD가 앱에 대한 IdP(ID 공급자)로 사용되고 있음을 알 수 있도록 테넌트에 앱을 추가하는 방법을 알아보았습니다. 일부 앱은 이미 사전에 구성되어 있으며 Azure AD 갤러리에 표시됩니다. 다른 앱은 갤러리에 없으며, 일반적인 앱을 만들어 수동으로 구성해야 합니다. 앱에 따라 암호 기반 SSO 옵션을 사용하지 못할 수도 있습니다. 앱에 대한 Single Sign-On 페이지에 암호 기반 옵션 목록이 표시되지 않는 경우 사용할 수 없습니다.

> [!IMPORTANT]
> 암호 기반 SSO에는 내 앱 브라우저 확장이 필요합니다. 자세히 알아보려면 [내 앱 배포 계획](my-apps-deployment-plan.md)을 참조하세요.

암호 기반 SSO의 구성 페이지는 단순합니다. 앱에서 사용하는 로그온 페이지의 URL만 포함합니다. 이 문자열은 사용자 이름 입력 필드를 포함하는 페이지여야 합니다.

URL을 입력한 후 **저장** 을 선택합니다. Azure AD는 사용자 이름 및 암호 입력 필드에 대한 로그인 페이지의 HTML을 구문 분석합니다. 시도가 성공하면 완료된 것입니다.
다음 단계는 [애플리케이션에 사용자 또는 그룹을 할당](./assign-user-or-group-access-portal.md)하는 것입니다. 사용자 및 그룹을 할당한 후 사용자가 애플리케이션에 로그인할 때 사용할 자격 증명을 제공할 수 있습니다. **사용자 및 그룹** 을 선택하고 사용자 또는 그룹의 행에 대한 확인란을 선택한 다음 **자격 증명 업데이트** 를 선택합니다. 마지막으로 사용자 또는 그룹에 사용할 사용자 이름 및 암호를 입력합니다. 그러지 않으면 시작할 때 자격 증명을 입력하라는 메시지가 사용자에게 표시됩니다.

## <a name="manual-configuration"></a>수동 구성

Azure AD의 구문 분석이 실패하는 경우 로그온을 수동으로 구성할 수 있습니다.

1. **\<application name>구성** 에서 **\<application name> 암호 Single Sign-On 설정 구성** 을 선택하면 **로그온 구성** 페이지가 표시됩니다.

2. **로그인 필드 수동 검색** 을 선택합니다. 로그인 필드의 수동 검색에 대해 설명하는 추가적인 지침이 표시됩니다.

   ![암호 기반 Single Sign-On 수동 구성](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **로그인 필드 캡처** 를 선택합니다. 새 탭에서 캡처 상태 페이지가 열리고 **메타데이터 캡처가 현재 진행 중임** 메시지가 표시됩니다.

4. **내 앱 확장 필요** 상자가 새 탭에 표시되면 **지금 설치** 를 선택하여 **내 앱 보안 로그인 확장** 브라우저 확장을 설치합니다. 브라우저 확장에는 Microsoft Edge, Chrome 또는 Firefox가 필요합니다. 그런 다음, 확장을 설치, 시작 및 사용하도록 설정하고 캡처 상태 페이지를 새로 고칩니다.

   그러면 브라우저 확장에서 입력한 URL을 표시하는 다른 탭이 열립니다.
5. 입력한 URL이 있는 탭에서 로그인 프로세스를 진행합니다. 사용자 이름 및 암호를 입력하고 로그인을 시도합니다. 올바른 암호를 제공할 필요는 없습니다.

   캡처된 로그인 필드를 저장하라는 메시지가 표시됩니다.
6. **확인** 을 선택합니다. 브라우저 확장은 캡처 상태 페이지를 업데이트하며 **메타데이터가 애플리케이션을 위해 업데이트됨** 메시지가 표시됩니다. 브라우저 탭이 닫힙니다.

7. Azure AD **로그온 구성** 페이지에서 **예, 앱에 성공적으로 로그인했습니다** 를 선택합니다.

8. **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](./assign-user-or-group-access-portal.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
