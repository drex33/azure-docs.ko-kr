---
title: AD FS에서 Azure Active Directory로 애플리케이션 인증 이동
description: Azure Active Directory를 사용하여 AD FS(Active Directory Federation Services)를 대체하고 사용자에게 모든 애플리케이션에 대한 Single Sign-On을 제공하는 방법을 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a9d6ca9769e19f300242a50c2016d1e942663567
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614786"
---
# <a name="move-application-authentication-to-azure-active-directory"></a>애플리케이션 인증을 Azure Active Directory로 이동

[Azure AD(Active Directory)](../fundamentals/active-directory-whatis.md)는 사용자, 파트너, 고객에게 애플리케이션에 액세스하고 모든 플랫폼과 디바이스에서 협업하는 단일 ID를 활용할 수 있도록 하는 범용 ID 플랫폼을 제공합니다. Azure AD에는 [전체 ID 관리 기능 도구 모음](../fundamentals/active-directory-whatis.md)이 있습니다. 애플리케이션 인증 및 권한 부여를 Azure AD로 표준화하면 이와 같은 이점을 누릴 수 있습니다.

> [!TIP]
> 이 문서는 개발자를 대상으로 작성되었습니다. Azure AD로 애플리케이션을 이동하려는 프로젝트 관리자와 관리자의 경우 [Azure AD로 애플리케이션 인증 마이그레이션](migrate-application-authentication-to-azure-active-directory.md)을 읽어야 합니다.

## <a name="azure-ad-benefits"></a>Azure AD 혜택

사용자 계정이 포함된 온-프레미스 디렉터리가 있는 경우 사용자가 인증하는 여러 애플리케이션이 있을 가능성이 높습니다. 이러한 각 앱은 사용자가 자신의 ID를 사용하여 액세스하도록 구성됩니다.

사용자가 온-프레미스 Active Directory에 직접 인증할 수도 있습니다. AD FS(Active Directory Federation Services)는 표준 기반 온-프레미스 ID 서비스입니다. 사용자에게 각 애플리케이션에 개별적으로 로그인하도록 요구하지 않고 신뢰할 수 있는 비즈니스 파트너 간에 SSO(Single Sign-On) 기능을 사용하는 기능을 확장합니다. 이를 페더레이션된 ID라고 합니다.

