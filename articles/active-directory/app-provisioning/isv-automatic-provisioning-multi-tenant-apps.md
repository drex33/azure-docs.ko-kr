---
title: Azure Active Directory의 다중 테넌트 애플리케이션에 대해 자동 사용자 프로비저닝 사용
description: Azure Active Directory에서 자동화된 프로비저닝을 활성화하기 위한 독립 소프트웨어 공급 업체용 가이드
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: zhchia, arvinh
ms.openlocfilehash: 9dc13785f0cd493127cee3937f3e14e1750b93d7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991245"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application-in-azure-active-directory"></a>Azure Active Directory의 다중 테넌트 애플리케이션에 대해 자동 사용자 프로비저닝 사용

자동 사용자 프로비저닝은 SaaS(Software as a Service) 애플리케이션과 같은 대상 시스템에서 사용자 ID의 생성, 유지 관리 및 제거를 자동화하는 프로세스입니다.

## <a name="why-enable-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 사용하는 이유

사용자의 첫 번째 로그인 전에 애플리케이션에 사용자 레코드가 있어야 하는 애플리케이션은 사용자 프로비저닝이 필요합니다. 서비스 공급자와 고객이 누릴 수 있는 이점이 각각 있습니다.

### <a name="benefits-to-you-as-the-service-provider"></a>서비스 공급자의 이점

* Microsoft ID 플랫폼을 사용하여 애플리케이션의 보안을 강화합니다.

* 고객이 애플리케이션을 도입하기 위해 실제로 투입하는 노력 및 인식하는 노력을 줄입니다.

* SCIM(도메인 간 ID 관리 시스템) 기반 프로비저닝을 사용하여 자동 사용자 프로비저닝을 위해 여러 IdP(ID 공급자)와 통합하는 비용을 절감합니다.

* 고객이 사용자 프로비저닝 문제를 해결하는 데 도움이 되는 풍부한 로그를 제공하여 지원 비용을 낮춥니다.

* [Azure AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps)에서 애플리케이션 가시성을 높입니다.

* 앱 자습서 페이지에서 우선 순위가 지정된 목록을 가져옵니다.

### <a name="benefits-to-your-customers"></a>고객의 이점

* 사용자가 역할을 변경하거나 퇴사하는 경우 애플리케이션에 대한 액세스를 자동으로 제거하여 보안을 강화합니다.

* 수동 프로비저닝과 관련된 인적 오류 및 반복 작업을 방지하여 애플리케이션에 대한 사용자 관리를 간소화합니다.

* 사용자 지정 개발된 프로비저닝 솔루션을 호스트하고 유지 관리하는 비용을 줄입니다.

## <a name="choose-a-provisioning-method"></a>프로비전 메서드 선택

Azure AD는 애플리케이션에 대한 자동 사용자 프로비저닝을 사용할 수 있도록 여러 통합 경로를 제공합니다.

* [Azure AD 프로비저닝 서비스](../app-provisioning/user-provisioning.md)는 Azure AD에서 애플리케이션으로 사용자 프로비저닝 및 프로비저닝 해제(아웃바운드 프로비저닝)와 애플리케이션에서 Azure AD로 사용자 프로비저닝 및 프로비저닝 해제(인바운드 프로비저닝)를 관리합니다. 이 서비스는 애플리케이션에서 제공하는 SCIM(도메인 간 ID 관리 시스템) 사용자 관리 API 엔드포인트에 연결합니다.

* [Microsoft Graph](/graph/)를 사용하는 경우 애플리케이션은 Microsoft Graph API를 쿼리하여 사용자 및 그룹의 Azure AD에서 애플리케이션으로 인바운드 및 아웃바운드 프로비저닝을 관리합니다.

* 애플리케이션이 페더레이션에 SAML을 사용하는 경우에는 SAML JIT(Security Assertion Markup Language Just-in-time) 사용자 프로비저닝을 사용하도록 설정할 수 있습니다. SAML 토큰에 전송되는 클레임 정보를 사용하여 사용자를 프로비저닝합니다.

애플리케이션에 사용할 통합 옵션을 결정하려면 상위 수준 비교 표를 참조하고 각 옵션에 대한 자세한 정보를 확인하세요.

