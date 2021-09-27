---
title: Azure 구독의 청구 소유권을 양도하는 방법 소개
description: 이 문서에서는 Azure 구독의 청구 소유권을 다른 계정으로 양도하기 전에 알아 두어야 할 사항에 대해 설명합니다.
keywords: azure 구독 양도, 구독 양도 azure, azure 구독을 다른 계정에 양도, azure 구독 소유자 변경, azure 구독을 다른 계정으로 양도, azure 청구 양도
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 03a2395adde34be7e83c8d151d7e0ad2893b5c95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655717"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Azure 구독의 청구 소유권을 양도하는 방법 소개

이 문서는 Azure 구독의 청구 소유권을 다른 계정으로 양도하기 전에 알아 두어야 할 사항을 이해하는 데 도움이 됩니다. 

곧 퇴사하거나 구독 요금이 청구되는 계정을 다른 계정으로 변경하려는 경우 Azure 구독의 청구 소유권을 양도해야 합니다. 청구 소유권을 다른 계정으로 양도하면 청구 작업에 대한 새 계정 권한이 관리자에게 제공됩니다. 관리자는 지불 방법을 변경하고 요금을 보고 구독을 취소할 수 있습니다.

청구 소유권을 유지하면서 구독 유형만 변경하려면 [Azure 구독을 다른 제안으로 전환](../manage/switch-azure-offer.md)을 참조하세요. 구독의 리소스에 액세스할 수 있는 사용자를 제어하려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

EA(기업계약) 고객인 경우 엔터프라이즈 관리자가 계정 간에 구독의 청구 소유권을 양도할 수 있습니다.

계정의 대금 청구 관리자만 구독의 소유권을 양도할 수 있습니다.

## <a name="determine-if-you-are-a-billing-administrator"></a>청구 관리자인지 확인

<a name="whoisaa"></a>

