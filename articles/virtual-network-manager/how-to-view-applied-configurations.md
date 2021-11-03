---
title: Azure Virtual Network Manager에서 적용 한 구성 보기 (미리 보기)
description: Azure Virtual Network Manager에서 적용 한 구성을 확인 하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0d0f0d0ed5eb100aff4df02a65ab4902da1b7c16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053242"
---
# <a name="view-configurations-applied-by-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager에서 적용 한 구성 보기 (미리 보기)

Azure Virtual Network Manager는 구성이 올바르게 적용 되 고 있는지 확인할 수 있는 몇 가지 다른 방법을 제공 합니다. 이 문서에서는 가상 네트워크 및 가상 머신 수준에서 적용 되는 구성을 확인할 수 있는 방법을 살펴보겠습니다. 활동 로그에 표시 되는 작업을 계속 진행 합니다.

## <a name="virtual-network-visibility"></a>Virtual network 표시 유형

Virtual Network Manager에서 구성을 배포한 후에는 가상 네트워크 리소스에서 적용 된 구성을 볼 수 있습니다. 

1. 가상 네트워크 리소스로 이동 하 여 *설정* 에서 **네트워크 관리자** 를 선택 합니다. 연결 탭에는 가상 네트워크와 연결 된 모든 연결 구성이 표시 됩니다. 

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-connectivity.png" alt-text="가상 네트워크에 연결 된 연결 구성의 스크린샷":::

2. **SecurityAdmin** 탭을 선택 하 여 가상 네트워크에 현재 적용 된 모든 보안 규칙을 표시 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-security.png" alt-text="가상 네트워크에 연결 된 보안 규칙의 스크린샷":::

## <a name="virtual-machine-visibility"></a>가상 컴퓨터 표시 유형

가상 컴퓨터 수준에서 연결 구성에 대 한 유효 경로 및 Virtual Network Manager에서 적용 되는 보안 규칙을 볼 수 있습니다.

### <a name="applied-security-rules"></a>적용 된 보안 규칙

1. Virtual Network Manager에서 구성을 적용 한 가상 네트워크의 가상 머신으로 이동 합니다. 그런 다음 왼쪽 메뉴 창의 *설정* 아래에서 **네트워킹** 을 선택 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/virtual-machine.png" alt-text="가상 컴퓨터 개요 페이지의 스크린샷":::

1. 인바운드 네트워크 보안 그룹의 목록과 Virtual Network Manager에서 적용 하는 인바운드 보안 규칙에 대 한 섹션도 표시 됩니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-inbound-rules.png" alt-text="가상 컴퓨터 아웃 바운드 보안 규칙의 스크린샷":::

1. **아웃 바운드 포트 규칙** 탭을 선택 하 여 가상 컴퓨터에 대 한 아웃 바운드 보안 규칙을 표시 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-outbound-rules.png" alt-text="가상 컴퓨터 인바운드 보안 규칙의 스크린샷":::

### <a name="effective-routes"></a>유효한 경로

1. 적용 된 연결 구성에 대 한 유효 경로를 확인 하려면 가상 컴퓨터의 *네트워킹* 설정에서 네트워크 인터페이스 이름을 선택 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="가상 컴퓨터 네트워크 인터페이스 카드를 선택 하는 스크린샷":::

1. 그런 다음 *지원 + 문제 해결* 에서 **유효 경로** 를 선택 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface.png" alt-text="VM 네트워크 인터페이스 카드에서 유효 경로 단추의 스크린샷":::

