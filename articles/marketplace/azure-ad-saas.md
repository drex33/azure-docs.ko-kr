---
title: 상업용 마켓플레이스의 Azure Active Directory 및 거래 가능한 SaaS 제품
description: Microsoft 상업용 마켓플레이스에서 Azure Active Directory가 거래 가능한 SaaS 제품에서 작동하는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 224fda3a9f308bfb0b6683d58bbc6de3c2822259
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059320"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>상업용 마켓플레이스의 Azure AD 및 거래 가능한 SaaS 제품

Microsoft 클라우드 기반 ID 및 액세스 관리 서비스인 Azure AD([Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md))는 사용자가 내부 및 외부 리소스에 로그인하고 액세스할 수 있도록 해줍니다. Microsoft 상업용 마켓플레이스의 경우 Azure AD를 통해 게시자, 구매자, 사용자 등 모든 사용자가 거래 가능한 SaaS 제품을 더욱 쉽고 안전하게 처리할 수 있습니다. Azure AD를 사용하여 게시자는 SaaS(software as a service) 앱에 사용자를 자동으로 프로비전할 수 있고 구매자는 프로비전된 사용자를 관리할 수 있습니다. 

또한 [Azure AD single sign-on](../active-directory/manage-apps/what-is-single-sign-on.md)(SSO)으로 사용자는 Azure AD에서 앱에 안전하고 편리하게 로그인할 수 있습니다. 뿐만 아니라 참여가 더욱 빨라지고 환경이 최적화되어 구매자와 사용자는 게시자 SaaS 앱과의 첫 번째 상호 작용에서 자신감을 얻게 됩니다. 이는 긍정적인 인상을 심어줘 가시성이 구축되고 반복 거래가 촉진됩니다.

