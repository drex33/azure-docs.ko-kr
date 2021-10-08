---
title: Microsoft 상업적 marketplace에 대 한 SaaS 제품 계획-Azure Marketplace
description: Microsoft 파트너 센터의 상용 마켓플레이스 프로그램을 사용 하 여 Microsoft AppSource, Azure Marketplace 또는 클라우드 솔루션 공급자 (CSP) 프로그램을 통해 나열 하거나 판매 하기 위한 새 SaaS (software as a service) 제품을 계획 합니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5a16b61c70b94f6095012aff077b8bd37ea2f8a1
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669495"
---
# <a name="plan-a-saas-offer-for-the-commercial-marketplace"></a>상업적 marketplace에 대 한 SaaS 제안 계획

이 문서에서는 Microsoft 상업용 Marketplace에 SaaS(Software as a Service) 제품을 게시하는 다양한 옵션과 요구 사항을 설명합니다. SaaS는 온라인 구독을 통해 고객에게 소프트웨어 솔루션 및 라이선스를 제공할 수 있는 제품입니다. SaaS 게시자는 고객의 제품 사용을 지원하는 데 필요한 인프라를 관리하고 비용을 지불해야 합니다. 이 문서는 파트너 센터를 통해 상업용 Marketplace에 게시할 제품을 준비하는 데 도움이 됩니다.

## <a name="listing-options"></a>나열 옵션

새 SaaS 제품 게시를 준비할 때 어떤 _목록_ 옵션을 선택할 것인지 결정해야 합니다. 선택하는 목록 옵션에 따라 파트너 센터에서 제품을 만들 때 제공해야 하는 추가 정보가 결정됩니다. [상업용 Marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)에 설명된 대로 **제품 설정** 페이지에서 목록 옵션을 정의할 것입니다.

다음 표는 상업용 Marketplace의 SaaS 제품에 대한 목록 옵션을 보여줍니다.

