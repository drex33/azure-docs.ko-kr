---
title: '자습서: 보안 허브 및 스포크 네트워크 만들기'
description: 이 자습서에서는 Azure Virtual Network Manager를 사용하여 허브 및 스포크 네트워크를 만드는 방법을 알아봅니다. 그런 다음 보안 정책을 사용하여 모든 가상 네트워크를 보호합니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 672b1218a52ee94e226c5b7c763c8e7622b60267
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101529"
---
# <a name="tutorial-create-a-secured-hub-and-spoke-network"></a>자습서: 보안 허브 및 스포크 네트워크 만들기

이 자습서에서는 Azure Virtual Network Manager를 사용하여 허브 및 스포크 네트워크 토폴로지를 만듭니다. 그런 다음 허브 가상 네트워크에 가상 네트워크 게이트웨이를 배포하여 스포크 가상 네트워크의 리소스가 VPN을 사용하여 원격 네트워크와 통신할 수 있도록 합니다. 또한 포트 80 및 443에서 인터넷 아웃바운드 네트워크 트래픽을 차단하도록 보안 구성을 구성합니다. 마지막으로 가상 네트워크 및 가상 머신 설정을 확인하여 구성이 올바르게 적용되었는지 확인합니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 여러 가상 네트워크를 만듭니다.
> * 가상 네트워크 게이트웨이를 배포합니다.
> * 허브 및 스포크 네트워크 토폴로지를 만듭니다.
> * 포트 80 및 443에서 트래픽을 차단하는 보안 구성을 만듭니다.
> * 구성이 적용되었는지 확인합니다.

## <a name="prerequisite"></a>필수 조건

* 이 자습서의 단계를 완료하려면 먼저 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만들어야 합니다.

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

이 절차에서는 세 개의 가상 네트워크를 만드는 과정을 안내합니다. 하나는 “미국 서부” 지역에 있고 다른 두 개는 “미국 동부” 지역에 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **+ 리소스 만들기** 를 선택하고 **가상 네트워크** 를 검색합니다. 그런 다음 **만들기** 를 선택하여 가상 네트워크 구성을 시작합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet.png" alt-text="가상 네트워크 만들기 페이지의 스크린샷.":::

1. ‘기본 사항’ 탭에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-basic.png" alt-text="허브 및 스포크 가상 네트워크에 대한 기본 사항 탭의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 이 가상 네트워크를 배포할 구독을 선택합니다. |
    | Resource group | 가상 네트워크를 저장할 새 리소스 그룹을 선택하거나 만듭니다. 이 빠른 시작에서는 **myAVNMResourceGroup** 이라는 리소스 그룹을 사용합니다.
    | Name | 가상 네트워크 이름으로 **VNet-A-WestUS** 를 입력합니다. |
    | 지역 | **미국 서부** 지역을 선택합니다. |

 1. IP 주소 탭에서 다음 네트워크 주소 공간을 구성합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-addresses.png" alt-text="허브 및 스포크 가상 네트워크에 대한 IP 주소 탭의 스크린샷.":::

    | 설정 | 값 |
    | -------- | ----- |
    | IPv4 주소 공간 | 주소 공간으로 **10.3.0.0/16** 을 입력합니다. |
    | 서브넷 이름 | 서브넷 이름으로 **default** 를 입력합니다. |
    | 서브넷 주소 공간 | 서브넷 주소 공간에 **10.3.0.0/24** 를 입력합니다. |

1. **검토 + 만들기** 를 선택하고 **만들기** 를 선택하여 가상 네트워크를 배포합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet-validation.png" alt-text="허브 및 스포크 가상 네트워크에 대한 유효성 검사 페이지의 스크린샷.":::

1. 2~5단계를 반복하여 다음 정보를 사용해 동일한 리소스 그룹에 두 개의 가상 네트워크를 더 만듭니다.

    **두 번째 가상 네트워크**:
    * 이름: **VNet-A-EastUS**
    * 지역: **미국 동부**
    * IPv4 주소 공간: **10.4.0.0/16**
    * 서브넷 이름: **default**
    * 서브넷 주소 공간: **10.4.0.0/24**

    **세 번째 가상 네트워크:**
    * 이름: **VNet-B-EastUS**
    * 지역: **미국 동부**
    * IPv4 주소 공간: **10.5.0.0/16**
    * 서브넷 이름: **default**
    * 서브넷 주소 공간: **10.5.0.0/24**