1. *ConnectedGroup* 의 다음 홉 유형이 포함 된 경로는 메시 구성의 일부 이거나 네트워크 그룹에 대 한 [*직접 연결이*](concept-connectivity-configuration.md#direct-connectivity) 설정 된 경우입니다. 허브 및 스포크 가상 네트워크 간의 경로는 다음 홉 유형 *Vnetpeering 링* 또는 *globalvnetpeering 링* 으로 표시 됩니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-routes.png" alt-text="연결 된 그룹 및 허브 경로를 표시 하는 유효 경로의 스크린샷" lightbox="./media/how-to-view-applied-configurations/effective-routes-expanded.png":::

    > [!NOTE]
    > 허브 가상 네트워크 주소 공간도 *ConnectedGroup* 에도 **포함** 됩니다. 따라서 허브 및 스포크 가상 네트워크 간에 가상 네트워크 피어 링이 실패 하면 연결 된 그룹에 있기 때문에 서로 통신할 수 있습니다.
    > 

### <a name="effective-security-rules"></a>유효한 보안 규칙

1. 적용 된 보안 규칙 구성에 대 한 유효 보안 규칙을 확인 하려면 가상 컴퓨터의 *네트워킹* 설정에서 네트워크 인터페이스 이름을 선택 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="보안 규칙에 대 한 가상 컴퓨터 네트워크 인터페이스 카드를 선택 하는 스크린샷":::

1. 그런 다음 *지원 + 문제 해결* 에서 **유효한 보안 규칙** 을 선택 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface-security-rules.png" alt-text="VM 네트워크 인터페이스 카드에 대 한 유효한 보안 규칙 단추의 스크린샷":::

1. Azure Virtual Network 관리자의 이름을 선택 하 여 가상 컴퓨터에 연결 된 보안 관리 규칙을 확인 합니다.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-security-rules.png" alt-text="가상 머신과 연결 된 효과적인 보안 규칙의 스크린샷":::

## <a name="activity-log"></a>활동 로그

Azure Virtual Network Manager 리소스에 대 한 활동 로그를 확인 하 여 사용자 또는 네트워크 관리자가 수행한 변경 내용을 볼 수 있습니다. 활동 로그를 보려면 Azure Portal의 네트워크 관리자 리소스로 이동 합니다. 왼쪽 창 메뉴에서 **활동 로그** 를 선택 합니다. 필요한 경우 *Timespan* 을 조정 하 고 더 많은 필터를 추가 하 여 작업 목록을 좁힙니다. Azure Portal 위쪽에서 서비스를 검색 하 여 *활동 로그* 를 볼 수도 있습니다.

:::image type="content" source="./media/how-to-view-applied-configurations/activity-log.png" alt-text="네트워크 관리자에 대 한 활동 로그 페이지의 스크린샷":::

### <a name="list-of-operations"></a>작업 목록

다음 목록에는 활동 로그에 표시 되는 작업이 포함 되어 있습니다.

| 이름 | 설명 |
| ---- | ----------- |
| Commit | 구성의 배포가 지역에 커밋 되었습니다. |
| ConnectivityConfiguration 삭제 | 네트워크 관리자에서 연결 구성을 삭제 하는 중입니다. |
| NetworkGroups 삭제 | 네트워크 관리자에서 네트워크 그룹을 삭제 하는 중입니다.|
| 규칙 삭제 | 규칙 컬렉션에서 규칙을 삭제 합니다. |
| RuleCollections 삭제 | 보안 관리 구성에서 규칙 컬렉션을 삭제 합니다. |
| SecurityAdminConfigurations 삭제 | 네트워크 관리자에서 보안 관리자 구성을 삭제 합니다. |
| ListDeploymentStatus | 연결 또는 보안 관리자 구성의 배포 상태를 확인 하는 중입니다. |
| ListActiveConnectivityConfiguration | 가상 네트워크에 적용 된 연결 구성 목록 보기|
| ListActiveSecurityAdminRules | 가상 네트워크에 적용 되는 보안 관리자 구성 목록을 확인 하는 중입니다. |
| ConnectivityConfiguration를 작성 합니다. | 새 연결 구성을 만드는 중입니다. |
| NetworkGroups 쓰기 | 새 네트워크 그룹을 만드는 중입니다. |
| NetworkManager 쓰기 | 새 Azure Virtual Network Manager 인스턴스를 만듭니다. |
| 쓰기 규칙 | 규칙 컬렉션에 추가할 새 보안 규칙을 만듭니다. |
| RuleCollections 쓰기 | 새 규칙 컬렉션을 만들어 보안 관리자 구성에 추가 합니다. |
| SecurityAdminConfiguration 작성 | 새 보안 관리자 구성 만들기 |

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
- 질문과 대답은 [네트워크 관리자 FAQ](faq.md) 를 참조 하십시오.
