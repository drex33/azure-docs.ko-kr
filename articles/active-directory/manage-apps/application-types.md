---
title: ID 관리를 위해 테넌트를 사용하여 앱 보기
titleSuffix: Azure AD
description: ID 관리를 위해 Azure Active Directory 테넌트를 사용하여 모든 애플리케이션을 보는 방법을 이해합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a2030df0bd70781f299ee7db60df0e3ae49b8568
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620719"
---
# <a name="viewing-apps-using-your-azure-active-directory-tenant-for-identity-management"></a>ID 관리를 위해 Azure Active Directory 테넌트를 사용하여 앱 보기

[애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)에서 기본 사항을 알아봅니다. 여기서는 ID 관리를 위해 Azure AD 테넌트를 사용하여 모든 앱을 보는 방법을 알아봅니다. 이 문서에서는 찾고 있는 앱 유형을 좀 더 자세히 살펴봅니다.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>내 모든 애플리케이션 목록에 특정 애플리케이션이 나타나는 이유는 무엇입니까?

**모든 애플리케이션** 으로 필터링한 경우 **모든 애플리케이션** **목록** 은 테넌트의 모든 서비스 주체 개체를 보여 줍니다. 서비스 주체 개체는 다양한 방법으로 이 목록에 나타날 수 있습니다.

- 다음을 포함하여 애플리케이션 갤러리에서 모든 애플리케이션을 추가하는 경우:

  - **Azure AD - 엔터프라이즈 애플리케이션** – Azure AD 포털의 **엔터프라이즈 애플리케이션** 옵션을 사용하여 테넌트에 추가한 앱. 일반적으로 SAML 표준을 사용하여 통합된 앱입니다.
  - **Azure AD - 앱 등록** – Azure AD 포털의 **앱 등록** 옵션을 사용하여 테넌트에 추가한 앱. 일반적으로 Open ID Connect 및 OAuth 표준을 사용하는 사용자 개발 앱입니다.
  - **애플리케이션 프록시 애플리케이션** – 외부로 보안 Single Sign-On을 제공하려는 온-프레미스 환경에서 실행 중인 애플리케이션입니다.
- Azure Active Directory와 통합된 타사 애플리케이션에 등록하거나 로그인하는 경우. 한 가지 예로 [Smartsheet](https://app.smartsheet.com/b/home) 또는 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)이 있습니다.
- Microsoft 365와 같은 Microsoft 앱
- Azure 리소스에 대한 관리 ID를 사용하는 경우. 자세한 내용은 [관리 ID 유형](../managed-identities-azure-resources/overview.md#managed-identity-types)을 참조하세요.
- [애플리케이션 레지스트리](../develop/quickstart-register-app.md)를 사용하여 사용자 지정 개발된 애플리케이션을 만들어 새 애플리케이션 등록을 추가하는 경우
- [V2.0 애플리케이션 레지스트리 포털](../develop/quickstart-register-app.md)을 사용하여 사용자 지정 개발된 애플리케이션을 만들어 새 애플리케이션 등록을 추가하는 경우
- Visual Studio의 [ASP.net 인증 방법](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) 또는 [연결된 서비스](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)를 사용하여 개발 중인 애플리케이션을 추가하는 경우
- [Azure AD PowerShell 모듈](/powershell/azure/active-directory/install-adv2)을 사용하여 서비스 주체 개체를 만드는 경우
- 테넌트의 데이터를 사용하도록 관리자 권한으로 [애플리케이션에 동의](../develop/howto-convert-app-to-be-multi-tenant.md)하는 경우
- 테넌트의 데이터를 사용하도록 [사용자가 애플리케이션에 동의](../develop/howto-convert-app-to-be-multi-tenant.md)하는 경우
- 테넌트에 데이터를 저장하는 특정 서비스를 활성화하는 경우. 한 가지 예는 암호 재설정 정책을 안전하게 저장하도록 서비스 주체로 모델링되는 암호 재설정입니다.

앱을 디렉터리에 추가하는 방법 및 이유에 대한 자세한 내용은 [애플리케이션을 Azure AD에 추가하는 방법](../develop/active-directory-how-applications-are-added.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
