---
title: Azure Virtual Network Manager 범위 이해 및 작업
description: Azure Virtual Network Manager 범위 및 가상 네트워크 관리에 미치는 영향에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: e21cfe528c3cba9c190ba667552deb7070884aa3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102899"
---
# <a name="understand-and-work-with-azure-virtual-network-manager-preview-scopes"></a>Azure Virtual Network Manager(미리 보기) 범위 이해 및 작업

이 문서에서는 Azure Virtual Network Manager에서 *범위* 개념을 사용하여 관리 그룹 또는 구독이 Virtual Network Manager의 특정 기능을 사용하도록 설정하는 방법에 대해 알아봅니다. 또한 Virtual Network Manager를 사용할 때 *계층 구조와* 계층 구조가 사용자에게 미치는 영향에 대해서도 알아봅니다. 

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="network-manager"></a>네트워크 관리자

*네트워크 관리자는* 네트워크 *그룹,* *구성* 및 *규칙과* 같은 자식 리소스로 구성된 최상위 개체입니다. 

* **네트워크 그룹** - 특정 연결 또는 보안 관리자 정책을 적용할 수 있는 전체 범위의 하위 집합입니다.

* **구성** - Azure Virtual Network Manager는 연결 구성과 보안 구성이라는 두 가지 유형의 구성을 제공합니다. 연결 구성을 사용하면 네트워크 토폴로지 만들 수 있지만 보안 구성을 사용하면 가상 네트워크에서 적용할 수 있는 규칙 컬렉션을 만들 수 있습니다.

* 규칙 - 규칙 컬렉션은 가상 네트워크에 대한 전역 수준에서 네트워크 트래픽을 허용하거나 거부할 수 있는 네트워크 보안 규칙 집합입니다. 

> [!NOTE]
> Azure Virtual Network Manager를 삭제하려면 모든 자식 리소스를 제거해야 합니다.
>

## <a name="scope"></a>범위

Azure Virtual Network Manager 내의 *범위는* 기능을 적용할 수 있는 리소스 집합입니다. 범위를 지정할 때 네트워크 관리자가 리소스를 관리할 수 있는 액세스 권한을 제한합니다. 범위의 값은 관리 그룹 수준 또는 구독 수준에 있을 수 있습니다. 리소스 계층을 관리하는 방법을 알아보려면 [Azure 관리 그룹](../governance/management-groups/overview.md)를 참조하세요. 관리 그룹을 범위로 선택하면 모든 자식 리소스가 범위 내에 포함됩니다. 

> [!NOTE]
> 동일한 계층 구조의 범위가 겹치는 여러 네트워크 관리자를 만드는 것은 지원되지 않습니다.
> 

## <a name="features"></a>기능

기능은 Azure Virtual Network Manager에서 관리할 수 있는 범위 액세스입니다. Azure Virtual Network Manager에는 현재 *Connectivity* 및 *SecurityAdmin의* 두 가지 기능 범위가 있습니다. 동일한 Virtual Network Manager 인스턴스에서 두 기능 범위를 모두 사용하도록 설정할 수 있습니다. 각 기능에 대한 자세한 내용은 연결 및 [SecurityAdmin](concept-security-admins.md) [을](concept-connectivity-configuration.md) 참조하세요.

> [!NOTE]
> 기능은 Azure Virtual Network Manager를 배포하는 동안에만 사용할 수 있습니다. 기능 범위가 하나만 있는 Virtual Network Manager 인스턴스를 배포하는 경우 새 Azure Virtual Network Manager를 다시 배포하여 두 기능을 모두 사용하도록 설정해야 합니다.
>

## <a name="hierarchy"></a>계층

Azure Virtual Network Manager를 사용하면 계층 구조에서 네트워크 리소스를 관리합니다. 계층 구조는 여러 Virtual Network Manager 인스턴스가 겹치는 범위를 관리할 수 있고 각 Virtual Network Manager 내의 구성이 서로 오버레이할 수도 있다는 것을 의미합니다. 예를 들어 한 Virtual Network Manager의 최상위 [관리 그룹을](../governance/management-groups/overview.md) 가질 수 있으며 자식 관리 그룹을 다른 Virtual Network Manager의 범위로 사용할 수 있습니다. 동일한 리소스를 포함하는 서로 다른 Virtual Network Manager 인스턴스 간에 구성 충돌이 있는 경우 범위가 더 높은 Virtual Network Manager의 구성이 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만드는 방법을 알아봅니다.
- 네트워크 그룹 에 대해 [알아봅니다.](concept-network-groups.md)
