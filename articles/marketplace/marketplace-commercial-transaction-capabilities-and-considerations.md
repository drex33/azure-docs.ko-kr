---
title: Microsoft 상업용 Marketplace 의 기능
description: 이 문서에서는 상업용 Marketplace 거래 옵션에 대한 가격 책정, 청구, 송장 처리 및 지급 고려 사항에 대해 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 77ec7a7dad4b215ae22bf3766172f1e92e932593
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736201"
---
# <a name="commercial-marketplace-transact-capabilities"></a>상업용 Marketplace의 거래 기능

이 문서에서는 상업용 Marketplace를 통해 *거래 가능* 제품 판매에 대한 가격 책정, 청구, 송장 작성 및 지급 고려 사항에 대해 설명합니다. 거래 불가능한(무료 또는 BYOL) 제품 게시에 대한 자세한 내용은 [목록 옵션 소개](determine-your-listing-type.md)를 참조하세요.

## <a name="transactions-by-listing-option"></a>게시 옵션별 트랜잭션

게시자나 Microsoft는 상업용 Marketplace의 제품에 대한 소프트웨어 라이선스 트랜잭션을 관리할 책임이 있습니다. 제품에 대해 선택하는 목록 옵션에 따라 트랜잭션 관리자가 결정됩니다. 각 게시 옵션의 가용성 및 설명은 [목록 옵션 소개](determine-your-listing-type.md)를 참조하세요.

### <a name="transact-publishing-option"></a>거래 게시 옵션

Microsoft를 통한 판매를 선택하면 Microsoft 상거래 기능을 활용하고 검색 및 평가에서 구매 및 구현에 이르는 엔드투엔드 경험을 제공합니다. *거래 가능한* 제품은 Microsoft가 게시자를 대신하여 소프트웨어 라이선스에 대해 현금 교환을 용이하게 하기 위한 것입니다. 거래 제품은 기존 Microsoft 구독 또는 신용 카드로 요금이 청구되므로 Microsoft에서는 게시자를 대신해 클라우드 Marketplace 트랜잭션을 호스트할 수 있습니다.

파트너 센터에서 새 제품을 만들 때 거래 옵션을 선택합니다. 이 옵션은 제품 형식에 대해 거래를 사용할 수 있는 경우에만 표시됩니다.

## <a name="transact-overview"></a>거래 개요

거래 옵션을 사용하는 경우 Microsoft는 타사 소프트웨어의 판매 및 일부 제품 형식의 배포를 고객의 Azure 구독에서 사용할 수 있도록 설정합니다. 게시자는 제품에 대해 가격 책정 모델을 선택하는 경우 인프라 요금 및 사용자 고유의 소프트웨어 라이선스 요금의 청구에 대해 고려해야 합니다.

현재 다음 제품 형식에서 거래 게시 옵션이 지원됩니다.

| 제품 유형 | 청구 주기 | 요금제 청구 | 가격 책정 모델 |
| ------------ | ------------- | ------------- | ------------- |
| Azure 애플리케이션 <br>(관리형 애플리케이션) | 매월 | 예 | 사용량 기준 |
| Azure Virtual Machine | 매월* | 아니요 | 사용량 기준, BYOL |
| SaaS(Software as a Service) | 매월 및 매년 | 예 | 정액제, 사용자당, 사용량 기준. |
|||||

\* Azure Virtual Machine은 사용량 기반 청구 플랜을 제공합니다. 이러한 플랜은 코어, 코어 크기 또는 시장 및 코어 크기 사용량에 따라 구독 사용 시간에 대해 매월 청구됩니다.

### <a name="metered-billing"></a>요금제 청구

*Marketplace 계량 서비스* 를 사용하면 계약(권한)에 포함된 월간 또는 연간 요금 외에 종량제(소비 기반) 요금을 지정할 수 있습니다. 대역폭, 티켓 또는 처리된 이메일 등 지정한 Marketplace 계량 서비스 차원에 대한 사용 비용을 청구 할 수 있습니다. SaaS 제품의 요금제에 대한 자세한 내용은 [상업용 Marketplace 계량 서비스를 사용하여 SaaS 요금제 청구](./partner-center-portal/saas-metered-billing.md)를 참조하세요. Azure 애플리케이션 제품의 요금제 청구에 대한 자세한 내용은 [관리되는 애플리케이션 요금제 청구](marketplace-metering-service-apis.md)를 참조하세요.

