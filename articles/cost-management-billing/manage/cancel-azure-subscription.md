---
title: Azure 구독 취소
description: 무료 평가판 구독과 같은 Azure 구독을 취소하는 방법을 설명합니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: banders
ms.openlocfilehash: c8961575a4055c94a36a89da0d99d2aed4c6e50c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563586"
---
# <a name="cancel-your-azure-subscription"></a>Azure 구독 취소

더 이상 필요하지 않은 경우 Azure Portal에서 Azure 구독을 취소할 수 있습니다.

필수 사항은 아니지만 Microsoft는 구독을 취소하기 전에 다음 작업을 수행하는 것을 *권장* 합니다.

* 데이터를 백업합니다. 예를 들어 Azure Storage 또는 SQL에 데이터를 저장하는 경우 복사본을 다운로드합니다. 가상 머신을 사용하는 경우 해당 이미지를 로컬로 저장합니다.
* 서비스를 종료합니다. [관리 포털의 페이지 리소스](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)로 이동한 후 실행 중인 가상 머신, 애플리케이션 또는 기타 서비스를 모두 **중지** 합니다.
* 데이터를 마이그레이션하는 것이 좋습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.
* 모든 리소스 및 모든 리소스 그룹을 삭제합니다.
* `AssignableScopes`에서 이 구독을 참조하는 사용자 지정 역할이 있는 경우 해당 사용자 지정 역할을 업데이트하여 구독을 제거해야 합니다. 구독을 취소한 후 사용자 지정 역할을 업데이트하려고 하면 오류가 발생할 수 있습니다. 자세한 내용은 [사용자 지정 역할 관련 문제 해결](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) 및 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

유료 Azure 지원 계획을 취소해도 남은 구독 기간에 대해 요금이 청구됩니다. 자세한 내용은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 참조하세요.

## <a name="who-can-cancel-a-subscription"></a>누가 구독을 취소할 수 있나요?

아래 표에서는 구독을 취소하는 데 필요한 사용 권한을 설명합니다.

