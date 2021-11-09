---
title: 전자 상거래 플랫폼 및 Azure Marketplace에 맞게 비즈니스 조정
description: 전자 상거래 플랫폼(Azure Marketplace)에 맞게 비즈니스를 조정합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: ee5613a316d1a45e296ca25af759590192342c3e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057407"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>전자 상거래 플랫폼에 맞게 비즈니스 조정

이 문서에서는 상용 마켓플레이스 UI(사용자 인터페이스) 및 프로그래밍 방식 API(애플리케이션 프로그래밍 인터페이스)를 결합하여 비즈니스 프로세스를 지원하는 방법을 설명합니다. API 아래의 링크는 개발자가 CRM 시스템을 상용 마켓플레이스와 통합하는 데 사용할 수 있는 특정 인터페이스를 가리킵니다.

## <a name="overview-of-activities"></a>활동 개요

아래 활동은 순차적이지 않습니다. 사용하는 활동은 비즈니스 요구 사항 및 판매 프로세스에 따라 달라집니다. 이 가이드에서는 다양한 API를 통합하여 각 활동을 자동화하는 방법을 보여 줍니다.

| <center>활동 | ISV 판매 활동 | 해당 Marketplace API | 해당 Marketplace UI |
| --- | --- | --- | --- |
| <center>**1. 제품 마케팅**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | 제품 메시징, 위치 설정, 프로모션, 가격 책정 만들기 | 제품 메시징, 위치 설정, 프로모션, 가격 책정 만들기<br>[파트너 수집 API](https://apidocs.microsoft.com/services/partneringestion/)<br>[Azure 앱 온보딩 API](azure-app-apis.md)</ul> | 제품 메시징, 위치 설정, 프로모션, 가격 책정 만들기<br>PC(파트너 센터) → 제품 만들기 |
| <center>**2. 수요 창출**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | 제품 프로모션<br>잠재 고객 양성<br>평가판, 시험판 및 PoC | 제품 프로모션<br>잠재 고객 양성<br>평가판, 시험판 및 PoC<br>[D365, SFDC 및 Marketo용 잠재 고객 CRM 커넥터](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[SalesForce CRM용 공동 판매 커넥터](/partner-center/connector-salesforce)<br>[Dynamics 365 CRM용 공동 판매 커넥터](/partner-center/connector-dynamics) | 제품 프로모션<br>잠재 고객 양성<br>평가판, 시험판 및 PoC<br>Azure Marketplace 및 AppSource<br>PC Marketplace Insights<br>PC 공동 판매 기회 |
| <center>**3. 협상 및 견적 만들기**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | T&C<br>가격 책정<br>할인 승인<br>최종 견적 | T&C<br>가격 책정<br>할인 승인<br>최종 견적<br>[VM용 CPP API](cloud-partner-portal-api-overview.md)<br>[AAD API용 Microsoft Graph](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[파트너 센터 '7' API 제품군](https://apidocs.microsoft.com/services/partnercenter) | T&C<br>가격 책정<br>할인 승인<br>최종 견적<br>PC → 플랜(퍼블릭 또는 프라이빗) |
| <center>**4. 판매**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | 계약 서명<br>수익 인식<br>청구<br>결제 | 계약 서명<br>수익 인식<br>청구<br>결제<br>[SaaS 처리 API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[Reporting API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | 계약 서명<br>수익 인식<br>청구<br>결제<br>Azure Portal/관리 센터<br>PC 마켓플레이스 보상<br>PC 지급 보고서<br>PC 마켓플레이스 분석<br>PC 공동 판매 마감 |
| <center>**5. 유지 관리**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | 반복 청구<br>초과분<br>기술 지원 서비스 | 반복 청구<br>초과분<br>기술 지원 서비스<br>[SaaS/AMA: 청구 API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[SaaS 처리 API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[파트너 지급 API](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[데이터 요금제 API](marketplace-metering-service-apis.md)<br>[(EA 고객) Azure Consumption API](/rest/api/consumption/)<br>[(EA 고객) Azure 요금 목록 API](/rest/api/consumption/charges/list) | 반복 청구<br>초과분<br>기술 지원 서비스<br>PC 지급 보고서<br>PC 마켓플레이스 분석 |
| <center>**6. 계약 종료**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | 갱신 또는<br>종료 |갱신 또는<br>종료 <br>[SaaS 처리 API v.2](./partner-center-portal/pc-saas-fulfillment-apis.md)<br>AMA/VM: 자동 갱신 | 갱신 또는<br>종료<br>PC 마켓플레이스 분석 |
|

## <a name="next-steps"></a>다음 단계

- 필요에 따라 각 API에 대한 위 링크를 방문하세요.