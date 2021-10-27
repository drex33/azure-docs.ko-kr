---
title: Azure Active Directory의 B2B 협업이란?
description: Azure Active Directory B2B 협업은 외부 파트너와 안전하게 리소스를 공유하고 공동으로 작업할 수 있도록 게스트 사용자 액세스를 지원합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c633d162803b6c0f617c3bcaafc9907a559ef32
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070359"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Azure Active Directory B2B의 게스트 사용자 액세스란?

Azure AD(Azure Active Directory) B2B(Business-to-Business) 협업은 게스트 사용자를 초대하여 조직과 협업할 수 있는 외부 ID 내의 기능입니다. B2B 협업을 통해 회사의 애플리케이션과 서비스를 다른 조직의 게스트 사용자와 안전하게 공유하면서 자체 회사 데이터에 대한 제어를 유지할 수 있습니다. Azure AD 또는 IT 부서가 없는 경우에도 크든 작든, 외부 파트너와 안전하게 작업하세요. 간단한 초대 및 사용 프로세스를 통해 파트너는 자체 자격 증명을 사용하여 회사 리소스에 액세스할 수 있습니다. 개발자는 Azure AD B2B API를 사용하여 초대 프로세스를 사용자 지정하거나 셀프 서비스 등록 포털과 같은 애플리케이션을 작성할 수 있습니다. 게스트 사용자와 관련된 라이선스 및 가격 책정 정보는 [Azure Active Directory 외부 ID 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)을 참조하세요.  

