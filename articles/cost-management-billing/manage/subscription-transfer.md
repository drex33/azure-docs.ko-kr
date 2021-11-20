---
title: Azure 구독 및 예약 전송 허브
description: 이 문서에서는 Azure 구독을 전송 하는 데 필요한 사항을 이해 하 고 자세한 정보를 확인할 수 있는 다른 문서에 대 한 링크를 제공 합니다.
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 500e6c63a650f5f4eaea030f3e191ac0ac5a9b2c
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132870353"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Azure 구독 및 예약 전송 허브

이 문서에서는 _제품_ 이라고 하는 azure 구독 및 azure 예약에 대해 지원 되는 전송 유형을 설명 합니다. 또한 다양 한 청구 계약 간에 Azure 제품을 전송 하는 데 필요한 사항을 이해 하 고 특정 전송에 대 한 자세한 정보를 볼 수 있는 다른 문서에 대 한 링크를 제공 합니다. Azure 제품은 다양 한 Azure 계약 유형에 따라 만들어지며 원본 계약 유형에 서 다른 유형으로의 전송은 원본 및 대상 규약 유형에 따라 달라 집니다. Azure 제품 전송은 원본 및 대상 계약 유형에 따라 자동 또는 수동 프로세스가 될 수 있습니다. 수동 프로세스의 경우 계약 유형에 따라 수동 노력이 얼마나 필요한 지 결정 됩니다.