## <a name="deploy-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 배포

허브 가상 네트워크에 가상 네트워크 게이트웨이를 배포합니다. 이 가상 네트워크 게이트웨이는 스포크에서 허브를 게이트웨이로 사용하도록 설정하는 데 필요합니다.

1. **+ 리소스 만들기** 를 선택하고 **가상 네트워크 게이트웨이** 를 검색합니다. 그런 다음 **만들기** 를 선택하여 가상 네트워크 게이트웨이 구성을 시작합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-gateway.png" alt-text="가상 네트워크 게이트웨이 만들기 페이지의 스크린샷.":::

1. 기본 사항 탭에서 다음 설정을 입력하거나 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-basics.png" alt-text="가상 네트워크 게이트웨이 만들기 기본 사항 탭의 스크린샷." lightbox="./media/tutorial-create-secured-hub-and-spoke/gateway-basics-expanded.png":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다. 가상 네트워크 게이트웨이를 배포하는 데 약 30분이 걸릴 수 있습니다. 이 배포가 완료되기를 기다리는 동안 다음 섹션으로 이동할 수 있습니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-validation.png" alt-text="가상 네트워크 게이트웨이 만들기 유효성 검사 페이지의 스크린샷.":::

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

1. Azure Virtual Network Manager 인스턴스로 이동합니다. 이 자습서에서는 [빠른 시작](create-virtual-network-manager-portal.md) 가이드를 사용하여 이미 만든 것으로 가정합니다.

1. 설정 아래에서 **네트워크 그룹** 을 선택하고 **+ 추가** 를 선택하여 새 네트워크 그룹을 만듭니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="네트워크 그룹 추가 단추의 스크린샷.":::

1. *기본* 탭에서 다음 정보를 입력합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-basics.png" alt-text="네트워크 그룹 만들기 기본 사항 탭의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | 네트워크 그룹 이름으로 **myNetworkGroupB** 를 입력합니다. |
    | 설명 | 이 네트워크 그룹에 대한 설명을 제공합니다. |

1. **조건문** 탭을 선택합니다. 매개 변수로 드롭다운에서 **이름** 을 선택합니다. 연산자 항목에서 **포함** 을 선택합니다. 조건에 **VNet-** 를 입력합니다. 이 조건문은 이전에 만든 세 개의 가상 네트워크를 이 네트워크 그룹에 추가합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-conditional.png" alt-text="네트워크 그룹 조건문 만들기 탭의 스크린샷.":::

1. 선택한 가상 네트워크를 확인해야 하는 경우 **평가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/evaluate-vnet.png" alt-text="유효한 가상 네트워크 페이지의 스크린샷.":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-validation.png" alt-text="네트워크 그룹 만들기 유효성 검사 페이지의 스크린샷.":::

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>허브 및 스포크 연결 구성 만들기

1. 설정 아래에서 **구성** 을 선택하고 **+ 구성 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-configuration.png" alt-text="네트워크 관리자에 대한 구성 추가 단추의 스크린샷.":::

1. 드롭다운 메뉴에서 **연결** 을 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="구성 드롭다운 메뉴의 스크린샷.":::

1. 연결 구성에서 다음 정보를 입력하고 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/connectivity-configuration.png" alt-text="연결 구성 추가 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | Name | 구성 이름으로 **HubA** 를 입력합니다. |
    | 설명 | 이 연결 구성에서 수행할 작업에 대한 설명을 제공합니다. |
    | 토폴로지 | **허브 및 스포크** 를 선택합니다. |

1. 허브 및 스포크 토폴로지를 선택하면 더 많은 필드가 표시됩니다. 다음 설정을 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/hub-configuration.png" alt-text="연결 구성의 허브 선택 스크린샷.":::

    | 설정 | 값 |
    | -------- | ----- |
    | 허브 | 허브 가상 네트워크로 **VNet-A-West** 를 선택합니다. |
    | 기존 피어링 | 이 옵션을 **선택 취소** 된 상태로 둡니다. |
    | 스포크 네트워크 그룹 | **네트워크 그룹 추가** 를 선택하고 구성에 **myNetworkGroupB** 를 추가합니다. |