Microsoft 365 및 Azure AD 기반 앱과 함께 AD FS에 직접 페더레이션된 SaaS(Software as a Service) 또는 사용자 지정 기간 업무 앱을 보유한 많은 조직이 있습니다.

  ![온-프레미스에 직접 연결된 애플리케이션](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> 애플리케이션 보안을 강화하기 위해서는 온-프레미스 및 클라우드 환경에서 단일 액세스 제어 및 정책 집합을 보유하는 것이 목표로 해야 합니다.

  ![Azure AD를 통해 연결된 애플리케이션](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>마이그레이션할 앱 유형

ID 및 액세스 관리를 위한 단일 제어 평면을 제공하므로 모든 애플리케이션 인증을 Azure AD로 마이그레이션하는 것이 가장 좋습니다.

애플리케이션은 인증을 위해 최신 또는 레거시 프로토콜을 사용할 수 있습니다. Azure AD로 마이그레이션을 계획하는 경우 최신 인증 프로토콜(예: SAML 및 Open ID Connect)을 사용하는 앱을 먼저 마이그레이션하는 것이 좋습니다. Azure 앱 갤러리의 기본 제공 커넥터를 사용하거나 Azure AD에 애플리케이션을 등록하여 Azure AD에서 인증하도록 해당 앱을 다시 구성할 수 있습니다. 이전 프로토콜을 사용하는 앱은 애플리케이션 프록시를 사용하여 통합할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Azure AD 애플리케이션 프록시를 사용하여 원격 사용자용 온-프레미스 앱 게시](../app-proxy/what-is-application-proxy.md)
* [애플리케이션 관리란?](what-is-application-management.md)
* [애플리케이션을 Azure AD로 마이그레이션하기 위한 AD FS 애플리케이션 활동 보고서](migrate-adfs-application-activity.md)
* [Azure AD Connect Health를 사용하여 AD FS 모니터링](../hybrid/how-to-connect-health-adfs.md)

### <a name="the-migration-process"></a>마이그레이션 프로세스

앱 인증을 Azure AD로 이동하면서 앱 및 구성을 테스트합니다. 프로덕션 환경으로 이동하는 경우 마이그레이션 테스트에 기존 테스트 환경을 계속 사용하는 것이 좋습니다. 테스트 환경을 현재 사용할 수 없는 경우 애플리케이션 아키텍처에 따라 [Azure App Service](https://azure.microsoft.com/services/app-service/) 또는 [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)를 사용하여 설정할 수 있습니다.

앱 구성을 개발할 별도의 테스트용 Azure AD 테넌트를 설정할 수도 있습니다.

마이그레이션 프로세스는 다음과 같습니다.

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>1단계 - 현재 상태: 프로덕션 앱이 AD FS로 인증

  ![마이그레이션 1단계 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>2단계 – (선택 사항)앱의 테스트 인스턴스를 테스트 Azure AD 테넌트에 연결

앱의 테스트 인스턴스를 테스트 Azure AD 테넌트에 연결하도록 구성을 업데이트하고 필요한 변경 작업을 수행합니다. 그러면 테스트 Azure AD 테넌트의 사용자로 앱을 테스트할 수 있습니다. 개발 프로세스 중에 [Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 요청과 응답을 비교하고 확인할 수 있습니다.

별도의 테스트 테넌트를 설정하는 것이 불가능한 경우 아래 3단계에 설명된 대로 해당 단계를 건너뛰고 앱의 테스트 인스턴스를 프로덕션 Azure AD 테넌트로 연결합니다.

  ![마이그레이션 2단계 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>3단계 – 앱의 테스트 인스턴스를 프로덕션 Azure AD 테넌트에 연결

앱의 테스트 인스턴스를 프로덕션 Azure AD 테넌트에 연결하도록 구성을 업데이트합니다. 이제 프로덕션 테넌트의 사용자로 테스트할 수 있습니다. 필요한 경우 이 문서의 사용자 전환 섹션을 검토합니다.

  ![마이그레이션 3단계 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>4단계 – 프로덕션 앱을 프로덕션 Azure AD 테넌트에 연결

프로덕션 앱의 구성을 업데이트하여 프로덕션 Azure AD 테넌트에 연결되도록 합니다.

  ![마이그레이션 4단계 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS로 인증하는 앱은 권한에 Active Directory 그룹을 사용할 수 있습니다. 마이그레이션을 시작하기 전에 [Azure AD Connect 동기화](../hybrid/how-to-connect-sync-whatis.md)를 사용하여 온-프레미스 환경과 Azure AD 간 ID 데이터를 동기화합니다. 애플리케이션을 마이그레이션할 때 동일한 사용자에게 액세스 권한을 부여하도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인합니다.

### <a name="line-of-business-apps"></a>기간 업무 앱

기간 업무 앱은 조직에서 개발한 앱 또는 표준 패키지 제품입니다. 예를 들어 Windows Identity Foundation 및 SharePoint 앱(SharePoint Online 아님)을 기반으로 하는 앱이 있습니다.

OAuth 2.0, OpenID Connect 또는 WS-Federation을 사용하는 기간 업무 앱은 [앱 등록](../develop/quickstart-register-app.md)으로 Azure AD와 통합할 수 있습니다. [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 애플리케이션 페이지에서 SAML 2.0 또는 WS-Federation을 [비갤러리 애플리케이션](add-application-portal.md)으로 사용하는 사용자 지정 앱을 통합합니다.

## <a name="saml-based-single-sign-on"></a>SAML 기반 Single Sign-On

인증에 SAML 2.0을 사용하는 앱은 [SAML 기반 SSO](what-is-single-sign-on.md)(Single Sign-On)를 사용하도록 구성할 수 있습니다. SAML 기반 SSO를 사용하면 SAML 클레임에서 정의하는 규칙에 따라 사용자를 특정 애플리케이션 역할에 매핑할 수 있습니다.

SAML 기반 SSO에 맞게 SaaS 애플리케이션을 구성하려면 [빠른 시작: SAML 기반 Single Sign-On 설정](add-application-portal-setup-sso.md)을 참조하세요.

  ![SSO SAML 사용자 스크린샷 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

많은 SaaS 애플리케이션에는 SAML 기반 SSO 구성을 단계별로 안내하는 [애플리케이션별 자습서](../saas-apps/tutorial-list.md)가 있습니다.

  ![앱 자습서](media/migrate-adfs-apps-to-azure/app-tutorial.png)

일부 앱은 쉽게 마이그레이션할 수 있습니다. 사용자 지정 클레임과 같이 더 복잡한 요구 사항이 있는 앱의 경우 Azure AD 및/또는 Azure AD Connect에서 추가로 구성해야 할 수도 있습니다. 지원되는 클레임 매핑에 대한 자세한 내용은 [방법: 테넌트의 특정 앱용 토큰에서 내보낸 클레임 사용자 지정(미리 보기)](../develop/active-directory-claims-mapping.md)을 참조하세요.

특성을 매핑할 때 다음 제한 사항에 유의하세요.

* AD FS에서 발급할 수 있는 특성을 동기화된 경우에도 모든 특성이 Azure AD에서 SAML 토큰으로 내보낼 특성으로 표시되는 것은 아닙니다. 특성을 편집하는 경우 **값** 드롭다운 목록에 Azure AD에서 사용할 수 있는 다양한 특성이 표시됩니다. [Azure AD Connect 동기화 항목](../hybrid/how-to-connect-sync-whatis.md) 구성을 확인하여 필요한 특성(예: **samAccountName**)이 Azure AD와 동기화되었는지 확인합니다. 확장 특성을 사용하여 Azure AD의 표준 사용자 스키마에 속하지 않는 모든 클레임을 내보낼 수 있습니다.
* 가장 일반적인 시나리오에서는 **NameID** 클레임 및 다른 일반 사용자 식별자 클레임만 앱에 필요합니다. 추가 클레임이 필요한지 확인하려면 AD FS에서 발급한 클레임을 검토합니다.
* 일부 클레임은 Azure AD에서 보호되므로 모든 클레임을 발급할 수 있는 것은 아닙니다.
* 암호화된 SAML 토큰을 사용하는 기능은 현재 미리 보기로 제공됩니다. [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.

### <a name="software-as-a-service-saas-apps"></a>SaaS(Software as a service) 앱

사용자가 SaaS 앱(예: Salesforce, ServiceNow 또는 Workday)에 로그인하고 AD FS와 통합된 경우 SaaS 앱에 대해 페더레이션 로그온을 사용하게 됩니다.

Azure AD에서 대부분의 SaaS 애플리케이션을 구성할 수 있습니다. Microsoft에는 [Azure AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에 SaaS 앱에 대해 미리 구성된 연결이 많기 때문에 더 쉽게 전환할 수 있습니다. SAML 2.0 애플리케이션은 Azure AD 앱 갤러리를 통하거나 [비갤러리 애플리케이션](add-application-portal.md)으로 Azure AD와 통합할 수 있습니다.

OAuth 2.0 또는 OpenID Connect를 사용하는 앱은 [앱 등록](../develop/quickstart-register-app.md)으로 Azure AD와 유사하게 통합할 수 있습니다. 레거시 프로토콜을 사용하는 앱은 [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용하여 Azure AD에 인증할 수 있습니다.

SaaS 앱을 온보딩하는 데 문제가 있는 경우 [SaaS 애플리케이션 통합 지원 별칭](mailto:SaaSApplicationIntegrations@service.microsoft.com)에 문의할 수 있습니다.

### <a name="saml-signing-certificates-for-sso"></a>SSO용 SAML 서명 인증서

서명 인증서는 모든 SSO 배포의 중요한 부분입니다. Azure AD는 서명 인증서를 만들어 SaaS 애플리케이션에 대한 SAML 기반 페더레이션된 SSO를 설정합니다. 갤러리 또는 비갤러리 애플리케이션을 추가한 후에는 페더레이션된 SSO 옵션을 사용하여 추가된 애플리케이션을 구성합니다. [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](manage-certificates-for-federated-single-sign-on.md)를 참조하세요.

### <a name="saml-token-encryption"></a>SAML 토큰 암호화

AD FS와 Azure AD는 애플리케이션으로 이동하는 SAML 보안 어설션을 암호화하는 기능인 토큰 암호화를 제공합니다. 어설션은 공개 키를 사용하여 암호화되고, 일치하는 프라이빗 키를 사용하여 수신 애플리케이션에서 암호가 해독됩니다. 토큰 암호화를 구성하는 경우 X.509 인증서 파일을 업로드하여 공개 키를 제공합니다.

Azure AD SAML 토큰 암호화 및 구성 방법에 대한 자세한 내용은 [방법: Azure AD SAML 토큰 암호화 구성](howto-saml-token-encryption.md)을 참조하세요.  

> [!NOTE]
> 토큰 암호화는 Azure AD(Azure Active Directory) 프리미엄 기능입니다. Azure AD 버전, 기능 및 가격 책정에 대한 자세한 내용은 [Azure AD 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요.

### <a name="apps-and-configurations-that-can-be-moved-today"></a>바로 이동할 수 있는 앱 및 구성

현재 쉽게 이동할 수 있는 앱에는 표준 구성 요소 및 클레임 집합을 사용하는 SAML 2.0 앱이 포함됩니다. 표준 항목은 다음과 같습니다.

* 사용자 계정 이름
* 전자 메일 주소
* 이름
* Surname
* SAML **NameID** 로 대체되는 특성(Azure AD 메일 특성, 메일 접두사, 직원 ID, 확장 특성(1-15) 또는 온-프레미스 **SamAccountName** 특성 포함). 자세한 내용은 [NameIdentifier 클레임 편집](../develop/active-directory-saml-claims-customization.md)을 참조하세요.
* 사용자 지정 클레임.

다음과 같은 경우에는 Azure AD로 마이그레이션하기 위한 추가 구성 단계가 필요합니다.

* AD FS에서 사용자 지정 권한 부여 또는 MFA(다단계 인증) 규칙 사용자 지정. [Azure AD 조건부 액세스](../conditional-access/overview.md) 기능을 사용하여 구성합니다.
* 여러 개의 회신 URL 엔드포인트가 있는 앱. PowerShell 또는 Azure Portal 인터페이스를 사용하여 Azure AD에서 구성합니다.
* SAML 버전 1.1 토큰이 필요한 WS-Federation 앱(예: SharePoint 앱). PowerShell을 사용하여 수동으로 구성할 수 있습니다. 갤러리에서 SharePoint 및 SAML 1.1 애플리케이션용으로 사전 통합된 일반 템플릿을 추가할 수도 있습니다. SAML 2.0 프로토콜을 지원합니다.
* 복잡한 클레임 발급 변환 규칙. 지원되는 클레임 매핑에 대한 자세한 내용은 다음을 참조하세요.
  * [Azure Active Directory의 클레임 매핑](../develop/active-directory-claims-mapping.md)
  * [Azure Active Directory의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>현재 Azure AD에서 지원되지 않는 앱 및 구성

특정 기능이 필요한 앱은 현재 마이그레이션할 수 없습니다.

#### <a name="protocol-capabilities"></a>프로토콜 기능

다음 프로토콜 기능이 필요한 앱은 현재 마이그레이션할 수 없습니다.

* WS-Trust ActAs 패턴 지원
* SAML 아티팩트 확인
* 서명된 SAML 요청의 서명 확인 ‎
  > [!Note]
  > 서명된 요청은 수락되지만, 서명이 확인되지는 않습니다.

  Azure AD는 애플리케이션에 미리 구성된 엔드포인트에만 토큰을 반환하기 때문에 대부분의 경우 서명 확인이 필요하지 않을 수 있습니다.

#### <a name="claims-in-token-capabilities"></a>토큰의 클레임 기능

다음 토큰의 클레임 기능이 필요한 앱은 현재 마이그레이션할 수 없습니다.

* Azure AD 디렉터리가 아닌 특성 저장소의 클레임(해당 데이터가 Azure AD에 동기화되지 않은 경우). 자세한 내용은 [Azure AD 동기화 API 개요](/graph/api/resources/synchronization-overview)를 참조하세요.
* 디렉터리 다중 값 특성의 발급. 예를 들어 현재는 프록시 주소에 대해 다중 값 클레임을 발급할 수 없습니다.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 앱 설정 매핑

마이그레이션을 위해서는 온-프레미스에서 애플리케이션을 구성하는 방법을 평가하고 해당 구성을 Azure AD에 매핑해야 합니다. AD FS와 Azure AD는 비슷하게 작동하므로 신뢰, 로그온 및 로그아웃 URL 및 식별자를 구성하는 개념이 두 경우 모두에 적용됩니다. Azure AD에서 쉽게 구성할 수 있도록 애플리케이션의 AD FS 구성 설정을 문서화합니다.

### <a name="map-app-configuration-settings"></a>애플리케이션 구성 설정 매핑

다음 표에서는 Azure AD 엔터프라이즈 애플리케이션과 AD FS 신뢰 당사자 트러스트 간의 가장 일반적인 몇 가지 설정 매핑을 설명합니다.

* AD FS - 앱에 대한 AD FS 신뢰 당사자 트러스트에서 설정을 찾습니다. 신뢰 당사자를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다.
* Azure AD - 설정은 [Azure Portal](https://portal.azure.com/) 내에서 각 애플리케이션의 SSO 속성에 대해 구성됩니다.

| 구성 설정| AD FS| Azure AD에서 구성하는 방법| SAML 토큰 |
| - | - | - | - |
| **앱 로그온 URL** <p>SP(서비스 공급자)가 시작한 SAML 흐름에서 앱에 로그인하는 사용자의 URL입니다.| 해당 없음| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 회신 URL** <p>IdP(ID 공급자)의 관점에서 본 앱의 URL입니다. IdP는 사용자가 IdP에 로그인한 후 사용자 및 토큰을 해당 URL로 보냅니다.  **SAML 어설션 소비자 엔드포인트** 라고도 합니다.| **엔드포인트** 탭 선택| SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 대상 요소. 예제 값: `https://contoso.my.salesforce.com` |
| **앱 로그아웃 URL** <p>사용자가 앱에서 로그아웃할 때 로그아웃 정리 요청이 전송되는 URL입니다. IdP는 다른 모든 앱에서도 사용자를 로그아웃하도록 요청을 보냅니다.| **엔드포인트** 탭 선택| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 식별자** <p>IdP의 관점에서 본 앱 식별자입니다. 로그온 URL 값은 종종 식별자에 사용되지만 항상 그렇지는 않습니다.  때로는 앱에서 “엔터티 ID”라고 합니다.| **식별자** 탭 선택|SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 **Audience** 요소에 매핑됩니다. |
| **앱 페더레이션 메타데이터** <p>앱의 페더레이션 메타데이터 위치입니다. IdP에서 엔드포인트 또는 암호화 인증서와 같은 특정 구성 설정을 자동으로 업데이트하는 데 사용합니다.| **모니터링** 탭 선택| 해당 없음. Azure AD는 애플리케이션 페더레이션 메타데이터를 직접 사용하도록 지원하지 않습니다. 페더레이션 메타데이터를 수동으로 가져올 수 있습니다.| 해당 없음 |
| **사용자 ID/이름 ID** <p>Azure AD 또는 AD FS의 사용자 ID를 앱에 고유하게 표시하는 데 사용되는 특성입니다.  이 특성은 일반적으로 사용자의 UPN 또는 메일 주소입니다.| 클레임 규칙. 대부분의 경우 클레임 규칙은 **NameIdentifier** 로 끝나는 형식의 클레임을 발급합니다.| **사용자 특성 및 클레임** 헤더에서 식별자를 찾을 수 있습니다. UPN이 기본적으로 사용됩니다.| SAML 토큰의 **NameID** 요소에 매핑됩니다. |
| **기타 클레임** <p>일반적으로 IdP에서 앱으로 전송되는 기타 클레임 정보의 예로는 이름, 성, 메일 주소, 그룹 멤버 자격이 있습니다.| AD FS에서는 신뢰 당사자에 대한 다른 클레임 규칙으로 찾을 수 있습니다.| **사용자 특성 및 클레임** 헤더에서 식별자를 찾을 수 있습니다. **보기** 를 선택하고 다른 모든 사용자 특성을 편집합니다.| 해당 없음 |

### <a name="map-identity-provider-idp-settings"></a>IdP(ID 공급자) 설정 매핑

애플리케이션이 SSO용 AD FS에 대해 Azure AD에 연결되도록 구성합니다. 여기서는 SAML 프로토콜을 사용하는 SaaS 앱을 중점적으로 다룹니다. 그러나 해당 개념은 사용자 지정 기간 업무 앱에도 적용됩니다.

> [!NOTE]
> Azure AD의 구성 값은 Azure 테넌트 ID가 {tenant-id}를 대체하고 애플리케이션 ID가 {application-id}를 대체하는 패턴을 따릅니다. 해당 내용은 [Azure Portal](https://portal.azure.com/)의 **Azure Active Directory > 속성** 에서 확인할 수 있습니다.

* 디렉터리 ID를 선택하여 테넌트 ID를 확인합니다.
* 애플리케이션 ID를 선택하여 애플리케이션 ID를 확인합니다.

 개략적으로 다음 주요 SaaS 앱 구성 요소를 Azure AD에 매핑합니다.

| 요소| 구성 값 |
| - | - |
| ID 공급자 발급자| https:\//sts.windows.net/{tenant-id}/ |
| ID 공급자 로그인 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| ID 공급자 로그아웃 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 페더레이션 메타데이터 위치| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>SaaS 앱에 대한 SSO 설정 매핑

SaaS 앱은 인증 요청을 보내는 위치와 받은 토큰의 유효성을 검사하는 방법을 알아야 합니다. 다음 표에서는 앱의 SSO 설정을 구성하는 요소와 AD FS 및 Azure AD 내의 해당 값 또는 위치를 설명합니다.

| 구성 설정| AD FS| Azure AD에서 구성하는 방법 |
| - | - | - |
| **IdP 로그온 URL** <p>앱의 관점에서 본 IdP의 로그온 URL(사용자가 로그인을 위해 리디렉션되는 위치)입니다.| AD FS 로그온 URL은 “/adfs/ls/” 뒤에 오는 AD FS 페더레이션 서비스 이름입니다. <p>예: `https://fs.contoso.com/adfs/ls/`| [tenant_id]를 테넌트 ID로 바꿉니다. <p> ‎SAML-P 프로토콜을 사용하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>‎WS-Federation 프로토콜을 사용하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 로그아웃 URL**<p>앱의 관점에서 본 IdP의 로그아웃 URL(사용자가 앱에서 로그아웃할 때 리디렉션되는 위치)입니다.| 로그아웃 URL은 로그온 URL과 동일하거나 “wa = wsignout1.0”이 추가된 URL과 동일합니다. 예: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| [tenant_id]를 테넌트 ID로 바꿉니다.<p>SAML-P 프로토콜을 사용하는 앱의 경우:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> ‎WS-Federation 프로토콜을 사용하는 앱의 경우: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **토큰 서명 인증서**<p>IdP는 인증서의 프라이빗 키를 사용하여 발급된 토큰에 서명합니다. 앱이 신뢰하도록 구성된 것과 동일한 IdP에서 토큰이 제공되었는지 확인합니다.| AD FS 토큰 서명 인증서는 AD FS 관리의 **인증서** 아래에 있습니다.| Azure Portal에서 **SAML 서명 인증서** 헤더에 있는 애플리케이션 **Single Sign-on 속성** 에 있습니다. 여기서는 앱에 업로드할 인증서를 다운로드할 수 있습니다.  <p>애플리케이션에 둘 이상의 인증서가 있는 경우 모든 인증서는 페더레이션 메타데이터 XML 파일에 있습니다. |
| **식별자/“발급자”**<p>앱의 관점에서 본 IdP의 식별자(때로는 “발급자 ID”라고 함)입니다.<p>SAML 토큰에서 값은 발급자 요소로 표시됩니다.| AD FS에 대한 식별자는 일반적으로 AD FS 관리의 **서비스 > 페더레이션 서비스 속성 편집** 아래에 있는 페더레이션 서비스 식별자입니다. 예: `http://fs.contoso.com/adfs/services/trust`| [tenant_id]를 테넌트 ID로 바꿉니다.<p>https:\//sts.windows.net/{tenant-id}/ |
| **IdP 페더레이션 메타데이터**<p>공개적으로 사용할 수 있는 IdP의 페더레이션 메타데이터에 대한 위치입니다. (일부 앱은 URL, 식별자 및 토큰 서명 인증서를 개별적으로 구성하는 관리자 대신 연합 메타데이터를 사용합니다.)| AD FS 페더레이션 메타데이터 URL은 AD FS 관리의 **서비스 > 엔드포인트 > 메타데이터 > 형식: 페더레이션 메타데이터** 아래에 있습니다. 예: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD의 해당 값은 [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)과 같은 패턴을 따릅니다. {TenantDomainName} 값을 “contoso.onmicrosoft.com” 형식의 테넌트 이름으로 바꿉니다.   <p>자세한 내용은 [페더레이션 메타데이터](../azuread-dev/azure-ad-federation-metadata.md)를 참조하세요. |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD에서 AD FS 보안 정책 표시

앱 인증을 Azure AD로 이동하는 경우 기존 보안 정책을 Azure AD에서 사용할 수 있는 동등하거나 대체 가능한 변형 형식으로 매핑합니다. 앱 소유자가 요구하는 보안 표준을 충족하면서 해당 매핑을 수행하면 나머지 앱 마이그레이션을 훨씬 쉽게 진행할 수 있습니다.

각 규칙 예제에서는 AD FS에서의 규칙의 예시, AD FS 규칙 언어와 동일한 코드, 그리고 이를 Azure AD에 매핑되는 방법을 확인할 수 있습니다.

### <a name="map-authorization-rules"></a>권한 부여 규칙 매핑

다음은 AD FS의 다양한 유형의 권한 부여 규칙과 Azure AD에 매핑하는 방법의 예입니다.

#### <a name="example-1-permit-access-to-all-users"></a>예제 1: 모든 사용자에게 액세스 허용

AD FS에서 모든 사용자에게 액세스 허용:

  ![SAML로 Single Sign-On 설정 대화상자를 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

다음 방법 중 하나로 Azure AD에 매핑됩니다.

1. **사용자 할당 필요** 를 **아니요** 로 설정합니다.

    ![SaaS 앱에 대한 액세스 제어 정책 편집 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > **사용자 할당 필요** 를 **예** 로 설정하려면 애플리케이션에 사용자를 할당하여 액세스 권한을 얻어야 합니다. **아니요** 로 설정하면 모든 사용자가 액세스할 수 있습니다. 해당 스위치는 **내 앱** 환경에서 사용자에게 표시되는 항목을 제어하지 않습니다.

1. **사용자 및 그룹 탭** 에서 애플리케이션을 **모든 사용자** 자동 그룹에 할당합니다. 기본 **모든 사용자** 그룹을 사용할 수 있으려면 Azure AD 테넌트에서 [동적 그룹을 사용하도록 설정](../enterprise-users/groups-create-rule.md)해야 합니다.

    ![Azure AD의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>예제 2: 그룹을 명시적으로 허용

AD FS에서 명시적 그룹 권한 부여:

  ![도메인 관리자 클레임 허용 규칙에 대한 규칙 편집 대화 상자를 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

해당 규칙을 Azure AD에 매핑하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 AD FS 사용자 그룹에 해당하는 [사용자 그룹을 만듭니다](../fundamentals/active-directory-groups-create-azure-portal.md).
1. 그룹에 앱 권한을 할당합니다.

    ![할당 추가 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>예 3: 특정 사용자에게 권한 부여

AD FS에서 명시적 사용자 권한 부여:

  ![들어오는 클레임 유형이 기본 SID인 특정 사용자 허용 클레임 규칙에 대한 규칙 편집 대화 상자를 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

해당 규칙을 Azure AD에 매핑하려면 다음을 수행합니다.

* [Azure Portal](https://portal.azure.com/)에서 아래와 같이 앱의 할당 추가 탭을 통해 앱에 사용자를 추가합니다.

    ![Azure의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>다단계 인증 규칙 매핑

[MFA(다단계 인증)](../authentication/concept-mfa-howitworks.md) 및 AD FS의 온-프레미스 배포는 AD FS와 페더레이션되기 때문에 마이그레이션 후에도 계속 작동합니다. 그러나 Azure AD의 조건부 액세스 워크플로에 연결된 Azure의 기본 제공 MFA 기능으로 마이그레이션하는 것이 좋습니다.

다음은 AD FS의 MFA 규칙 유형 예시와 다양한 조건에 따라 Azure AD에 매핑하는 방법입니다.

AD FS의 MFA 규칙 설정:

  ![Azure Portal에서 Azure AD에 대한 조건을 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>예제 1: 사용자/그룹에 따른 MFA 적용

사용자/그룹 선택기는 그룹별(그룹 SID) 또는 사용자별(기본 SID)로 MFA를 적용할 수 있는 규칙입니다. 사용자/그룹 할당 외에도 AD FS MFA 구성 UI의 모든 추가 확인란은 사용자/그룹 규칙이 적용된 후 평가되는 추가 규칙으로 작동합니다.

Azure AD에서 사용자 또는 그룹에 대한 MFA 규칙을 지정합니다.

1. [새 조건부 액세스 정책](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)을 만듭니다.
1. **할당** 을 선택합니다. MFA를 적용하려는 사용자 또는 그룹을 추가합니다.
1. 아래와 같이 **액세스 제어** 옵션을 구성합니다.

    ‎![액세스 권한을 부여할 수 있는 권한 부여 창을 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

#### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>예제 2: 등록되지 않은 디바이스에 MFA 적용

Azure AD에서 등록되지 않은 디바이스에 대한 MFA 규칙을 지정합니다.

1. [새 조건부 액세스 정책](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)을 만듭니다.
1. **할당** 을 **모든 사용자** 로 설정합니다.
1. 아래와 같이 **액세스 제어** 옵션을 구성합니다.

    ![액세스 권한을 부여하고 기타 제한을 지정할 수 있는 권한 부여 창을 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

**여러 컨트롤의 경우** 옵션을 **선택된 컨트롤 중 하나가 필요함** 으로 설정하면 사용자가 해당 확인란에 지정된 조건 중 하나라도 충족하는 경우 사용자에게 앱에 대한 액세스 권한을 부여합니다.

#### <a name="example-3-enforce-mfa-based-on-location"></a>예제 3: 위치에 따른 MFA 적용

Azure AD에서 사용자의 위치에 따른 MFA 규칙을 지정합니다.

1. [새 조건부 액세스 정책](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)을 만듭니다.
1. **할당** 을 **모든 사용자** 로 설정합니다.
1. [Azure AD에서 명명된 위치를 구성합니다](../conditional-access/location-condition.md). 위치를 구성하지 않은 경우 회사 네트워크 내부의 페더레이션을 신뢰합니다.
1. **조건 규칙** 을 구성하여 MFA를 적용할 위치를 지정합니다.

    ![조건 규칙을 위한 위치 창을 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. 아래와 같이 **액세스 제어** 옵션을 구성합니다.

    ![액세스 제어 정책 매핑](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>내보내기 특성을 클레임 규칙으로 매핑

AD FS에서 클레임 규칙으로서의 내보내기 특성:

  ![클레임으로서의 내보내기 특성에 대한 규칙 편집 대화 상자를 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

규칙을 Azure AD에 매핑하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **엔터프라이즈 애플리케이션** 을 선택하고 **Single Sign-On** 을 선택하여 SAML 기반 로그온 구성을 확인합니다.

    ![엔터프라이즈 애플리케이션에 대한 Single Sign-On 페이지를 보여주는 스크린샷.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. **편집**(강조 표시됨)을 선택하여 특성을 수정합니다.

    ![사용자 특성 및 클레임을 편집하는 페이지](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>기본 제공 액세스 제어 정책 매핑

AD FS 2016의 기본 제공 액세스 제어 정책:

  ![Azure AD 기본 제공 액세스 제어](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Azure AD에서 기본 제공 정책을 구현하려면 [새 조건부 액세스 정책](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)을 사용하고 액세스 제어를 구성하거나 AD FS 2016에서 사용자 지정 정책 디자이너를 사용하여 액세스 제어 정책을 구성합니다. 규칙 편집기에는 모든 종류의 순열을 만드는 데 도움이 되는 전체 허용 및 제외 옵션 목록이 있습니다.

  ![Azure AD 액세스 제어 정책](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

이 표에는 몇 가지 유용한 허용 및 제외 옵션과 Azure AD에 매핑하는 방법이 나와 있습니다.

| 옵션 | Azure AD에서 허용 옵션을 구성하는 방법| Azure AD에서 제외 옵션을 구성하는 방법 |
| - | - | - |
| 특정 네트워크| Azure AD의 [명명된 위치](../conditional-access/location-condition.md)에 매핑| [신뢰할 수 있는 위치](../conditional-access/location-condition.md)에 **제외** 옵션 사용 |
| 특정 그룹| [사용자/그룹 할당 설정](assign-user-or-group-access-portal.md)| 사용자 및 그룹에서 **제외** 옵션 사용 |
| 특정 신뢰 수준이 있는 디바이스| 할당 -> 조건에 있는 **디바이스 상태** 에서 설정| 디바이스 상태 조건에서 **제외** 옵션을 사용하고 **모든 디바이스** 포함 |
| 요청의 특정 클레임| 해당 설정은 마이그레이션할 수 없음| 해당 설정은 마이그레이션할 수 없음 |

Azure Portal에서 신뢰할 수 있는 위치에 대해 제외 옵션을 구성하는 방법의 예는 다음과 같습니다.

  ![액세스 제어 정책 매핑 스크린샷](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 사용자 전환

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD에서 AD FS 그룹 동기화

권한 부여 규칙을 매핑할 때 AD FS로 인증하는 앱은 권한에 대해 Active Directory 그룹을 사용할 수 있습니다. 이 경우 애플리케이션을 마이그레이션하기 전에 [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)를 사용하여 해당 그룹을 Azure AD와 동기화합니다. 애플리케이션을 마이그레이션할 때 동일한 사용자에게 액세스 권한을 부여하도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인해야 합니다.

자세한 내용은 [Active Directory에서 동기화된 그룹 특성을 사용하기 위한 필수 조건](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.

### <a name="set-up-user-self-provisioning"></a>사용자 자동 프로비저닝 설정

일부 SaaS 애플리케이션은 사용자가 애플리케이션에 처음 로그인할 때 사용자를 자동으로 프로비저닝하는 기능을 지원합니다. Azure AD에서 앱 프로비저닝은 사용자가 액세스해야 하는 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 애플리케이션에서 사용자 ID와 역할을 자동으로 만드는 것을 의미합니다. 마이그레이션된 사용자는 이미 SaaS 애플리케이션에 계정이 있습니다. 마이그레이션 후에 추가된 새 사용자는 모두 프로비저닝해야 합니다. 애플리케이션이 마이그레이션되면 [SaaS 앱 프로비저닝](../app-provisioning/user-provisioning.md)을 테스트합니다.

### <a name="sync-external-users-in-azure-ad"></a>Azure AD에서 외부 사용자 동기화

기존 외부 사용자는 AD FS에서 다음 두 가지 방법으로 설정할 수 있습니다.

* **조직 내 로컬 계정을 사용하는 외부 사용자** - 내부 사용자 계정이 작동하는 것과 같은 방식으로 해당 계정을 계속 사용할 수 있습니다. 해당 외부 사용자 계정에는 조직 내에 계정 이름이 있습니다. 단, 계정 메일은 외부를 가리킬 수 있습니다. 마이그레이션을 진행하면서 해당 ID를 사용할 수 있는 경우 해당 사용자가 자신의 회사 ID를 사용하도록 사용자를 마이그레이션하여 [Azure AD B2B](../external-identities/what-is-b2b.md)에서 제공하는 이점을 활용할 수 있습니다. 그러면 자체 회사 로그온으로 로그인하는 경우가 많은 해당 사용자에 대한 로그인 프로세스가 간소화됩니다. 외부 사용자에 대한 계정을 관리하지 않아도 되므로 조직의 관리도 더 쉬워집니다.
* **페더레이션된 외부 ID** - 현재 외부 조직과 페더레이션하는 경우 다음과 같은 몇 가지 방법을 사용할 수 있습니다.
  * [Azure Portal에서 Azure Active Directory B2B 협업 사용자를 추가합니다](../external-identities/add-users-administrator.md). 개별 구성원이 익숙한 앱과 자산을 계속 사용할 수 있도록 Azure AD 관리 포털에서 파트너 조직으로 B2B 협업 초대를 사전에 보낼 수 있습니다.
  * B2B 초대 API를 사용하여 파트너 조직의 개별 사용자에 대한 요청을 생성하는 [셀프 서비스 B2B 등록 워크플로를 만듭니다](../external-identities/self-service-portal.md).

기존 외부 사용자를 구성한 방법과 관계없이 그룹 멤버 자격 또는 특정 권한에서 해당 계정과 연결된 권한이 있을 수 있습니다. 해당 권한을 마이그레이션하거나 정리해야 하는지 평가합니다. 외부 사용자를 나타내는 조직 내의 계정은 사용자가 외부 ID로 마이그레이션된 후에 사용하지 않도록 설정해야 합니다. 마이그레이션 프로세스는 리소스에 연결하는 기능이 중단될 수 있으므로 비즈니스 파트너와 논의해야 합니다.

## <a name="migrate-and-test-your-apps"></a>앱 마이그레이션 및 테스트

이 문서에서 설명하는 마이그레이션 프로세스를 따른 후, [Azure Portal](https://aad.portal.azure.com/)로 이동하여 마이그레이션이 성공했는지 테스트합니다.

다음 지침을 따릅니다.

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택하고 목록에서 앱을 찾습니다.
1. **관리** > **사용자 및 그룹** 을 선택하여 앱에 하나 이상의 사용자 또는 그룹을 할당합니다.
1. **관리** > **조건부 액세스** 를 선택합니다. 정책 목록을 검토하고 [조건부 액세스 정책](../conditional-access/overview.md)을 사용하여 애플리케이션에 대한 액세스를 차단하지 않는지 확인합니다.

앱을 구성하는 방법에 따라 SSO가 제대로 작동하는지 확인합니다.

| 인증 유형| 테스트 |
| :- | :- |
| OAuth/OpenID Connect| **엔터프라이즈 애플리케이션 > 권한** 을 선택하고 앱의 사용자 설정에서 애플리케이션에 동의했는지 확인합니다.|
| SAML 기반 SSO | **Single Sign-On** 에 있는 [SAML 설정 테스트](debug-saml-sso-issues.md) 단추를 사용합니다. |
| 암호 기반 SSO |  [MyApps 보안 로그인](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)[-](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)[확장](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 다운로드하고 설치합니다. 해당 확장을 사용하면 SSO 프로세스를 사용해야 하는 조직의 클라우드 앱을 시작할 수 있습니다. |
| 애플리케이션 프록시 | 커넥터가 실행 중이고 애플리케이션에 할당되었는지 확인합니다. 자세한 내용은 [애플리케이션 프록시 문제 해결 가이드](../app-proxy/application-proxy-troubleshoot.md)를 참조하세요. |

> [!NOTE]
> 이전 AD FS 환경의 쿠키는 사용자 머신에 유지됩니다. 사용자가 새 Azure AD 로그인 대신 이전 AD FS 로그인 환경으로 이동할 수 있으므로 해당 쿠키는 마이그레이션 문제를 일으킬 수 있습니다. 사용자 브라우저 쿠키를 수동으로 삭제하거나 스크립트를 사용하여 삭제해야 할 수 있습니다. System Center Configuration Manager나 유사한 플랫폼을 사용할 수도 있습니다.

### <a name="troubleshoot"></a>문제 해결

마이그레이션된 애플리케이션의 테스트에서 오류가 발생하는 경우 기존 AD FS 신뢰 당사자로 돌아가기 전 문제 해결 첫 번째 단계일 수 있습니다. [Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](debug-saml-sso-issues.md)을 참조하세요.

### <a name="rollback-migration"></a>마이그레이션 롤백

마이그레이션이 실패하면 기존 신뢰 당사자를 AD FS 서버에 그대로 두고 신뢰 당사자에 대한 액세스 권한을 제거하는 것이 좋습니다. 그러면 배포 중에 필요한 경우 빠르게 대체할 수 있습니다.

### <a name="employee-communication"></a>직원 통신

계획된 가동 중단 창 자체는 최소화할 수 있지만, AD FS에서 Azure AD로 전환하는 동안 해당 기간을 직원들에게 사전에 전달하는 계획을 세워야 합니다. 앱 경험에 문제에 대한 피드백 단추나 기술 지원팀에 문의할 수 있는 경로가 있는지 확인합니다.

배포가 완료되면 성공적인 배포를 사용자에게 알리고 수행해야 하는 모든 단계를 알릴 수 있습니다.

* 사용자가 [내 앱](https://myapps.microsoft.com)을 사용하여 마이그레이션된 모든 애플리케이션에 액세스하도록 지시합니다.
* 사용자에게 MFA 설정을 업데이트해야 할 수 있음을 알려줍니다.
* 셀프 서비스 암호 재설정을 배포하는 경우 사용자는 인증 방법을 업데이트하거나 확인해야 할 수 있습니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조하세요.

### <a name="external-user-communication"></a>외부 사용자 통신

해당 사용자 그룹은 일반적으로 문제 발생 시 가장 심각한 영향을 받습니다. 보안 상태가 외부 파트너에 대한 다른 조건부 액세스 규칙이나 위험 프로필의 집합을 나타내는 경우 특히 심각한 영향을 받습니다. 외부 파트너가 클라우드 마이그레이션 일정을 알고 있는지 확인하고 외부 협업에 고유한 모든 흐름을 테스트하는 파일럿 배포에 참여하는 것이 권장되는 기간을 확보해야 합니다. 마지막으로 문제가 있는 경우 기술 지원팀에 액세스할 수 있는 방법이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 인증을 Azure AD로 마이그레이션](https://aka.ms/migrateapps/whitepaper)을 읽어보세요.
* [조건부 액세스](../conditional-access/overview.md) 및 [MFA](../authentication/concept-mfa-howitworks.md)를 설정합니다.
* 단계별 코드 샘플: [개발자를 위한 AD FS에서 Azure AD로의 애플리케이션 마이그레이션 플레이북](https://aka.ms/adfsplaybook)을 사용해보세요.