전송을 수행하려면 계정의 청구를 관리할 수 있는 액세스 권한이 있는 사용자를 찾습니다. 이들에게는 [Azure Portal](https://portal.azure.com)에서 청구 정보에 액세스하고, 구독 만들기, 청구서 보기 및 결제, 결제 방법 업데이트와 같은 다양한 청구 작업을 수행할 수 있는 권한이 부여됩니다.

### <a name="check-if-you-have-billing-access"></a>청구 액세스 권한이 있는지 확인

1. 청구 액세스 권한이 있는 계정을 확인하려면 [Azure Portal의 Cost Management + Billing 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)에 방문합니다.

2. 왼쪽 메뉴에서 **청구 계정** 을 선택합니다.

3. **청구 범위** 목록 페이지에는 청구 정보에 대한 액세스 권한이 있는 모든 구독이 표시됩니다.

### <a name="check-by-subscription"></a>구독별 확인

1. 구독의 계정 관리자를 잘 모를 경우 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문하세요. 

2. 확인하려는 구독을 선택합니다.

3. **설정** 제목 아래에서 **속성** 을 선택합니다. 구독 계정 관리자가 누구인지 파악하려면 **계정 관리자** 상자를 참조하세요.

   > [!NOTE]
   > 모든 구독 유형에 속성이 표시되는 것은 아닙니다.

## <a name="supported-subscription-types"></a>지원되는 구독 유형

Azure Portal의 구독 양도 기능은 아래에 나열된 유형의 구독에서 사용할 수 있습니다. 현재 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 또는 [AIO(Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) 구독은 양도할 수 없습니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. 지원 플랜과 같은 다른 구독을 양도하려면 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

- [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise(MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> EA 포털 사용

<sup>2</sup> Azure 웹 사이트에서 가입하는 동안 생성된 계정에만 지원됩니다.

## <a name="resources-transferred-with-subscriptions"></a>구독과 함께 양도되는 리소스

VM, 디스크, 웹 사이트 등의 모든 리소스가 새 계정에 양도됩니다. 그러나 구독을 다른 Azure AD 테넌트의 계정에 양도하는 경우 구독에 대한 [관리자 역할](../manage/add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)은 양도되지 않습니다. 또한 [앱 등록](../../active-directory/develop/quickstart-register-app.md) 및 다른 테넌트 관련 서비스는 구독과 함께 양도되지 않습니다.

## <a name="transfer-account-ownership-to-another-countryregion"></a>다른 국가/지역에 계정 소유권 양도

안타깝게도 Azure Portal을 사용하여 국가 또는 지역 간에 구독을 양도할 수는 없습니다. 그러나 Azure 지원 요청을 열면 양도할 수 있습니다. 지원 요청을 만들려면 [고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="transfer-a-subscription-from-one-account-to-another"></a>한 계정에서 다른 계정으로 구독 양도

두 계정의 관리자인 경우 계정 간에 구독을 양도할 수 있습니다. 두 계정은 개념적으로 서로 다른 두 사용자의 계정으로 간주되므로 계정 간에 구독을 양도할 수 있습니다.
구독을 양도하는 데 필요한 단계를 보려면 [Azure 구독의 청구 소유권 양도](../manage/billing-subscription-transfer.md)를 참조하세요.

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>구독 양도 시 가동 중지 시간이 발생하지 않음

구독을 동일한 Azure AD 테넌트의 계정으로 양도하는 경우 구독에서 실행 중인 리소스에 영향을 주지 않습니다. 그러나 PowerShell에 저장된 컨텍스트 정보는 업데이트되지 않으므로 제거하거나 설정을 변경해야 할 수 있습니다. 구독을 다른 테넌트의 계정에 양도하고 구독을 해당 테넌트로 이전하기로 결정하는 경우 구독의 리소스를 관리하기 위한 [Azure 역할이 할당된](../../role-based-access-control/role-assignments-portal.md) 모든 사용자, 그룹 및 서비스 주체의 액세스 권한이 사라집니다. 서비스 가동 중지 시간이 발생할 수 있습니다.

## <a name="new-account-usage-and-billing-history"></a>새 계정 사용량 및 청구 내역

새 계정의 사용자에게 제공되는 유일한 정보는 구독의 지난달 요금입니다. 나머지 사용량 및 청구 내역은 구독과 함께 양도되지 않습니다.

## <a name="manually-migrate-data-and-services"></a>데이터 및 서비스를 수동으로 마이그레이션

구독을 양도하면 해당 리소스도 양도됩니다. 구독 소유권을 양도할 수 없는 경우 리소스를 수동으로 마이그레이션할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

## <a name="remaining-subscription-credits"></a>남은 구독 크레딧 

Visual Studio 또는 Microsoft 파트너 네트워크 구독이 있는 경우 월간 크레딧이 지급됩니다. 크레딧은 새 계정에 구독과 함께 양도되지 않습니다. 양도 요청을 수락하려는 사용자는 Visual Studio 라이선스가 있어야만 양도 요청을 수락할 수 있습니다. 구독에서는 사용자 계정에 제공되는 Visual Studio 크레딧을 사용합니다. 자세한 내용은 [Visual Studio 및 파트너 네트워크 구독 양도](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)를 참조하세요.

## <a name="users-keep-access-to-transferred-resources"></a>사용자가 양도된 리소스에 계속 액세스할 수 있음

구독의 리소스에 대한 액세스 권한이 있는 사용자는 소유권이 양도되더라도 액세스 권한을 유지한다는 점에 유의하세요. 그러나 [관리자 역할](../manage/add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거될 수 있습니다. 계정이 구독의 테넌트가 아닌 Azure AD 테넌트에 있고 양도 요청을 보낸 사용자가 구독을 사용자 계정의 테넌트로 이동하는 경우 액세스가 손실됩니다. 

구독의 리소스에 액세스할 수 있는 Azure 역할이 할당된 사용자는 Azure Portal에서 확인할 수 있습니다. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다. 그런 다음, 확인하려는 구독을 선택한 후 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다. 다음으로, 페이지 맨 위에서 **역할 할당** 을 선택합니다. 역할 할당 페이지에는 구독에 액세스할 수 있는 모든 사용자가 나열됩니다.

양도 과정에서 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거되더라도 원래 소유자 계정에 속한 사용자는 다음을 비롯한 다른 보안 메커니즘을 통해 구독에 계속 액세스할 수도 있습니다.

* 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)를 참조하세요.
* 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.
* Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

수신자가 리소스 액세스를 제한해야 하는 경우 서비스와 연결된 비밀을 업데이트하는 것을 고려해야 합니다. 대부분의 리소스를 업데이트할 수 있습니다. [Azure Portal](https://portal.azure.com)에 로그인한 후 허브 메뉴에서 **모든 리소스** 를 선택합니다. 다음으로, 리소스를 선택합니다. 그리고 리소스 페이지에서 **설정** 을 선택합니다. 여기서 기존 비밀을 보고 업데이트할 수 있습니다.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>소유권을 받으면 사용량만큼 요금을 지불합니다.

양도된 시점 이후부터 보고되는 사용량에 대한 요금을 결제할 책임은 양수인의 계정에 있습니다. 양도하기 전에 발생하지만 나중에 보고되는 일부 사용량이 있을 수 있습니다. 사용량은 양수인 계정의 청구서에 포함됩니다.

## <a name="use-a-different-payment-method"></a>다른 결제 방법 사용

양도 요청을 수락하는 과정에서 계정에 연결된 기존 결제 방법을 선택하거나 새 결제 방법을 추가할 수 있습니다.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>기업계약 구독 소유권 양도

엔터프라이즈 관리자는 원래 계정 소유자가 더 이상 조직의 일부가 아닌 경우에도 모든 계정에 대한 계정 소유권을 업데이트할 수 있습니다. Azure 기업계약 계정을 양도하는 방법에 대한 자세한 내용은 [Azure Enterprise 양도](../manage/ea-transfers.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 청구 소유권 양도](../manage/billing-subscription-transfer.md)
