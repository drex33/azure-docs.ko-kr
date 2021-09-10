---
title: Azure Active Directory의 사용자 지정 역할에 대한 앱 권한 | Microsoft Docs
description: Azure Portal, PowerShell 또는 Graph API에서 사용자 지정 Azure AD 역할에 대한 엔터프라이즈 앱 권한을 미리 봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: b0a8b3abf4e119096959b640be4b44051a198916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736152"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory의 사용자 지정 역할에 대한 엔터프라이즈 애플리케이션 권한

이 문서에는 Azure AD(Azure Active Directory)의 사용자 지정 역할 정의에 대해 현재 사용 가능한 엔터프라이즈 애플리케이션 권한이 포함되어 있습니다. 이 문서에서는 몇 가지 일반적인 시나리오에 대한 권한 목록과 엔터프라이즈 앱 권한의 전체 목록을 확인할 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="enterprise-application-permissions"></a>엔터프라이즈 애플리케이션 권한

이러한 권한을 사용하는 방법에 대한 자세한 내용은 [엔터프라이즈 앱을 관리하는 사용자 지정 역할 할당](custom-enterprise-apps.md)을 참조하세요.

#### <a name="assigning-users-or-groups-to-an-application"></a>애플리케이션에 사용자 또는 그룹 할당

SAML 기반 Single Sign-On 애플리케이션에 액세스할 수 있는 사용자 및 그룹의 할당을 위임합니다. 필요한 사용 권한

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

#### <a name="creating-gallery-applications"></a>갤러리 애플리케이션 만들기

특히 ServiceNow, F5, Salesforce와 같은 Azure AD Gallery 애플리케이션의 만들기를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationTemplates/instantiate

#### <a name="configuring-basic-saml-urls"></a>기본 SAML URL 구성

SAML 기반 Single Sign-On 애플리케이션에 대한 기본 SAML 구성의 업데이트 및 읽기를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

#### <a name="rolling-over-or-creating-signing-certs"></a>서명 인증서 롤오버 또는 만들기

SAML 기반 Single Sign-On 애플리케이션에 대한 서명 인증서 관리를 위임합니다. 필요한 권한은 다음과 같습니다.

microsoft.directory/applications/credentials/update

#### <a name="update-expiring-sign-in-cert-notification-email-address"></a>만료되는 로그인 인증서 알림 이메일 주소 업데이트

SAML 기반 Single Sign-On 애플리케이션에 대해 만료되는 로그인 인증서 알림 이메일 주소 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

#### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML 토큰 서명 및 로그인 알고리즘 관리

SAML 기반 Single Sign-On 애플리케이션에 대한 SAML 토큰 서명 및 로그인 알고리즘의 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

#### <a name="manage-user-attributes-and-claims"></a>사용자 특성 및 클레임 관리

SAML 기반 Single Sign-On 애플리케이션에 대한 사용자 특성 및 클레임의 만들기, 삭제 및 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>앱 프로비저닝 권한

UI를 통해 작업, 스키마 또는 자격 증명 관리와 같은 쓰기 작업을 수행하려면 프로비저닝 페이지를 볼 수 있는 읽기 권한도 필요합니다.

현재 범위를 모든 사용자/그룹 또는 할당된 사용자/그룹으로 설정하려면 synchronizationJob 및 synchronizationCredentials 권한이 모두 필요합니다.

#### <a name="turn-on-or-restart-provisioning-jobs"></a>프로비저닝 작업 설정 또는 다시 시작

프로비저닝 작업을 설정, 해제 및 다시 시작하는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

#### <a name="configure-the-provisioning-schema"></a>프로비저닝 스키마 구성  

특성 매핑에 대한 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

#### <a name="read-provisioning-settings-associated-with-the-application-object"></a>애플리케이션 개체와 연결된 프로비저닝 설정 읽기

개체와 연결된 프로비저닝 설정을 읽는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applications/synchronization/standard/read

#### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>서비스 주체와 연결된 프로비저닝 설정 읽기

서비스 주체와 연결된 프로비저닝 설정을 읽는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronization/standard/read

#### <a name="authorize-application-access-for-provisioning"></a>프로비저닝을 위한 애플리케이션 액세스 권한 부여  

프로비저닝을 위해 애플리케이션 액세스 권한을 부여하는 기능을 위임합니다. 예제 입력 Oauth 전달자 토큰입니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="application-proxy-permissions"></a>애플리케이션 프록시 권한

애플리케이션의 애플리케이션 프록시 속성에 대해 쓰기 작업을 수행하려면 애플리케이션의 기본 속성 및 인증을 업데이트할 수 있는 권한도 필요합니다.

애플리케이션의 애플리케이션 프록시 속성에 대해 읽기 및 쓰기 작업을 수행하려면 페이지에 표시된 속성 목록의 일부이기 때문에 커넥터 그룹을 볼 수 있는 읽기 권한도 필요합니다.

#### <a name="delegate-application-proxy--connector-management"></a>애플리케이션 프록시 커넥터 관리 위임

커넥터 관리에 대한 만들기, 읽기, 업데이트, 삭제 작업을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/connectorGroups/allProperties/read
- microsoft.directory/connectorGroups/allProperties/update
- microsoft.directory/connectorGroups/create
- microsoft.directory/connectorGroups/delete
- microsoft.directory/connectors/allProperties/read
- microsoft.directory/connectors/create


