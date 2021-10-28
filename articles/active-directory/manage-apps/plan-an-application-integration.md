---
title: Azure Active Directory와 앱 통합 시작
description: 이 문서는 온-프레미스 애플리케이션 및 클라우드 애플리케이션과 Azure Active Directory(AD)를 통합하는 시작 가이드입니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: a19a0cebe3323e16b8f5cc7744c9e4db79393073
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615774"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>애플리케이션과 Azure Active Directory 통합 시작 가이드

이 항목에서는 Azure AD(Active Directory)와 애플리케이션을 통합하기 위한 프로세스를 간단히 설명합니다. 아래의 각 섹션에는 더 자세한 항목의 요약이 포함되므로 이 시작 가이드의 어떤 부분이 사용자와 관련 있는지 식별할 수 있습니다.

자세한 배포 계획을 다운로드하려면 [다음 단계](#next-steps)를 참조하세요.

## <a name="take-inventory"></a>인벤토리 가져오기

애플리케이션을 Azure AD와 통합하기 전에 현재 위치와 목표를 아는 것이 중요합니다.  다음 질문은 Azure AD 애플리케이션 통합 프로젝트에 대해 생각해볼 수 있도록 하기 위한 용도로 제공됩니다.

### <a name="application-inventory"></a>애플리케이션 인벤토리

* 애플리케이션은 모두 어디에 있습니까? 누가 소유합니까?
* 애플리케이션에 어떤 종류의 인증이 필요합니까?
* 누가 어떤 애플리케이션에 액세스하려 합니까?
* 새 애플리케이션을 배포하시겠습니까?
  * 사내에 구축하고 Azure 컴퓨팅 인스턴스에 배포하시겠습니까?
  * Azure 애플리케이션 갤러리에서 사용할 수 있는 애플리케이션을 사용하시겠습니까?

### <a name="user-and-group-inventory"></a>사용자 및 그룹 인벤토리

* 사용자 계정은 어디에 있습니까?
  * 온-프레미스 Active Directory
  * Azure AD
  * 사용자가 소유한 별도 애플리케이션 데이터베이스 내에서
  * 허용되지 않은 애플리케이션에서
  * 위 항목 모두
* 개별 사용자는 현재 어떤 사용 권한 및 역할 할당을 가지고 있습니까? 액세스를 검토해야 하거나 사용자 액세스 및 역할 할당이 적절하다고 생각합니까?
* 그룹은 온-프레미스 Active Directory 내에 만들어 집니까?
  * 그룹을 어떻게 구성합니까?
  * 그룹 멤버는 누구입니까?
  * 그룹은 현재 어떤 사용 권한/역할 할당을 가지고 있습니까?
* 통합하기 전에 사용자/그룹 데이터베이스를 정리해야 합니까?  (이는 중요한 질문입니다. 쓰레기를 넣고 쓰레기를 얻는 현상.)

### <a name="access-management-inventory"></a>액세스 관리 인벤토리

* 애플리케이션에 대한 사용자 액세스를 현재 어떻게 관리합니까? 변경해야 합니까?  예를 들어 [Azure RBAC](../../role-based-access-control/role-assignments-portal.md)와 같이 액세스를 관리하는 다른 방법을 고려한 적이 있습니까?
* 누구에게 어떤 액세스가 필요합니까?

아마 이러한 모든 질문에 대한 답변은 없겠지만 괜찮습니다.  이 가이드는 이러한 일부 질문에 대답하고 합리적인 결정을 내릴 수 있습니다.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Cloud Discovery를 사용하여 허용되지 않은 클라우드 애플리케이션 찾기

위에서 설명한 대로 지금까지 조직에서 관리하지 않은 애플리케이션이 있을 수 있습니다.  인벤토리 프로세스의 일부로 허용되지 않은 클라우드 애플리케이션을 찾을 수 있습니다. [Cloud Discovery 설정](/cloud-app-security/set-up-cloud-discovery)을 참조하세요.

## <a name="integrating-applications-with-azure-ad"></a>Azure AD와 애플리케이션 통합

다음 문서에서는 애플리케이션을 Azure AD와 통합하는 여러 가지 방법을 설명하고 일부 지침을 제공합니다.

* [사용할 Active Directory 결정](../fundamentals/active-directory-whatis.md)
* [Azure 애플리케이션 갤러리에서 애플리케이션 사용](what-is-single-sign-on.md)
* [SaaS 애플리케이션 통합 자습서 목록](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Azure AD 갤러리에 나열되지 않은 앱에 대한 기능

조직에 이미 있는 애플리케이션 또는 Azure AD 애플리케이션 갤러리의 일부가 아닌 공급업체의 타사 애플리케이션을 추가할 수 있습니다. [사용권 계약](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)에 따라 다음과 같은 기능을 사용할 수 있습니다.

* [SAML(Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID 공급자를 지원하는 애플리케이션의 셀프 서비스 통합(SP 시작 또는 IdP 시작)
* [암호 기반 SSO](sso-options.md#password-based-sso)를 사용하여 HTML 기반 로그인 페이지가 있는 웹 애플리케이션의 셀프 서비스 통합
* [사용자 프로비저닝에 SCIM(System for Cross-Domain Identity Management) 프로토콜](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 사용하는 애플리케이션의 셀프 서비스 연결
* [Office 365 앱 시작 관리자](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) 또는 [내 앱](https://myapplications.microsoft.com/)에서 애플리케이션에 대한 링크를 추가하는 기능

사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 지침을 찾는다면 [Azure AD용 인증 시나리오](../develop/authentication-vs-authorization.md)를 참조하세요. [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)와 같은 최신 프로토콜을 사용하여 사용자를 인증하는 앱을 개발할 때 Azure Portal에서 [앱 등록](../develop/quickstart-register-app.md) 경험을 사용하여 Microsoft ID 플랫폼에 등록할 수 있습니다.

### <a name="authentication-types"></a>인증 유형

애플리케이션에는 각자 다른 인증 요구 사항이 있을 수 있습니다. Azure AD를 사용하면 SAML 2.0, WS-페더레이션 또는 OpenID Connect 프로토콜 및 암호 Single Sign-On을 사용하는 애플리케이션에서 서명 인증서를 사용할 수 있습니다. 애플리케이션 인증 유형에 대한 자세한 내용은 [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](manage-certificates-for-federated-single-sign-on.md) 및 [암호 기반 Single Sign On](what-is-single-sign-on.md)을 참조하세요.

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Azure AD 앱 프록시를 사용하는 SSO 사용

Microsoft Azure AD 애플리케이션 프록시를 사용하여 어디서든 어떤 디바이스에서든 안전하게 프라이빗 네트워크 내부에 위치한 애플리케이션에 액세스를 제공할 수 있습니다. 환경 내에서 애플리케이션 프록시 커넥터를 설치한 후에 Azure AD를 이용하여 쉽게 구성될 수 있습니다.

### <a name="integrating-custom-applications"></a>사용자 지정 애플리케이션 통합

Azure 애플리케이션 갤러리에 사용자 지정 애플리케이션을 추가하려면 [Azure AD 앱 갤러리에 앱 게시](../develop/v2-howto-app-gallery-listing.md)를 참조하세요.

## <a name="managing-access-to-applications"></a>애플리케이션에 대한 액세스 관리

다음 문서는 애플리케이션이 Azure AD와 통합되면 Azure AD 커넥터 및 Azure AD를 사용하여 애플리케이션에 대한 액세스를 관리할 수 있는 방법을 설명합니다.

* [Azure AD를 사용하는 앱에 대한 액세스 관리](what-is-access-management.md)
* [Azure AD 커넥터를 사용하여 자동화](../app-provisioning/user-provisioning.md)
* [애플리케이션에 사용자 지정](./assign-user-or-group-access-portal.md)
* [애플리케이션에 그룹 지정](./assign-user-or-group-access-portal.md)
* [계정 공유](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [GitHub](../fundamentals/active-directory-deployment-plans.md)에서 Azure Active Directory 배포 계획을 다운로드할 수 있습니다. 갤러리 애플리케이션의 경우 [Azure Portal](https://portal.azure.com)을 통해 Single Sign-On, 조건부 액세스 및 사용자 프로비저닝에 대한 배포 계획을 다운로드할 수 있습니다.

Azure Portal에서 배포 계획을 다운로드하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **엔터프라이즈 애플리케이션** | **앱 선택** | **배포 계획** 을 선택합니다.
