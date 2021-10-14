---
title: Azure 구독 및 예약 전송 허브
description: 이 문서는 Azure 구독을 전송하는 데 필요한 사항을 이해하는 데 도움이 되며, 자세한 내용은 다른 문서에 대한 링크를 제공합니다.
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: dbe893dc471f055dd5b062e7ce5dcf492dd0f421
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008688"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Azure 구독 및 예약 전송 허브

이 문서에서는 제품이라고 하는 Azure 구독 및 Azure 예약에 대해 지원되는 전송 유형을 _설명합니다._ 또한 다른 청구 계약 간에 Azure 제품을 전송하는 데 필요한 사항을 이해하고 특정 전송에 대한 자세한 정보를 제공하는 다른 문서에 대한 링크를 제공합니다. Azure 제품은 다양한 Azure 계약 유형에 따라 생성되며 원본 계약 유형에서 다른 계약 유형으로의 이전은 원본 및 대상 계약 유형에 따라 달라집니다. Azure 제품 전송은 원본 및 대상 계약 유형에 따라 자동 또는 수동 프로세스일 수 있습니다. 수동 프로세스인 경우 계약 유형에 따라 필요한 수동 작업이 결정됩니다.

> [!NOTE]
> 다양한 유형의 Azure 제품이 있지만 모든 제품을 한 유형에서 다른 유형으로 전송할 수 있는 것은 아닙니다. 지원되는 제품 전송만 이 문서에 설명되어 있습니다. 이 문서에서 해결되지 않은 상황에 대한 도움이 필요한 경우 지원을 위해 [Azure 지원 요청을](https://go.microsoft.com/fwlink/?linkid=2083458) 만들 수 있습니다.

또한 이 문서는 Azure 제품의 청구 소유권을 다른 계정으로 _양도하기 전에_ 알아야 할 사항을 이해하는 데 도움이 됩니다. 조직을 떠나거나 제품을 다른 계정으로 청구하려는 경우 Azure 제품의 청구 소유권을 이전할 수 있습니다. 청구 소유권을 다른 계정으로 양도하면 청구 작업에 대한 새 계정 권한이 관리자에게 제공됩니다. 결제 방법을 변경하고, 요금을 보고, 제품을 취소할 수 있습니다.

청구 소유권을 유지하지만 제품 유형을 변경하려면 Azure [구독을 다른 제안으로 전환을 참조하세요.](switch-azure-offer.md) 제품의 리소스에 액세스할 수 있는 사람을 제어하려면 [Azure 기본 제공 역할을 참조하세요.](../../role-based-access-control/built-in-roles.md)

EA(기업계약) 고객인 경우 엔터프라이즈 관리자는 EA 포털의 계정 간에 제품의 청구 소유권을 양도할 수 있습니다. 자세한 내용은 [Azure 구독 또는 계정 소유권 변경](ea-portal-administration.md#change-azure-subscription-or-account-ownership)을 참조하세요.

이 문서에서는 제품 전송에 중점을 둡니다. 그러나 일부 제품 전송 시나리오에는 리소스 전송이 필요하기 때문에 리소스 전송에 대해서도 설명합니다.

다른 Azure AD 테넌트 간의 제품 전송에 대한 자세한 내용은 [Azure 구독을 다른 Azure AD 디렉터리로 이전을 참조하세요.](../../role-based-access-control/transfer-subscription.md)

> [!NOTE]
> 대부분의 청구 소유권 이전은 기본 제품의 서비스 테넌트는 변경하지 않습니다. 가동 중지 시간은 발생하지 않습니다. 그러나 청구 테넌트는 변경되더라도 실행 중인 서비스 또는 리소스에 영향을 주지 않습니다.

## <a name="product-transfer-planning"></a>제품 이전 계획

제품 이전 계획을 시작할 때 다음 질문에 대답하는 데 필요한 정보를 고려합니다.

- 제품 전송이 필요한 이유는 무엇인가요?
- 제품 이전의 원하는 타임라인은 무엇인가요?
- 제품의 현재 제품 유형은 무엇이며 어떤 제품으로 이전하시겠습니까?
  - PAYG(종량제)라고도 하는 MOSP(Microsoft Online Service Program)
  - CSP의 이전 Azure 제품
  - CSP의 새 Azure 제안(MPA(Microsoft 파트너 계약 Azure 플랜)이라고도 함)
  - EA(기업 계약)
  - Azure.com 통해 Azure 서비스를 구매할 때 Microsoft 담당자 및 개별 MCA를 통해 Azure 서비스를 구매할 때 Enterprise 동작의 MCA(Microsoft 고객 계약)
  - MSDN, BizSpark, EOPEN, Azure Pass 및 무료 평가판과 같은 기타
- 양도를 수행하기 위해 제품에 필요한 권한이 있나요? 각 전송 유형에 필요한 특정 사용 권한은 다음 제품 이전 지원 표에 나열되어 있습니다.
  - 계정의 청구 관리자만 구독 소유권을 양도할 수 있습니다.
  - 청구 관리자 소유자만 예약 소유권을 양도할 수 있습니다.
- 예약을 활용하고 구독을 사용하여 예약을 전송해야 하는 기존 구독이 있나요?

이전을 계속하기 전에 각 질문에 대한 답변이 있어야 합니다.

위의 질문에 대한 답변은 다른 사용자와 조기에 소통하여 기대치 및 타임라인을 설정하는 데 도움이 될 수 있습니다. 제품 이전 활동은 크게 다르지만 전송이 예상보다 오래 걸릴 수 있습니다.

원본 및 대상 제안 유형 질문에 대한 답변은 따라야 하는 기술 경로를 정의하고 전송 조합에 포함될 수 있는 제한 사항을 식별하는 데 도움이 됩니다. 제한 사항은 다음 섹션에서 자세히 설명합니다.

## <a name="product-transfer-support"></a>제품 이전 지원

다음 표에서는 다양한 계약 유형 간의 제품 이전 지원에 대해 설명합니다. 각 전송 유형에 대한 자세한 내용을 확인할 수 있는 링크가 제공됩니다.

현재 무료 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 또는 [AIO(Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) 제품에는 전송이 지원되지 않습니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

지원 플랜은 이전할 수 없습니다. 지원 플랜이 있는 경우 이전 계약에 대해 취소한 다음 새 계약에 대한 새 계약을 구입해야 합니다.

개발/테스트 제품은 다음 표에 표시되지 않습니다. 개발/테스트 제품에 대한 전송은 다른 제품 유형과 동일한 방식으로 처리됩니다. 예를 들어 EA 개발/테스트 제품 전송은 EA 제품 전송 방식으로 처리됩니다.

> [!NOTE]
> 대부분의 지원되는 제품 전송을 통해 예약 전송. 그러나 다음 표에 언급된 대로 예약이 전송되지 않는 일부 전송이 있습니다.

| 원본(현재) 제품 계약 유형 | 대상(미래) 제품 계약 유형 | 메모 |
| --- | --- | --- |
| EA | MOSP(PAYG) | <ul><li>EA 등록에서 MOSP 구독으로 이전하려면 [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요합니다. <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| EA | MCA - 개별 | <ul><li>자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 예약은 자동으로 전송되지 않으므로 [전송하려면 청구 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 열어야 합니다. |
| EA | EA | <ul><li>EA 등록 간에 양도하려면 [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요합니다. <li>예약은 등록 간에 자동으로 양도되지 않으므로 양도하려면 [청구 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 열어야 합니다. <li> 동일한 등록 내에서 양도는 계정 소유자 변경과 동일한 작업입니다. 자세한 내용은 [EA 구독 또는 계정 소유권 변경을 참조하세요.](ea-portal-administration.md#change-azure-subscription-or-account-ownership) |
| EA | MCA - Enterprise | <ul><li> 모든 등록 제품 전송은 EA에서 MCA 전환 프로세스의 일부로 완료됩니다. 자세한 내용은 [Microsoft 고객 계약 청구 계정에서 기업계약 작업 완료를 참조하세요.](mca-enterprise-operations.md) <li> 등록의 모든 제품이 아닌 특정 제품을 양도하려는 경우 Microsoft 고객 계약 [대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. |
| EA | MPA | <ul><li> MSP(Azure Expert 관리형 서비스 공급자)로 인증된 CSP 직접 청구 파트너만 [EA(직접 기업계약)가](https://partner.microsoft.com/membership/azure-expert-msp) 있는 고객을 위해 Azure 제품 전송을 요청할 수 있습니다. 자세한 내용은 [MPA 계정에 대한 Azure 구독의 청구 소유권 얻기를 참조하세요.](mpa-request-ownership.md) 제품 이전은 MCA(Microsoft 고객 계약)에 동의하고 CSP 프로그램을 통해 Azure 플랜을 구매한 고객에게만 허용됩니다. <li> 제한 사항 및 제한이 있습니다. 자세한 내용은 [CSP 파트너에게 EA 구독 이전을 참조하세요.](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner)  |
| MCA - 개별 | MOSP(PAYG) | <ul><li> 자세한 내용은 [Azure 구독의 청구 소유권을 다른 계정으로 이전을 참조하세요.](billing-subscription-transfer.md) <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| MCA - 개별 | MCA - 개별 | <ul><li>자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 셀프 서비스 예약 전송이 지원됩니다. |
| MCA - 개별 | EA | <ul><li> 자세한 내용은 [EA로 구독 이전을 참조하세요.](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea) <li> 셀프 서비스 예약 전송이 지원됩니다. |
| MCA - 개별 | MCA-Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요.<li> 셀프 서비스 예약 전송이 지원 됩니다. |
| MCA-Enterprise | MOSP | <ul><li> [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요 합니다. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| MCA-Enterprise | MCA-개별 | <ul><li> 자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 예약은 자동으로 전송 되지 않으므로 [대금 청구 지원 티켓](https://azure.microsoft.com/support/create-ticket/) 을 열어 전송 해야 합니다. |
| MCA-Enterprise | MCA-Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 예약은 자동으로 전송 되지 않으므로 [대금 청구 지원 티켓](https://azure.microsoft.com/support/create-ticket/) 을 열어 전송 해야 합니다.  |
| CSP의 이전 Azure 제품 | CSP의 이전 Azure 제품 | <ul><li> [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요 합니다. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| CSP의 이전 Azure 제품 | MPA | 자세한 내용은 [azure 요금제에서 고객의 azure 구독을 다른 CSP에 전송](/partner-center/transfer-azure-subscriptions-under-azure-plan)을 참조 하세요. |
| MPA | EA | <ul><li> 자동 전송은 지원 되지 않습니다. 모든 전송에는 기존 MPA 제품에서 새로 만들었거나 기존 EA 제품으로 수동으로 이동 하는 리소스가 필요 합니다. <li> [리소스 전송 수행](#perform-resource-transfers) 섹션의 정보를 사용 합니다. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| MPA | MPA | <ul><li> 자세한 내용은 [azure 요금제에서 고객의 azure 구독을 다른 CSP에 전송](/partner-center/transfer-azure-subscriptions-under-azure-plan)을 참조 하세요. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| MOSP (PAYG) | MOSP (PAYG) | <ul><li> 구독의 청구 소유자를 변경 하는 경우 [Azure 구독의 청구 소유권을 다른 계정으로 전송](billing-subscription-transfer.md)을 참조 하세요. <li> 예약은 자동으로 전송 되지 않으므로 [대금 청구 지원 티켓](https://azure.microsoft.com/support/create-ticket/) 을 열어 전송 해야 합니다.  |
| MOSP (PAYG) | MCA-개별 | <ul><li> 자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 셀프 서비스 예약 전송이 지원 됩니다. |
| MOSP (PAYG) | EA | 자세한 내용은 [EA로 구독 전송](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea)을 참조 하세요. |
| MOSP (PAYG) | MCA-Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 셀프 서비스 예약 전송이 지원 됩니다. |

## <a name="perform-resource-transfers"></a>리소스 전송 수행

일부 제품 전송에서는 구독 간에 Azure 리소스를 수동으로 이동 해야 합니다. 리소스를 이동 하면 가동 중지 시간이 발생할 수 있으며 Vm, NSGs, App Services 등의 Azure 리소스 유형을 이동 하는 다양 한 제한 사항이 있습니다.

Microsoft는 구독 간에 리소스를 자동으로 이동 하는 도구를 제공 하지 않습니다. 필요한 경우 구독 간에 Azure 리소스를 수동으로 이동 해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요. 많은 수의 리소스를 이동할 때 추가 시간과 계획이 필요 합니다.

## <a name="other-planning-considerations"></a>기타 계획 고려 사항

제품 전송을 시작 하기 전에 다른 고려 사항에 대 한 자세한 내용을 보려면 다음 섹션을 참조 하십시오.

### <a name="resources-transfer-with-subscriptions"></a>구독과 함께 리소스 전송

청구 소유권이 변경 되는 경우 리소스는 영향을 받지 않습니다. 그러나 리소스를 이동 하거나 서비스 테 넌 트를 변경 하는 경우 리소스에 영향을 줄 수 있습니다.

### <a name="transfer-a-product-from-one-account-to-another"></a>한 계정에서 다른 계정으로 제품 전송

두 계정의 관리자 인 경우 계정 간에 제품을 전송할 수 있습니다. 계정은 개념적으로 두 명의 사용자 계정을 고려 하므로 계정 간에 제품을 전송할 수 있습니다.

제품을 전송 하는 데 필요한 단계를 보려면 [Azure 구독의 청구 소유권 이전](billing-subscription-transfer.md)을 참조 하세요.

### <a name="transferring-a-product-shouldnt-create-downtime"></a>제품을 전송 하면 가동 중지 시간이 발생 하지 않아야 합니다.

동일한 Azure AD 테 넌 트의 계정으로 제품을 전송 하는 경우 구독에서 실행 중인 리소스에 영향을 주지 않습니다. 그러나 PowerShell에 저장된 컨텍스트 정보는 업데이트되지 않으므로 제거하거나 설정을 변경해야 할 수 있습니다. 리소스를 이동 하거나 서비스 테 넌 트를 변경 하는 경우 리소스에 영향을 줄 수 있습니다.

### <a name="new-account-usage-and-billing-history"></a>새 계정 사용량 및 청구 내역

새 계정에 대해 사용자가 사용할 수 있는 유일한 정보는 전송 시점부터 시작 하 여 사용 및 청구 내역입니다. 사용량 및 청구 기록은 제품과 함께 전송 되지 않습니다.

### <a name="remaining-product-credits"></a>남은 제품 크레딧

Visual Studio 또는 Microsoft 파트너 네트워크 제품이 있는 경우 월간 크레딧을 받습니다. 크레딧이 새 계정에서 제품과 함께 전달 되지 않습니다. 양도 요청을 수락하려는 사용자는 Visual Studio 라이선스가 있어야만 양도 요청을 수락할 수 있습니다. 이 제품은 사용자 계정에서 사용할 수 있는 Visual Studio 크레딧을 사용 합니다. 자세한 내용은 [Visual Studio 및 파트너 네트워크 구독 양도](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)를 참조하세요.

### <a name="users-keep-access-to-transferred-resources"></a>사용자가 양도된 리소스에 계속 액세스할 수 있음

제품의 리소스에 대 한 액세스 권한이 있는 사용자는 청구 소유권이 전송 될 때 액세스 권한을 유지 해야 합니다. 그러나 [관리자 역할](add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거될 수 있습니다. 계정이 제품의 테 넌 트 외의 Azure AD 테 넌 트에 있고 전송 요청을 보낸 사용자가 해당 제품을 계정의 테 넌 트로 이동 하는 경우 액세스가 손실 됩니다.

Azure Portal의 제품에 있는 리소스에 액세스 하기 위해 Azure 역할 할당이 있는 사용자를 볼 수 있습니다. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다. 그런 다음 확인 하려는 제품을 선택 하 고 왼쪽 창에서 **액세스 제어 (IAM)** 를 선택 합니다. 그런 다음 페이지 맨 위에서  **역할 할당**  을 선택 합니다. 역할 할당 페이지에는 제품에 대 한 액세스 권한이 있는 모든 사용자가 나열 됩니다.

전송 중에 [Azure 역할 할당이](../../role-based-access-control/role-assignments-portal.md) 제거 되더라도 원래 소유자 계정의 사용자는 다음을 비롯 한 다른 보안 메커니즘을 통해 계속 해 서 제품에 액세스할 수 있습니다.

- 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)를 참조하세요.
- 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.
- Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

수신자가 리소스에 대 한 액세스를 제한 해야 하는 경우 서비스와 연결 된 암호를 업데이트 하는 것을 고려해 야 합니다. 대부분의 리소스를 업데이트할 수 있습니다. [Azure Portal](https://portal.azure.com/)에 로그인한 후 허브 메뉴에서 **모든 리소스** 를 선택합니다. 다음으로, 리소스를 선택합니다. 그리고 리소스 페이지에서 **설정** 을 선택합니다. 여기서 기존 비밀을 보고 업데이트할 수 있습니다.

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>소유권을 받으면 사용량만큼 요금을 지불합니다.

양도된 시점 이후부터 보고되는 사용량에 대한 요금을 결제할 책임은 양수인의 계정에 있습니다. 전송 전에 발생 했지만 나중에 보고 된 몇 가지 사용법이 있을 수 있습니다. 사용량은 양수인 계정의 청구서에 포함됩니다.

### <a name="transfer-enterprise-agreement-product-ownership"></a>기업계약 제품 소유권 이전

엔터프라이즈 관리자는 원래 계정 소유자가 더 이상 조직의 일부가 아닌 경우에도 모든 계정에 대한 계정 소유권을 업데이트할 수 있습니다. Azure 기업계약 계정을 양도하는 방법에 대한 자세한 내용은 [Azure Enterprise 양도](ea-transfers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [리소스를 새 리소스 그룹 또는 구독으로 이동합니다.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
