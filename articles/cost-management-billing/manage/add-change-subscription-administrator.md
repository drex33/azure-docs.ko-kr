---
title: Azure 구독 관리자 추가 또는 변경
description: Azure RBAC(Azure 역할 기반 액세스)를 사용하여 Azure 구독 관리자를 추가하거나 변경하는 방법을 설명합니다.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: 913ab605d17e47d5fa8f1ec751a618fe5dbfe123
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000975"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure 구독 관리자 추가 또는 변경


Azure 리소스에 대한 액세스를 관리하려면 적절한 관리자 역할이 있어야 합니다. Azure에는 선택할 수 있는 몇 가지 기본 제공 역할이 있는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)라는 인증 시스템이 있습니다. 이러한 역할은 관리 그룹, 구독 또는 리소스 그룹과 같은 다른 범위에서 할당할 수 있습니다. 기본적으로 새 Azure 구독을 만드는 사람은 다른 사용자에게 구독에 대한 관리 액세스 권한을 할당할 수 있습니다.

이 문서에서는 구독 범위에서 Azure RBAC를 사용하여 사용자에 대한 관리자 역할을 추가하거나 변경하는 방법을 설명합니다.

Azure RBAC를 사용하여 리소스에 대한 액세스를 관리하는 것이 좋습니다. 그러나 여전히 클래식 배포 모델을 사용 중이고, [Azure 서비스 관리 PowerShell 모듈](/powershell/module/servicemanagement/azure.service)을 사용하여 클래식 리소스를 관리하는 경우에는 클래식 관리자를 사용해야 합니다.

> [!TIP]
> Azure Portal을 통해서만 클래식 리소스를 관리하는 경우 클래식 관리자를 사용할 필요가 없습니다.

자세한 내용은 [Azure Resource Manager 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md) 및 [Azure 클래식 구독 관리자](../../role-based-access-control/classic-administrators.md)를 참조하세요.

## <a name="determine-account-billing-administrator"></a>계정의 대금 청구 관리자 확인

<a name="whoisaa"></a>

대금 청구 관리자는 계정의 청구를 관리하는 권한을 가진 사용자입니다. 이들에게는 [Azure Portal](https://portal.azure.com)에서 청구 정보에 액세스하고, 구독 만들기, 청구서 보기 및 결제, 결제 방법 업데이트와 같은 다양한 청구 작업을 수행할 수 있는 권한이 부여됩니다.

자신이 대금 청구 관리자를 맡고 있는 계정을 확인하려면 [Azure Portal의 Cost Management + Billing 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)를 방문하세요. 그런 다음, 왼쪽 창에서 **모든 청구 범위** 를 선택합니다. 구독 페이지에는 사용자가 대금 청구 관리자를 맡고 있는 모든 구독이 표시됩니다.

구독의 계정 관리자를 잘 모를 경우 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문하세요. 그런 다음, 확인하려는 구독을 선택한 후 **설정** 에서 확인합니다. **속성** 을 선택하면 구독의 계정 관리자가 **계정 관리자** 상자에 표시됩니다.


## <a name="assign-a-subscription-administrator"></a>구독 관리자 할당

<a name="add-an-admin-for-a-subscription"></a>

사용자를 Azure 구독의 관리자로 만들기 위해 기존 청구 관리자는 구독 범위에서 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할(Azure 역할)을 할당합니다. 소유자 역할을 할당하면 다른 사용자에게 액세스를 위임할 수 있는 권한을 비롯한 구독의 리소스에 대한 모든 권한이 사용자에게 제공됩니다. 이러한 단계는 다른 역할 할당과 동일합니다.

구독에 대한 계정 청구 관리자가 누구인지 확실하지 않은 경우 다음 단계를 사용하여 확인합니다.

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 엽니다.
1. 확인하려는 구독을 선택한 다음 **설정** 에서 확인합니다.
1. **속성** 을 선택합니다. 구독의 계정 청구 관리자가 **계정 관리자** 상자에 표시됩니다.

### <a name="to-assign-a-user-as-an-administrator"></a>관리자 권한으로 사용자를 할당하려면

- 구독 범위에서 사용자에게 소유자 역할을 할당합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

* [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
* [Azure의 다양한 역할 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory의 관리자 역할 사용 권한](../../active-directory/roles/permissions-reference.md)