1. 네트워크 그룹을 추가한 후 다음 옵션을 선택합니다. 그런 다음 추가를 선택하여 연결 구성을 만듭니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-options.png" alt-text="네트워크 그룹 구성의 설정 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | 전이성 | **네트워크 그룹 내에서 피어링 사용** 확인란을 선택합니다. 이 설정을 사용하면 동일한 지역의 네트워크 그룹에 있는 스포크 가상 네트워크가 서로 직접 통신할 수 있습니다. |
    | 글로벌 메시 | 이 옵션을 **선택 취소** 된 상태로 둡니다. 두 스포크 모두 동일한 지역에 있기 때문에 이 설정이 필요하지 않습니다. |
    | 게이트웨이 | **허브를 게이트웨이로 사용** 을 선택합니다. |

## <a name="deploy-the-connectivity-configuration"></a>연결 구성 배포

연결 구성을 배포하기 전에 가상 네트워크 게이트웨이가 성공적으로 배포되었는지 확인합니다. 허브 및 스포크 구성을 배포할 때 **허브를 게이트웨이로 사용** 을 사용하도록 설정했지만 게이트웨이가 없는 경우 배포가 실패합니다. 자세한 내용은 [허브를 게이트웨이로 사용](concept-connectivity-configuration.md#use-hub-as-a-gateway)을 참조하세요.

1. 설정 아래에서 **배포** 를 선택한 다음 **구성 배포** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="네트워크 관리자의 배포 페이지의 스크린샷.":::

1. **연결** 구성 유형을 선택하고 직전 섹션에서 만든 **HubA** 구성을 선택합니다. 그런 다음 대상 지역으로 **미국 서부** 및 **미국 동부** 를 선택하고 **배포** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-configuration.png" alt-text="구성 배포 페이지의 스크린샷.":::

1. **확인** 을 선택하여 기존 구성을 덮어쓰고 보안 관리자 구성을 배포한다는 것을 확인합니다. 

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="배포 확인 메시지의 스크린샷.":::

1. 이제 해당 지역에 대한 목록에 배포가 표시됩니다. 구성 배포가 완료되는 데 약 15~20분이 걸릴 수 있습니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="배포 목록의 배포 진행 중 스크린샷.":::

## <a name="create-security-configuration"></a>보안 구성 만들기

1. 설정 아래에서 **구성** 을 다시 선택하고 **+ 구성 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-security-configuration.png" alt-text="네트워크 관리자에 대한 다른 구성을 추가하는 스크린샷.":::

1. 메뉴에서 **SecurityAdmin** 을 선택하여 SecurityAdmin 구성 만들기를 시작합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-drop-down.png" alt-text="드롭다운 메뉴의 SecurityAdmin 스크린샷.":::

1. 구성 이름으로 **mySecurityConfig** 를 입력하고 **+ 규칙 컬렉션 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-admin-configuration.png" alt-text="보안 관리자 구성 페이지의 스크린샷.":::

1. 규칙 컬렉션 이름으로 **myRuleCollection** 을 입력하고 대상 네트워크 그룹에 **myNetworkGroupB** 를 선택합니다. 그런 다음 **+ 규칙 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule-collection.png" alt-text="규칙 컬렉션 추가 페이지의 스크린샷.":::

1. 다음 설정을 입력하고 선택한 다음 **추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule.png" alt-text="규칙 추가 페이지의 스크린샷.":::

1. **저장** 을 선택하여 규칙 컬렉션을 구성에 추가합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/save-rule-collection.png" alt-text="규칙 컬렉션에 대한 저장 단추의 스크린샷.":::

1. **추가** 를 선택하여 보안 관리자 구성을 만듭니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-configuration.png" alt-text="구성을 만드는 추가 단추의 스크린샷.":::

## <a name="deploy-the-security-admin-configuration"></a>보안 관리자 구성 배포

1. 설정 아래에서 **배포** 를 선택한 다음 **구성 배포** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployments.png" alt-text="Virtual Network Manager의 보안 배포 페이지의 스크린샷.":::

1. **SecurityAdmin** 의 구성 유형을 선택하고 직전 섹션에서 만든 **mySecurityConfig** 구성을 선택합니다. 그런 다음 대상 지역으로 **미국 서부** 및 **미국 동부** 를 선택하고 **배포** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-security.png" alt-text="보안 구성 배포 스크린샷.":::

1. **확인** 을 선택하여 기존 구성을 덮어쓰고 보안 관리자 구성을 배포한다는 것을 확인합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="보안 구성 배포 확인 메시지의 스크린샷.":::

1. 이제 선택한 지역에 대한 목록에 배포가 표시됩니다. 구성 배포가 완료되는 데 약 15~20분이 걸릴 수 있습니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-in-progress.png" alt-text="배포 목록의 보안 배포 진행 중 스크린샷.":::

## <a name="verify-deployment-of-configurations"></a>구성 배포 확인

### <a name="verify-from-a-virtual-network"></a>가상 네트워크에서 확인

1. **VNet-A-WestUS** 가상 네트워크로 이동하여 설정 아래에서 **네트워크 관리자** 를 선택합니다. **HubA** 연결 구성이 적용된 것을 볼 수 있습니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-connectivity-configuration.png" alt-text="가상 네트워크에 적용된 연결 구성의 스크린샷.":::

1. **설정** 에서 *피어링* 을 선택합니다. Virtual Network Manage가 이름에 *AVNM* 을 사용하여 만든 가상 네트워크 피어링이 표시됩니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-peerings.png" alt-text="Virtual Network Manager가 만든 가상 네트워크 피어링의 스크린샷.":::

1. **SecurityAdmin** 탭을 선택하여 이 가상 네트워크에 적용된 보안 관리자 규칙을 확인합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-admin-configuration.png" alt-text="가상 네트워크에 적용된 보안 관리자 규칙의 스크린샷.":::

### <a name="verify-from-a-vm"></a>VM에서 확인

1. **VNet-A-EastUS** 에 테스트 Windows VM을 배포합니다. 

1. *VNet-A-EastUS* 에서 만든 테스트 VM으로 이동하고 설정 아래에서 **네트워킹** 을 선택합니다. **아웃바운드 포트 규칙** 을 선택하면 보안 관리자 규칙이 적용된 것을 볼 수 있습니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-security-rules.png" alt-text="테스트 VM의 네트워크 보안 규칙을 보여 주는 스크린샷.":::

1. 네트워크 인터페이스 이름을 선택합니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-network-settings.png" alt-text="테스트 VM의 네트워크 설정을 보여 주는 스크린샷.":::

1. 그런 다음, 지원 + 문제 해결에서 **유효 경로** 를 선택하여 가상 네트워크 피어링에 대한 경로를 확인합니다. 다음 홉이 `VNetGlobalPeering`인 `10.3.0.0/16` 경로가 허브 가상 네트워크에 대한 경로입니다. 다음 홉이 `ConnectedGroup`인 `10.5.0.0/16` 경로가 다른 스포크 가상 네트워크에 대한 경로입니다. **전이성** 을 사용하도록 설정하면 모든 스포크 가상 네트워크가 *ConnectedGroup* 에 있게 됩니다.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/effective-routes.png" alt-text="테스트 VM 네트워크 인터페이스의 유효 경로를 보여 주는 스크린샷." lightbox="./media/tutorial-create-secured-hub-and-spoke/effective-routes-expanded.png" :::

## <a name="clean-up-resources"></a>리소스 정리

Azure Virtual Network Manager가 더 이상 필요하지 않은 경우 리소스를 삭제하기 전에 다음 사항을 모두 충족해야 합니다.

* 모든 지역에 구성이 배포되지 않습니다.
* 모든 구성이 삭제되었습니다.
* 모든 네트워크 그룹이 삭제되었습니다.

리소스 그룹을 삭제하기 전에 [구성 요소 제거 검사 목록](concept-remove-components-checklist.md)을 사용하여 여전히 사용할 수 있는 자식 리소스가 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[보안 관리자 구성](how-to-block-network-traffic-portal.md)을 사용하여 네트워크 트래픽을 차단하는 방법에 대해 알아봅니다.
