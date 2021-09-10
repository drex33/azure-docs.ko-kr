---
title: '자습서: Azure Route Server와 Quagga 네트워크 가상 어플라이언스 간의 피어링 구성'
description: 이 자습서에서는 Azure Route Server를 구성하고 Quagga 네트워크 가상 어플라이언스와 피어링하는 방법을 보여줍니다.
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823956"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>자습서: Azure Route Server와 Quagga 네트워크 가상 어플라이언스 간의 피어링 구성

이 자습서에서는 Azure Route Server를 가상 네트워크에 배포하고 Quagga 네트워크 가상 어플라이언스와 BGP 피어링 연결을 설정하는 방법을 보여줍니다. 5개의 서브넷이 있는 가상 네트워크를 배포할 것입니다. 서브넷 중 하나는 Azure Route Server 전용이고 나머지는 Quagga NVA 전용입니다. Quagga NVA는 Route Server와 경로를 교환하도록 구성됩니다. 마지막으로, Azure Route Server 및 Quagga NVA에서 경로가 제대로 교환되는지 테스트합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 5개의 서브넷이 있는 가상 네트워크 만들기
> * Azure Route Server 배포
> * Quagga를 실행하는 가상 머신 배포
> * Route Server 피어링 구성
> * 학습된 경로 확인

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure Route Server와 Quagga NVA를 배포하려면 가상 네트워크가 필요합니다. 배포마다 고유한 전용 서브넷이 사용됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 화면의 왼쪽 상단에서 **리소스 만들기** 를 선택하고 **가상 네트워크** 를 검색합니다. 그런 다음 **생성** 를 선택합니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="새 가상 네트워크 리소스를 만드는 스크린샷":::

1. *가상 네트워크 만들기* 의 *기본 사항* 탭에서 다음 정보를 입력하거나 선택하고 **다음: IP 주소 >** 를 선택합니다.

    | 설정 | 값 |
    | -------- | ----- | 
    | 구독 | 이 배포의 구독을 선택합니다. |
    | Resource group | 이 배포에 사용할 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. | 
    | Name | 가상 네트워크의 이름을 입력합니다. 이 자습서에서는 *myVirtualNetwork* 를 사용합니다.
    | 지역 | 이 가상 네트워크를 배포할 지역을 선택합니다. 이 자습서에서는 *미국 서부* 를 사용합니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="가상 네트워크의 [기본 사항] 탭 설정 스크린샷":::

1. **IP 주소** 탭에서 *가상 네트워크 주소 공간* 을 **10.1.0.0/16** 으로 구성합니다. 다음 5개 서브넷을 구성합니다.

    | 서브넷 이름 | 서브넷 주소 범위 |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="가상 네트워크의 IP 주소 설정 스크린샷":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과한 후 **만들기** 를 선택합니다.

## <a name="create-the-azure-route-server"></a>Azure Route Server 만들기

Route Server는 BGP 피어링 연결을 사용하여 NVA와 통신하고 가상 네트워크 경로를 교환하는 데 사용됩니다.

1. https://aka.ms/routeserver로 이동합니다.

1. **+ 새 Route Server 만들기** 를 선택합니다.

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Route Server 방문 페이지의 스크린샷":::

1. **Route Server 만들기** 페이지에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | -------- | ----- |
    | 구독 | 이전 섹션에서 가상 네트워크가 만들어진 것과 동일한 구독을 선택합니다. | 
    | Resource group | *myRouteServerRG* 라는 기존 리소스 그룹을 선택합니다. |
    | Name | Route Server 이름 *myRouteServer* 를 입력합니다. |
    | 지역 | **미국 서부** 지역을 선택합니다. |
    | Virtual Network | *myVirtualNetwork* 가상 네트워크를 선택합니다. |
    | 서브넷 | 앞에서 만든 *RouteServerSubnet(10.1.0.0/25)* 을 선택합니다. |
    | 공용 IP 주소 | Route Server에서 사용할 기존 표준 공용 IP 주소를 선택하거나 새로 만듭니다. 이 IP 주소는 Route Server 구성을 관리하는 백 엔드 서비스에 대한 연결을 보장합니다. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="Route Server 만들기의 [기본 사항] 탭 스크린샷":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과한 후 **만들기** 를 선택합니다. Route Server가 배포될 때까지 약 15분이 걸립니다.

## <a name="create-quagga-network-virtual-appliance"></a>Quagga 네트워크 가상 어플라이언스 만들기

Quagga 네트워크 가상 어플라이언스를 구성하려면 Linux 가상 머신을 배포해야 합니다.

1. Azure Portal에서 **+ 리소스 만들기 > 컴퓨팅 > 가상 머신** 을 차례로 선택합니다. 그런 다음 **생성** 를 선택합니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="새 가상 머신 만들기 페이지의 스크린샷":::