이 문서에 안내된 지침은 상업용 마켓플레이스에서 SaaS 제품을 인증하는 데 도움이 됩니다. 인증에 대한 자세한 내용은 [SaaS와 관련된](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) 정책을 비롯한 자세한 [상업용 마켓플레이스 인증 정책](/legal/marketplace/certification-policies#100-general)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

파트너 센터에서 [SaaS 제품을 만들](./create-new-saas-offer.md) 때 제품 목록에 표시되는 특정 목록 옵션 집합에서 선택을 합니다. 어떤 항목을 선택하느냐에 따라 상업용 마켓플레이스에서 제품이 거래되는 방법이 결정됩니다. Microsoft를 통해 판매되는 제품을 거래 가능 제품이라고 합니다. Microsoft가 모든 거래 가능 제품에 대해 사용자를 대신해 고객에게 요금을 청구합니다. Microsoft을 통한 판매와 트랜잭션 호스팅의 대리를 선택하는 경우(옵션 **예**) 거래 가능 제품을 만들기로 결정한 것으로, 이러한 경우 이 문서를 참조하십시오. 문서는 전부 읽을 것을 권장합니다.

상업용 마켓플레이스를 통해 제품 나열만 하고 거래는 개인적으로 처리하기로 선택하는 경우(옵션 **아니오**) 잠재 고객이 제품에 액세스하는 방법에는 지금 받기(무료), 무료 평가판, 문의처 등 세 가지 옵션이 있습니다. **지금 받기(무료)** 또는 **무료 평가판** 을 선택하는 경우 이 문서를 읽을 필요가 없습니다. 대신 [상업용 마켓플레이스에 무료 또는 평가판 SaaS 제품용 방문 페이지 빌드](./azure-ad-free-or-trial-landing-page.md)에서 자세한 내용을 확인하세요. **문의처** 를 선택하는 경우 직접적인 게시자 책임은 없습니다. 파트너 센터에서 제품 만들기를 계속합니다.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD가 SaaS 제품을 위한 상업용 마켓플레이스에서 작동하는 방식

Azure AD는 상업용 마켓플레이스 솔루션의 원활한 구매, 처리 및 관리를 가능하게 합니다. 그림 1에서는 게시자, 구매자 및 사용자가 구독을 구매하고 활성화하는 방법을 보여 줍니다. 또한 고객이 상업용 마켓플레이스에서 구매한 SaaS 애플리케이션을 사용하고 관리하는 방법을 보여 줍니다. 이 그림의 목적상 구매자는 상업용 마켓플레이스에서 구매를 일으키는 SaaS 애플리케이션 사용자입니다.

그림 1에서와 같이 구매자가 제품을 선택하면 구매, 구독 및 사용자 관리를 포함하는 워크플로 체인이 시작합니다. 이 체인 내에서 귀하는 구매자로서 특정 요구 사항에 대한 책임이 있으며 Microsoft가 주요 지점에서 지원을 제공합니다.

***그림 1: 상업용 마켓플레이스에서 SaaS 제품에 Azure AD 사용하기***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="구매 관리, 구독 관리 및 선택적 사용자 관리 프로세스 단계를 보여 줍니다.":::

다음 섹션에서는 각 프로세스 단계에 대한 요구 사항의 세부 정보를 제공합니다.

## <a name="process-steps-for-purchase-management"></a>구매 관리를 위한 프로세스 단계

다음 그림은 구매 관리를 위한 4 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="구매 관리를 위한 4 가지 프로세스 단계를 보여 줍니다.":::

이 테이블은 구매 관리 프로세스 단계에 대한 세부 정보를 제공합니다.

| 프로세스 단계 | 게시자 작업 | 게시자에 대한 권장 또는 필수 사항 |
| ------------ | ------------- | ------------- |
| 1. 구매자는 Azure ID ID를 사용하여 상업용 마켓플레이스에 로그인하고 SaaS 제품 선택합니다. | 게시자 작업이 필요하지 않습니다. | 해당 없음 |
| 2. 구매 후에 구매자가 Azure 마켓플레이스에서 **계정 구성** 또는 AppSource에서 **지금 구성** 을 선택하면 해당 제품에 대한 게시자의 방문 페이지로 이동합니다. 구매자는 Azure AD SSO를 사용하여 게시자의 SaaS 애플리케이션에 로그인할 수 있어야 하며, Azure AD 관리자 승인이 필요하지 않은 최소한의 동의만 할 것을 요청 받아야 합니다. | 제품의 [방문 페이지](azure-ad-transactable-saas-landing-page.md)는 Azure AD 또는 MSA(Microsoft 계정) ID로 이용하고 필요한 추가 프로비전 또는 설치가 용이하도록 설계합니다. | 필수 |
| 3. 게시자가 SaaS 처리 API로부터 구매 정보를 요청합니다. | 방문 페이지의 애플리케이션 ID에서 생성된 [액세스 토큰](./partner-center-portal/pc-saas-registration.md)을 사용하여 [엔드포인트 해결을 호출](./partner-center-portal/pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription)함으로써 구매 세부 정보를 검색합니다. | 필수 |
| 4. Azure AD 및 Microsoft Graph API를 통해 게시자는 게시자의 SaaS 애플리케이션에서 구매자를 프로비전하는 데 필요한 회사 및 사용자 세부 정보를 수집합니다.  | Azure AD 사용자 토큰을 해체하여 이름 및 이메일을 찾거나, [Microsoft Graph API를 호출](/graph/use-the-api)하고 위임된 권한을 사용하여 로그인한 사용자에 대한 [정보를 검색](/graph/api/user-get)합니다. | 필수 |
||||

## <a name="process-steps-for-subscription-management"></a>구독 관리를 위한 프로세스 단계

다음 그림은 구독 관리를 위한 2 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="구독 관리를 위한 2 가지 프로세스 단계를 보여 줍니다.":::

이 테이블은 구독 관리 프로세스 단계에 대한 세부 정보를 설명합니다.

| 프로세스 단계 | 게시자 작업 | 게시자에 대한 권장 또는 필수 사항 |
| ------------ | ------------- | ------------- |
| 5. 게시자는 SaaS 처리 API를 통해 SaaS 애플리케이션 구독을 관리합니다. | [SaaS 처리 API](./partner-center-portal/pc-saas-fulfillment-apis.md)를 통해 구독 변경 및 기타 관리 작업을 처리합니다.<br><br>이 단계에서는 프로세스 3 단계에서 설명한 대로 액세스 토큰이 필요합니다. | 필수 |
| 6. 계량 가격 책정을 사용하는 경우 게시자는 사용량 이벤트를 계량 서비스 API로 내보냅니다. | SaaS 앱이 사용량 기반 청구 기능을 제공하는 경우 [마켓플레이스 계량 서비스 API](marketplace-metering-service-apis.md)를 통해 사용량을 보고합니다.<br><br>이 단계에서는 3 단계에서 설명한 대로 액세스 토큰이 필요합니다. | 계량 시 필요 |
||||

## <a name="process-steps-for-user-management"></a>사용자 관리를 위한 프로세스 단계

이 그림은 사용자 관리를 위한 3 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="사용자 관리의 3 가지 선택적 처리 단계를 보여 줍니다.":::

프로세스 7 ~ 9 단계는 선택적 사용자 관리 프로세스 단계입니다. Azure AD SSO(single sign-on)를 지원하는 게시자에게 추가 혜택을 제공하는 프로세스 단계입니다. 이 테이블은 사용자 관리 프로세스 단계에 대한 세부 정보를 설명합니다.

| 프로세스 단계 | 게시자 작업 | 게시자에 대한 권장 또는 필수 사항 |
| ------------ | ------------- | ------------- |
| 7. 구매자 회사에서 Azure AD 관리자는 Azure AD를 통해 사용자 및 그룹에 대한 액세스를 선택적으로 관리할 수 있습니다. | 사용자에 대해 Azure AD SSO를 설정하는 경우(9 단계) 게시자 작업은 필요하지 않습니다. | 해당 없음 |
| 8. Azure AD 프로비저닝 서비스는 Azure AD와 게시자의 SaaS 애플리케이션 간에 변경 사항을 전달합니다. | 사용자가 추가 및 제거될 때 Azure AD에서 업데이트를 수신하도록 [SCIM 엔드포인트를 구현](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md)합니다. | 권장 |
| 9. 앱이 권한이 부여되고 프로비전되면 구매자 회사의 사용자가 Azure AD SSO를 사용하여 게시자의 SaaS 애플리케이션에 로그인할 수 있습니다. | [Azure AD SSO를 사용](../active-directory/manage-apps/what-is-single-sign-on.md)하여 사용자가 하나의 계정으로 게시자의 SaaS 애플리케이션에 한 번 로그인할 수 있도록 합니다. | 권장 |
||||

## <a name="next-steps"></a>다음 단계

- [상업용 마켓플레이스에 거래 가능 SaaS 제품용 방문 페이지 빌드](azure-ad-transactable-saas-landing-page.md)
- [상업용 마켓플레이스에 무료 또는 평가판 SaaS 제품용 방문 페이지 빌드](azure-ad-free-or-trial-landing-page.md)
- [상업용 마켓플레이스에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)