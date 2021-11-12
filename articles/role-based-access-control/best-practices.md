---
title: Azure RBAC에 대한 모범 사례
description: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하기 위한 모범 사례.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 8f42a384c0ef8605de42243fcbb232d3ff615583
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301346"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC에 대한 모범 사례

이 문서에서는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하기 위한 몇 가지 모범 사례를 설명합니다. 이러한 모범 사례는 Azure RBAC에 대한 Microsoft와 고객의 경험에서 비롯된 것입니다.

## <a name="only-grant-the-access-users-need"></a>사용자에게 필요한 액세스 권한만 부여

Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신, 특정 범위에서 특정 작업만 허용할 수 있습니다.

액세스 제어 전략을 계획할 때 작업을 완료하는 데 필요한 최소한의 권한만 사용자에게 부여하는 것이 가장 좋습니다. 처음에는 더 넓은 범위에서 더 넓은 역할을 할당하는 것이 더 편리하게 보이겠지만, 이렇게 하지 마세요. 사용자 지정 역할을 만들 때 사용자에게 필요한 권한만 포함합니다. 역할과 범위를 제한함으로써 보안 주체가 손상된 경우 리소스를 위험으로부터 제한할 수 있습니다.

다음 다이어그램은 Azure RBAC에 대한 권장 패턴을 보여줍니다.

![Azure RBAC 및 최소 권한](./media/best-practices/rbac-least-privilege.png)

역할을 할당하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)을 참조하세요.

## <a name="limit-the-number-of-subscription-owners"></a>구독 소유자 수 제한

손상된 소유자에 의한 위반 가능성을 줄이려면 최대 3개의 구독 소유자가 있어야 합니다. 이 권장 사항은 클라우드의 Microsoft Defender에서 모니터링할 수 있습니다. 클라우드에 대 한 Defender의 기타 id 및 액세스 권장 사항은 [보안 권장 사항-참조 가이드](../security-center/recommendations-reference.md)를 참조 하세요.

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 사용

권한 있는 계정을 악의적인 사이버 공격으로부터 보호하기 위해 Azure Active Directory PIM(Privileged Identity Management)를 사용하여 권한 노출 시간을 줄이고 보고서 및 경고를 통해 해당 사용에 대한 가시성을 증가시킬 수 있습니다. PIM은 Azure AD 및 Azure 리소스에 대한 Just-In-Time 권한 액세스를 제공하여 권한 있는 계정을 보호합니다. 권한이 자동으로 취소된 후에 액세스 시간이 바인딩될 수 있습니다. 

자세한 내용은 [Azure AD Privileged Identity Management란?](../active-directory/privileged-identity-management/pim-configure.md)을 참조하세요.

## <a name="assign-roles-to-groups-not-users"></a>사용자가 아닌 그룹에 역할 할당

역할 할당을 보다 쉽게 관리하려면 사용자에게 직접 역할을 할당하지 마세요. 대신 그룹에 역할을 할당하세요. 사용자 대신 그룹에 역할을 할당하면 역할 할당 수를 최소화할 수 있습니다. 이 경우 [구독당 역할 할당이 2000개](troubleshooting.md#azure-role-assignments-limit)로 제한됩니다. 

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 문제 해결](troubleshooting.md)