> [!NOTE]
> 여러 유형의 Azure 제품이 있지만 한 유형에 서 다른 유형으로 모든 제품을 전송할 수 있는 것은 아닙니다. 지원 되는 제품 양도이 문서에 설명 되어 있습니다. 이 문서에서 다루지 않은 상황에 대 한 도움이 필요한 경우 지원에 대 한 [Azure 지원 요청](https://go.microsoft.com/fwlink/?linkid=2083458) 을 만들 수 있습니다.

이 문서는 Azure 제품의 청구 소유권을 다른 계정으로 이전 _하기 전에_ 알아두어야 할 사항을 이해 하는 데도 도움이 됩니다. 조직을 탈퇴 하거나 다른 계정으로 제품에 대 한 요금이 청구 되도록 하려면 Azure 제품의 청구 소유권을 이전 하는 것이 좋습니다. 청구 소유권을 다른 계정으로 양도하면 청구 작업에 대한 새 계정 권한이 관리자에게 제공됩니다. 지불 방법을 변경 하 고 요금을 확인 하 고 제품을 취소할 수 있습니다.

청구 소유권을 유지 하 되 제품 유형을 변경 하려면 [Azure 구독을 다른 제품으로 전환](switch-azure-offer.md)을 참조 하세요. 제품의 리소스에 액세스할 수 있는 사용자를 제어 하려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

ea (기업계약) 고객 인 경우 엔터프라이즈 관리자는 ea 포털의 계정 간에 제품의 청구 소유권을 양도할 수 있습니다. 자세한 내용은 [Azure 구독 또는 계정 소유권 변경](ea-portal-administration.md#change-azure-subscription-or-account-ownership)을 참조하세요.

이 문서에서는 제품 전송에 대해 집중적으로 설명 합니다. 그러나 리소스 전송은 일부 제품 전송 시나리오에 필요 하기 때문에 설명 되어 있습니다.

서로 다른 azure AD 테 넌 트 간의 제품 전송에 대 한 자세한 내용은 [azure 구독을 다른 AZURE ad 디렉터리에 전송](../../role-based-access-control/transfer-subscription.md)을 참조 하세요.

> [!NOTE]
> 대부분의 청구 소유권 전송은 기본 제품의 서비스 테 넌 트를 변경 하지 않습니다. 가동 중지 시간을 발생 시 키 지 않습니다. 그러나 청구 테 넌 트가 변경 되는 경우에도 변경 내용은 실행 중인 서비스 또는 리소스에 영향을 주지 않습니다.

## <a name="product-transfer-planning"></a>제품 양도 계획

제품 전송 계획을 시작할 때 다음 질문에 대 한 대답에 필요한 정보를 고려 하십시오.

- 제품 양도가 필요한 이유는 무엇 인가요?
- 제품 전송에 대 한 원하는 타임 라인은 무엇 인가요?
- 제품의 현재 제안 유형 및 전송 하려는 항목은 무엇 인가요?
  - MOSP (Microsoft Online Service Program) (종 량 제 (PAYG) 라고도 함)
  - CSP의 이전 Azure 제품
  - CSP의 새로운 Azure 제품 (MPA (Microsoft 파트너 계약)을 사용 하는 Azure 요금제 라고도 함)
  - EA(기업 계약)
  - Azure.com를 통해 azure 서비스를 구매할 때 microsoft 담당자 및 개별 MCA를 통해 azure 서비스를 구매할 때 Enterprise 동작의 microsoft MCA (고객 계약)
  - MSDN, BizSpark, eopen, Azure Pass 및 무료 평가판과 같은 기타 항목
- 제품에 대 한 필수 사용 권한이 있어야 전송을 수행할 수 있나요? 각 전송 유형에 필요한 특정 사용 권한은 다음 제품 이전 지원 표에 나열 되어 있습니다.
  - 계정의 대금 청구 관리자만 구독 소유권을 양도할 수 있습니다.
  - 청구 관리자 소유자만 예약 소유권을 전송할 수 있습니다.
- 예약을 활용 하는 기존 구독이 있나요? 구독을 사용 하 여 예약을 전송 해야 하나요?

모든 전송 작업을 계속 하기 전에 각 질문에 대 한 답변이 있어야 합니다.

위의 질문에 대 한 답변은 예상과 타임 라인을 설정 하기 위해 다른 사용자와 조기에 통신 하는 데 도움이 될 수 있습니다. 제품 전송 작업은 크게 다르지만 전송은 예상 보다 오래 걸릴 수 있습니다.

원본 및 대상 제공 유형 질문에 대 한 답변은 따라야 하는 기술 경로를 정의 하 고 전송 조합에 포함 될 수 있는 제한 사항을 파악 하는 데 도움이 됩니다. 제한 사항은 다음 섹션에서 자세히 설명 합니다.

## <a name="product-transfer-support"></a>제품 양도 지원

다음 표에서는 다양 한 계약 형식 간의 제품 전송 지원에 대해 설명 합니다. 각 전송 형식에 대 한 자세한 내용은 링크를 제공 합니다.

현재 전송은 [무료 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 또는 [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 제품에 대해 지원 되지 않습니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

지원 플랜을 전송할 수 없습니다. 지원 계획이 있는 경우 이전 계약의 계약을 취소 하 고 새 계약에 대 한 새 계약을 구입 해야 합니다.

다음 표에는 개발/테스트 제품이 나와 있지 않습니다. 개발/테스트 제품에 대 한 전송은 다른 제품 유형과 동일한 방식으로 처리 됩니다. 예를 들어 ea 개발/테스트 제품 전송은 EA 제품 전송 방식으로 처리 됩니다.

> [!NOTE]
> 지원 되는 제품 전송이 가장 많은 예약 전송 그러나 다음 표에 설명 된 것 처럼 예약이 전송 되지 않는 몇 가지 전송이 있습니다.

| 원본 (현재) 제품 계약 유형 | 대상 (향후) 제품 계약 유형 | 메모 |
| --- | --- | --- |
| EA | MOSP (PAYG) | <ul><li>EA 등록에서 MOSP 구독으로 전송 하려면 [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요 합니다. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| EA | MCA-개별 | <ul><li>자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 예약은 자동으로 전송 되지 않으므로 [대금 청구 지원 티켓](https://azure.microsoft.com/support/create-ticket/) 을 열어 전송 해야 합니다. |
| EA | EA | <ul><li>EA 등록 간에 전송 하려면 [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요 합니다. <li>예약은 등록 간에 자동으로 전송 되지 않으므로 [청구 지원 티켓](https://azure.microsoft.com/support/create-ticket/) 을 열어 전송 해야 합니다. <li> 동일한 등록 내에서 전송은 계정 소유자를 변경 하는 것과 같은 작업입니다. 자세한 내용은 [EA 구독 또는 계정 소유권 변경](ea-portal-administration.md#change-azure-subscription-or-account-ownership)을 참조 하세요. |
| EA | MCA-Enterprise | <ul><li> 모든 등록 제품 전송은 EA의 MCA 전환 프로세스의 일부로 완료 됩니다. 자세한 내용은 [Microsoft 고객 계약에 대 한 청구 계정에서 기업계약 작업 완료](mca-enterprise-operations.md)를 참조 하세요. <li> 등록의 모든 제품이 아니라 특정 제품을 전송 하려는 경우 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. |
| EA | MPA | <ul><li> [azure MSP MSP (관리 서비스 공급자)](https://partner.microsoft.com/membership/azure-expert-msp) 로 인증 된 CSP 직접 청구 파트너만 EA (직접 기업계약)가 있는 고객을 위해 azure 제품을 전송 하도록 요청할 수 있습니다. 자세한 내용은 [MPA 계정에 대 한 Azure 구독의 청구 소유권 가져오기](mpa-request-ownership.md)를 참조 하세요. Microsoft MCA (고객 계약)를 수락 하 고 CSP 프로그램을 사용 하 여 Azure 요금제를 구매한 고객에 대해서만 제품 전송이 허용 됩니다. <li> 제한 사항 및 제한 사항이 있습니다. 자세한 내용은 [CSP 파트너에 게 EA 구독 전송](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner)을 참조 하세요.  |
| MCA-개별 | MOSP (PAYG) | <ul><li> 자세한 내용은 [Azure 구독의 청구 소유권을 다른 계정으로 전송](billing-subscription-transfer.md)을 참조 하세요. <li> 예약은 자동으로 전송 및 전송 되지 않습니다. |
| MCA-개별 | MCA-개별 | <ul><li>자세한 내용은 [Microsoft 고객 계약에 대 한 Azure 구독 청구 소유권 이전](mca-request-billing-ownership.md)을 참조 하세요. <li> 셀프 서비스 예약 전송이 지원 됩니다. |
| MCA-개별 | EA | <ul><li> 자세한 내용은 [EA로 구독 전송](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea)을 참조 하세요. <li> 셀프 서비스 예약 전송이 지원 됩니다. |
| MCA-개별 | MCA - Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요.<li> 셀프 서비스 예약 전송이 지원됩니다. |
| MCA - Enterprise | MOSP | <ul><li> [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요합니다. <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| MCA - Enterprise | MCA - 개별 | <ul><li> 자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 예약은 자동으로 전송되지 않으므로 [전송하려면 청구 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 열어야 합니다. |
| MCA - Enterprise | MCA - Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 예약은 자동으로 전송되지 않으므로 [전송하려면 청구 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 열어야 합니다.  |
| CSP의 이전 Azure 제품 | CSP의 이전 Azure 제품 | <ul><li> [청구 지원 티켓이](https://azure.microsoft.com/support/create-ticket/)필요합니다. <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| CSP의 이전 Azure 제품 | MPA | 자세한 내용은 [고객의 Azure 구독을 다른 CSP로 이전(Azure 플랜에 따라)을](/partner-center/transfer-azure-subscriptions-under-azure-plan)참조하세요. |
| MPA | EA | <ul><li> 자동 전송은 지원되지 않습니다. 모든 전송을 수행하려면 리소스를 기존 MPA 제품에서 새로 만든 또는 기존 EA 제품으로 수동으로 이동해야 합니다. <li> [리소스 전송 수행](#perform-resource-transfers) 섹션의 정보를 사용합니다. <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| MPA | MPA | <ul><li> 자세한 내용은 [고객의 Azure 구독을 다른 CSP로 이전(Azure 플랜에 따라)을](/partner-center/transfer-azure-subscriptions-under-azure-plan)참조하세요. <li> 예약은 자동으로 전송되지 않으며 전송은 지원되지 않습니다. |
| MOSP(PAYG) | MOSP(PAYG) | <ul><li> 구독의 청구 소유자를 변경하는 경우 Azure 구독의 [청구 소유권을 다른 계정으로 이전을 참조하세요.](billing-subscription-transfer.md) <li> 예약은 자동으로 전송되지 않으므로 [전송하려면 청구 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 열어야 합니다.  |
| MOSP(PAYG) | MCA - 개별 | <ul><li> 자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 셀프 서비스 예약 전송이 지원됩니다. |
| MOSP(PAYG) | EA | 자세한 내용은 [EA에 구독 이전을 참조하세요.](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea) |
| MOSP(PAYG) | MCA - Enterprise | <ul><li> 자세한 내용은 [Microsoft 고객 계약 대한 Azure 구독 청구 소유권 이전을](mca-request-billing-ownership.md)참조하세요. <li> 셀프 서비스 예약 전송이 지원됩니다. |

## <a name="perform-resource-transfers"></a>리소스 전송 수행

일부 제품 전송에서는 구독 간에 Azure 리소스를 수동으로 이동해야 합니다. 리소스를 이동하면 가동 중지 시간이 발생할 수 있으며 VM, NSG, App Services 등과 같은 Azure 리소스 종류를 이동하는 데 다양한 제한이 있습니다.

Microsoft는 구독 간에 리소스를 자동으로 이동하는 도구를 제공하지 않습니다. 필요한 경우 구독 간에 Azure 리소스를 수동으로 이동해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동을](../../azure-resource-manager/management/move-resource-group-and-subscription.md)참조하세요. 이동할 리소스가 많은 경우 추가 시간 및 계획이 필요합니다.

## <a name="other-planning-considerations"></a>기타 계획 고려 사항

제품 이전을 시작하기 전에 다른 고려 사항에 대해 자세히 알아보려면 다음 섹션을 읽어보세요.

### <a name="transfer-terms-and-conditions"></a>이전 약관

전송을 보내거나 동의하면 계약조건에 동의하는 것입니다. 다음 정보는 추가 세부 정보를 제공합니다.

#### <a name="send-transfer"></a>전송 보내기

전송 요청을 보낼 때 는 **전송 요청 보내기** 옵션을 선택해야 합니다. 선택하여 다음 약관에도 동의합니다.

_이 전송 요청을 보내면 선택한 항목이 전환 날짜(전송이 성공적으로 완료된 날짜)를 기준으로 계정으로 전송된다는 것을 승인하고 동의합니다. 전환 날짜를 기준으로 전송 항목과 관련된 모든 진행 중인 예약된 청구에 대해 Microsoft에 책임을 져야 합니다. Microsoft에서 선불 구독(예약 인스턴스 포함)을 계정으로 이동하면 입니다. 계정에 양도된 선불 구독을 취소할 수 없다는 데 동의합니다._

#### <a name="transfer-acceptance"></a>양도 동의

전송을 수락하는 경우 에서 **검토 + 유효성 검사** 옵션을 선택해야 합니다. 선택하여 다음 약관에도 동의합니다.

_이 전송 요청을 수락하면 표시된 항목이 전환 날짜(전송이 성공적으로 완료된 날짜)를 기준으로 지정된 대상 계정으로 전송된다는 것을 승인하고 동의합니다. 선택한 경우 선불 구독(예약 인스턴스 포함)은 대상 계정으로 이동되며 전환 날짜를 기준으로 이전 항목과 관련된 지속적인 지불 의무(있는 경우)에 대해 Microsoft에 더 이상 책임을 지지 않습니다._

### <a name="resources-transfer-with-subscriptions"></a>구독을 통해 리소스 전송

청구 소유권만 변경되면 리소스는 영향을 받지 않습니다. 그러나 리소스 이동 또는 서비스 테넌트 변경이 수행되면 리소스가 영향을 받을 수 있습니다.

### <a name="transfer-a-product-from-one-account-to-another"></a>한 계정에서 다른 계정으로 제품 전송

두 계정의 관리자인 경우 계정 간에 제품을 양도할 수 있습니다. 계정은 개념적으로 서로 다른 두 사용자의 계정으로 간주되므로 계정 간에 제품을 양도할 수 있습니다.

제품을 양도하는 데 필요한 단계를 보려면 [Azure 구독의 청구 소유권 이전을](billing-subscription-transfer.md)참조하세요.

### <a name="transferring-a-product-shouldnt-create-downtime"></a>제품을 전송해도 가동 중지 시간이 만들어지지 않습니다.

동일한 Azure AD 테넌트의 계정으로 제품을 양도하는 경우 구독에서 실행되는 리소스에는 영향을 미치지 않습니다. 그러나 PowerShell에 저장된 컨텍스트 정보는 업데이트되지 않으므로 제거하거나 설정을 변경해야 할 수 있습니다. 리소스 이동 또는 서비스 테넌트 변경을 수행하면 리소스가 영향을 받을 수 있습니다.

### <a name="new-account-usage-and-billing-history"></a>새 계정 사용량 및 청구 내역

사용자가 새 계정에 사용할 수 있는 유일한 정보는 양도 시간부터 시작하는 사용량 및 청구 기록입니다. 사용량 및 청구 내역은 제품과 함께 전송되지 않습니다.

### <a name="remaining-product-credits"></a>나머지 제품 크레딧

Visual Studio 또는 Microsoft 파트너 네트워크 제품이 있는 경우 월별 크레딧을 받게 됩니다. 크레딧은 새 계정의 제품과 함께 전달되지 않습니다. 양도 요청을 수락하려는 사용자는 Visual Studio 라이선스가 있어야만 양도 요청을 수락할 수 있습니다. 제품은 사용자의 계정에서 사용할 수 있는 Visual Studio 크레딧을 사용합니다. 자세한 내용은 [Visual Studio 및 파트너 네트워크 구독 양도](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)를 참조하세요.

### <a name="users-keep-access-to-transferred-resources"></a>사용자가 양도된 리소스에 계속 액세스할 수 있음

제품의 리소스에 대한 액세스 권한이 있는 사용자는 청구 소유권이 양도될 때 계속 액세스할 수 있습니다. 그러나 [관리자 역할](add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거될 수 있습니다. 계정이 제품의 테넌트 이외의 Azure AD 테넌트에 있고 전송 요청을 보낸 사용자가 제품을 계정의 테넌트로 이동하는 경우 액세스가 손실됩니다.

Azure 역할 할당이 있는 사용자를 보고 Azure Portal 제품의 리소스에 액세스할 수 있습니다. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다. 그런 다음, 확인하려는 제품을 선택한 다음, 왼쪽 창에서 **액세스 제어(IAM)를** 선택합니다. 다음으로, 페이지 위쪽에서  **역할 할당을**  선택합니다. 역할 할당 페이지에는 제품에 대한 액세스 권한이 있는 모든 사용자가 나열됩니다.

양도하는 동안 [Azure 역할 할당이](../../role-based-access-control/role-assignments-portal.md) 제거되더라도 원래 소유자 계정의 사용자는 다음을 비롯한 다른 보안 메커니즘을 통해 제품에 계속 액세스할 수 있습니다.

- 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)를 참조하세요.
- 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.
- Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

수신자가 리소스에 대 한 액세스를 제한 해야 하는 경우 서비스와 연결 된 암호를 업데이트 하는 것을 고려해 야 합니다. 대부분의 리소스를 업데이트할 수 있습니다. [Azure Portal](https://portal.azure.com/)에 로그인한 후 허브 메뉴에서 **모든 리소스** 를 선택합니다. 다음으로, 리소스를 선택합니다. 그리고 리소스 페이지에서 **설정** 을 선택합니다. 여기서 기존 비밀을 보고 업데이트할 수 있습니다.

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>소유권을 받으면 사용량만큼 요금을 지불합니다.

양도된 시점 이후부터 보고되는 사용량에 대한 요금을 결제할 책임은 양수인의 계정에 있습니다. 전송 전에 발생 했지만 나중에 보고 된 몇 가지 사용법이 있을 수 있습니다. 사용량은 양수인 계정의 청구서에 포함됩니다.

### <a name="transfer-enterprise-agreement-product-ownership"></a>기업계약 제품 소유권 이전

엔터프라이즈 관리자는 원래 계정 소유자가 더 이상 조직의 일부가 아닌 경우에도 모든 계정에 대한 계정 소유권을 업데이트할 수 있습니다. Azure 기업계약 계정을 양도하는 방법에 대한 자세한 내용은 [Azure Enterprise 양도](ea-transfers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [새 리소스 그룹 또는 구독으로 리소스를 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)합니다.
