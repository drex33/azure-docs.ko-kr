---
title: Azure Virtual Network Manager(미리 보기)란?
description: Azure Virtual Network Manager에서 가상 네트워크의 관리 및 확장성을 간소화하는 방법을 알아봅니다.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 05c43d5e21c637fef321dc39cd341befedfba81e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029356"
---
# <a name="what-is-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager(미리 보기)란?

Azure Virtual Network Manager는 구독 전체에서 가상 네트워크를 전역적으로 그룹화, 구성, 배포 및 관리할 수 있는 관리 서비스입니다. Virtual Network Manager를 사용하면 가상 네트워크를 식별하고 논리적으로 분할하는 네트워크 그룹을 정의할 수 있습니다. 그런 다음 원하는 연결 및 보안 구성을 결정하고 네트워크 그룹에서 선택한 모든 가상 네트워크에서 한 번에 적용할 수 있습니다. 

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-azure-virtual-network-manager-work"></a>Azure Virtual Network Manager는 어떻게 작동하나요?

:::image type="content" source="./media/overview/management-group.png" alt-text="Virtual Network Manager의 관리 그룹 다이어그램":::

만들기 프로세스 중에 Azure Virtual Network Manager에서 관리하는 범위를 정의합니다. 범위는 구독 또는 [관리 그룹](../governance/management-groups/overview.md) 수준에서 정의할 수 있습니다. 범위를 정의한 후에는 Virtual Network Manager의 *연결* 및 *SecurityAdmin* 역할과 같은 기능을 사용할 수 있습니다.

Virtual Network Manager 인스턴스를 배포한 후 조건문을 사용하여 *네트워크 그룹* 을 만들어 이름, 태그 또는 ID(동적 멤버 자격)를 기준으로 가상 네트워크를 선택합니다. 특정 가상 네트워크(정적 멤버 자격)를 선택할 수도 있습니다. 정의된 네트워크 그룹 규칙은 가상 네트워크 멤버 자격에 대해 정의한 규칙을 설명하는 사용자 지정 이니셔티브 정의 및 해당 할당으로 Azure Policy에 반영됩니다. Azure Policy 이니셔티브에 대한 자세한 내용은 [Azure Policy 이니셔티브 구조](../governance/policy/concepts/initiative-definition-structure.md)를 참조하세요. 이러한 정책은 현재 읽기 전용 모드에서 사용할 수 있습니다. 이러한 정책을 만들고, 업데이트하고, 삭제하는 방법에 대한 자세한 내용은 [네트워크 그룹 및 Azure Policy](concept-network-groups.md#network-group-and-azure-policy)를 참조하세요. 그런 다음 토폴로지 및 보안 요구 사항에 따라 이러한 네트워크 그룹에 적용되는 연결 및/또는 보안 구성을 만듭니다. 

연결 구성을 사용하면 메시 또는 허브 및 스포크 네트워크 토폴로지를 만들 수 있습니다. 보안 구성을 사용하면 전역 수준에서 하나 이상의 네트워크 그룹에 적용할 수 있는 규칙의 컬렉션을 정의할 수 있습니다. 원하는 네트워크 그룹 및 구성을 만들었으면 선택한 모든 지역에 구성을 배포할 수 있습니다.

## <a name="key-benefits"></a>주요 이점

* 지역 및 구독 전체에서 전역적으로 연결 및 보안 정책을 중앙에서 관리합니다.

* 메시 네트워크를 관리하는 복잡성 없이 허브 및 스포크 구성에서 스포크 간의 전이적 통신을 사용합니다.

* 전 세계에서 중복 및 복제를 통해 확장성과 가용성이 뛰어난 서비스입니다.

* 네트워크 보안 그룹 규칙을 재정의하는 글로벌 네트워크 보안 규칙을 만들 수 있습니다.

* 가상 네트워크 피어링을 사용하여 서로 다른 가상 네트워크에서 리소스 간에 대기 시간이 낮고 대역폭이 높습니다.

* 특정 지역 시퀀스와 선택한 주파수를 통해 네트워크 변경 사항을 롤아웃 합니다.

## <a name="public-preview-regions"></a>공개 미리 보기 지역

* 미국 중북부

* 미국 서부

* 미국 서부 2

* 미국 동부

* 미국 동부 2

* 북유럽

* 서유럽

* 프랑스 중부

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
- Azure Virtual Network Manager의 [네트워크 그룹](concept-network-groups.md)에 대해 자세히 알아봅니다
- [연결 구성](concept-connectivity-configuration.md)으로 수행할 수 있는 작업에 대해 알아봅니다.
- [보안 관리자 구성](concept-security-admins.md)에 대해 자세히 알아봅니다.
