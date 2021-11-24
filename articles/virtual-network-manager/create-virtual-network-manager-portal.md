---
title: '빠른 시작: Azure Portal을 사용하여 Azure Virtual Network Manager에서 메시 네트워크 토폴로지 만들기'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Virtual Network Manager에서 메시 네트워크 토폴로지를 만드는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021, mode-portal
ms.openlocfilehash: 13164ec568b93613390ed435d25be79c66e342a8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049560"
---
# <a name="quickstart-create-a-mesh-network-topology-with-azure-virtual-network-manager-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Virtual Network Manager에서 메시 네트워크 토폴로지 만들기

모든 가상 네트워크 연결을 관리하기 위해 Azure Portal을 사용하여 Azure Virtual Network Manager를 시작합니다.

이 빠른 시작에서는 세 개의 가상 네트워크를 배포하고 Azure Virtual Network Manager를 사용하여 메시 네트워크 토폴로지를 만듭니다. 그런 다음 연결 구성이 적용되었는지 확인합니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="register-subscription-for-public-preview"></a>공개 미리 보기에 대한 구독 등록

1. [미리 보기 기능](https://portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade) 페이지로 이동합니다.

1. **AllowAzureNetworkManager** 를 검색합니다.

1. *AllowAzureNetworkManager* 옆에 있는 확인란을 선택한 다음, **+ 등록** 을 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/registration.png" alt-text="미리 보기 기능 등록 페이지의 스크린샷.":::

## <a name="create-virtual-network-manager"></a>Virtual Network Manager 만들기

1. **+ 리소스 만들기** 를 선택하고 **Network Manager** 를 검색합니다. 그런 다음, **만들기** 를 선택하여 Azure Virtual Network Manager 설정을 시작합니다.

1. ‘기본 사항’ 탭에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-basics.png" alt-text="네트워크 관리자 만들기 기본 사항 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | Azure Virtual Network Manager를 배포하려는 구독을 선택합니다. |
    | Resource group | Azure Virtual Network Manager를 저장할 리소스 그룹을 선택하거나 만듭니다. 이 예제에서는 이전에 만든 **myAVNMResourceGroup** 을 사용합니다.
    | 이름 | 이 Azure Virtual Network Manager 인스턴스의 이름을 입력합니다. 이 예제에서는 **myAVNM** 이라는 이름을 사용합니다. |
    | 지역 | 이 배포의 지역을 선택합니다. Azure Virtual Network Manager는 모든 지역의 가상 네트워크를 관리할 수 있습니다. 선택한 지역은 Virtual Network Manager 인스턴스를 배포할 위치입니다. |
    | 설명 | (선택 사항) 이 Virtual Network Manager 인스턴스 및 관리 대상 작업에 대한 설명을 제공합니다. |
    | [범위](concept-network-manager-scope.md#scope) | Azure Virtual Network Manager에서 관리할 수 있는 범위를 정의합니다.
    | [기능](concept-network-manager-scope.md#features) | Azure Virtual Network Manager에서 사용하도록 설정할 기능을 선택합니다. 사용 가능한 기능은 연결, SecurityAdmin 또는 모두 선택입니다. </br> 연결 - 범위 내의 가상 네트워크 간에 전체 메시 또는 허브 및 스포크 네트워크 토폴로지 만들기 기능을 사용하도록 설정합니다. </br> SecurityAdmin - 글로벌 네트워크 보안 규칙을 만드는 기능을 사용하도록 설정합니다. |

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-validation.png" alt-text="네트워크 관리자 리소스 만들기에 대한 유효성 검사 페이지의 스크린샷.":::

## <a name="create-three-virtual-networks"></a>가상 네트워크 3개 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **+ 리소스 만들기** 를 선택하고 **가상 네트워크** 를 검색합니다. 그런 다음 **만들기** 를 선택하여 가상 네트워크 구성을 시작합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-vnet.png" alt-text="가상 네트워크 만들기 페이지의 스크린샷.":::

1. 기본 사항 탭에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-basic.png" alt-text="가상 네트워크 만들기 기본 사항 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 이 가상 네트워크를 배포할 구독을 선택합니다. |
    | Resource group | 가상 네트워크를 저장할 새 리소스 그룹을 선택하거나 만듭니다. 이 빠른 시작에서는 **myAVNMResourceGroup** 이라는 새 리소스 그룹을 사용합니다.
    | 이름 | 가상 네트워크 이름으로 **VNetA** 를 입력합니다. |
    | 지역 | **미국 서부** 를 선택합니다. |

1. **다음: IP 주소 >** 를 선택하고 다음 네트워크 주소 공간을 구성합니다.

     :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-ip.png" alt-text="가상 네트워크 만들기 IP 주소 페이지의 스크린샷.":::

    | 설정 | 값 |
    | -------- | ----- |
    | IPv4 주소 공간 | 10.0.0.0/16 |
    | 서브넷 이름 | 기본값 |
    | 서브넷 주소 공간 | 10.0.0.0/24 |

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택하여 가상 네트워크를 배포합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-validation.png" alt-text="가상 네트워크 만들기에 대한 유효성 검사 페이지의 스크린샷.":::

1. 2~5단계를 반복하여 다음 정보를 사용해 두 개의 가상 네트워크를 더 만듭니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 3단계에서 선택한 것과 동일한 구독을 선택합니다. |
    | Resource group | **myAVNMResourceGroup** 을 선택합니다. |
    | 이름 | 두 번째 가상 네트워크에는 **VNetB** 를 입력하고 세 번째 가상 네트워크에는 **VNetC** 를 입력합니다. |
    | 지역 | 리소스 그룹을 선택하면 지역이 선택됩니다. |
    | VNetB IP 주소 | IPv4 주소 공간: 10.1.0.0/16 </br> 서브넷 이름: 기본값 </br> 서브넷 주소 공간: 10.1.0.0/24|
    | VNetC IP 주소 | IPv4 주소 공간: 10.2.0.0/16 </br> 서브넷 이름: 기본값 </br> 서브넷 주소 공간: 10.2.0.0/24|

## <a name="create-a-network-group"></a>네트워크 그룹 만들기

1. 만든 Azure Virtual Network Manager 인스턴스로 이동합니다.

1. 설정 아래에서 **네트워크 그룹** 을 선택한 다음 **+ 추가** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group.png" alt-text="네트워크 그룹 추가 단추의 스크린샷.":::

1. 네트워크 그룹 추가 페이지에서 네트워크 그룹의 **이름** 을 입력합니다. 이 예제에서는 **myNetworkGroup** 이라는 이름을 사용합니다. **다음: 정적 그룹 멤버 >** 를 선택하여 네트워크 그룹에 가상 네트워크를 추가하기 시작합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-basics.png" alt-text="네트워크 그룹 만들기 기본 사항 탭의 스크린샷.":::

1. 정적 그룹 멤버 탭에서 **+ 가상 네트워크 추가** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks-button.png" alt-text="가상 네트워크 추가 단추의 스크린샷.":::

1. 가상 네트워크 추가 페이지에서 이전에 만든 세 개의 가상 네트워크(VNetA, VNetB, VNetC)를 모두 선택합니다. 그런 다음 **추가** 를 선택하여 선택을 커밋합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks.png" alt-text="네트워크 그룹에 가상 네트워크 추가 페이지의 스크린샷.":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과한 후 **만들기** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/review-create.png" alt-text="새 네트워크 그룹에 대한 검토 및 만들기 단추의 스크린샷.":::

1. 새 네트워크 그룹이 네트워크 그룹 페이지에 추가된 것을 볼 수 있습니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-groups-list.png" alt-text="새 네트워크 그룹이 추가된 네트워크 그룹 페이지의 스크린샷.":::

## <a name="create--a-connectivity-configuration"></a>연결 구성 생성

1. 설정 아래에서 **구성** 을 선택하고 **+ 구성 추가** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-configuration.png" alt-text="네트워크 관리자에 대한 구성 추가 단추의 스크린샷.":::

1. 드롭다운 메뉴에서 **연결** 을 선택하여 연결 구성 만들기를 시작합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="구성 드롭다운 메뉴의 스크린샷.":::

1. 연결 구성 추가 페이지에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration.png" alt-text="연결 구성 추가 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | Name | 이 연결 구성의 이름을 입력합니다. |
    | 설명 | (선택 사항) 이 연결 구성에 대한 설명을 제공합니다. |
    | 토폴로지 | 이 구성을 사용하여 만들려는 토폴로지 유형을 선택합니다. 이 예제에서는 **메시** 토폴로지를 사용합니다. |

1. 메시 토폴로지를 선택하면 **글로벌 메시** 및 **네트워크 그룹** 옵션이 표시됩니다. 모든 가상 네트워크는 동일한 지역에 있기 때문에 이 설정에 글로벌 메시가 필요하지 않습니다. **+ 네트워크 그룹 추가** 를 선택하고 직전 섹션에서 만든 네트워크 그룹을 선택합니다. **선택** 을 클릭하여 네트워크 그룹을 구성에 추가합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group-configuration.png" alt-text="연결 구성에 네트워크 그룹 추가 스크린샷.":::

1. **추가** 를 선택하여 구성을 만듭니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-connectivity-configuration.png" alt-text="연결 구성 만들기 스크린샷.":::

1. 구성 페이지에 새 연결 구성이 추가된 것을 볼 수 있습니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="연결 구성 목록의 스크린샷.":::

## <a name="deploy-the-connectivity-configuration"></a>연결 구성 배포

사용자 환경에 구성을 적용하려면 배포를 통해 구성을 커밋해야 합니다. 가상 네트워크가 배포된 **미국 서부** 지역에 구성을 배포해야 합니다.

1. 설정 아래에서 **배포** 를 선택한 다음 **구성 배포** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="네트워크 관리자의 배포 페이지의 스크린샷.":::

1. 다음 설정을 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deploy-configuration.png" alt-text="구성 배포 페이지의 스크린샷.":::

    | 설정 | 값 |
    | ------- | ----- |
    | 구성 형식 | 배포하려는 구성의 유형을 선택합니다. 이 예제에서는 **연결** 구성을 배포합니다. |
    | 구성 | 이전 섹션에서 만든 **myConnectivityConfig** 구성을 선택합니다. |
    | 대상 지역 | 이 구성을 배포할 지역을 선택합니다. 모든 가상 네트워크가 만들어진 지역인 **미국 서부** 지역을 선택합니다. |

1. **배포** 를 선택한 다음 **확인** 을 선택하여 기존 구성 덮어쓰기를 확인합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="배포 확인 메시지의 스크린샷.":::

1. 이제 선택한 지역에 대한 목록에 배포가 표시됩니다. 구성 배포가 완료되는 데 약 15~20분이 걸릴 수 있습니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="구성 배포 진행 중 상태의 스크린샷.":::

## <a name="confirm-configuration-deployment"></a>구성 배포 확인

1. 배포 페이지에서 **새로 고침** 을 선택하여 커밋한 구성의 업데이트된 상태를 확인합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-status.png" alt-text="배포 상태 업데이트를 위한 새로 고침 단추의 스크린샷.":::

1. **VNetA** 가상 네트워크로 이동하여 설정에서 **네트워크 관리자** 를 선택합니다. 가상 네트워크와 연결된 Azure Virtual Network Manager를 사용하여 배포한 구성이 표시됩니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="VNetA 가상 네트워크와 연결된 연결 구성의 스크린샷.":::

1. **VNetB** 및 **VNetC** 도 동일하게 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure Virtual Network Manager가 더 이상 필요하지 않은 경우 리소스를 삭제하기 전에 다음 사항을 모두 충족해야 합니다.

* 어떤 지역에도 배포된 구성이 없습니다.
* 모든 구성이 삭제되었습니다.
* 모든 네트워크 그룹이 삭제되었습니다.

1. 지역에서 모든 구성을 제거하려면 대상 지역에 **없음** 구성을 배포합니다. **배포** 를 선택하고 **확인** 을 선택하여 확인합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="없음 연결 구성 배포 스크린샷.":::

1. 구성을 삭제하려면 Azure Virtual Network Manager의 왼쪽 창에서 설정 아래의 **구성** 을 선택합니다. 제거하려는 구성 옆의 확인란을 선택하고 리소스 페이지 맨 위에서 **삭제** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-configuration.png" alt-text="연결 구성에 대한 삭제 단추의 스크린샷.":::

1. 네트워크 그룹을 삭제하려면 Azure Virtual Network Manager의 왼쪽 창에서 설정 아래의 **네트워크 그룹** 을 선택합니다. 제거하려는 네트워크 그룹 옆의 확인란을 선택하고 리소스 페이지 맨 위에서 **삭제** 를 선택합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-group.png" alt-text="네트워크 그룹에 대한 삭제 단추의 스크린샷.":::

1. 모든 네트워크 그룹이 제거되면 이제 목록에서 Azure Virtual Network Manager를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택하여 리소스를 삭제할 수 있습니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-manager.png" alt-text="Azure Virtual Network Manager에 대한 삭제 단추의 스크린샷.":::

1. 리소스 그룹을 삭제하려면 리소스 그룹을 찾아 **리소스 그룹 삭제** 를 선택합니다. 리소스 그룹의 이름을 입력하고 **삭제** 를 선택하여 삭제를 확인합니다.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-resource-group.png" alt-text="리소스 그룹에 대한 삭제 단추의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

Azure Virtual Network Manager를 만들었다면 이제 보안 관리 구성을 사용하여 네트워크 트래픽을 차단하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [보안 관리 규칙을 사용하여 네트워크 트래픽 차단](how-to-block-network-traffic-portal.md)