### <a name="billing-infrastructure-costs"></a>인프라 비용 청구

**Virtual Machines** 및 **Azure 애플리케이션** 의 경우 Azure 인프라 사용 요금이 고객의 Azure 구독에 청구됩니다. 인프라 사용 요금의 가격이 책정되면 고객 청구서에 소프트웨어 공급 기업의 라이선스 요금과는 별도로 표시됩니다.

**SaaS 앱** 의 경우 게시자는 Azure 인프라 사용 요금 및 소프트웨어 라이선스 요금을 단일 비용 항목으로 처리해야 합니다. 이는 고객에게 고정 요금으로 표시됩니다. Azure 인프라 사용량은 관리되며 게시자에게 직접 청구됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 일반적으로 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 추가하는 것을 선택합니다. 소프트웨어 라이선스 요금은 측정되지 않거나 사용자 사용량을 기준으로 하지 않습니다.

## <a name="pricing-models"></a>가격 책정 모델

사용되는 트랜잭션 옵션에 따라 구독 요금은 다음과 같습니다.

- **구독 가격 책정**: 소프트웨어 라이선스 요금은 정액 요금 또는 사용자 단위로 청구되는 월간 또는 연간 정기 구독 요금으로 표시됩니다. 정기 구독 요금은 고객의 중도 취소 또는 사용하지 않은 서비스에 대해 비례 배분되어 계산되지 않습니다. 고객이 구독 기간 중 구독을 업그레이드하거나 다운그레이드하는 경우 정기 구독 요금은 비례 배부되어 계산될 수 있습니다.
- **사용량 기반 가격 책정**: Azure Virtual Machine 제품의 경우 제품 사용 범위에 따라 고객에게 청구됩니다. 가상 머신 이미지의 경우 VM 이미지에서 배포하는 가상 머신 사용량에 대해 게시자가 설정한 시간당 Azure Marketplace 요금이 고객에게 부과됩니다. 시간당 요금은 가상 머신 크기에 관계없이 균일할 수도 있고 크기별로 다를 수 있습니다. 1시간 미만은 분 단위로 요금이 청구됩니다. 플랜은 매월 청구됩니다.
- **요금제 가격 책정**: Azure 애플리케이션 제품 및 SaaS 제품의 경우 게시자는 [ Marketplace 계량 서비스](marketplace-metering-service-apis.md)를 사용하여 구성한 사용자 지정 미터 측정 기준에 따라 사용량에 대해 청구할 수 있습니다. 이러한 변경 사항은 계약(권한)에 포함된 월간 또는 연간 요금에 추가됩니다. 사용자 지정 미터 측정 기준의 예로는 대역폭, 티켓 또는 처리된 이메일이 있습니다. 게시자는 각 플랜에 대해 하나 이상의 측정 기준을 정의할 수 있지만 제품당 최대 30 개입니다. 게시자는 제품에서 각 미터를 정의하여 개별 사용자의 사용량을 추적해야 합니다. 이벤트는 한 시간 이내에 Microsoft에 보고해야 합니다. Microsoft는 해당 청구 기간에 대해 게시자가 보고한 사용량 정보를 토대로 고객에게 요금을 부과합니다.

> [!NOTE]
> 솔루션을 사용한 후 사용량에 따라 청구된 제품은 환불되지 않습니다.

