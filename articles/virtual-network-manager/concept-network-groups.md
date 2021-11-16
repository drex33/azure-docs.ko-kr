---
title: Azure Virtual Network Manager(미리 보기)의 네트워크 그룹이란?
description: 네트워크 그룹이 가상 네트워크를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5da75fba68e285ea4b01feee514bd38f6ceae036
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493603"
---
# <a name="what-is-a-network-group-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager(미리 보기)의 네트워크 그룹이란?

이 문서에서는 *네트워크 그룹에* 대해 알아보고 네트워크 그룹을 통해 가상 네트워크를 그룹화하여 보다 쉽게 관리하게 하는 방법을 알아봅니다. *또한 정적 그룹 멤버 자격* 및 동적 그룹 멤버 *자격과* 각 멤버 자격 유형을 사용하는 방법에 대해서도 알아봅니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="network-group"></a>네트워크 그룹

*네트워크 그룹은* 수동으로 선택하거나 조건문을 사용하여 선택한 가상 네트워크 집합입니다. 가상 네트워크를 수동으로 선택하면 *정적 멤버라고* 합니다. 조건문을 사용하여 선택한 가상 네트워크를 *동적 멤버라고* 합니다. 

## <a name="static-membership"></a>정적 멤버 자격

네트워크 그룹을 만들 때 제공된 목록에서 개별 가상 네트워크를 수동으로 선택하여 그룹에 가상 네트워크를 추가할 수 있습니다. 가상 네트워크 목록은 Azure Virtual Network Manager 배포 시 정의된 범위(관리 그룹 또는 구독)에 따라 달라집니다. 이 방법은 네트워크 그룹에 추가하려는 가상 네트워크가 몇 개인 경우에 유용합니다. 새 변경 내용을 적용하려면 정적 멤버를 포함하는 구성에 대한 업데이트를 다시 배포해야 합니다.

## <a name="dynamic-membership"></a>동적 멤버 자격

동적 멤버 자격은 정의한 조건문을 충족하는 경우 여러 가상 네트워크를 한 번에 선택할 수 있는 유연성을 제공합니다. 이 방법은 하나 이상의 구독에 수백 또는 수천 개의 가상 네트워크가 있고 이름, ID 또는 태그로 소수의 가상 네트워크를 선택해야 하는 시나리오에 유용합니다. 각 조건은 나열된 순서대로 처리되고 구성은 해당 조건을 충족하기 위해 가상 네트워크에 적용됩니다. 조건문을 구성하는 방법을 알아보려면 [동적 멤버 자격 에서 요소 제외를](how-to-exclude-elements.md)참조하세요.

## <a name="network-group-and-azure-policy"></a>네트워크 그룹 및 Azure 정책

네트워크 그룹을 만들면 Azure Virtual Network Manager에서 가상 네트워크 멤버 자격 변경에 대한 알림을 받을 수 있도록 Azure 정책이 만들어집니다. 정의된 정책은 볼 수 있지만 현재 사용자가 편집할 수 없습니다. 네트워크 그룹에 대한 Azure 정책 정의 및 할당 만들기, 변경 및 삭제는 현재 Azure Network Manager를 통해서만 가능합니다. 

Azure Network Manager 리소스에 대한 Azure Policy 이니셔티브 정의 및 할당을 만들려면 필요한 구성을 사용하여 네트워크 그룹을 만들고 배포합니다. 기존 Azure Policy 이니셔티브 정의 또는 해당 할당을 업데이트하려면 변경 내용을 변경하고 Azure Virtual Network Manager 리소스 내의 네트워크 그룹에 배포해야 합니다. Azure Policy 이니셔티브 정의 및 할당을 삭제하려면 정책과 연결된 Azure Virtual Network Manager 리소스를 배포 및 삭제해야 합니다. 여기에는 구성 배포 해제, 구성 삭제 및 네트워크 그룹 삭제가 포함될 수 있습니다. 삭제에 대한 자세한 내용은 구성 요소 제거에 대한 Azure Virtual Network Manager [검사 목록을 검토하세요.](concept-remove-components-checklist.md)  

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
- Azure Virtual Network Manager를 사용하여 [허브 및 스포크 토폴로지를](how-to-create-hub-and-spoke.md) 만드는 방법을 알아봅니다.
- [SecurityAdmin 구성을](how-to-block-network-traffic-portal.md)사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
