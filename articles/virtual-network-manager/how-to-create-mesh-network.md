---
title: Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지 만들기(미리 보기)
description: Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지를 만드는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fea577a1aa3d2a26d218a7d7c97140d5f58661f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052761"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지 만들기(미리 보기)

이 문서에서는 Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지를 만드는 방법을 알아봅니다. 이 구성을 사용하면 동일한 네트워크 그룹에 있는 동일한 지역의 모든 가상 네트워크가 서로 통신할 수 있습니다. 연결 구성에서 전역 메시 설정을 사용하도록 설정하여 지역 간 연결을 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> *Azure Virtual Network Manager는* 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

* [메시](concept-connectivity-configuration.md#mesh-network-topology) 네트워크 토폴로지 알아보기
* Azure [Virtual Network Manager 인스턴스를](create-virtual-network-manager-portal.md#create-virtual-network-manager)만들었습니다.
* 메시 구성에서 사용하려는 가상 네트워크를 식별하거나 새 [가상 네트워크를](../virtual-network/quick-create-portal.md)만듭니다.

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

이 섹션에서는 허브 및 스포크 네트워크 토폴로지에서 사용할 가상 네트워크가 포함된 네트워크 그룹을 만드는 데 도움이 됩니다.

1. Azure Virtual Network Manager 인스턴스로 이동합니다. 이 방법 가이드에서는 [빠른 시작](create-virtual-network-manager-portal.md) 가이드를 사용하여 만든 것으로 가정합니다.

1. *설정* 아래에서 **네트워크 그룹을** 선택한 **다음, + 추가를** 선택하여 새 네트워크 그룹을 만듭니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="네트워크 그룹 추가 단추의 스크린샷.":::

1. 기본 *사항* 탭에서 네트워크 그룹에 대한 **이름** 및 **설명을** 입력합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="네트워크 그룹 추가에 대한 기본 사항 탭의 스크린샷":::

1. 가상 네트워크를 수동으로 추가하려면 **정적 그룹 구성원** 탭을 선택합니다. 자세한 내용은 정적 멤버 를 [참조하세요.](concept-network-groups.md#static-membership)

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="정적 그룹 구성원 탭의 스크린샷.":::

1. 가상 네트워크를 동적으로 추가하려면 **조건문 탭을** 선택합니다. 자세한 내용은 [동적 멤버 자격을 참조하세요.](concept-network-groups.md#dynamic-membership)

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="조건문 탭의 스크린샷.":::

1. 네트워크 그룹에 대해 선택한 가상 네트워크에 만족하면 **검토 + 만들기를** 선택합니다. 그런 다음, 유효성 검사가 통과되면 **만들기를** 선택합니다.

## <a name="create-a-mesh-connectivity-configuration"></a>메시 연결 구성 만들기

이 섹션에서는 이전 섹션에서 만든 네트워크 그룹을 사용하여 메시 구성을 만드는 방법을 안내합니다.

1. *설정* 아래에서 **구성을** 선택한 **다음, + 구성 추가를** 선택합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="구성 목록의 스크린샷.":::

1. 드롭다운 메뉴에서 **연결을** 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="구성 드롭다운 메뉴의 스크린샷.":::

1. 연결 *구성 추가* 페이지에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/how-to-create-mesh-network/connectivity-configuration.png" alt-text="연결 구성 추가 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | 이 구성의 *이름을* 입력합니다. |
    | 설명 | *선택 사항* 이 구성에서 수행할 작업을 설명합니다. |
    | 토폴로지 | **메시** 토폴로지 선택 |
    | 전역 메시 | 동일한 네트워크 그룹의 가상 네트워크 간에 지역 간 연결을 사용하도록 설정하려면 이 옵션을 선택합니다. |

1. 그런 **다음, + 네트워크 그룹 추가를** 선택합니다. 

1. 네트워크 *그룹 추가* 페이지에서 이 구성에 추가할 네트워크 그룹을 선택합니다. 그런 **다음, 추가를** 선택하여 저장합니다.

1. **추가를** 다시 선택하여 메시 연결 구성을 만듭니다.

## <a name="deploy-the-mesh-configuration"></a>메시 구성 배포

이 구성이 사용자 환경에 적용하려면 선택한 가상 네트워크가 만들어지는 지역에 구성을 배포해야 합니다.

1. *설정* 아래에서 **배포를** 선택한 **다음, 구성 배포를** 선택합니다.

1. 구성 *배포에서* 다음 설정을 선택합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="구성 배포 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | 구성 형식 | **연결을** 선택합니다. |
    | 구성 | 이전 섹션에서 만든 구성의 이름을 선택합니다. |
    | 대상 지역 | 구성에 대해 선택한 가상 네트워크에 적용되는 지역을 Select all. |

1. **배포를** 선택한 **다음, 확인을** 선택하여 선택한 지역에 구성을 커밋합니다.

1. 구성 배포에는 최대 15~20분이 걸릴 수 있습니다. **새로 고침** 단추를 선택하여 배포 상태를 확인합니다.

## <a name="confirm-deployment"></a>배포 확인

1. [적용된 구성 보기를 참조하세요.](how-to-view-applied-configurations.md)

1. 가상 네트워크 간의 연결을 테스트하려면 각 가상 네트워크에 테스트 가상 머신을 배포하고 가상 네트워크 간에 ICMP 요청을 시작합니다.

## <a name="next-steps"></a>다음 단계

- 보안 [관리자 규칙에](concept-security-admins.md) 대해 알아보기
- [SecurityAdmin 구성을](how-to-block-network-traffic-portal.md)사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
