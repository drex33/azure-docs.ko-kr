---
title: 기존 Azure 구독을 테넌트에 추가 - Azure AD
description: Azure AD(Azure Active Directory) 테넌트에 기존 Azure 구독을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc428a79e38f390d926f268bd8147eb7ee06a648
ms.sourcegitcommit: 04d2dec0316576388e6e10c1558b0a0b3688d01c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112421845"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가

Azure 구독은 Azure AD(Azure Active Directory)와 신뢰 관계입니다. 구독은 사용자, 서비스 및 디바이스를 인증하기 위해 Azure AD를 신뢰합니다.

여러 구독은 동일한 Azure AD 디렉터리를 신뢰할 수 있습니다. 각 구독은 단일 디렉터리만 신뢰할 수 있습니다.

하나 이상의 Azure 구독은 Azure 서비스에 보안 주체 및 디바이스를 인증하고 권한을 부여하기 위해 Azure AD(Azure Active Directory) 인스턴스와 신뢰 관계를 설정할 수 있습니다.  구독이 만료되면 Azure AD 서비스의 신뢰할 수 있는 인스턴스는 그대로 유지되지만 보안 주체는 Azure 리소스에 대한 액세스 권한을 잃게 됩니다.

사용자가 Microsoft 클라우드 서비스에 등록할 때 새 Azure AD 테넌트가 만들어지고 사용자는 전역 관리자 역할의 구성원이 됩니다. 그러나 구독 소유자가 기존 테넌트에 구독을 조인하는 경우 소유자는 전역 관리자 역할에 할당되지 않습니다.

모든 사용자에게는 인증을 위한 *홈* 디렉터리 하나가 있습니다. 사용자는 다른 디렉터리의 게스트가 될 수도 있습니다. Azure AD에서 각 사용자의 홈 디렉터리와 게스트 디렉터리를 모두 확인할 수 있습니다.

:::image type="content" source="media/active-directory-how-subscriptions-associated-directory/trust-relationship-azure-ad.png" alt-text="Azure 구독과 Azure Active Directory 간의 트러스트 관계를 보여 주는 스크린샷":::

> [!Important]
> 구독을 다른 디렉터리에 연결하는 경우 [Azure 역할 기반 액세스 제어](../../role-based-access-control/role-assignments-portal.md)를 사용해 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다. 서비스 관리자 및 공동 관리자를 비롯한 클래식 구독 관리자도 액세스 권한을 잃게 됩니다.
>
> AKS(Azure Kubernetes Service) 클러스터를 다른 구독으로 이동하거나 클러스터 소유 구독을 새 테넌트로 이동하면 손실된 역할 할당 및 서비스 주체 권한으로 인해 클러스터가 기능을 잃게 됩니다. 자세한 내용은 [AKS(Azure Kubernetes Service)](../../aks/index.yml)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

구독을 연결하거나 추가하려면 먼저 다음 작업을 수행해야 합니다.

- 구독을 연결하거나 추가한 후에 수행되는 변경 내용 및 받게 될 영향의 내용에 대해서는 다음 목록을 검토하세요.

  - Azure RBAC를 사용해 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다.
  - 서비스 관리자 및 공동 관리자도 권한을 잃습니다.
  - 키 자격 증명 모음이 있는 경우 해당 자격 증명 모음에 액세스할 수 없으며 연결한 후 이를 수정해야 합니다.
  - Virtual Machines 또는 Logic Apps와 같은 리소스의 관리 ID가 있는 경우 연결 후 다시 사용하도록 설정하거나 다시 만들어야 합니다.
  - 등록된 Azure Stack이 있는 경우 연결 후 다시 등록해야 합니다.
  - 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