|구독 유형     |취소할 수 있는 사람  |
|---------|---------|
|Azure 웹 사이트를 통해 Azure에 가입할 때 생성되는 구독입니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다. |  서비스 관리자 및 구독 소유자  |
|[Microsoft 기업계약](https://azure.microsoft.com/pricing/enterprise-agreement/) 및 [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  서비스 관리자 및 구독 소유자       |
|[Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0017g/) 및 [DevTest용 Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  구독 소유자      |

서비스 관리자 또는 구독 소유자 역할이 없는 계정 관리자는 Azure 구독을 취소할 수 없습니다. 그러나 계정 관리자는 자신을 서비스 관리자로 만들고 구독을 취소할 수 있습니다. 자세한 내용은 [서비스 관리자 변경](../../role-based-access-control/classic-administrators.md#change-the-service-administrator)을 참조하세요.


## <a name="cancel-subscription-in-the-azure-portal"></a>Azure Portal에서 구독 취소

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.
1. 취소할 구독을 선택합니다.
1. **개요** 를 선택한 다음, **구독 취소** 를 선택합니다.
    ![취소 단추를 보여 주는 스크린샷](./media/cancel-azure-subscription/cancel_ibiza.png)
1. 메시지에 따라 취소를 완료합니다.

> [!NOTE]
> 파트너는 고객이 요청하거나 미지불 또는 사기 행위의 경우 구독을 일시 중단하거나 취소할 수 있습니다. 자세한 내용은 [구독 일시 중지 및 취소](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription)를 참조하세요.

## <a name="cancel-a-support-plan"></a>지원 플랜 취소

Azure 웹 사이트, Azure Portal을 통해 지원 플랜을 구입했거나 Microsoft 고객 계약에 따라 지원 플랜을 체결한 경우 지원 플랜을 취소할 수 있습니다. Microsoft 담당자나 파트너를 통해 지원 플랜을 구매한 경우 지원을 요청하세요. 

1. Azure Portal에서 **Cost Management + Billing** 으로 이동합니다.
1. **청구** 아래에서 **반복 청구** 를 선택합니다.
1. 지원 플랜 항목의 오른쪽에서 줄임표( **...** )를 선택하고 **자동 갱신 해제** 를 선택합니다.

## <a name="what-happens-after-subscription-cancellation"></a>구독 취소 후에는 어떻게 되나요?

취소하면 청구가 즉시 중지됩니다. 그러나 취소가 포털에 표시되기까지 최대 10분이 걸릴 수 있습니다. 청구 기간 중에 취소하는 경우 기간이 만료된 후 일반적인 청구서 날짜에 최종 청구서를 보냅니다.

취소한 후에는 서비스를 사용할 수 없습니다. 즉, 가상 머신의 할당이 취소되고 임시 IP 주소가 해제되며 스토리지는 읽기 전용이 됩니다.

구독이 취소된 후 Microsoft는 사용자가 액세스해야 하거나 마음이 바뀐 경우를 대비해서 30-90일 동안 기다렸다가 데이터를 영구 삭제합니다. 데이터 유지 요금은 부과되지 않습니다. 자세한 내용은 [Microsoft 보안 센터 - 데이터 관리 방법](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)을 참조하세요.

## <a name="delete-free-trial-or-pay-as-you-go-subscriptions"></a>평가판 또는 종량제 구독 삭제

평가판 또는 종량제 구독이 있는 경우 구독이 자동으로 삭제되기까지 90일을 기다릴 필요가 없습니다. 구독을 취소한 후 *3일* 후에 구독을 삭제할 수 있습니다. 구독을 취소한 후 3일이 지나면 **구독 삭제** 옵션을 사용할 수 없습니다.

1. 구독을 취소한 날로부터 3일 동안 기다립니다.
1. Azure Portal의 [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 페이지에서 구독을 선택합니다.
1. 삭제할 구독을 선택합니다.
1. **개요** 를 선택한 다음, **구독 삭제** 를 선택합니다.

**참고:** 구독을 취소한 후 90일이 지나면 구독이 자동으로 삭제됩니다.

## <a name="delete-other-subscriptions"></a>기타 구독 삭제

수동으로 삭제할 수 있는 유일한 구독 유형은 평가판 및 종량제 구독입니다. 다른 모든 구독 유형은 [구독 취소](#cancel-subscription-in-the-azure-portal) 프로세스를 통해서만 삭제됩니다. 즉, 평가판 또는 종량제 구독이 아니면 구독을 직접 삭제할 수 없습니다. 그러나 구독을 취소하면 구독을 즉시 삭제하도록 요청하는 [Azure 지원 요청](https://go.microsoft.com/fwlink/?linkid=2083458)을 만들 수 있습니다.

## <a name="reactivate-a-subscription"></a>구독 다시 활성화

종량제 요금을 사용하는 구독을 실수로 취소한 경우 [Azure Portal에서 다시 활성화](subscription-disabled.md)할 수 있습니다.

종량제 요금을 사용하는 구독이 아닌 경우 구독을 다시 활성화하려면 취소 후 90일 이내에 지원 센터로 문의하세요.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Azure Portal에 구독 취소 옵션이 표시되지 않는 이유는 무엇인가요? 

구독을 취소하는 데 필요한 권한이 없을 수 있습니다. 다양한 유형의 구독을 취소할 수 있는 사용자에 대한 설명은 [누가 구독을 취소할 수 있나요?](#who-can-cancel-a-subscription)를 참조하세요.

## <a name="how-do-i-delete-my-azure-account"></a>내 Azure 계정을 삭제하려면 어떻게 해야 하나요?

*모든 개인 정보를 포함하여 내 계정을 제거해야 합니다. 활성(평가판) 구독을 이미 취소했습니다. 활성 구독이 없으며 내 계정을 완전히 삭제하려고 합니다*.

* 조직을 통해 Azure Active Directory 계정이 있는 경우 Azure AD 관리자가 계정을 삭제할 수 있습니다. 그 후에는 서비스를 사용할 수 없습니다. 즉, 가상 머신의 할당이 취소되고 임시 IP 주소가 해제되며 스토리지는 읽기 전용이 됩니다. 요약하면, 취소하면 청구가 즉시 중지됩니다.

* 조직을 통해 Azure AD 계정이 없는 경우 Azure 구독을 취소하고 삭제한 다음, 계정에서 신용 카드를 제거할 수 있습니다. 이 작업이 계정을 삭제하지 않지만 작동하지 않는 것으로 렌더링합니다. 한 단계 더 나아가 다른 용도로 사용되지 않는 경우에는 관련된 Microsoft 계정을 삭제할 수도 있습니다.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Visual Studio Professional 계정을 취소하려면 어떻게 해야 하나요?

[갱신 및 취소](/visualstudio/subscriptions/faq/admin/renewal-cancellation) 문서를 참조하세요. Visual Studio Azure 구독이 있는 경우 해당 구독도 취소하고 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

- 필요한 경우 [Azure Portal](subscription-disabled.md)에서 종량제 구독을 다시 활성화할 수 있습니다.
