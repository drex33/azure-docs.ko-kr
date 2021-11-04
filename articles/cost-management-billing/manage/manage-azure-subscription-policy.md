---
title: Azure 구독 정책 관리
description: Azure 구독 정책 관리를 통해 서로 다른 디렉터리 간 Azure 구독 이동을 제어하는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 2530cdd36a2844c5b311e97d9747c8734d808206
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464930"
---
# <a name="manage-azure-subscription-policies"></a>Azure 구독 정책 관리

이 문서에서는 구독 작업에 대해 Azure 구독 정책을 구성하여 서로 다른 디렉터리 간 Azure 구독 이동을 제어하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 디렉터리 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator)만 구독 정책을 편집할 수 있습니다. 구독 정책을 편집하려면 먼저 전역 관리자가 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한을 높여야](../../role-based-access-control/elevate-access-global-admin.md) 합니다. 그런 후에야 구독 정책을 편집할 수 있습니다.
- 다른 모든 사용자는 현재 정책 설정을 읽을 수만 있습니다.

## <a name="available-subscription-policy-settings"></a>사용 가능한 구독 정책 설정

다음의 정책 설정을 사용하여 서로 다른 디렉터리 간 Azure 구독 이동을 제어할 수 있습니다.

### <a name="subscriptions-leaving-aad-directory"></a>AAD 디렉터리에서 나가는 구독

사용자가 현재 디렉터리로부터 구독을 이동하는 것을 정책으로 허용하거나 중지합니다. [구독 소유자](../../role-based-access-control/built-in-roles.md#owner)는 [Azure 구독의 디렉터리](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 자신이 속한 다른 디렉터리로 변경할 수 있습니다. 이렇게 되면 거버넌스 문제로 이어질 수 있으므로 전역 관리자는 디렉터리 사용자가 디렉터리를 변경하는 것을 허용하거나 허용하지 않을 수 있습니다.

### <a name="subscriptions-entering-aad-directory"></a>AAD 디렉터리에 들어오는 구독

현재 디렉터리에 액세스 권한이 있는 사용자가 다른 디렉터리에서 현재 디렉터리로 구독을 이동하는 것을 정책으로 허용하거나 중지합니다. [구독 소유자](../../role-based-access-control/built-in-roles.md#owner)는 [Azure 구독의 디렉터리](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 자신이 속한 다른 디렉터리로 변경할 수 있습니다. 이렇게 되면 거버넌스 문제로 이어질 수 있으므로 전역 관리자는 디렉터리 사용자가 디렉터리를 변경하는 것을 허용하거나 허용하지 않을 수 있습니다.

### <a name="exempted-users"></a>예외 사용자

거버넌스를 이유로 전역 관리자가 모든 구독이 현재 디렉터리에 들어오고 나가는 것을 차단할 수 있습니다. 그러나 특정 사용자가 두 작업을 수행하도록 허용할 수도 있습니다. 두 경우 모두 사용자가 다른 모든 사용자에게 적용되는 정책 설정을 무시할 수 있는 예외 사용자 목록을 구성할 수 있습니다.

## <a name="setting-subscription-policy"></a>구독 정책 설정

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **구독** 으로 이동합니다. **정책 관리** 가 명령 모음에 표시됩니다.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="구독의 정책 관리를 보여주는 스크린샷" lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. **정책 관리** 를 선택하여 디렉터리에 설정된 현재 구독 정책의 세부 정보를 확인합니다. [높은 권한이](../../role-based-access-control/elevate-access-global-admin.md) 있는 전역 관리자는 예외 사용자 추가 또는 제거를 비롯해 설정을 편집할 수 있습니다.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="특정 정책 설정 및 예외 사용자를 보여주는 스크린샷" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. 아래쪽에 있는 **변경 내용 저장** 을 선택하여 변경 내용을 저장합니다. 변경 내용은 즉시 적용됩니다.

## <a name="read-subscription-policy"></a>구독 정책 읽기

전역 관리자가 아니더라도 여전히 구독 정책 영역으로 이동하여 디렉터리의 정책 설정을 볼 수 있지만 편집할 수는 없습니다. 개인 정보 보호를 위해 예외 사용자 목록은 볼 수 없습니다. 디렉터리 설정에서 허용하는 한 자신의 전역 관리자가 정책 변경에 대한 요청을 제출하는 것을 볼 수 있습니다.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="읽기 권한자에게 표시되는 구독의 정책 관리를 보여주는 스크린샷" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>다음 단계

- [Cost Management + Billing 설명서](../index.yml)를 읽어보세요.