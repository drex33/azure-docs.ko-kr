---
title: Azure Virtual Network Manager (미리 보기)를 사용 하 여 허브 및 스포크 토폴로지 만들기
description: Azure Virtual Network Manager를 사용 하 여 허브 및 스포크 네트워크 토폴로지를 만드는 방법에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c07eccfda6492a5957ce9d2f0df6e0133e5b9d3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102894"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager (미리 보기)를 사용 하 여 허브 및 스포크 토폴로지 만들기

이 문서에서는 Azure Virtual Network Manager를 사용 하 여 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아봅니다. 이 구성을 사용 하는 경우 허브 역할을 할 가상 네트워크를 선택 하면 모든 스포크 가상 네트워크는 기본적으로 허브에 대 한 양방향 피어 링을 갖게 됩니다. 또한 스포크 가상 네트워크 간의 직접 연결을 사용 하도록 설정 하 고 스포크 가상 네트워크에서 허브의 가상 네트워크 게이트웨이를 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> *Azure Virtual Network Manager* 는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [허브 및 스포크](concept-connectivity-configuration.md#hub-and-spoke-topology) 네트워크 토폴로지를 참조 하세요.
* [Azure Virtual Network Manager 인스턴스](create-virtual-network-manager-portal.md#create-virtual-network-manager)를 만들었습니다.
* 허브 및 스포크 구성에서 사용 하려는 가상 네트워크를 식별 하거나 새 [가상 네트워크](../virtual-network/quick-create-portal.md)를 만듭니다. 

## <a name="create-a-network-group"></a><a name="group"></a> 네트워크 그룹 만들기

이 섹션은 허브 및 스포크 네트워크 토폴로지에 사용할 가상 네트워크를 포함 하는 네트워크 그룹을 만드는 데 도움이 됩니다.

1. Azure Virtual Network Manager 인스턴스로 이동 합니다. 이 방법 가이드에서는 [빠른](create-virtual-network-manager-portal.md) 시작 가이드를 사용 하 여 만든 것으로 가정 합니다.

1. *설정* 아래에서 **네트워크 그룹** 을 선택 하 고 **+ 추가** 를 선택 하 여 새 네트워크 그룹을 만듭니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="네트워크 그룹 추가 단추의 스크린샷":::

1. *기본 사항* 탭에서 네트워크 그룹의 **이름** 및 **설명** 을 입력 합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="네트워크 그룹 추가에 대 한 기본 사항 탭의 스크린샷":::

1. 가상 네트워크를 수동으로 추가 하려면 **정적 그룹 구성원** 탭을 선택 합니다. 자세한 내용은 [정적 멤버](concept-network-groups.md#static-membership)를 참조 하세요.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="정적 그룹 구성원 탭의 스크린샷":::

1. 가상 네트워크를 동적으로 추가 하려면 **조건부 문** 탭을 선택 합니다. 자세한 내용은 [동적 멤버 자격](concept-network-groups.md#dynamic-membership)을 참조 하세요.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="조건부 문 탭의 스크린샷":::

1. 네트워크 그룹에 대해 선택한 가상 네트워크에 만족 했으면 **검토 + 만들기** 를 선택 합니다. 그런 다음 유효성 검사를 통과 한 후 **만들기** 를 선택 합니다.
 
## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>허브 및 스포크 연결 구성 만들기

이 섹션에서는 이전 섹션에서 만든 네트워크 그룹을 사용 하 여 허브 및 스포크 구성을 만드는 방법을 안내 합니다.

1. *설정* 아래에서 **구성** 을 선택 하 고 **+ 구성 추가** 를 선택 합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="구성 목록의 스크린샷":::

1. 드롭다운 메뉴에서 **연결** 을 선택 합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="구성 드롭다운 메뉴의 스크린샷":::

1. *연결 구성 추가* 페이지에서 다음 정보를 입력 하거나 선택 합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/connectivity-configuration.png" alt-text="연결 구성 추가 페이지의 스크린샷":::

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | 이 구성의 *이름을* 입력 하십시오. |
    | 설명 | *선택 사항* 이 구성에서 수행할 작업에 대 한 설명을 입력 합니다. |
    | 토폴로지 | **허브 및 스포크** 토폴로지를 선택 합니다. |
    | 허브 | 허브 가상 네트워크 역할을 할 가상 네트워크를 선택 합니다. |
    | 기존 피어 링 | 이 구성에 정의 된 네트워크 그룹의 가상 네트워크 간에 이전에 만든 모든 VNet 피어 링을 제거 하려면이 확인란을 선택 합니다. |

1. 그런 다음 **+ 네트워크 그룹 추가** 를 선택 합니다. 

1. *네트워크 그룹 추가* 페이지에서이 구성에 추가 하려는 네트워크 그룹을 선택 합니다. 그런 다음 **추가** 를 선택 하 여 저장 합니다.

1. *스포크 네트워크 그룹* 의 네트워크 그룹 이름 옆에 다음과 같은 세 가지 옵션이 표시 됩니다.
    
    :::image type="content" source="./media/how-to-create-hub-and-spoke/spokes-settings.png" alt-text="스포크 네트워크 그룹 설정의 스크린샷" lightbox="./media/how-to-create-hub-and-spoke/spokes-settings-expanded.png":::

    * *직접 연결*: 동일한 지역의 네트워크 그룹에 있는 가상 네트워크 간에 VNet 피어 링을 설정 하려면 **네트워크 그룹 내에서 피어 링 사용** 을 선택 합니다.
    * *전역 메시*: 여러 지역에서 네트워크 그룹의 모든 가상 네트워크에 대해 VNet 피어 링을 설정 하려면 **지역 간 메시 연결 사용** 을 선택 합니다.
    * *게이트웨이*:이 네트워크 그룹에서 온-프레미스로 트래픽을 전달 하는 데 사용 하도록 할 허브 가상 네트워크에 가상 네트워크 게이트웨이가 있는 경우 **허브를 게이트웨이로 사용** 을 선택 합니다.

    각 네트워크 그룹에 대해 사용 하도록 설정할 설정을 선택 합니다.

1. 마지막으로 **추가** 를 선택 하 여 허브 및 스포크 연결 구성을 만듭니다.

## <a name="deploy-the-hub-and-spoke-configuration"></a>허브 및 스포크 구성 배포

환경에서이 구성을 적용 하려면 선택한 가상 네트워크를 만든 지역에 구성을 배포 해야 합니다.

1. *설정* 아래에서 **배포** 를 선택한 다음 **구성 배포** 를 선택 합니다.

1. *구성 배포* 에서 다음 설정을 선택 합니다.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="구성 배포 페이지의 스크린샷":::

    | 설정 | 값 |
    | ------- | ----- |
    | 구성 형식 | **연결** 을 선택 합니다. |
    | 구성 | 이전 섹션에서 만든 구성의 이름을 선택 합니다. |
    | 대상 지역 | 구성에 대해 선택한 가상 네트워크에 적용 되는 모든 지역을 선택 합니다. |

1. **배포** 를 선택한 다음 **확인** 을 선택 하 여 선택한 지역에 구성을 커밋합니다.

1. 구성의 배포에는 최대 15-20 분이 걸릴 수 있으며, **새로 고침** 단추를 선택 하 여 배포 상태를 확인 합니다.

## <a name="confirm-deployment"></a>배포 확인

1. [적용 된 구성 보기](how-to-view-applied-configurations.md)를 참조 하세요.

1. 스포크 간의 *직접 연결* 을 테스트 하려면 각 스포크 가상 네트워크에 가상 머신을 배포 합니다. 그런 다음 한 가상 머신에서 다른 가상 머신으로 ICMP 요청을 시작 합니다.

## <a name="next-steps"></a>다음 단계

- [보안 관리자 규칙](concept-security-admins.md) 에 대 한 자세한 정보
- [SecurityAdmin 구성을](how-to-block-network-traffic-portal.md)사용 하 여 네트워크 트래픽을 차단 하는 방법에 대해 알아봅니다.