1. ‘기본 사항’ 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | -------- | ----- |
    | 구독 | 앞에서 배포한 가상 네트워크와 동일한 구독을 선택합니다. |
    | Resource group | *myRouteServerRG* 라는 기존 리소스 그룹을 선택합니다. |
    | 가상 머신 이름 | 이름으로 **Quagga** 를 입력합니다. |
    | 지역 | **미국 서부** 지역을 선택합니다. |
    | 이미지 | **Ubuntu 18.04 LTS - Gen 1** 을 선택합니다. |
    | 크기 | **Standard_B2s - 2vcpus, 4GiB 메모리** 를 선택합니다. |
    | 인증 유형 | **암호** 선택 |
    | 사용자 이름 | *azureuser* 를 입력합니다. *quagga* 를 사용자 이름으로 사용하면 이후 단계에서 설치 스크립트가 실패하므로 사용하지 마세요. |
    | 암호 | 원하는 암호를 입력하고 확인합니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **SSH(22)** 를 선택합니다. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="새 가상 머신을 만드는 [기본 사항] 탭의 스크린샷" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. **네트워킹** 탭에서 다음 네트워크 설정을 선택합니다.

    | 설정 | 값 |
    | -------- | ----- |
    | Virtual Network | **myVirtualNetwork** 를 선택합니다. |
    | 서브넷 | **subnet3(10.1.4.0/24)** 을 선택합니다. |
    | 공용 IP | 기본값으로 둡니다. |
    | NIC 네트워크 보안 그룹 추가 | 기본값으로 둡니다. **기본**. |
    | 공용 인바운드 포트 | 기본값으로 둡니다. **선택한 포트 허용** |
    | 인바운드 포트 선택 | 기본값으로 둡니다. **SSH(22)** |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="새 가상 머신을 만드는 [네트워킹] 탭의 스크린샷" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과한 후 **만들기** 를 선택합니다. VM을 배포하는 데 10분이 걸립니다.

1. VM이 배포된 후에는 Quagga VM의 네트워킹 설정으로 이동하여 네트워크 인터페이스를 선택합니다.

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="Quagga VM의 네트워킹 페이지 스크린샷":::

1. *설정* 에서 **IP 구성** 을 선택한 다음, **ipconfig1** 을 선택합니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="Quagga VM의 IP 구성 페이지 스크린샷":::

1. 할당을 *동적* 에서 **정적** 으로 변경한 다음, IP 주소를 *10.1.4.4* 에서 **10.1.4.10** 으로 변경합니다. 이 IP는 이후 단계에서 실행되는 이 [스크립트](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)에서 사용됩니다. 다른 IP 주소를 사용하려면 스크립트에서 IP를 업데이트해야 합니다. **저장** 을 선택하여 VM의 IP 구성을 업데이트합니다.

1. [Putty](https://www.putty.org/)를 사용하여 VM을 만드는 데 사용된 공용 IP 주소 및 자격 증명으로 Quagga VM에 연결합니다.

1. 로그인한 후에는 스크립트를 실행하는 동안 오류가 발생하지 않도록 `sudo su`를 입력하여 슈퍼 사용자로 전환합니다. 이 [스크립트](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)를 복사하여 Putty 세션에 붙여넣습니다. 이 스크립트는 다른 네트워크 설정과 함께 Quagga를 사용하여 가상 머신을 구성합니다. 가상 머신에서 실행하기 전에 네트워크 환경에 맞게 스크립트를 업데이트합니다. 스크립트가 설치를 완료하는 데 몇 분 정도 걸립니다.

## <a name="configure-route-server-peering"></a>Route Server 피어링 구성

1. 이전 단계에서 만든 Route Server로 이동합니다.

1. *설정* 에서 **피어** 를 선택합니다. 그런 다음, **+ 추가** 를 선택하여 새 피어를 추가합니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="Route Server에 대한 피어 페이지의 스크린샷":::

1. *피어 추가* 페이지에서 다음 정보를 입력한 다음, **추가** 를 선택하여 구성을 저장합니다.

    | Setting | 값 |
    | ------- | ----- |
    | Name | 이 피어를 식별하는 이름을 입력합니다. **Quagga**. |
    | ASN | Quagga NVA의 ASN 번호를 입력합니다. **65001** 은 스크립트에 정의된 ASN입니다. |
    | IPv4 주소 | Quagga NVA 가상 머신의 개인 IP를 입력합니다. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="피어 추가 페이지의 스크린샷":::

1. 피어를 추가하면 피어 페이지가 다음과 같이 표시됩니다.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="구성된 피어의 스크린샷":::
    
## <a name="check-learned-routes"></a>학습된 경로 확인
1. Route Server가 학습한 경로를 확인하려면 다음 명령을 사용합니다.

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    출력은 다음과 비슷합니다. 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="Route Server가 학습한 경로의 스크린샷":::

1. Quagga NVA가 학습한 경로를 확인하려면 `vtysh`를 입력한 다음, `show ip bgp`를 입력합니다. 출력은 다음과 비슷합니다.

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>리소스 정리

Route Server 및 모든 관련 리소스가 더 이상 필요 없으면 **myRouteServerRG** 리소스 그룹을 삭제해도 됩니다.

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="리소스 그룹 삭제 단추 스크린샷":::

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 Route Server 문제 해결 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Route Server 문제 해결](troubleshoot-route-server.md)
