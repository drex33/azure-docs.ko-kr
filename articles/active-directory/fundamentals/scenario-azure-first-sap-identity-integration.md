---
title: 시나리오 - Azure Active Directory를 사용하여 SAP 플랫폼 및 애플리케이션에 대한 액세스 보호
description: SAP 플랫폼 및 애플리케이션에 대한 액세스를 보호하는 방법에 대한 설계자 및 IT 관리자용 가이드입니다.
services: active-directory
author: xstof
manager: alberts
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: christoc
ms.reviewer: ''
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 2c987a951cbaf3795757ab0b57e8dcea0eb47aee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064426"
---
# <a name="scenario---using-azure-active-directory-to-secure-access-to-sap-platforms-and-applications"></a>시나리오 - Azure Active Directory를 사용하여 SAP 플랫폼 및 애플리케이션에 대한 액세스 보호

이 문서에서는 Azure Active Directory를 기본 사용자 인증 서비스로 사용할 때 SAP 플랫폼 및 애플리케이션의 기술 설계 및 구성에 대한 조언을 제공합니다.

## <a name="terminology-used-in-this-guide"></a>이 가이드에서 사용되는 용어

| 약어                                                                                                          | 설명                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [BTP](https://www.sap.com/products/business-technology-platform.html)                                                 | SAP Business Technology Platform. SAP의 전체 기술 제품입니다. 여기서 설명하는 대부분의 SAP 기술은 BTP의 일부입니다. 공식적으로 SAP Cloud Platform으로 알려진 제품은 SAP BTP의 일부입니다. |
| [IAS](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US)                                       | SAP Cloud Identity Services - Identity Authentication Service. SAP에서 제공하는 다중 테넌트 클라우드 ID 공급자 서비스입니다. IAS는 사용자가 자신의 SAP 서비스 인스턴스에 인증하는 데 도움이 됩니다.           |
| [IDS](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d6a8db70bdde459f92f2837349f95090.html) | SAP ID Service. 고객과 파트너를 SAP에서 운영하는 PaaS 및 SaaS 서비스에 인증하기 위해 SAP에서 사용하는 IAS의 인스턴스입니다.                                                                                 |
| [IPS](https://help.sap.com/viewer/f48e822d6d484fa5ade7dda78b64d9f5/Cloud/en-US/2d2685d469a54a56b886105a06ccdae6.html) | SAP Cloud Identity Services - Identity Provisioning Service.  IPS는 ID를 여러 저장소/대상 시스템 간에 동기화하는 데 도움이 됩니다.                                                           |
| [XSUAA](https://blogs.sap.com/2019/01/07/uaa-xsuaa-platform-uaa-cfuaa-what-is-it-all-about/)                          | Extended Services for Cloud Foundry User Account and Authentication.  XSUAA는 SAP BTP 내의 다중 테넌트 OAuth 권한 부여 서버입니다.                                                                   |
| [CF](https://www.cloudfoundry.org/)                                                                                   | Cloud Foundry. Cloud Foundry는 SAP에서 BTP에 대한 다중 클라우드 제품(AWS, Azure, GCP, Alibaba)을 구축한 환경입니다.                                                                                      |
| [Fiori](https://www.sap.com/products/fiori/develop.html)                                                              | SAP의 웹 기반 사용자 환경입니다(데스크톱 기반 환경과 반대).                                                                                                                            |

## <a name="overview"></a>개요

SAP 및 Microsoft 기술 스택에는 사용자 인증 및 권한 부여 시나리오에서 역할을 수행하는 많은 서비스와 구성 요소가 있습니다. 기본 서비스는 아래 다이어그램에 나와 있습니다.

![SAP 환경 개요](./media/scenario-azure-first-sap-identity-integration/sap-landscape-overview.png)

구성할 수 있는 시나리오의 순열이 많으므로 Azure AD ID 우선 전략에 부합하는 한 시나리오에 집중합니다. 다음과 같이 가정합니다.

- 모든 ID를 Azure AD에서만 중앙에서 제어하려고 합니다.
- 유지 관리 노력을 최대한 줄이고 Microsoft 및 SAP에서 인증 및 앱 액세스를 자동화하려고 합니다.
- IAS를 사용하는 Azure AD에 대한 일반 지침은 BTP에 배포된 앱 및 IAS에 구성된 SAP SaaS 앱에 적용됩니다. BTP(예: Azure AD 그룹과의 역할 매핑 사용) 및 SAP SaaS 앱(예: 역할 기반 권한 부여에 ID 프로비전 서비스 사용)에 적용할 수 있는 특정 권장 사항도 제공됩니다.
- 또한 사용자가 Azure AD에서 이미 프로비전되어 있고 사용자가 작동하도록 프로비전되어야 하는 모든 SAP 시스템에 프로비전되었다고 가정합니다.  이를 달성하는 방법에 관계없이 프로비전은 수동으로, 온-프레미스 Active Directory에서 Azure AD Connect를 통해 또는 HR 시스템(예: SAP SuccessFactors)을 통해 수행될 수 있습니다. 따라서 이 문서에서 SuccessFactors는 사용자(기존)가 로그온하는 다른 애플리케이션과 같은 애플리케이션으로 간주됩니다.  SuccessFactors에서 Azure AD로의 사용자 프로비전은 다루지 않습니다.

이러한 가정을 기반으로 하여 아래 다이어그램에 제시된 제품 및 서비스에 주로 초점을 맞춥니다. 이러한 구성 요소는 클라우드 기반 환경에서 인증 및 권한 부여와 가장 관련성이 높은 다양한 구성 요소입니다.

![범위 내 SAP 서비스](./media/scenario-azure-first-sap-identity-integration/sap-services-in-scope.png)

## <a name="recommendations"></a>권장 사항

### <a name="summary"></a>요약

- [1 - SAP Identity Authentication Service를 통해 SAP Business Technology Platform 및 SAP SaaS 애플리케이션에서 페더레이션 인증 사용](#1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service)
- [2 - 인증에 Azure AD 사용 및 권한 부여에 IAS/BTP 사용](#2---use-azure-ad-for-authentication-and-iasbtp-for-authorization)
- [3 - IAS/BTP의 역할 컬렉션을 통해 권한 부여 Azure AD 그룹 사용](#3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp)
- [4 - ID 요구 사항이 비슷한 애플리케이션에만 단일 BTP 하위 계정 사용](#4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements)
- [5 - 모든 최종 사용자 인증 및 권한 부여에 프로덕션 IAS 테넌트 사용](#5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization)
- [6 - SAML 서명 인증서 롤오버 프로세스 정의](#6---define-a-process-for-rollover-of-saml-signing-certificates)

### <a name="1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service"></a>1 - SAP Identity Authentication Service를 통해 SAP Business Technology Platform 및 SAP SaaS 애플리케이션에서 페더레이션 인증 사용

#### <a name="context"></a>컨텍스트

BTP의 애플리케이션은 BTP/XSUAA와 ID 공급자 간에 SAML 2.0 프로토콜을 사용하여 사용자를 인증하기 위해 [신뢰 구성](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/cb1bc8f1bd5c482e891063960d7acd78.html)을 통해 ID 공급자를 사용할 수 있습니다.  OpenID Connect 프로토콜에서 애플리케이션 자체와 BTP/XSUAA 사이에 사용되더라도 SAML 2.0만 지원됩니다(이 컨텍스트에서는 관련이 없음).

BTP에서 SAP ID Service(기본값)에 대한 신뢰 구성을 설정할 수 있지만, 권한 있는 사용자 디렉터리가 Azure AD인 경우 사용자가 기존 Azure AD 계정을 사용하여 로그인할 수 있도록 **페더레이션** 을 설정할 수 있습니다.

페더레이션 외에도 필요에 따라 Azure AD 사용자가 BTP에서 미리 프로비전되도록 **사용자 프로비저닝** 을 설정할 수도 있습니다. 그러나 이에 대한 기본 지원은 없습니다(Azure AD -> SAP Identity Authentication Service에만 해당). 기본 지원과 통합된 통합 솔루션이 BTP Identity Provisioning Service입니다. 사용자 계정을 미리 프로비전하면 권한 부여 목적(예: 역할에 사용자 추가)에 유용할 수 있습니다. 그러나 요구 사항에 따라 이는 Azure AD 그룹(아래 참조)을 사용하여 달성할 수도 있습니다. 즉, 사용자 프로비저닝이 전혀 필요하지 않을 수 있습니다.

페더레이션 관계를 설정하는 경우 다음과 같은 여러 옵션이 있습니다.

- BTP/XSUAA에서 직접 Azure AD로 페더레이션하도록 선택할 수 있습니다.
- 회사 ID 공급자("SAML 프록시"라고도 함)로 Azure AD와 페더레이션하도록 설정된 IAS와의 페더레이션하도록 선택할 수 있습니다.

SAP SaaS 애플리케이션의 경우 최종 사용자의 간편한 온보딩을 위해 IAS가 프로비전되고 미리 구성됩니다.  (예: SuccessFactors, Marketing Cloud, Cloud4Customer, Sales Cloud 등이 있습니다.) IAS가 대상 앱과 직접 연결되고 프록시가 XSUAA에 설정되지 않으므로 이 시나리오는 덜 복잡합니다. 어떤 경우든 이 설정에는 일반적으로 IAS가 있는 Azure AD와 동일한 규칙이 적용됩니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

신뢰할 수 있는 사용자 디렉터리가 Azure AD인 경우 BTP에서 IAS에 대한 신뢰 구성을 설정하는 것이 좋습니다.  IAS는 이에 따라 회사 ID 공급자로 Azure AD와 페더레이션하도록 설정됩니다.

![SAP 신뢰 구성](./media/scenario-azure-first-sap-identity-integration/sap-trust-configuration.png)

BTP의 신뢰 구성에서 "로그온하는 동안 섀도 사용자 만들기"를 사용하도록 설정하는 것이 좋습니다.  이렇게 하면 BTP에서 아직 만들지 않은 사용자가 IAS/Azure AD를 통해 처음 로그인할 때 계정을 자동으로 얻습니다. 이 설정을 사용하지 않도록 설정하면 미리 프로비전된 사용자만 로그인할 수 있습니다.

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

페더레이션을 사용하는 경우 BTP 하위 계정 수준에서 신뢰 구성을 정의하도록 선택할 수 있습니다. 이 경우 사용하는 다른 하위 계정에 대해 구성을 반복해야 합니다. IAS를 중간 신뢰 구성으로 사용하면 여러 하위 계정에서 중앙 집중식 구성을 활용할 수 있으며, [위험 기반 인증](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/bc52fbf3d59447bbb6aa22f80d8b6056.html) 및 중앙 집중식 [어설션 특성 보강](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/7124201682434efb946e1046fde06afe.html)과 같은 IAS 기능을 사용할 수 있습니다. 사용자 환경을 보호하려면 이러한 고급 보안 기능을 단일 위치에만 적용해야 합니다. 이는 IAS이거나 Azure AD를 신뢰할 수 있는 단일 사용자 저장소로 유지하는 경우(이 백서의 전제와 같이) Azure AD [조건부 액세스 관리](../conditional-access/overview.md)에서 중앙에서 처리됩니다.

참고: IAS에서는 해당 하위 계정 내에서 하나 이상의 애플리케이션을 배포할 수 있더라도 모든 하위 계정은 "애플리케이션"으로 간주됩니다.  IAS 내에서 이러한 모든 애플리케이션은 동일한 회사 ID 공급자(이 경우 Azure AD)와 페더레이션하도록 설정할 수 있습니다.

#### <a name="summary-of-implementation"></a>구현 요약

Azure AD:

- 필요에 따라 [Azure AD를 Seamless SSO(Seamless Single Sign-On)로 구성](../hybrid/how-to-connect-sso.md)합니다. 그러면 사용자가 회사 네트워크에 연결된 회사 디바이스에 있을 때 자동으로 로그인됩니다. 이 기능을 사용하도록 설정되면 사용자가 Azure AD에 로그인하는 데 암호를 입력할 필요가 없고, 일반적으로 사용자 이름으로도 입력할 수 있습니다.

Azure AD 및 IAS:

- 설명서에 따라 Azure AD를 페더레이션(프록시) 모드에서 IAS에 연결합니다([SAP 문서](https://developers.sap.com/tutorials/cp-ias-azure-ad.html), [Microsoft 문서](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)). UPN이 반드시 이메일 주소일 필요는 없으므로 Azure AD의 SSO 구성에 대한 `NameID` 설정에 확인합니다.
- "[조건부 인증](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/0143dce88a604533ab5ab17e639fec09.html)" 페이지로 이동하고 "기본 인증 ID 공급자"를 Azure AD 디렉터리를 나타내는 회사 ID 공급자로 설정하여 Azure AD를 사용하도록 "번들 애플리케이션"을 구성합니다.

BTP:

- IAS([SAP 문서](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/7c6aa87459764b179aeccadccd4f91f3.html#loio7c6aa87459764b179aeccadccd4f91f3))에 대한 신뢰 구성을 설정하고, "[사용자 로그온에 사용 가능](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/LATEST/en-US/affb201b1a36497996c2144c28683aed.html)" 및 "로그온하는 동안 섀도 사용자 만들기"가 모두 사용하도록 설정되어 있는지 확인합니다.
- 필요에 따라 사용자가 항상 Azure AD를 통해 인증하고 ID 공급자를 선택할 수 있는 화면이 표시되지 않도록 기본 "SAP ID 서비스" 신뢰 구성에서 "사용자 로그온에 사용 가능"을 사용하지 않도록 설정합니다.

### <a name="2---use-azure-ad-for-authentication-and-iasbtp-for-authorization"></a>2 - 인증에 Azure AD 사용 및 권한 부여에 IAS/BTP 사용

#### <a name="context"></a>컨텍스트

Azure AD에 대한 페더레이션을 통해 사용자 **인증** 에 대해 BTP 및 IAS가 구성된 경우 **권한 부여** 를 구성하기 위한 여러 옵션이 있습니다.

- Azure AD에서 Azure AD 사용자 및 그룹을 Azure AD의 SAP IAS 인스턴스를 나타내는 엔터프라이즈 애플리케이션에 할당할 수 있습니다.
- IAS에서 위험 기반 인증을 사용하여 로그인을 허용하거나 차단하고, BTP의 애플리케이션에 대한 액세스를 방지하는 작업을 수행할 수 있습니다.
- BTP에서 역할 컬렉션을 사용하여 애플리케이션에 액세스하고, 특정 역할을 얻을 수 있는 사용자 및 그룹을 정의할 수 있습니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

Azure AD 자체에서 권한을 직접 부여하지 않고 Azure AD의 엔터프라이즈 애플리케이션에서 "[사용자 할당 필요](../manage-apps/assign-user-or-group-access-portal.md)"를 명시적으로 해제하는 것이 좋습니다. SAML 애플리케이션의 경우 이 설정은 기본적으로 사용하도록 설정되어 있으므로 이를 사용하지 않도록 설정하는 명시적 작업을 수행해야 합니다.

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

애플리케이션이 IAS를 통해 페더레이션되면 Azure AD의 관점에서 사용자는 로그인 흐름 중에 기본적으로 "IAS에 인증"됩니다.  즉, Azure AD에는 사용자가 로그인을 시도하는 최종 BTP 애플리케이션에 대한 정보가 없습니다. 또한 Azure AD의 권한 부여는 매우 정교하지 않은 권한 부여를 수행하는 데만 사용할 수 있음을 의미합니다. 예를 들어 사용자가 BTP의 *모든* 애플리케이션에 로그인할 수 있도록 허용하거나 로그인할 수 *없도록* 허용합니다. 또한 이는 BTP 하위 계정 수준에서 앱과 인증 메커니즘을 격리하는 SAP의 전략을 강조합니다.

이는 "사용자 할당 필요"를 사용하는 유효한 이유가 될 수 있지만, 이제 권한 부여 정보를 유지 관리해야 하는 두 개의 다른 위치가 있음을 의미합니다. 두 위치는 모두 엔터프라이즈 애플리케이션의 Azure AD(여기서는 *모든* BTP 애플리케이션에 적용) 및 각 BTP 하위 계정에 있습니다. 이로 인해 권한 부여 설정이 한 곳에서는 업데이트되고 다른 곳에서는 업데이트되지 않는 혼동 및 잘못된 구성이 발생할 수 있습니다. 예: 사용자가 BTP에서 허용되었지만 Azure AD의 애플리케이션에 할당되지 않아 인증이 실패했습니다.

#### <a name="summary-of-implementation"></a>구현 요약

IAS와의 페더레이션 관계를 나타내는 Azure AD 엔터프라이즈 애플리케이션에서 "[사용자 할당 필요](../manage-apps/assign-user-or-group-access-portal.md)"를 사용하지 않도록 설정합니다. 또한 이는 [Microsoft Docs에서 설명한 대로 사용자 할당](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md#assign-the-azure-ad-test-user)을 안전하게 건너뛸 수 있음을 의미합니다.

### <a name="3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp"></a>3 - IAS/BTP의 역할 컬렉션을 통해 권한 부여 Azure AD 그룹 사용

#### <a name="context"></a>컨텍스트

BTP 애플리케이션에 대한 권한 부여를 구성하려는 경우 여러 옵션이 있습니다.

- 로그인한 사용자를 기반으로 하여 애플리케이션 자체 내에서 세분화된 액세스 제어를 구성할 수 있습니다.
- 사용자 할당 또는 그룹 할당을 기반으로 하여 BTP의 역할 및 역할 컬렉션을 통해 액세스를 지정할 수 있습니다.

최종 구현에서는 두 전략의 조합을 사용할 수 있습니다. 그러나 역할 컬렉션을 통해 할당하는 경우 이 작업은 사용자 단위로 수행하거나 구성된 ID 공급자의 그룹을 사용할 수 있습니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

세분화된 권한 부여를 위해 Azure AD를 신뢰할 수 있는 원본으로 사용하려는 경우 Azure AD 그룹을 사용하고 BTP의 역할 컬렉션에 할당하는 것이 좋습니다. 사용자에게 특정 애플리케이션에 대한 액세스 권한을 부여하는 것은 단순히 IAS/BTP에서 추가로 구성할 필요 없이 사용자를 관련 Azure AD 그룹에 추가하는 것을 의미합니다.

이 구성을 사용하는 경우 표시 이름("sAMAccountName")이 아닌 Azure AD 그룹의 그룹 ID(개체 ID)를 그룹의 고유 식별자로 사용하는 것이 좋습니다. 즉, Azure AD에서 발급한 SAML 토큰에서 그룹 ID를 "그룹" 어설션으로 사용해야 합니다.  또한 그룹 ID는 BTP의 역할 컬렉션에 할당하는 데 사용됩니다.

![SAP에서 역할 컬렉션 사용](./media/scenario-azure-first-sap-identity-integration/sap-use-role-collections.png)

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

*사용자* 를 BTP의 역할 컬렉션에 직접 할당하는 경우 Azure AD에서 권한 부여 결정을 중앙화하지 않습니다. 또한 이는 사용자가 BTP의 역할 컬렉션에 할당되기 전에 IAS에 이미 있어야 함을 의미합니다. 그리고 사용자 프로비저닝 대신 페더레이션을 사용하는 것이 좋다는 점을 고려할 때 사용자 할당을 수행하려는 시점에서 사용자의 섀도 계정이 IAS에 아직 있지 않을 수 있음을 의미합니다. Azure AD 그룹을 사용하고 역할 컬렉션에 할당하면 이러한 문제가 제거됩니다.

그룹을 역할 컬렉션에 할당하는 것은 Azure AD를 *권한 부여* 에 사용하지 않는 이전 권장 사항과 모순될 수 있습니다. 그러나 이 경우에도 BTP에서 권한 부여 결정을 계속 수행하고 있으며, 이제 Azure AD에서 유지 관리되는 그룹 멤버 자격에 따라 결정 된다는 것입니다.

그룹 ID는 전역적으로 고유하고 변경할 수 없으며 나중에 다른 그룹에 다시 사용할 수 없으므로 이름 대신 Azure AD 그룹의 그룹 ID를 사용하는 것이 좋습니다. 반면 그룹 이름을 사용하면 이름이 변경될 때 문제가 발생할 수 있으며, 그룹이 삭제되고 다른 그룹을 동일한 이름으로 만들지만 애플리케이션에 액세스할 수 없어야 하는 사용자가 포함되는 보안 위험이 있습니다.

#### <a name="summary-of-implementation"></a>구현 요약

Azure AD:

- BTP의 애플리케이션에 액세스해야 하는 사용자를 추가할 수 있는 그룹을 만듭니다(예: BTP의 각 역할 컬렉션에 대한 Azure AD 그룹 만들기).
- IAS와의 페더레이션 관계를 나타내는 Azure AD 엔터프라이즈 애플리케이션에서 SAML 사용자 특성 및 클레임을 구성하여 [보안 그룹에 대한 그룹 클레임을 추가](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)합니다.
    - 원본 특성을 "그룹 ID"로 설정하고, 이름을 `Groups`로 설정합니다(대문자 'G'를 사용하여 정확히 이와 같이 표기).
    - 또한 클레임 페이로드를 작게 유지하고 Azure AD에서 SAML 어설션의 그룹 클레임 수를 150개로 제한하는 제한을 방지하기 위해 클레임에서 반환되는 그룹을 명시적으로 할당된 그룹으로만 제한하는 것이 좋습니다.  
        - "사용자와 연결된 그룹 중 어떤 그룹이 클레임에서 반환되어야 합니까?" 아래에서 "애플리케이션에 할당된 그룹"으로 대답합니다.  그런 다음, 클레임으로 포함하려는 그룹에 대해 "사용자 및 그룹" 섹션을 사용하고 "사용자/그룹 추가"를 선택하여 엔터프라이즈 애플리케이션에 할당합니다.

        ![Azure AD 그룹 클레임 구성](./media/scenario-azure-first-sap-identity-integration/sap-aad-group-claim-configuration.png)

IAS:

- 회사 ID 공급자 구성의 ID 페더레이션 옵션 아래에서 "[ID 인증 사용자 저장소 사용](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/LATEST/en-US/c029bbbaefbf4350af15115396ba14e2.html)"을 사용하지 않도록 설정해야 합니다. 그렇지 않으면 Azure AD의 그룹 정보가 BTP에 대한 SAML 토큰에 보존되지 않고 권한 부여가 실패합니다.

BTP:

- 해당 하위 계정의 애플리케이션에서 사용하는 역할 컬렉션에서 IAS ID 공급자에 대한 구성을 추가하고 이름을 Azure AD 그룹의 그룹 ID(개체 ID)로 설정하여 [역할 컬렉션을 사용자 그룹에 매핑](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/51acfc82c0c54db59de0a528f343902c.html)합니다.

### <a name="4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements"></a>4 - ID 요구 사항이 비슷한 애플리케이션에만 단일 BTP 하위 계정 사용

#### <a name="context"></a>컨텍스트

BTP 내에서 각 하위 계정에는 여러 애플리케이션이 포함될 수 있습니다. 그러나 IAS의 관점에서 "번들 애플리케이션"은 완전한 BTP 하위 계정이며 그 안에 있는 더 세분화된 애플리케이션이 아닙니다. 즉, IAS의 모든 신뢰 설정, 인증 및 액세스 구성과 브랜딩 및 레이아웃 옵션이 해당 하위 계정 내의 모든 애플리케이션에 적용됩니다. 마찬가지로 BTP의 모든 신뢰 구성 및 역할 컬렉션은 해당 하위 계정 내의 모든 애플리케이션에도 적용됩니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

ID 수준(사용자, 그룹, ID 공급자, 역할, 신뢰 구성, 브랜딩 등)에 대한 요구 사항이 비슷한 경우에만 여러 애플리케이션을 단일 BTP 하위 계정으로 결합하는 것이 좋습니다.

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

ID 요구 사항이 매우 다른 여러 애플리케이션을 BTP의 단일 하위 계정으로 결합하면 결국에는 안전하지 않거나 더 쉽게 잘못 구성될 수 있는 구성이 될 수 있습니다.  예를 들어 BTP의 단일 애플리케이션에 대해 ID 공급자와 같은 공유 리소스에 대한 구성이 변경되면 이 공유 리소스를 사용하는 모든 애플리케이션에 영향을 줍니다.

#### <a name="summary-of-implementation"></a>구현 요약

BTP의 하위 계정에서 여러 애플리케이션을 그룹화하는 방법을 신중하게 고려해야 합니다. 자세한 내용은 [SAP 계정 모델 설명서](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/8ed4a705efa0431b910056c0acdbf377.html)를 참조하세요.

### <a name="5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization"></a>5 - 모든 최종 사용자 인증 및 권한 부여에 프로덕션 IAS 테넌트 사용

#### <a name="context"></a>컨텍스트

IAS를 사용할 때 일반적으로 프로덕션 및 개발/테스트 테넌트가 있습니다. BTP의 다른 하위 계정 또는 애플리케이션에 대해 사용할 ID 공급자(IAS 테넌트)를 선택할 수 있습니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

최종 사용자가 로그인해야 하는 *애플리케이션* 의 개발/테스트 버전 또는 환경의 컨텍스트에서도 최종 사용자와의 상호 작용에는 항상 프로덕션 IAS 테넌트를 사용하는 것이 좋습니다.

프로덕션 테넌트와 격리하여 수행해야 하는 ID 관련 구성 테스트에만 다른 IAS 테넌트를 사용하는 것이 좋습니다.

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

IAS는 Azure AD와 페더레이션하도록 설정된 중앙 집중식 구성 요소이므로 페더레이션 및 ID 구성을 설정하고 유지 관리해야 하는 하나의 위치만 있습니다. 다른 IAS 테넌트에서 이를 복제하면 최종 사용자 액세스와 관련하여 구성이 환경 간에 잘못되거나 불일치가 발생할 수 있습니다.

### <a name="6---define-a-process-for-rollover-of-saml-signing-certificates"></a>6 - SAML 서명 인증서 롤오버 프로세스 정의

#### <a name="context"></a>컨텍스트

페더레이션을 Azure AD와 IAS 간 및 IAS와 BTP 간에 구성하는 경우 암호화에 사용되는 X.509 인증서와 두 당사자 간에 보내지는 SAML 토큰의 암호화 서명이 포함된 SAML 메타데이터가 교환됩니다. 이러한 인증서는 만료 날짜가 있으며 정기적으로 업데이트해야 합니다(예를 들어 인증서가 손상된 응급 상황에도).

참고: SAML 어설션에 서명하는 데 사용되는 초기 Azure AD 인증서의 기본 유효 기간은 3년입니다(Azure AD의 글로벌 인증서로 서명된 OpenID Connect 및 OAuth 2.0 토큰과 달리 인증서는 엔터프라이즈 애플리케이션에만 해당됨). [만료 날짜가 다른 새 인증서를 생성](../manage-apps/manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)하거나 사용자 고유의 인증서를 만들어 가져올 수 있습니다.

인증서가 만료되면 더 이상 사용할 수 없으며 새 인증서를 구성해야 합니다. 따라서 SAML 토큰에 서명하는 데 사용되는 실제 인증서를 사용하여 신뢰 당사자(서명 유효성을 검사해야 함) 내의 인증서 구성을 최신 상태로 유지하는 프로세스를 설정해야 합니다.

경우에 따라 신뢰 당사자는 최신 메타데이터 정보를 동적으로 반환하는 메타데이터 엔드포인트를 제공하여 이를 자동으로 수행할 수 있습니다. 즉, 일반적으로 신뢰 당사자가 메타데이터를 정기적으로 검색하고 내부 구성 저장소를 업데이트할 수 있는 공개적으로 액세스 가능한 URL입니다.

그러나 IAS는 메타데이터 XML 파일 가져오기를 통해서만 회사 ID 공급자를 설정할 수 있으며, Azure AD 메타데이터의 동적 검색을 위한 메타데이터 엔드포인트(예: `https://login.microsoftonline.com/my-azuread-tenant/federationmetadata/2007-06/federationmetadata.xml?appid=my-app-id`)를 제공하도록 지원하지 않습니다. 마찬가지로 BTP는 IAS 메타데이터 엔드포인트(예: `https://my-ias-tenant.accounts.ondemand.com/saml2/metadata`)에서 새 신뢰 구성을 설정할 수 없으며, 메타데이터 XML 파일의 일회성 업로드도 필요합니다.

#### <a name="what-are-we-recommending"></a>권장 사항은 무엇인가요?

ID 페더레이션을 두 시스템(예: Azure AD 및 IAS, IAS 및 BTP) 간에 설정하는 경우 사용하는 인증서의 만료 날짜를 캡처해야 합니다. 이러한 인증서를 효율적으로 미리 바꿀 수 있는지 확인하고, 이러한 인증서를 사용하는 모든 신뢰 당사자의 새 메타데이터를 업데이트하는 문서화된 프로세스가 있는지 확인합니다.

앞에서 설명한 대로 BTP에서 IAS에 대한 신뢰 구성을 설정하는 것이 좋습니다. 그러면 IAS에서 Azure AD를 회사 ID 공급자로 페더레이션하도록 설정됩니다. 이 경우 다음 인증서(SAML 서명 및 암호화에 사용됨)가 중요합니다.

- BTP의 하위 계정 인증서: 변경되면 IAS에서 애플리케이션의 SAML 2.0 구성을 업데이트해야 합니다.
- IAS의 테넌트 인증서: 변경되면 Azure AD의 엔터프라이즈 애플리케이션 SAML 2.0 구성과 BTP의 신뢰 구성을 모두 업데이트해야 합니다.
- Azure AD의 엔터프라이즈 애플리케이션 인증서: 변경되면 IAS에서 회사 ID 공급자의 SAML 2.0 구성을 업데이트해야 합니다.

![SAML 서명 인증서 롤오버](./media/scenario-azure-first-sap-identity-integration/sap-rollover-saml-signing-certs.png)

SAP에는 [여기](https://blogs.sap.com/2017/12/06/sap-cloud-platform-integration-automated-notification-of-keystore-entries-reaching-expiry/) 및 [여기](https://blogs.sap.com/2019/03/01/sap-cloud-platform-integration-automated-notification-for-client-certificates-reaching-expiry/)서 SAP Cloud Platform 통합을 사용하는 클라이언트 인증서 알림 구현의 예제가 있습니다. 이는 Azure Integration Services 또는 PowerAutomate를 사용하여 조정할 수 있습니다. 그러나 이러한 서비스는 서버 인증서를 사용하도록 조정해야 합니다. 이러한 방법에는 사용자 지정 구현이 필요합니다.

#### <a name="why-this-recommendation"></a>권장 이유는 무엇인가요?

인증서가 만료되도록 허용되거나 적시에 대체되지만 인증서를 사용하는 신뢰 당사자가 새 인증서 정보로 업데이트되지 않는 경우 사용자는 더 이상 페더레이션을 통해 애플리케이션에 로그인할 수 없습니다. 이는 메타데이터를 다시 구성하여 서비스를 복원하는 동안 모든 사용자에게 상당한 가동 중지 시간을 의미할 수 있습니다.

#### <a name="summary-of-implementation"></a>구현 요약

Azure AD에서 [인증서 만료에 대한 이메일 알림 주소를 추가](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)하고 단일 개인(인증서가 만료될 때까지 더 이상 계정이 없을 수도 있음)에게 보내지 않도록 그룹 사서함으로 설정합니다. 기본적으로 엔터프라이즈 애플리케이션을 만든 사용자만 알림을 받습니다.

전체 인증서 롤오버 프로세스를 실행하는 자동화를 빌드하는 것이 좋습니다.  예를 들어 만료되는 인증서를 정기적으로 확인하고 모든 신뢰 당사자를 새 메타데이터로 업데이트하는 동안 인증서를 바꿀 수 있습니다.