| 자동 프로비저닝을 통해 사용 또는 향상되는 기능| Azure AD 프로비저닝 서비스(SCIM 2.0)| Microsoft Graph API(OData v4.0)| SAML JIT |
|---|---|---|---|
| Azure AD에서 사용자 및 그룹 관리| √| √| 사용자만 |
| 온-프레미스 Active Directory에서 동기화된 사용자 및 그룹 관리| √*| √*| 사용자만* |
| 프로비저닝하는 동안 사용자 및 그룹 외의 데이터에 액세스. Microsoft 365 데이터(Teams, SharePoint, 이메일, 일정, 문서 등)에 대한 액세스| X+| √| X |
| 비즈니스 규칙에 따라 사용자 만들기, 읽기 및 업데이트| √| √| √ |
| 비즈니스 규칙에 따라 사용자 삭제| √| √| X |
| Azure Portal의 모든 애플리케이션에 대한 자동 사용자 프로비저닝 관리| √| X| √ |
| 다중 ID 공급자 지원| √| X| √ |
| 게스트 계정 지원(B2B)| √| √| √ |
| 비 엔터프라이즈 계정 지원(B2C)| X| √| √ |

<sup>*</sup> – AD에서 Azure AD로 사용자를 동기화하려면 Azure AD Connect를 설치해야 합니다.  
<sup>+</sup > – 프로비저닝에 SCIM을 사용하는 경우에도 다른 용도로 애플리케이션을 Microsoft Graph와 통합할 수 있습니다.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 프로비저닝 서비스(SCIM)

Azure AD 프로비저닝 서비스는 다양한 IdP(ID 공급자) 및 애플리케이션(예: Slack, G Suite, Dropbox)에서 지원되는 프로비저닝을 위한 산업 표준인 [SCIM](https://aka.ms/SCIMOverview)을 사용합니다. Azure AD 외에 IdP를 지원하려는 경우 SCIM 규격 IdP가 SCIM 엔드포인트에 연결할 수 있으므로 Azure AD 프로비저닝 서비스를 사용하는 것이 좋습니다. 간단한 /User 엔드포인트를 빌드하면 자체 동기화 엔진을 유지 관리할 필요 없이 프로비저닝을 사용하도록 설정할 수 있습니다. 

Azure AD 프로비저닝 서비스가 SCIM을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요. 

* [SCIM 표준에 대한 자세한 정보](https://aka.ms/SCIMOverview)

* [SCIM(System for Cross-Domain Identity Management)을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동 프로비전](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD SCIM 구현 이해](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>프로비저닝을 위한 Microsoft Graph

프로비저닝에 Microsoft Graph를 사용하는 경우 Graph에서 사용할 수 있는 모든 풍부한 사용자 데이터에 액세스할 수 있습니다. 사용자 및 그룹의 세부 정보 외에도 사용자의 역할, 관리자 및 부하 직원, 소유 및 등록된 디바이스, [Microsoft Graph](/graph/api/overview)에서 사용할 수 있는 기타 수백 가지 데이터를 포함한 추가 정보를 가져올 수 있습니다. 

1,500만 개 이상의 조직 및 Fortune 500대 회사 중 90%는 Microsoft 365, Microsoft Azure 또는 Enterprise Mobility Suite와 같은 Microsoft 클라우드 서비스를 구독하면서 Azure AD를 사용합니다. Microsoft Graph를 사용하여 앱을 직원 등록(및 해지), 프로필 유지 관리 등의 관리 워크플로와 통합할 수 있습니다. 

프로비저닝에 Microsoft Graph를 사용하는 방법에 대해 자세히 알아보세요.

* [Microsoft Graph 홈페이지](https://developer.microsoft.com/graph)

* [Microsoft Graph 개요](/graph/overview)

* [Microsoft Graph 인증 개요](/graph/auth/)

* [Microsoft Graph 시작](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>프로비저닝에 SAML JIT 사용

애플리케이션에 처음 로그인할 때만 사용자를 프로비저닝하고 사용자를 자동으로 프로비저닝 해제하지 않아도 되는 경우 SAML JIT가 한 가지 옵션입니다. 애플리케이션은 SAML JIT를 사용하는 페더레이션 프로토콜로 SAML 2.0을 지원해야 합니다.

SAML JIT는 SAML 토큰의 클레임 정보를 사용하여 애플리케이션에서 사용자 정보를 만들고 업데이트합니다. 고객은 필요에 따라 Azure AD 애플리케이션에서 이러한 필수 클레임을 구성할 수 있습니다. 경우에 따라 애플리케이션 쪽에서 JIT 프로비저닝을 사용하도록 설정해야 고객이 이 기능을 사용할 수 있습니다. SAML JIT는 사용자를 만들고 업데이트하는 데 유용하지만 애플리케이션에서 사용자를 삭제하거나 비활성화할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션에 Single Sign-on을 사용하도록 설정합니다](../develop/v2-howto-app-gallery-listing.md).

* [애플리케이션 리스팅을 제출](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)하고 Microsoft와 파트너를 제휴하여 Microsoft의 사이트에 대한 설명서를 작성합니다.

* [Microsoft 파트너 네트워크(무료)에 참여하고 출시 계획을 작성합니다](https://partner.microsoft.com/explore/commercial).