- 다음 조건을 충족하는 계정을 사용하여 로그인해야 합니다.

  - 구독에 대한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할이 할당된 상태입니다. 소유자 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Portal를 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
  - 현재 디렉터리와 새 디렉터리에 모두 있습니다. 현재 디렉터리는 구독과 연결되어 있습니다. 새 디렉터리를 구독과 연결합니다. 다른 디렉터리에 대한 액세스 권한을 얻는 방법에 대한 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../external-identities/add-users-administrator.md)를 참조하세요.

- Azure CSP(클라우드 서비스 공급자) 구독(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft 내부 구독(MS-AZR-0015P) 또는 Microsoft Azure for Students Starter 구독(MS-AZR-0144P)을 사용하지 않는지 확인합니다.

## <a name="associate-a-subscription-to-a-directory"></a>디렉터리에 구독 연결<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

기존 구독을 Azure AD 디렉터리에 연결하려면 다음 단계를 수행합니다.

1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.

1. **디렉터리 변경** 을 선택합니다.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="디렉터리 변경 옵션이 강조 표시된 구독 페이지를 보여 주는 스크린샷.":::

1. 표시되는 경고를 검토한 다음 **변경** 을 선택합니다.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="샘플 디렉터리와 변경 단추가 강조 표시된 디렉터리 변경 페이지를 보여 주는 스크린샷.":::

   디렉터리가 구독에 맞게 변경되고 성공 메시지가 표시됩니다.

1. 구독 페이지에서 **디렉터리 전환** 을 선택하여 새 디렉터리로 이동합니다.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="예제 정보가 포함된 디렉터리 전환기 페이지를 보여 주는 스크린샷.":::

   모든 내용이 제대로 표시되려면 몇 시간이 걸릴 수 있습니다. 시간이 너무 오래 걸린다면 **전역 구독 필터** 를 확인합니다. 이동 된 구독이 숨겨지지 않았는지 확인합니다. Azure Portal에서 로그아웃했다가 다시 로그인하여 새 디렉터리를 확인해야 할 수 있습니다.

구독 디렉터리 변경은 서비스 수준 작업이므로 구독 청구 소유권에 영향을 주지 않습니다. 원본 디렉터리를 삭제하려면 구독 청구 소유권을 새 계정 관리자에게 양도해야 합니다. 청구 소유권을 전송하는 방법에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../../cost-management-billing/manage/billing-subscription-transfer.md)을 참조하세요.

## <a name="post-association-steps"></a>연결 이후 단계

구독을 다른 디렉터리에 연결한 후 작업을 계속하려면 다음 작업을 수행해야 할 수 있습니다.

- 키 자격 증명 모음이 있는 경우 주요 키 자격 증명 모음 테넌트 ID를 변경해야 합니다. 자세한 정보는 [구독 이동 후에 키 자격 증명 모음 테넌트 ID 변경](../../key-vault/general/move-subscription.md)을 참조하세요.

- 리소스에 대해 시스템 할당 관리 ID를 사용한 경우에는 해당 ID를 다시 사용하도록 설정해야 합니다. 사용자 할당 관리 ID를 사용한 경우에는 해당 ID를 다시 만들어야 합니다. 관리 ID를 다시 사용하도록 설정하거나 다시 만든 후에는 해당 ID에 할당된 사용 권한을 다시 설정해야 합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

- 이 구독으로 Azure Stack에 등록한 경우 다시 등록해야 합니다. 자세한 내용은 [Azure를 사용하여 Azure Stack Hub 등록](/azure-stack/operator/azure-stack-registration)을 참조하세요.

- 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테넌트를 만들려면 [Azure Active Directory에서 새 테넌트 만들기 빠른 시작](active-directory-access-create-new-tenant.md)을 참조하세요.

- Microsoft Azure 리소스 액세스를 제어하는 방법에 대해 자세히 알아보려면 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

- Azure AD 역할 할당 방법에 대해 더 자세히 알고 싶다면 [Azure Active Directory로 사용자에게 관리자 및 비 관리자 역할 할당](active-directory-users-assign-role-azure-portal.md)을 참조하세요.
