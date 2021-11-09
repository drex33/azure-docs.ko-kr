---
title: 상용 마켓플레이스에서 거래 가능 SaaS 제품용 방문 페이지 빌드
description: 거래 가능한 SaaS 제품의 방문 페이지를 빌드하는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: cd03abdf01d11fa3b6de869a7d95feafff0ca393
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060361"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>상용 마켓플레이스에서 거래 가능 SaaS 제품용 방문 페이지 빌드

이 문서에서는 Microsoft 상용 마켓플레이스에서 판매될 거래 가능 SaaS 앱의 방문 페이지를 빌드하는 과정을 안내합니다.

## <a name="overview"></a>개요

방문 페이지를 SaaS(software as a service) 제품의 "로비"로 생각할 수 있습니다. 구매자가 제품을 구독하면 상용 마켓플레이스는 그들을 방문 페이지로 보내 SaaS 애플리케이션에 대한 구독을 활성화하고 구성합니다. 이를 구매자가 구매한 항목을 확인하고 계정 정보를 확인할 수 있는 주문 확인 단계로 생각하세요. Azure Active Directory(Azure AD) 및 Microsoft Graph를 사용하여 구매자에 대해 SSO(Single Sign-On)를 사용하도록 설정하고 구독을 확인하고 활성화하는 데 사용할 수 있는 구매자의 중요한 세부 정보(고객의 이름, 이메일 주소 및 조직 포함)를 얻을 수 있습니다.

구독을 활성화하는 데 필요한 정보는 제한적이고 Azure AD 및 Microsoft Graph에서 제공되므로, 기본 동의 이상의 정보를 요청할 필요가 없습니다. 애플리케이션에서 추가 동의를 하기 위해 사용자 세부 정보가 필요한 경우 구독 활성화가 완료된 후 이 정보를 요청해야 합니다. 이를 통해 구매자의 원활한 구독 활성화를 촉진하고 구독을 중단하는 위험을 줄일 수 있습니다.

방문 페이지는 일반적으로 다음을 포함합니다.

- 청구 조건뿐만 아니라 구매한 제품 및 계획의 이름을 표시합니다.
- 성과 이름, 조직 및 이메일을 포함한 구매자 계정의 세부 정보를 표시합니다.
- 구매자에게 다른 계정 정보를 확인하거나 대체하도록 요청합니다.
- 활성화 후 다음 단계로 구매자를 안내합니다. 예를 들어 환영 이메일을 수신하거나 구독을 관리하거나 지원을 받거나 문서를 읽을 수 있습니다.

> [!NOTE]
> 구독을 활성화한 후 관리하는 경우에도 구매자는 방문 페이지로 이동합니다. 구매자의 구독이 활성화된 후에는 SSO를 사용하여 사용자가 로그인할 수 있도록 해야 합니다. 사용자를 계정 프로필 또는 구성 페이지로 안내하는 것이 좋습니다.

다음 섹션에서는 방문 페이지를 빌드하는 과정을 안내합니다.