| 목록 옵션 | 트랜잭션 프로세스 |
| ------------ | ------------- |
| 연락처 | 고객이 목록의 정보로 직접 연락합니다.``*``<br>제안을 게시한 후 다른 목록 옵션으로 변경할 수 있습니다. |
| 평가판 | 고객이 Azure AD(Azure Active Directory)를 통해 대상 URL로 리디렉션됩니다.``*``<br>제안을 게시한 후 다른 목록 옵션으로 변경할 수 있습니다. |
| 지금 받기(무료) | 고객이 Azure AD를 통해 대상 URL로 리디렉션됩니다.``*``<br>제안을 게시한 후 다른 목록 옵션으로 변경할 수 있습니다. |
| Microsoft를 통해 판매  | Microsoft를 통해 판매되는 제품을 _거래 가능_ 제품이라고 합니다. 거래 가능한 제품은 Microsoft가 파트너 대신 소프트웨어 라이선스 대금 교환을 용이하게 할 수 있는 제품입니다. Microsoft는 파트너가 선택하는 가격 책정 모델을 사용하여 SaaS 제품 대금을 청구하고, 파트너 대신 고객 트랜잭션을 관리합니다. Azure 인프라 사용 대금은 파트너에게 직접 청구됩니다. 파트너는 현재 가격 책정 모델에 인프라 비용을 포함해야 해야 합니다. 자세한 내용은 아래의 [SaaS 청구](#saas-billing)에 설명되어 있습니다.<br><br>**참고**: 제안이 게시되면 이 옵션을 변경할 수 없습니다.  |
|||

``*`` 게시자는 주문, 이행, 계량, 대금 청구, 청구서 발행, 결제 및 수금을 포함하여 소프트웨어 라이선스 트랜잭션의 모든 것을 지원할 책임이 있습니다.

이러한 목록 옵션에 대한 자세한 내용은 [상업용 Marketplace 거래 기능](marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조하세요.

제품이 게시된 후에는 제품에 대해 선택한 목록 옵션이 제품 목록 페이지의 왼쪽 위 모서리에 단추로 표시됩니다. 예를 들어 다음은 **지금 받기** 단추를 사용하는 Azure Marketplace의 제품 목록 페이지를 보여주는 스크린샷입니다.

![온라인 스토어의 제품 목록을 보여줍니다.](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>기술 요구 사항

기술 요구 사항은 제품에 대해 선택하는 목록 옵션에 따라 다릅니다.

_나에게 연락_ 목록 옵션은 기술 요구 사항이 없습니다. CRM(고객 관계 관리) 시스템을 연결하여 잠재 고객을 관리할 수 있는 옵션이 있습니다. 이 내용은 이 문서의 뒷부분에 나오는 [잠재 고객](#customer-leads) 섹션에 설명되어 있습니다.

_지금 받기(무료)_ , _평가판_ 및 _Microsoft를 통해 판매_ 목록 옵션의 기술 요구 사항은 다음과 같습니다.

- 사이트에서 구매자 인증에 MSA(Microsoft 계정)와 [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)를 모두 사용하도록 설정해야 합니다. Azure AD 계정이 있는 구매자에게 SSO(Single Sign-On)가 있는 Azure AD를 사용하여 애플리케이션에 로그인하도록 해야 합니다.
- 제품을 구매한 고객에게 원활한 로그인 및 온보딩 환경을 제공하는 랜딩 페이지를 만들어야 합니다. 방문 페이지에서 필요한 추가 프로비저닝 또는 설정을 완료할 수 있습니다. 방문 페이지를 만드는 방법에 대한 지침은 다음 문서를 참조하세요.
  - [상업용 Marketplace의 거래 가능 SaaS 제품에 대한 방문 페이지 작성](azure-ad-transactable-saas-landing-page.md)
  - [상업용 Marketplace의 체험판 또는 평가판 SaaS 제품에 대한 방문 페이지 작성](azure-ad-free-or-trial-landing-page.md)

이러한 추가 기술 요구 사항은 _Microsoft를 통해 판매_(거래 가능) 목록 옵션에만 적용됩니다.

- [SaaS 처리 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md)를 사용하여 Azure Marketplace 및 Microsoft AppSource와 통합해야 합니다. SaaS 구독과 상호 작용하여 사용자 계정 및 서비스 플랜을 생성, 업데이트 및 삭제할 수 있는 서비스를 노출해야 합니다. 24시간 내에 중요한 API 변경 내용이 지원되어야 합니다. 중요하지 않은 API 변경 내용은 정기적으로 릴리스됩니다. 수집된 필드의 사용법을 보여주는 다이어그램과 설명은 [API](./partner-center-portal/pc-saas-fulfillment-api-v2.md)에 대한 설명서에서 확인할 수 있습니다.
- 제품에 대한 플랜을 하나 이상 만들어야 합니다. 플랜 가격은 게시하기 전에 선택하는 가격 책정 모델(_정액제_ 또는 _사용자당_)을 기준으로 책정됩니다. [플랜](#plans)에 대한 자세한 내용은 이 문서의 뒷부분에 나와 있습니다.
- 고객은 언제든지 제품을 취소할 수 있습니다.

### <a name="technical-information"></a>기술 정보

거래 가능 제품을 만드는 경우 **기술 구성** 페이지에 필요한 다음 정보를 수집해야 합니다. 거래 가능 제품을 만드는 대신 독립적으로 트랜잭션을 처리하기로 선택하는 경우에는 이 섹션을 건너뛰고 [시험 사용](#test-drives)으로 이동합니다.

- **방문 페이지 URL**: 사용자가 상업용 Marketplace에서 제품을 구매한 후 리디렉션되는 SaaS 사이트 URL(예: `https://contoso.com/signup`)이며, 새로 만든 SaaS 구독의 구성 프로세스를 트리거합니다. 이 URL은 대화형 등록 페이지에 대한 프로비전 세부 정보를 가져오기 위해 처리 API를 호출하는 데 사용할 수 있는 토큰을 수신합니다.

  이 URL은 특정 고객의 SaaS 구매를 고유하게 식별하는 마켓플레이스 구매 ID 토큰 매개 변수를 사용하여 호출됩니다. [확인 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)를 사용하여 이 토큰을 해당 SaaS 구독 세부 정보와 교환해야 합니다. 이러한 세부 정보 및 고객 대화형 웹 페이지에서 수집하려는 기타 정보는 고객 온보딩 환경을 시작하는 데 사용할 수 있으며, 최종적으로 구독 기간을 시작하기 위한 API 활성 호출로 마무리되어야 합니다. 이 페이지에서 사용자는 Azure AD(Azure Active Directory)를 사용하여 원클릭 인증을 통해 가입해야 합니다.

  마켓플레이스 구매 식별 토큰 매개 변수를 사용하는 이 URL은 고객이 Azure Portal 또는 Microsoft 365 관리 센터에서 관리형 SaaS 환경을 시작할 때에도 호출됩니다. 제품을 구매한 신규 고객에게 처음으로 토큰을 제공하는 흐름과 SaaS 솔루션을 관리하는 기존 고객에게 다시 토큰을 제공하는 두 가지 흐름을 모두 처리해야 합니다.

    구성하는 방문 페이지는 연중무휴 가동되어야 합니다. 상업용 Marketplace에서 이루어지는 SaaS 제품의 신규 구매 또는 제품 활성 구독의 구성 요청에 대한 통지를 받을 수 있는 유일한 방법입니다.

- **연결 webhook** – Microsoft가 파트너에게 비동기 이벤트(예: SaaS 구독이 취소된 경우)를 보낼 수 있도록 파트너는 연결 webhook URL을 제공해야 합니다. Microsoft는 이 URL을 호출하여 파트너에게 이벤트를 알립니다.

  파트너가 제공하는 webhook는 연중무휴 가동되어야 합니다. 고객이 상업용 Marketplace를 통해 구매한 SaaS 구독에 대한 업데이트 소식을 받을 수 있는 유일한 방법입니다.

  > [!NOTE]
  > Azure Portal 내에서 단일 테넌트 [Azure AD(Azure Active Directory) 앱 등록](../active-directory/develop/howto-create-service-principal-portal.md)을 만들어야 합니다. 마켓플레이스 API를 호출할 때 앱 등록 정보를 사용하여 솔루션을 인증합니다. [테넌트 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동하여 **속성** 을 선택한 다음, 나열되는 디렉터리 ID 번호를 찾습니다. 예들 들어 `50c464d3-4930-494c-963c-1e951d15360e`입니다.

- **Azure Active Directory 테넌트 ID**: (디렉터리 ID라고도 함) Azure Portal 내에서 [Azure AD(Active Directory) 앱을 등록](../active-directory/develop/howto-create-service-principal-portal.md)해야 합니다. 그래야만 Microsoft가 앱을 API의 ACL(액세스 제어 목록)에 추가하여 파트너에게 호출 권한을 부여할 수 있습니다. Azure AD(Active Directory) 앱의 테넌트 ID를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동합니다. **표시 이름** 열에서 앱을 선택합니다. 그런 다음, 나열된 **디렉터리(테넌트) ID** 번호를 찾습니다(예: `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure Active Directory 애플리케이션 ID**: [애플리케이션 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)도 필요합니다. 이 값을 얻으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택합니다. 그런 다음, 나열된 애플리케이션(클라이언트) ID 번호를 찾습니다(예: `50c464d3-4930-494c-963c-1e951d15360e`).

  Azure AD 애플리케이션 ID는 파트너 센터 계정의 게시자 ID와 연결됩니다. 해당 계정의 모든 제품에 대해 동일한 애플리케이션 ID를 사용해야 합니다.

  > [!NOTE]
  > 게시자가 파트너 센터에 두 개 이상의 계정을 갖고 있는 경우 Azure AD 앱 등록 정보를 한 계정에만 사용할 수 있습니다. 동일한 테넌트 ID를 사용하는 경우 다른 게시자 계정에 속한 제품의 앱 ID 쌍을 사용할 수 없습니다.

## <a name="test-drives"></a>시험 사용
SaaS 앱을 시험 사용하도록 선택할 수 있습니다. 시험 사용을 통해 고객은 일정 시간 동안 사전 구성된 환경에 액세스할 수 있습니다. 모든 게시 옵션에 대해 시험 사용을 사용할 수 있지만 이 기능에는 추가 요구 사항이 있습니다. 시험 사용에 대한 자세한 내용은 [시험 사용이란?](what-is-test-drive.md)을 참조하세요. 다양한 종류의 시험 사용을 구성하는 방법에 대한 자세한 내용은 [시험 사용 기술 구성](test-drive-technical-configuration.md)을 참조하세요.

> [!TIP]
> 시험 사용은 [평가판](plans-pricing.md#free-trials)과 다릅니다. 시험 사용이나 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간에 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM(고객 관계 관리) 시스템에 연결하여 고객 정보를 수집해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품명, ID, 고객이 찾는 제품이 있는 온라인 스토어와 함께 구성된 CRM 시스템으로 전송됩니다. 상업용 Marketplace는 Azure 테이블을 사용하거나 Power Automate를 사용하여 HTTPS 엔드포인트를 구성하는 옵션을 비롯한 다양한 CRM 시스템을 지원합니다.

제품을 만드는 도중 또는 만든 이후, 언제든지 CRM 연결을 추가하거나 수정할 수 있습니다. 자세한 내용은 [상업용 Marketplace 제품의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

## <a name="selecting-an-online-store"></a>온라인 스토어 선택

SaaS 제품을 게시하면 해당 제품은 Microsoft AppSource, Azure Marketplace 또는 둘 다에 나열됩니다. 각 온라인 스토어는 고유한 고객 요구 사항을 충족합니다. AppSource는 비즈니스 솔루션, Azure Marketplace는 IT 솔루션에 특화되어 있습니다. 제품 유형, 거래 기능 및 범주에 따라 제품이 게시되는 위치가 결정됩니다. 범주 및 하위 범주는 솔루션 유형에 따라 각 온라인 스토어에 매핑됩니다. 

SaaS 제품이 IT 솔루션(Azure Marketplace)인 *동시에* 비즈니스 솔루션(AppSource)인 경우 각 온라인 스토어에 적용되는 범주와 하위 범주를 선택합니다. 두 온라인 스토어에 모두 게시된 제품은 IT 솔루션 *이자* 비즈니스 솔루션으로써 가치 제안을 모두 제공해야 합니다.

> [!IMPORTANT]
> [종량제](partner-center-portal/saas-metered-billing.md)를 사용하는 SaaS 제품은 Azure Marketplace 및 Azure Portal을 통해 제공됩니다. 개인 계획만 포함 된 SaaS 제품은 Azure Portal 및 AppSource를 통해 제공 됩니다.

| 종량제 | 공개 플랜 | 프라이빗 플랜 | 제공 위치: |
|---|---|---|---|
| 예             | 예         | 아니요           | Azure Marketplace 및 Azure Portal |
| 예             | 예         | 예          | Azure Marketplace 및 Azure Portal* |
| 예             | 아니요          | 예          | Azure Portal만 |
| 아니요              | 예          | 예          | Azure Portal 및 AppSource |
|||||

&#42; 제안의 비공개 요금제는 Azure Portal 및 AppSource를 통해서만 사용할 수 있습니다.

예를 들어 종량제 및 프라이빗 플랜만 제공하는(공개 플랜 없음) 제품은 고객이 Azure Portal에서 구매합니다. [Microsoft 상업용 Marketplace의 프라이빗 제품](private-offers.md)에 대해 자세히 알아보세요.

온라인 스토어에서 지원하는 목록 옵션에 대한 자세한 내용은 [온라인 스토어의 목록 및 가격 책정 옵션](determine-your-listing-type.md#listing-and-pricing-options-by-online-store)을 참조하세요. 범주 및 하위 범주에 대한 자세한 내용은 [상업용 Marketplace의 범주 및 하위 범주](categories.md)를 참조하세요.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의만 하면 되고 사용자 지정 사용 약관을 만들 필요는 없습니다.

표준 계약을 사용하도록 선택할 경우 표준 계약에 범용 수정계약 사용 약관과 최대 10개의 사용자 지정 수정계약을 추가할 수 있습니다. 표준 계약 대신 사용자 고유의 사용 약관을 사용할 수도 있습니다. 이러한 세부 정보는 **속성** 페이지에서 관리합니다. 자세한 내용은 [Microsoft 상업용 Marketplace 표준 계약](standard-contract.md)을 참조하세요.

> [!NOTE]
> 상업용 Marketplace 표준 계약을 사용하여 제품이 게시되면 사용자 고유의 사용자 지정 사용 약관을 이용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약 또는 고유의 사용 약관에 따라 솔루션을 제공합니다. 표준 계약의 사용 약관을 수정하려면 표준 계약 수정계약을 통해 이 작업을 수행할 수 있습니다.

## <a name="microsoft-365-integration"></a>Microsoft 365 통합

Microsoft 365와 통합하면 SaaS 제품은 Teams 앱, Office 추가 기능, SharePoint Framework 솔루션 등의 관련 무료 추가 기능을 통해 여러 Microsoft 365 앱 화면에서 연결된 환경을 제공할 수 있습니다. 고객이 E2E 솔루션(웹 서비스 + 관련 추가 기능)의 모든 패싯을 쉽게 검색하고 다음 정보를 제공하여 한 프로세스 내에서 배포하도록 도와줄 수 있습니다. 
  - SaaS 제품이 Microsoft Graph와 통합된 경우 SaaS 제품에서 통합을 위해 사용하는 AAD(Azure Active Directory) 앱 ID를 제공합니다. 관리자는 SaaS 제품이 AAD 앱 ID에 설정된 대로 적절하게 작동하는 데 필요한 액세스 권한을 검토하고, 배포 시 고급 관리자 권한이 필요한 경우 액세스 권한을 부여할 수 있습니다. 
    
     Microsoft를 통해 제품을 판매하기로 선택하는 경우 고객 구독 활성화를 완료하는 데 필요한 기본 사용자 정보를 얻기 위해 방문 페이지에서 사용하도록 등록한 것과 동일한 AAD 앱 ID입니다. 자세한 지침은 [상업용 Marketplace의 거래 가능 SaaS 제품에 대한 방문 페이지 작성](azure-ad-transactable-saas-landing-page.md)을 참조하세요. 
    
   -    연결하려는 SaaS 제품에서 작동하는 관련 추가 기능 목록을 제공합니다. 고객은 Microsoft AppSource에서 E2E 솔루션을 검색할 수 있으며, 관리자는 Microsoft 365 관리 센터를 통해 동일한 프로세스에서 연결한 SaaS와 모든 관련 추가 기능을 배포할 수 있습니다.
    
        관련 추가 기능을 연결하려면 추가 기능의 AppSource 링크를 제공해야 합니다. 즉, 추가 기능을 AppSource에 먼저 게시해야 합니다. 연결할 수 있는 지원되는 추가 기능 유형은 Teams 앱, Office 추가 기능 및 SPFx(SharePoint Framework) 솔루션입니다. 연결된 각 추가 기능은 SaaS 제품에 대해 고유해야 합니다. 

연결된 제품의 경우 AppSource에서 검색하면 SaaS와 연결된 추가 기능이 모두 포함된 하나의 결과가 반환됩니다. 고객은 SaaS 제품과 연결된 추가 기능의 제품 정보 페이지를 탐색할 수 있습니다. IT 관리자는 Microsoft 365 관리 센터 내에서 통합되고 연결된 환경을 통해 동일한 프로세스 내에서 SaaS 및 연결된 추가 기능을 검토하고 배포할 수 있습니다. 자세한 내용은 [Microsoft 365 앱 테스트 및 배포 - Microsoft 365 관리자](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps)를 참조하세요.

### <a name="microsoft-365-integration-support-limitations"></a>Microsoft 365 통합 지원 제한 사항

어떤 경우에나 AppSource에서 단일 E2E 솔루션으로 검색할 수 있지만, 위에서 설명한 대로 Microsoft 365 관리 센터를 통해 E2E 솔루션을 간단하게 배포하는 방법은 다음 시나리오에서 지원되지 않습니다.

   - "문의하기" 목록 전용 제품입니다. 
   - 동일한 추가 기능이 둘 이상의 SaaS 제품에 연결되어 있습니다.
   - SaaS 제품이 추가 기능에 연결되었지만 Microsoft Graph와 통합되지 않았으며 AAD 앱 ID가 제공되지 않았습니다.
  - SaaS 제품이 추가 기능에 연결되었지만 Microsoft Graph 통합을 위해 제공된 AAD 앱 ID가 여러 SaaS 제품 간에 공유됩니다.
 
## <a name="offer-listing-details"></a>제품 목록 세부 정보

파트너 센터에서 [새 SaaS 제품을 만들](create-new-saas-offer.md) 때 **제품 목록** 페이지에 텍스트, 이미지, 선택적 비디오 및 기타 세부 정보를 입력합니다. 다음 예제와 같이 고객이 상업용 Marketplace에서 제품 목록을 검색할 때 이 정보를 보게 됩니다.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="제품이 Microsoft AppSource에서 이렇게 표시됩니다.":::

**호출 설명**

1. 로고
2. 범주
3. 산업
4. 지원 주소(링크)
5. 사용 약관
6. 개인 정보 보호 정책
7. Offer name
8. 요약
9. Description
10. 스크린샷/비디오
11. 문서

다음 예제에서는 Azure Portal의 제품 목록을 보여줍니다.

![Azure Portal의 제품 목록을 보여줍니다.](./media/example-managed-service-azure-portal.png)

**호출 설명**

1. 제목
1. Description
1. 유용한 링크
1. 스크린샷

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 별도의 언급이 없는 경우 다음 항목이 필요합니다.

- **이름**: 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약**: 줄 바꿈 없이 100자 이하의 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **설명**: 이 설명은 상업용 Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 의도한 사용자 기반, 범주 또는 산업 협회, 앱 내 구매 기회, 필요한 정보 공개, 자세한 내용을 볼 수 있는 링크를 포함하는 것이 좋습니다.

    이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. HTML 태그를 사용하여 설명의 서식을 지정할 수도 있습니다. 이 상자에 HTML 태그를 포함하여 최대 3000자까지 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](/windows/uwp/publish/write-a-great-app-description)을 참조하세요.

- **시작 지침**: Microsoft를 통해 제품(거래 가능 제품)을 판매하기로 선택하는 경우 이 필드는 필수입니다. 이 지침은 고객이 SaaS 제품에 연결하는 데 도움이 됩니다. 보다 자세한 온라인 설명서에 최대 3000자의 텍스트와 링크를 추가할 수 있습니다.
- **검색 키워드**(선택 사항): 고객이 온라인 스토어에서 제품을 찾는 데 사용할 수 있는 최대 3개의 검색 키워드를 입력합니다. 제품 **이름** 및 **설명** 은 포함할 필요가 없습니다. 해당 텍스트는 검색에 자동으로 포함됩니다.
- **개인정보처리방침**: 회사의 개인정보처리방침에 대한 URL입니다. 파트너는 유효한 개인정보처리방침을 제공해야 하며 앱이 개인정보 관련 법률 및 규정을 준수하도록 관리할 책임이 있습니다.
- **연락처 정보**: 조직의 다음 연락처를 제공해야 합니다.
  - **고객 지원팀 연락처**: 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일을 제공합니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처**: 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일을 제공합니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
  - **CSP 프로그램 연락처**(선택 사항): CSP 프로그램을 옵트인하는 경우 이름, 전화 번호 및 이메일을 제공합니다. 그러면 해당 파트너가 질문이 있을 때 연락할 수 있습니다. 마케팅 자료의 URL을 포함할 수도 있습니다.
- **유용한 링크**(선택 사항): 제품 사용자를 위한 다양한 리소스의 링크를 제공할 수 있습니다. 예를 들어 포럼, FAQ, 릴리스 정보 등이 있습니다.
- **지원 문서**: 백서, 브로슈어, 검사 목록 또는 PowerPoint 프레젠테이션과 같은 고객 관련 문서를 3개까지 제공할 수 있습니다.
- **미디어 – 로고**: **큰** 로고의 PNG 파일을 제공합니다. 파트너 센터에서는 이 파일을 사용하여 **작은** 로고와 **중간** 로고를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.

   - 큰 로고(216x216~350x350픽셀, 필수)
   - 중간 로고(90x90픽셀, 선택 사항)
   - 작은 로고(48x48픽셀, 선택 사항)

  이러한 로고는 온라인 스토어의 여러 위치에 사용됩니다.

  - 작은 로고는 Azure Marketplace 검색 결과와 AppSource 기본 페이지 및 검색 결과 페이지에 표시 됩니다.
  - 중간 로고는 Microsoft Azure에서 새 리소스를 만들 때 표시됩니다.
  - Azure Marketplace 및 AppSource의 제품 목록 페이지에 많은 로고가 표시 됩니다.

- **미디어 - 스크린샷**: 다음 요구 사항에 따라 제품의 작동 방식을 보여주는 스크린샷을 적어도 하나 이상, 최대 5개까지 추가해야 합니다.
  - 1280x720픽셀
  - PNG 파일 유형
  - 자막 포함
- **미디어 - 비디오**(선택 사항): 다음 요구 사항에 따라 제품을 설명하는 비디오를 4개까지 추가할 수 있습니다.
  - Name
  - URL: YouTube 또는 Vimeo에만 호스팅해야 합니다.
  - 썸네일: 1280x720 PNG 파일

> [!Note]
> 제품이 일반적인 [상업용 Marketplace 인증 정책](/legal/marketplace/certification-policies#100-general) 및 [Software as a Service 정책](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)을 충족해야 합니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 플랜 대상 그룹과 다릅니다. 프라이빗 플랜은 선택한 특정 대상만 사용할 수 있습니다. 이를 통해 특정 고객과 사용자 지정 플랜에 대해 협상할 수 있습니다. 자세한 내용은 다음 플랜 섹션을 확인하세요.

MSA(Microsoft 계정) 또는 Azure AD(Azure Active Directory) 이메일 주소로 초대를 보낼 수 있습니다. 최대 10개의 이메일 주소를 수동으로 추가하거나 .csv 파일로 최대 20개를 가져옵니다. 제품이 이미 출시된 경우에도 제품의 변경 내용 또는 업데이트 내용을 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

## <a name="plans"></a>계획

거래 가능 제품에는 하나 이상의 플랜이 필요합니다. 플랜은 솔루션 범위, 한도, 관련 가격 책정을 정의합니다. 제품에 대한 여러 구독을 만들어 고객에게 다양한 기술 및 가격 옵션을 제공할 수 있습니다. 거래 가능 제품을 만드는 대신 거래를 독립적으로 처리하기로 선택하는 경우 **플랜** 페이지가 표시되지 않습니다. 이 경우 이 섹션을 건너뛰고 [추가 판매 기회](#additional-sales-opportunities)로 이동합니다.

가격 책정 모델, 평가판, 프라이빗 플랜을 포함한 플랜에 대한 일반적인 지침은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md)을 참조하세요. 다음 섹션에서는 SaaS 제품에만 해당하는 추가 정보를 설명합니다.

### <a name="saas-pricing-models"></a>SaaS 가격 책정 모델

SaaS 제품은 각 플랜에 두 가지 가격 책정 모델 중 하나를 사용할 수 있는데, 하나는 _정액제_ 이고 다른 하나는 _사용자당_ 입니다. 동일한 제품의 모든 플랜은 동일한 가격 책정 모델에 연결되어야 합니다. 예를 들어 어떤 제품의 한 플랜은 정액제, 다른 플랜은 사용자당 모델을 사용할 수 없습니다.

**정액제** - 단일 월간 또는 연간 정액제로 제품에 대한 액세스를 허용합니다. 이를 사이트 기반 가격 책정이라고도 합니다. 이 가격 책정 모델을 사용하는 경우 필요에 따라 정액 요금에 포함되지 않는 사용량에 대해 고객에게 요금을 청구하기 위해 마켓플레이스 계량 서비스 API를 사용하는 종량제 플랜을 정의할 수 있습니다. 종량제에 대한 자세한 내용은 [상업용 Marketplace 계량 서비스를 사용하는 SaaS 종량제](./partner-center-portal/saas-metered-billing.md)를 참조하세요. SaaS 서비스의 사용량이 갑자기 폭증하는 경우에도 이 옵션을 사용해야 합니다.

**사용자당** - 제품에 액세스하는 사용자 수 또는 상시 사용자 수를 기준으로 한 가격으로 제품에 대한 액세스를 허용합니다. 이 사용자 기반 모델을 사용하면 플랜에서 지원하는 최소 및 최대 사용자 수를 설정할 수 있습니다. 여러 플랜을 만들어서 사용자 수에 따라 다양한 가격을 구성할 수 있습니다. 이러한 필드는 선택 사항입니다. 선택하지 않을 경우 사용자 수에 제한이 없는 것으로 해석됩니다(최솟값은 1, 최댓값은 서비스에서 지원할 수 있는 최대 사용자 수). 이러한 필드는 플랜 업데이트의 일부로 편집할 수 있습니다.

> [!IMPORTANT]
> 제품을 게시한 후에는 가격 책정 모델을 변경할 수 없습니다. 또한 동일한 제품의 모든 플랜은 동일한 가격 책정 모델을 공유해야 합니다.

### <a name="saas-billing"></a>SaaS 청구

게시자의 Azure 구독에서 실행되는 SaaS 앱의 경우 인프라 사용 요금이 파트너에게 직접 청구됩니다. 고객은 실제 인프라 사용 요금을 볼 수 없습니다. 솔루션을 실행하기 위해 배포한 인프라 비용을 보정하기 위해 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격에 포함해야 합니다.

Microsoft를 통해 판매되는 SaaS 앱 제품은 정액제, 사용자당 또는 [종량제 서비스](./partner-center-portal/saas-metered-billing.md)를 사용하는 사용량 요금을 기준으로 월 또는 연 단위로 요금을 청구할 수 있습니다. 상업용 Marketplace는 에이전시 모델을 기반으로 작동합니다. 즉, 게시자가 가격을 설정하고, Microsoft가 고객에게 요금을 청구하고, Microsoft가 에이전시 수수료를 공제 후 게시자에게 수익을 지급합니다.

다음 예제는 에이전시 모델을 보여주는 비용 및 지급 분석 샘플입니다. 이 예제에서 Microsoft는 소프트웨어 라이선스에 대해 고객에게 $100.00를 청구하고 게시자에게 $80.00를 지급합니다.

| 라이선스 비용 | 매월 $100 |
| ------------ | ------------- |
| Azure 사용량 비용(D1/1개 코어) | 고객이 아닌 게시자에게 직접 청구됩니다. |
| Microsoft에서 고객에게 청구하는 요금 | 월간 $100.00(게시자는 발생 또는 이전된 인프라 비용을 라이선스 요금으로 계산해야 함) |
| **Microsoft에 청구되는 요금** | **매월 $100** |
| Microsoft는 3% Marketplace 서비스 요금을 청구 하 고 라이선스 비용의 97%를 지불 합니다. | 매월 $97.00 |
|

미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 전에 제품에 액세스할 수 있습니다. 이들은 상업용 Marketplace에서 제품이 어떻게 표시되는지 살펴볼 수 있으며, 파트너가 제품을 실시간으로 게시하기 전에 기능을 종합적으로 테스트할 수 있습니다. 

**미리 보기 대상 그룹** 페이지에서 제한된 미리 보기 대상 그룹을 정의할 수 있습니다. Microsoft를 통해 제품을 판매하지 않고 독립적으로 거래를 처리하기로 선택하는 경우 이 설정을 사용할 수 없습니다. 이 경우 이 섹션을 건너뛰고 [추가 판매 기회](#additional-sales-opportunities)로 이동하면 됩니다.

## <a name="test-offer"></a>제품 테스트

제품을 라이브로 게시하기 전에 미리 보기 기능을 사용하여 기술 구현을 개발하고, 테스트하고, 다양한 가격 책정 모델을 시험해야 합니다.

위험을 최소화하면서 SaaS 제품을 개발하고 테스트하려면 실험 및 테스트를 위한 DEV(테스트 및 개발) 제품을 만드는 것이 좋습니다. DEV 제품은 프로덕션(PROD) 제품과 분리됩니다.

DEV 제품을 실수로 구매하는 일이 발생하지 않도록, DEV 제품을 라이브로 게시하는 **라이브 전환** 단추를 절대 누르지 마세요.

![파트너 센터의 제품 개요 페이지를 보여줍니다. [라이브 전환] 단추와 미리 보기 링크가 표시됩니다. [유효성 검사 보고서 보기] 링크가 [자동 유효성 검사] 아래에도 표시됩니다.](./media/review-publish-offer/publish-status-saas.png)

개발 팀이 PROD 제품을 개발하고 테스트하는 데 사용할 별도의 DEV 제품을 만드는 이유는 다음과 같습니다.

- 실수로 인한 고객 요금 방지
- 가격 책정 모델 평가
- 실제 고객을 대상으로 하지 않는 플랜을 추가하지 않도록 방지

### <a name="avoid-accidental-customer-charges"></a>실수로 인한 고객 요금 방지

PROD 제품 대신 DEV 제품을 사용하여 개발 및 프로덕션 환경으로 처리하면 고객에게 실수로 요금을 청구하는 일을 방지할 수 있습니다.

Marketplace API를 호출하기 위해 서로 다른 두 Azure AD 앱을 등록하는 것이 좋습니다. 개발자는 한 Azure AD 앱을 DEV 제품의 설정으로 사용하고, 운영 팀은 PROD 앱 등록을 사용할 것입니다. 이렇게 하면 개발 팀이 API를 호출하여 매월 $100K를 지불하는 고객의 구독을 취소하는 등의 실수를 저지르지 않도록 방지할 수 있습니다. 또한 고객에게 사용하지 않은 종량제 요금을 청구하는 일이 없도록 방지할 수 있습니다.

### <a name="evaluate-pricing-models"></a>가격 책정 모델 평가

DEV 제품의 가격 책정 모델을 테스트하면 개발자가 다양한 가격 책정 모델을 시험할 때 위험을 줄일 수 있습니다.

게시자는 DEV 제품에 필요한 플랜을 만들어서 제품에 가장 적합한 가격 책정 모델을 결정할 수 있습니다. 개발자는 DEV 제품에서 여러 플랜을 만들어서 다양한 가격 책정 조합을 테스트할 수 있습니다. 예를 들어 여러 가지 사용자 지정 계량 차원 세트를 사용하여 플랜을 만들 수 있습니다. 정액제와 사용자 지정 계량 차원을 조합하여 다른 플랜을 만들 수 있습니다.

여러 가격 책정 옵션을 테스트하려면 각각의 고유한 가격 책정 모델에 대한 플랜을 만들어야 합니다. 자세한 내용은 [플랜](#plans)을 참조하세요.

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>실제 고객을 대상으로 하지 않는 플랜을 추가하지 않도록 방지

개발 및 테스트에 DEV 제품을 사용하면 PROD 제품의 불필요한 혼란을 줄일 수 있습니다. 예를 들어 지원 티켓을 제출하지 않고 다른 가격 책정 모델 또는 기술 구성을 테스트하기 위해 만드는 플랜을 삭제하면 안 됩니다. 따라서 DEV 제품에서 테스트하기 위한 플랜을 만들면 PROD 제품의 혼란을 줄일 수 있습니다.

PROD 제품의 혼란은 제품 및 마케팅 팀을 실망하게 만듭니다. 이들은 모든 플랜이 실제 고객을 대상으로 할 것이라고 기대합니다. 특히 서로 연관성이 없는 여러 팀이 있으며 각자 다른 샌드박스를 사용하려는 경우 두 가지 제품을 만들면 DEV 및 PROD에 대한 두 가지 다른 환경이 제공됩니다. 경우에 따라 다양한 팀원이 다양한 테스트 시나리오를 실행하는 대규모 팀을 지원하기 위해 여러 DEV 제품을 만들어야 합니다. 다른 팀원이 PROD 제품과 별도로 DEV 제품에서 작업하도록 하면 프로덕션 플랜을 최대한 프로덕션에 최적화된 상태로 유지하는 데 도움이 됩니다.

DEV 제품을 테스트하면 제품당 30개로 제한되는 사용자 지정 계량 차원 제한을 피할 수 있습니다. 개발자는 PROD 제품의 사용자 지정 계량 차원 제한에 영향을 주지 않고 DEV 제품에서 다른 계량 조합을 시도해 볼 수 있습니다.

## <a name="additional-sales-opportunities"></a>추가 판매 기회

Microsoft에서 지원하는 마케팅 및 판매 채널을 옵트인하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스 마지막 부분에서 다음과 같은 두 개의 탭이 표시됩니다.

- **CSP를 통한 재판매**: 이 옵션을 사용하면 Microsoft CSP(클라우드 솔루션 공급자) 파트너가 솔루션을 번들 제품의 일부로 재판매할 수 있습니다. 이 프로그램에 대한 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md)을 참조하세요.

- **Microsoft와 공동 판매**: 이 옵션을 사용하면 Microsoft 영업 팀이 고객의 요구 사항을 평가할 때 IP 공동 판매 적합 솔루션을 고려합니다. 공동 판매 자격에 대한 자세한 내용은 [공동 판매 상태의 요구 사항](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)을 참조하세요. 제품 평가를 준비하는 방법에 대한 자세한 내용은 [파트너 센터의 공동 판매 옵션](co-sell-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)