제품과 관련된 사용 요금을 변경하려는 게시자는 먼저 상업용 Marketplace에서 제품(또는 제품 내의 특정 플랜)을 제거해야 합니다. 제거는 [Microsoft 게시자 계약](/legal/marketplace/msft-publisher-agreement)의 요구 사항에 따라 수행해야 합니다. 그런 다음, 게시자는 새로운 사용 요금이 포함된 새 제품(또는 제품 내 플랜)을 게시할 수 있습니다. 제품 또는 플랜 삭제에 대한 자세한 내용은 [제품 또는 플랜 배포 중지](./update-existing-offer.md#stop-distribution-of-an-offer-or-plan)를 참조하세요.

### <a name="determine-offer-type-and-pricing-plan"></a>제품 유형 및 가격 책정 플랜 결정

이 순서도를 사용하여 상업용 Marketplace에서 소프트웨어를 판매하기 위한 적절한 *거래 가능한* 제품 유형과 가격 책정 플랜을 결정합니다. 이 차트는 게시자가 소프트웨어 제공 및 라이선스/청구 모델을 명확하게 이해하고 있다고 가정합니다.

일반적으로 SaaS 제품은 고객이 서비스에 가입하여 온라인으로 사용하려는 경우에 적합합니다. SaaS를 사용하면 게시자가 클라우드 인프라에서 소프트웨어를 호스팅하고 기술 인프라, 앱 소프트웨어 및 데이터 관리 책임을 관리하게 됩니다.

가상 머신 및 Azure 애플리케이션 제품은 고객이 자체 클라우드 인프라에서 패키지된 앱 또는 서비스(ARM 템플릿의 VM 이미지 및/또는 기타 Azure 서비스로)를 배포, 관리 및 실행하도록 하려는 경우에 적합합니다.

[![제품 유형 및 가격 책정 플랜을 결정하기 위한 순서도를 보여 줍니다.](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png)](media/commercial-marketplace-plans/offer-type-and-pricing-plan-flowchart.png#lightbox)
&nbsp;&nbsp;&nbsp;<sup>(1)</sup> [Microsoft Office Hours](https://microsoftcloudpartner.eventbuilder.com/MarketplaceDeveloperOfficeHours) 또는 [고객 지원팀](./support.md)에 문의하세요.<br>
&nbsp;&nbsp;&nbsp;<sup>(2)</sup> VM 제품 이미지를 Azure 앱 제품에 포함하여 가격 책정의 유연성을 높일 수 있습니다.<br>
&nbsp;&nbsp;&nbsp;<sup>(3)</sup> Azure 서비스는 VM 및 Azure 앱 제품에 대한 고객 테넌트에 배포되므로 고객은 인프라 비용을 지불합니다.

### <a name="usage-based-and-subscription-pricing"></a>사용량 기반 및 구독 가격 책정

사용 기반 또는 구독 트랜잭션으로 제품을 게시하면 Microsoft는 소프트웨어 라이선스 구매, 반품 및 지불 거절을 처리하는 기술과 서비스를 제공합니다. 이 시나리오에서 게시자는 Microsoft가 이러한 목적을 위한 에이전트 역할을 하도록 권한을 부여합니다. 게시자는 Microsoft가 소프트웨어 라이선스 거래를 용이하게 할 수 있도록 허용합니다. 게시자는 판매자, 공급자, 배포자 및 사용 허가자로 지정을 유지합니다.

Microsoft를 통해 고객은 Microsoft의 상업용 Marketplace 및 최종 사용자 사용권 계약의 사용 약관에 따라 소프트웨어를 주문, 라이선스 부여 및 사용할 수 있습니다. 사용자 고유의 최종 사용자 사용권 계약을 제공하거나 제품을 만들 때 [표준 계약](./standard-contract.md)을 선택해야 합니다.

### <a name="free-software-trials"></a>소프트웨어 평가판

거래 게시 시나리오의 경우 구독에 따라 소프트웨어 라이선스를 30일에서 120일까지 무료로 사용할 수 있습니다. 적용 가능한 Azure 인프라 사용량에 따라 고객이 변경됩니다.

### <a name="examples-of-pricing-and-store-fees"></a>가격 책정 및 스토어 요금 예

**사용량 기준**

사용량 기준 가격 책정의 비용 구조는 다음과 같습니다.

| **라이선스 비용** | **시간당 $1.00** |
|---------|---------|
| Azure 사용량 비용(D1/1개 코어) | 시간당 $0.14 |
| *Microsoft에서 고객에게 청구하는 요금* | *시간당 $1.14* |
||

이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $1.14를 청구합니다.

| **Microsoft에 청구되는 요금** | **시간당 $1.14**  |
|---------|---------|
| Microsoft는 라이선스 비용의 97%를 지불합니다. | 시간당 $0.97 |
| Microsoft은 라이선스 비용의 3%를 유지합니다.  |  시간당 $0.03 |
| Microsoft는 Azure 사용 비용의 100%를 유지합니다. | 시간당 $0.14 |
||

**BYOL(사용자 라이선스 필요)**

BYOL의 비용 구조는 다음과 같습니다.

| **라이선스 비용** | **사용자가 라이선스 요금을 협상하고 청구합니다.** |
|---------|---------|
|Azure 사용량 비용(D1/1개 코어)    |   시간당 $0.14     |
| *Microsoft에서 고객에게 청구하는 요금* | *시간당 $0.14* |
||

이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $0.14를 청구합니다.

| **Microsoft에 청구되는 요금** | **시간당 $0.14** |
|---------|---------|
| Microsoft는 Azure 사용량 비용을 유지합니다. | 시간당 $0.14 |
| Microsoft은 라이선스 비용의 0%를 유지합니다. | 시간당 $0.00 |
||

**SaaS 앱 구독**

SaaS 구독은 정액 요금으로 또는 사용자당 월간 또는 연간 단위로 가격이 책정될 수 있습니다. SaaS 제품에 대해 **Microsoft를 통해 판매** 옵션을 사용하도록 설정하는 경우 다음과 같은 비용 구조가 적용됩니다.

| **라이선스 비용** | **매월 $100.00** |
|--------------|---------|
| Azure 사용량 비용(D1/1개 코어) | 고객이 아닌 게시자에게 직접 청구됩니다. |
| *Microsoft에서 고객에게 청구하는 요금* | *월간 $100.00(참고: 게시자는 발생하거나 통과된 인프라 비용을 라이선스 요금으로 계산해야 함)* |
||

이 시나리오에서 Microsoft는 소프트웨어 라이선스에 대해 $100.00를 청구하고 $97.00를 지급합니다.

| **Microsoft에 청구되는 요금** | **매월 $100.00** |
|---------|---------|
| Microsoft는 라이선스 비용의 97%를 지불합니다.  | 매월 $97.00 |
| Microsoft은 라이선스 비용의 3%를 유지합니다. | 매월 $3.00 |

### <a name="commercial-marketplace-service-fees"></a>상업용 Marketplace 서비스 요금

고객이 상업용 Marketplace에서 거래 제품을 구매할 때 3% 표준 스토어 서비스 요금을 부과합니다.

### <a name="customer-invoicing-payment-billing-and-collections"></a>고객 처리 작성, 결제, 청구 및 수금

**송장 처리 및 결제**: 게시자는 고객의 기본 송장 처리 방법을 사용하여 구독 또는 [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) 소프트웨어 라이선스 요금을 제공할 수 있습니다.

**기업계약**: 고객의 기본 송장 작성 방법이 Microsoft 기업 계약인 경우 소프트웨어 라이선스 요금은 이 송장 처리 방법을 사용하여 Azure별 사용 요금과는 별도로 항목별 요금으로 청구됩니다.

**신용 카드 및 월별 청구서**: 고객은 신용 카드 및 월별 청구서를 사용하여 지불할 수 있습니다. 이 경우 소프트웨어 라이선스 요금은 기업 계약 시나리오와 마찬가지로 Azure별 사용 요금과는 별도로 항목별 요금으로 청구됩니다.

**무료 크레딧과 현금 약정 금액**: 일부 고객은 기업 계약의 현금 약정으로 Azure에 선불로 결제하거나 Azure 사용량에 대한 무료 크레딧을 받았습니다. 이러한 크레딧은 Azure 사용에 대한 요금을 지불하는 데는 사용할 수 있으나, 게시자 소프트웨어 라이선스 요금 지불에는 사용할 수 없습니다.

**요금 청구 및 징수**: 게시자 소프트웨어 라이선스 요금 청구는 고객이 선택한 송장 처리 방법을 사용하여 표시되며 송장 처리 타임라인을 따릅니다. 기업 계약을 사용하지 않는 고객은 Marketplace 소프트웨어 라이선스에 대해 월별로 청구됩니다. 기업 계약을 사용하는 고객은 분기별로 표시된 청구서를 통해 월별로 청구됩니다.

구독 또는 종량제(사용량 기준이라고도 함) 가격 책정 모델을 선택한 경우 Microsoft는 게시자의 에이전트 역할을 하며 대금 청구, 결제 및 수금의 모든 측면을 담당합니다.

### <a name="publisher-payout-and-reporting"></a>게시자 지급 및 보고

Microsoft가 에이전트로서 수금한 모든 소프트웨어 라이선스 요금은 달리 지정하지 않은 경우 3% 저장소 서비스 요금이 적용되고 게시자 지급 시점에 차감됩니다.

고객은 일반적으로 기업 계약 또는 종량제 계약이 활성화된 신용 카드를 사용하여 구매합니다. 계약 유형에 따라 청구, 송장 처리, 수금 및 지급 시점이 결정됩니다.

>[!NOTE]
>거래 게시 옵션에 대한 모든 보고 및 인사이트는 파트너 센터의 분석 섹션을 통해 사용할 수 있습니다.

#### <a name="billing-questions-and-support"></a>청구 관련 질문 및 지원

자세한 내용과 법률 정책은 [Microsoft 게시자 계약](/legal/marketplace/msft-publisher-agreement)을 참조하세요. 청구 관련 질문에 대한 도움말은 [상업용 Marketplace 게시자 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="transact-requirements"></a>거래 요구 사항

이 섹션에서는 각 제품 유형에 대한 거래 요구 사항을 설명합니다.

### <a name="requirements-for-all-offer-types"></a>모든 제품 유형에 대한 요구 사항

- 제품의 가격 책정 모델과 관계 없이 Microsoft 계정 및 금융 정보는 거래 게시 옵션에 필요합니다.
- 필수 금융 정보에는 지급 계정 및 세금 프로필이 포함됩니다.

이러한 계정을 설정하는 방법에 대한 자세한 내용은 [파트너 센터에서 상업용 Marketplace 계정 관리](manage-account.md)를 참조하세요.

### <a name="requirements-for-specific-offer-types"></a>특정 제품 유형에 대한 요구 사항

Microsoft를 통한 거래 기능은 다음 상업용 Marketplace 제품 유형에만 사용할 수 있습니다. 이 목록은 이러한 제품 유형을 상업용 Marketplace에서 거래할 수 있도록 하기 위한 요구 사항을 제공합니다.

- **Azure 애플리케이션(솔루션 템플릿 및 관리 형 애플리케이션 플랜**: 일부 경우에 Azure 인프라 사용 요금은 동일한 청구서에 소프트웨어 라이선스 요금과는 별도로 표시되어 고객에게 전달됩니다. 그러나 ISV 인프라 요금에 대한 관리형 플랜 앱을 구성하는 경우 Azure 리소스 요금은 게시자로 청구되고 고객은 인프라, 소프트웨어 라이선스 및 관리 서비스 비용을 포함하는 고정 요금을 받습니다.

- **Azure Virtual Machine**: 무료, BYOL 또는 사용량 기반 가격 책정 모델 중에서 선택합니다. 고객의 Azure 청구서에서 Microsoft는 게시자 소프트웨어 라이선스 요금을 기본 Azure 인프라 요금과는 별도로 표시합니다. Azure 인프라 요금은 게시자 소프트웨어 사용으로 발생됩니다.

- **SaaS 애플리케이션**: 다중 테넌트 솔루션이어야 하며 인증에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)를 사용하고 [SaaS 처리 API](partner-center-portal/pc-saas-fulfillment-api-v2.md)와 통합해야 합니다. Azure 인프라 사용량은 관리되고 사용자(게시자)에게 직접 청구되므로 Azure 인프라 사용 요금과 소프트웨어 라이선스 요금을 단일 비용 항목으로 고려해야 합니다. 자세한 지침은 [상업용 Marketplace에 대한 SaaS 제품을 계획하는 방법](plan-saas-offer.md#plans)을 참조하세요.

## <a name="private-plans"></a>비공개 플랜

협상된 거래별 가격 책정 또는 사용자 지정 구성으로 완성된 제품에 대해 비공개 플랜을 만들 수 있습니다.

비공개 플랜을 사용하면 공개적으로 제공되는 것보다 특정 고객에게 더 높거나 낮은 가격을 제공할 수 있습니다. 비공개 플랜은 제품에 대해 할인하거나 프리미엄을 추가하는 데 사용될 수 있습니다. 비공개 플랜은 플랜 수준에서 Azure 구독을 나열하면 둘 이상의 고객이 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대한 예는 온라인 스토어별 게시 패턴을 검토하세요.
- [제품 유형별 게시 가이드](publisher-guide-by-offer-type.md)
- [상업용 마켓플레이스 제품에 대한 요금제 및 가격 책정](./plans-pricing.md)