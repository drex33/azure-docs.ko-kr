---
title: 프로그래밍 분석에 대한 샘플 쿼리
description: 이러한 샘플 쿼리를 사용하여 Microsoft 상용 마켓플레이스 분석 데이터에 프로그래밍 방식으로 액세스합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 8/06/2021
ms.openlocfilehash: ac276f495ac2a5eb3bee6ac0f682185cf8424611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567258"
---
# <a name="sample-queries-for-programmatic-analytics"></a>프로그래밍 분석에 대한 샘플 쿼리

이 문서에서는 Microsoft 상업용 마켓플레이스 주문, 사용량 및 고객 보고서에 대한 샘플 쿼리를 제공합니다. [보고서 쿼리 생성하기](analytics-programmatic-access.md#create-report-query-api) API 엔드포인트를 호출하여 이러한 쿼리를 사용할 수 있습니다. 필요한 경우 [보고서 쿼리 생성하기](analytics-programmatic-access.md#create-report-query-api) API 호출을 수정하여 더 많은 열을 추가하고 계산 기간을 조정하고 필터 조건을 추가할 수 있습니다. 지원되는 기간은 6개월(6M), 12개월(12M) 및 사용자 지정 기간입니다.

열 이름, 특성 및 설명에 대한 자세한 내용은 다음 표를 참조하세요.

- [고객 정보 표](customer-dashboard.md#customer-details-table)
- [주문 세부 정보 표](orders-dashboard.md#orders-details-table)
- [사용량 세부 정보 표](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>고객 보고서 쿼리

이러한 샘플 쿼리는 고객 보고서에 적용됩니다.

| **쿼리 설명** | **샘플 쿼리** |
| --- | --- |
| 선택한 날짜까지의 파트너의 활성 고객 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| 선택한 날짜까지의 파트너의 고객 변동 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| 지난 6개월 동안 특정 지역의 새 고객 목록 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>사용량 보고서 쿼리

이러한 샘플 쿼리는 사용량 보고서에 적용됩니다.

| **쿼리 설명** | **샘플 쿼리** |
| --- | --- |
| 지난 6개월에 대한 "Azure에서 청구된" Marketplace 라이선스 형식에 대한 가상 머신(VM)의 표준화된 사용량 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 지난 12개월에 대한 "Azure에서 청구된" Marketplace 라이선스 형식에 대한 VM 원시 사용량 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| 최근 6개월에 대한 "사용자 라이선스 필요" 마켓플레이스 라이선스 형식에 대한 VM의 표준화된 사용량 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 최근 6개월에 대한 "사용자 라이선스 필요" 마켓플레이스 라이선스 형식에 대한 VM 원시 사용량 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 지난 달의 유료 요금제에 대한 사용량 날짜, 일일 총 표준화된 사용량 및 "예상 연장 요금(PC/CC)" 기준 | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| 지난 달의 유료 요금제에 대한 사용량 날짜, 일일 총 원시 사용량 및 "예상 연장 요금(PC/CC)" 기준 | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| 특정 제품에 대해, 지난 6개월에 대한 "Azure에서 청구된" Marketplace 라이선스 유형 VM의 표준화된 사용량 | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 특정 제품에 대해, 지난 6개월에 대한 계측된 사용량 | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>보고서 쿼리 주문

이러한 샘플 쿼리는 주문 보고서에 적용됩니다.

| **쿼리 설명** | **샘플 쿼리** |
| --- | --- |
| 최근 6개월에 대한 Azure 라이선스 유형 "기업"에 대한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| 최근 6개월에 대한 Azure 라이선스 유형 "종량제"에 대한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| 최근 6개월의 특정 제품에 대한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 최근 6개월의 활성 주문의 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 지난 6개월의 취소된 주문에 대한 주문 보고서 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
| 기간 시작, 기간 종료 날짜 및 예상 요금, 통화가 포함된 주문 보고서 | `SELECT OrderId, TermStartId, TermEndId, estimatedcharges from ISVOrderV2 WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 최근 6개월 동안 활성화된 평가판 주문에 대한 주문 보고서 | `SELECT OrderId from ISVOrderV2 WHERE OrderStatus = ‘Active’ and HasTrial = ‘True’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace 분석 데이터에 액세스하기 위한 API](analytics-available-apis.md)