> [!IMPORTANT]
>
> - **2021년 7월 12일부터**, Azure AD B2B 고객이 사용자 지정 또는 기간 업무 애플리케이션에 대한 셀프 서비스 등록과 함께 사용하기 위해 새로운 Google 통합을 설정하는 경우 인증이 시스템 웹 보기로 이동될 때까지 Google ID를 사용한 인증이 작동하지 않습니다. [자세히 알아보기](google-federation.md#deprecation-of-web-view-sign-in-support).
> - **2021년 9월 30부터** Google은 [포함된 웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 [외부 사용자 초대](google-federation.md) 또는 [셀프 서비스 등록](identity-providers.md)을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다. [자세한 정보를 알아보세요](google-federation.md#deprecation-of-web-view-sign-in-support).
> - **2021년 11월 1일부터** Microsoft는 B2B 협업 시나리오에 대해 관리되지 않는 Azure AD 계정과 테넌트를 만들어 더 이상 초대 상환을 지원하지 않습니다. 이때 모든 기존 테넌트의 이메일 일회용 암호 기능을 켜고 기본적으로 새 테넌트에서 사용하도록 설정하는 변경 작업을 롤아웃하기 시작합니다. 이 기능을 원하지 않는다면 [사용하지 않도록 설정](one-time-passcode.md#disable-email-one-time-passcode)할 수 있습니다.

## <a name="collaborate-with-any-partner-using-their-identities"></a>ID를 사용하여 파트너와 공동 작업

Azure AD B2B에서는 파트너가 자체 ID 관리 솔루션을 사용하므로 조직에 대한 외부 관리 오버헤드가 없습니다. 게스트 사용자는 자신의 회사, 학교 또는 소셜 ID로 앱 및 서비스에 로그인합니다.

- 파트너는 자체 ID 및 자격 증명을 사용하고, Azure AD는 필요하지 않습니다.
- 외부 계정 또는 암호를 관리할 필요가 없습니다.
- 계정을 동기화하거나 계정 수명 주기를 관리할 필요가 없습니다.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Azure AD 포털에서 게스트 사용자를 쉽게 초대

관리자는 게스트 사용자를 Azure Portal의 조직에 손쉽게 추가할 수 있습니다.

- 새 사용자를 추가하는 방법과 비슷하게 Azure AD에서 [새 게스트 사용자를 만듭니다](b2b-quickstart-add-guest-users-portal.md).
- 게스트 사용자를 앱 또는 그룹에 할당합니다.
- 사용 링크가 포함된 초대 이메일을 보내거나 공유할 앱의 직접 링크를 보냅니다.

![새 게스트 사용자 초대 항목 페이지를 보여 주는 스크린샷](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- 게스트 사용자는 몇 가지 간단한 [사용 단계](redemption-experience.md)에 따라 로그인합니다.

![권한 검토 페이지를 보여 주는 스크린샷](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>정책을 사용하여 안전하게 앱 및 서비스 공유

권한 부여 정책을 사용하여 회사 콘텐츠를 보호할 수 있습니다. 다단계 인증 등의 조건부 액세스 정책을 적용할 수 있습니다.

- 테넌트 수준에서 적용
- 애플리케이션 수준에서 적용
- 회사 앱과 데이터를 보호하기 위해 특정 게스트 사용자에 적용

![조건부 액세스 옵션을 보여 주는 스크린샷](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>애플리케이션 및 그룹 소유자가 자신의 게스트 사용자를 관리할 수 있음

Microsoft 애플리케이션인지 여부에 관계없이 공유하려는 애플리케이션에 게스트 사용자를 직접 추가할 수 있도록 게스트 사용자 관리를 애플리케이션 소유자에게 위임할 수 있습니다.

- 관리자가 셀프 서비스 앱 및 그룹 관리를 설정합니다.
- 관리자가 아닌 사용자는 자신의 [액세스 패널](https://myapps.microsoft.com)을 사용하여 애플리케이션 또는 그룹에 게스트 사용자를 추가합니다.

![게스트 사용자에 대한 액세스 패널을 보여 주는 스크린샷](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>B2B 게스트 사용자를 위한 온보딩 환경 사용자 지정

조직의 요구에 맞게 사용자 지정된 방식으로 외부 파트너를 등록하세요.

- [Azure AD 권한 관리](../governance/entitlement-management-overview.md)를 사용하여 [외부 사용자에 대한 액세스를 관리](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)하는 정책을 구성할 수 있습니다.
- [B2B 협업 초대 API](/graph/api/resources/invitation)를 사용하여 온보딩 환경을 사용자 지정합니다.

## <a name="integrate-with-identity-providers"></a>ID 공급자와 통합

Azure AD는 Facebook, Microsoft 계정, Google 또는 엔터프라이즈 ID 공급자와 같은 외부 ID 공급자를 지원합니다. 외부 사용자가 애플리케이션에 대해서만 새 계정을 만드는 대신 기존 소셜 또는 엔터프라이즈 계정으로 로그인할 수 있도록 ID 공급자와의 페더레이션을 설정할 수 있습니다. [External Identities용 ID 공급자](identity-providers.md)에 대해 자세히 알아봅니다.

![ID 공급자 페이지를 보여주는 스크린샷](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>셀프 서비스 가입 사용자 흐름 만들기

셀프 서비스 등록 사용자 흐름을 통해 앱에 액세스하려는 외부 사용자를 위한 등록 환경을 만들 수 있습니다. 등록 흐름의 일부로서, 다른 소셜 또는 엔터프라이즈 ID 공급자에 대한 옵션을 제공하고 사용자에 대한 정보를 수집할 수 있습니다. [셀프 서비스 등록 및 설정 방법](self-service-sign-up-overview.md)에 대해 알아봅니다.

[API 커넥터](api-connectors-overview.md)를 사용하여 셀프 서비스 등록 사용자 흐름을 외부 클라우드 시스템과 통합할 수도 있습니다. 사용자 지정 승인 워크플로와 연결하고, ID 확인을 수행하고, 사용자 제공 정보의 유효성을 검사할 수 있습니다.

![사용자 흐름 페이지를 보여주는 스크린샷](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>다음 단계

- [외부 ID 가격](external-identities-pricing.md)
- [포털에서 B2B 협업 게스트 사용자 추가](add-users-administrator.md)
- [초대 사용 프로세스 이해](redemption-experience.md)