1. 방문 페이지에 대한 [AZURE AD 앱 등록을 만듭니다](#create-an-azure-ad-app-registration).
1. [코드 샘플을 앱의 시작점으로](#use-a-code-sample-as-a-starting-point) 사용합니다.
1. [두 Azure AD 앱을 사용하여 프로덕션 환경에서 보안을 강화합니다](#use-two-azure-ad-apps-to-improve-security-in-production).
1. 상용 마켓플레이스에서 URL에 추가한 [마켓플레이스 구매 식별 토큰을 확인](#resolve-the-marketplace-purchase-identification-token)합니다.
1. [ID 토큰에 인코딩된 클레임에서 정보를 읽습니다](#read-information-from-claims-encoded-in-the-id-token)(로그인 후에 Azure AD로부터 요청과 함께 전송됨).
1. [MICROSOFT GRAPH API를 사용하여](#use-the-microsoft-graph-api) 필요에 따라 추가 정보를 수집합니다.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

상용 마켓플레이스는 Azure AD와 완전히 통합됩니다. 구매자는 [AZURE AD 계정 또는 Microsoft 계정(MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)로 인증된 마켓플레이스에 도착합니다. 구매 후 구매자는 상용 마켓플레이스에서 방문 페이지 URL로 이동하여 SaaS 애플리케이션의 구독을 활성화하고 관리합니다. 구매자가 Azure AD SSO를 사용하여 애플리케이션에 로그인하도록 허용해야 합니다. 방문 페이지 URL은 제안의 [기술 구성](plan-saas-offer.md#technical-information) 페이지에 지정됩니다.

[!INCLUDE [pound-sign-note](./includes/pound-sign-note.md)]

ID를 사용할 때 첫 번째 단계는 방문 페이지가 Azure AD 애플리케이션으로 등록되어 있는지 확인하는 것입니다. 애플리케이션을 등록하면 Azure AD를 사용하여 사용자를 인증하고 사용자 리소스에 대한 액세스를 요청할 수 있습니다. 애플리케이션 정의로 간주될 수 있으며, 이를 통해 서비스가 앱의 설정에 따라 앱에 토큰을 발급하는 방법을 알 수 있습니다.

### <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 애플리케이션 등록

시작하려면 [새 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md) 방법에 대한 지침을 따르세요. 다른 회사의 사용자가 앱을 방문할 수 있도록 하려면 애플리케이션을 사용할 수 있는 사람을 설정할 때 다중 테넌트 옵션 중 하나를 선택해야 합니다.

Microsoft Graph API를 쿼리하려면 [웹 API에 액세스하도록 새 애플리케이션을 구성합니다](../active-directory/develop/quickstart-configure-app-access-web-apis.md). 이 애플리케이션에 대한 API 액세스 권한을 선택하는 경우 **User.Read** 의 기본값만으로 구매자의 개인 정보를 수집해도 온보딩 프로세스를 원활하고 자동으로 설정하는 데 충분합니다. 관리자 **동의 필요** 라는 레이블이 지정된 API 사용 권한을 요청하지 마세요. 이렇게 하면 관리자가 아닌 모든 사용자가 방문 페이지를 방문할 수 없게 됩니다.

온보딩 또는 프로비전 프로세스의 일환으로 상승된 권한이 필요한 경우 마켓플레이스에서 보낸 모든 구매자가 방문 페이지와 최초로 상호 작용할 수 있도록 Azure AD의 [증분 동의](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) 기능을 사용하는 것이 좋습니다.

## <a name="use-a-code-sample-as-a-starting-point"></a>코드 샘플을 시작점으로 사용

Azure AD 로그인을 사용하는 간단한 웹 사이트를 구현할 수 있는 몇 가지 샘플 앱을 제공했습니다. 애플리케이션이 Azure AD에 등록되고 나면 **빠른 시작** 블레이드는 그림 1에 표시된 것처럼 일반적인 애플리케이션 유형 및 개발 스택 목록을 제공합니다. 사용자 환경과 일치하는 항목을 선택하고 다운로드 및 설정 지침을 따릅니다.

***그림 1: Azure Portal의 빠른 시작 블레이드***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure Portal의 빠른 시작 블레이드를 보여 줍니다.":::

코드를 다운로드하고 개발 환경을 설정한 후 이전 절차에서 기록한 애플리케이션 ID, 테넌트 ID 및 클라이언트 암호를 반영하도록 앱의 구성 설정을 변경합니다. 정확한 단계는 사용 중인 샘플에 따라 달라진다는 점에 유의합니다.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>두 Azure AD 앱을 사용하여 프로덕션 환경에서 보안 강화

이 문서에서는 상용 마켓플레이스 SaaS 제품의 방문 페이지를 구현하는 데 필요한 아키텍처를 간소화된 버전으로 제공합니다. 프로덕션에서 페이지를 실행하는 경우 또 다른 보안 애플리케이션을 통해서만 SaaS 처리 API와 통신하여 보안을 개선하는 것이 좋습니다. 이렇게 하려면 두 개의 새 애플리케이션을 만들어야 합니다.

- 먼저 SaaS 처리 API에 연결하는 기능이 없는 경우를 제외하고 이 시점까지 설명된 다중 테넌트 방문 페이지 애플리케이션입니다. 이 기능은 아래에 설명된 대로 다른 애플리케이션으로 오프로드됩니다.
- 둘째로, SaaS 처리 API와의 통신을 소유하는 애플리케이션입니다. 이 애플리케이션은 단일 테넌트여야 하고, 사용자의 조직에서만 사용해야 하며, 이 앱에서만 API에 대한 액세스를 제한하도록 액세스 제어 목록을 설정할 수 있습니다.

이렇게 하면 [문제의 분리](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) 원칙을 관찰하는 시나리오에서 솔루션을 사용할 수 있습니다. 예를 들어 방문 페이지는 등록된 첫 번째 Azure AD 앱을 사용하여 사용자의 로그인을 허용합니다. 사용자가 로그인한 후에는 방문 페이지에서 두 번째 Azure AD를 사용하여 SaaS 처리 API를 호출하고 확인 작업을 호출하는 액세스 토큰을 요청합니다.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>마켓플레이스 구매 식별 토큰 확인

구매자가 사용자의 방문 페이지로 보내지면 URL 매개 변수에 토큰이 추가됩니다. 이 토큰은 Azure AD에서 발급한 토큰 및 서비스 간 인증에 사용되는 액세스 토큰과 다르며, 구독의 세부 정보를 가져오는 [SaaS 처리 API](./partner-center-portal/pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) 확인 호출용 입력으로 사용됩니다. SaaS 처리 API에 대한 모든 호출과 마찬가지로 서비스 간 요청도 앱의 Azure AD 애플리케이션 ID 사용자를 기반으로 하는 서비스 간 인증을 위한 액세스 토큰으로 인증됩니다.

> [!NOTE]
> 대부분의 경우 두 번째 단일 테넌트 애플리케이션에서 이 호출을 수행하는 것이 좋습니다. 이 문서 앞부분의 [두 AZURE AD 앱을 사용하여 프로덕션 환경에서 보안 강화](#use-two-azure-ad-apps-to-improve-security-in-production)를 참조하세요.

### <a name="request-an-access-token"></a>액세스 토큰 요청

SaaS 처리 API를 사용하여 애플리케이션을 인증하려면 Azure AD OAuth 엔드포인트를 호출하여 생성할 수 있는 액세스 토큰이 필요합니다. [게시자의 인증 토큰을 가져오는 방법을](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)참조하세요.

### <a name="call-the-resolve-endpoint"></a>확인 엔드포인트 호출

SaaS 처리 AAPI는 [확인](./partner-center-portal/pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) 엔드포인트를 구현하며, 이는 마켓플레이스 토큰의 유효성을 확인하고 구독 정보를 반환하기 위해 호출될 수 있습니다.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>ID 토큰에 인코딩된 클레임에서 정보 읽기

[OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) 흐름의 일부로 Azure AD는 구매자가 방문 페이지로 이동할 때 요청에 [ID 토큰](../active-directory/develop/id-tokens.md)을 추가합니다. 이 토큰에는 이 표에 표시된 정보를 비롯하여 활성화 프로세스에서 유용하게 쓰일 수 있는 여러 가지 기본 정보가 포함되어 있습니다.

| 값 | Description |
| ------------ | ------------- |
| aud | 이 토큰의 대상 그룹입니다. 이 경우 애플리케이션 ID와 일치해야 하며 유효성을 검사해야 합니다. |
| preferred_username | 방문 사용자의 기본 사용자 이름입니다. 이메일 주소, 전화번호 또는 기타 식별자일 수 있습니다. |
| 이메일 | 사용자의 이메일 주소입니다. 이 필드는 비어 있을 수 있습니다. |
| name | 사람이 인식할 수 있으며 토큰의 주체를 식별하는 값입니다. 이 경우 구매자 이름이 됩니다. |
| oid | 애플리케이션 간에 사용자를 고유하게 식별하는 Microsoft ID 시스템의 식별자입니다. Microsoft Graph는 이 값을 지정된 사용자 계정에 대한 ID 속성으로 반환합니다. |
| tid | 구매자가 속해 있는 Azure AD 테넌트를 나타내는 식별자입니다. MSA ID의 경우 이는 항상 ``9188040d-6c67-4c5b-b112-36a304b66dad``입니다. 자세한 내용은 다음 섹션의 Microsoft Graph API 사용을 참조하세요. |
| sub | 이 특정 애플리케이션에서 사용자를 고유하게 식별하는 식별자입니다. |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API 사용

ID 토큰에는 구매자를 식별하기 위한 기본 정보가 포함되어 있지만 온보딩 프로세스를 완료하려면 활성화 프로세스에서 구매자의 회사 같은 추가 세부 정보가 필요할 수 있습니다. [MICROSOFT GRAPH API](/graph/use-the-api)를 사용하여 이 정보를 요청하면 사용자가 같은 정보를 향후 다시 입력하지 않도록 방지할 수 있습니다. 표준 **사용자. 읽기** 권한에는 기본적으로 다음 정보가 포함됩니다.

| 값 | Description |
| ------------ | ------------- |
| displayName | 사용자의 주소록에 표시되는 이름입니다. |
| givenName | 사용자의 이름입니다. |
| jobTitle | 사용자의 직위입니다. |
| mail | 사용자의 SMTP 주소입니다. |
| MobilePhone | 사용자의 기본 휴대폰 번호입니다. |
| preferredLanguage | 사용자의 기본 설정 언어에 대한 ISO 639-1 코드입니다. |
| surname | 사용자의 성입니다. |
|||

사용자의 회사 이름 또는 사용자의 위치(국가)와 같은 추가 속성을 선택하여 요청에 포함할 수 있습니다. 자세한 내용은 [사용자 리소스 형식에 대한 속성](/graph/api/resources/user#properties)을 참조하세요.

Azure AD에 등록된 대부분의 앱은 회사의 Azure AD 테넌트에서 사용자 정보를 읽을 수 있도록 위임된 권한을 부여합니다. 해당 정보와 관련하여 Microsoft Graph로 이뤄지는 모든 요청에는 인증을 위한 액세스 토큰이 들어 있어야 합니다. 액세스 토큰을 생성하는 특정 단계는 사용 중인 기술 스택에 따라 다르지만 샘플 코드에 예제가 포함되어 있습니다. 자세한 내용은 [사용자를 대신하여 액세스 권한 가져오기](/graph/auth-v2-user)를 참조하세요.

> [!NOTE]
> MSA 테넌트 계정(테넌트 ID ``9188040d-6c67-4c5b-b112-36a304b66dad``)은 ID 토큰을 사용하여 이미 수집된 것보다 더 많은 정보를 반환하지 않습니다. 따라서 이러한 계정에 대한 Graph API 호출을 건너뛸 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)