#### <a name="delegate-application-proxy-settings-management"></a>애플리케이션 프록시 설정 관리 위임

앱에서 애플리케이션 프록시 속성에 대한 만들기, 읽기, 업데이트, 삭제 작업을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/applications/applicationProxy/update 
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update 
- microsoft.directory/applications/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/connectorGroups/allProperties/read

#### <a name="read-application-proxy-settings-for-an-app"></a>앱에서 애플리케이션 프록시 설정 읽기

앱에서 애플리케이션 프록시 속성에 대한 읽기 권한을 위임합니다. 필요한 권한은 다음과 같습니다.
 
- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 

#### <a name="update-url-configuration-application-proxy-settings-for-an-app"></a>앱에서 URL 구성 애플리케이션 프록시 설정 업데이트 

애플리케이션 프록시 외부 URL, 내부 URL, SSL 인증서 속성을 업데이트하기 위한 CRUD(만들기, 읽기, 업데이트 및 삭제) 권한을 위임합니다. 필요한 권한은 다음과 같습니다. 

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 
- microsoft.directory/applications/basic/update 
- microsoft.directory/applications/authentication/update
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update

## <a name="full-list-of-permissions"></a>전체 권한 목록

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/applicationPolicies/allProperties/read | 애플리케이션 정책의 모든 속성을 읽습니다. |
> | microsoft.directory/applicationPolicies/allProperties/update | 애플리케이션 정책의 모든 속성을 업데이트합니다. |
> | microsoft.directory/applicationPolicies/basic/update | 애플리케이션 정책의 표준 속성 업데이트 |
> | microsoft.directory/applicationPolicies/create | 애플리케이션 정책 만들기 |
> | microsoft.directory/applicationPolicies/createAsOwner | 애플리케이션 정책을 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/applicationPolicies/delete | 애플리케이션 정책 삭제 |
> | microsoft.directory/applicationPolicies/owners/read | 애플리케이션 정책의 소유자 읽기 |
> | microsoft.directory/applicationPolicies/owners/update | 애플리케이션 정책의 소유자 속성 업데이트 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 개체 목록에 적용된 애플리케이션 정책 읽기 |
> | microsoft.directory/applicationPolicies/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory에서 servicePrincipals를 만들고 삭제하며 모든 속성을 읽고 업데이트합니다. |
> | microsoft.directory/servicePrincipals/allProperties/read | servicePrincipals의 모든 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/allProperties/update | servicePrincipals의 모든 속성을 업데이트합니다. |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 서비스 주체 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 서비스 주체에 할당된 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성 업데이트 |
> | microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성 업데이트 |
> | microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipals/create | 서비스 주체 만들기 |
> | microsoft.directory/servicePrincipals/createAsOwner | 서비스 주체를 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 속성을 업데이트합니다. |
> | microsoft.directory/servicePrincipals/delete | 서비스 주체 삭제 |
> | microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정 |
> | microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 읽기 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 서비스 주체의 위임된 권한 부여 읽기 |
> | microsoft.directory/servicePrincipals/owners/read | 서비스 주체의 소유자를 읽습니다. |
> | microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자를 업데이트합니다. |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/policies/read | 서비스 주체의 정책을 읽습니다. |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책을 업데이트합니다. |
> | microsoft.directory/servicePrincipals/standard/read | 서비스 주체의 표준 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성을 업데이트합니다. |
> | microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션 인스턴스화 |
> | microsoft.directory/auditLogs/allProperties/read | 감사 로그 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 로그인 보고서를 읽습니다. |
> | microsoft.directory/applications/applicationProxy/read | 모든 유형의 애플리케이션에 대한 모든 애플리케이션 프록시 속성을 읽습니다. |
> | microsoft.directory/applications/applicationProxy/update | 모든 유형의 애플리케이션에 대한 모든 애플리케이션 프록시 속성을 업데이트합니다. |
> | microsoft.directory/applications/applicationProxyAuthentication/update | 모든 유형의 애플리케이션에 대한 애플리케이션 프록시 인증 속성을 업데이트합니다. |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | 모든 유형의 애플리케이션에 대한 애플리케이션 프록시 내부 및 외부 URL을 업데이트합니다. |
> | microsoft.directory/applications/applicationProxySslCertificate/update | 모든 유형의 애플리케이션에 대한 애플리케이션 프록시 사용자 지정 도메인을 업데이트합니다. |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/connectorGroups/create | 애플리케이션 프록시 커넥터 그룹 만들기 |
> | microsoft.directory/connectorGroups/delete | 애플리케이션 프록시 커넥터 그룹 삭제 |
> | microsoft.directory/connectorGroups/allProperties/read | 애플리케이션 프록시 커넥터 그룹의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/allProperties/update | 애플리케이션 프록시 커넥터 그룹의 모든 속성 업데이트 |
> | microsoft.directory/connectors/create | 애플리케이션 프록시 커넥터 만들기 |
> | microsoft.directory/connectors/allProperties/read | 애플리케이션 프록시 커넥터의 모든 속성 읽기 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 서비스 주체 리소스에 대한 작업 동기화의 모든 측면을 관리합니다. |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정을 읽습니다. |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 서비스 주체 리소스에 대한 스키마 동기화의 모든 측면을 관리합니다. |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure AD PowerShell 및 Graph API를 사용하여 사용자 지정 역할 만들기](custom-create.md)
- [역할 할당 나열](view-assignments.md)
