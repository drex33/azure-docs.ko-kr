---
title: '빠른 시작: Azure Portal을 사용하여 Route Server 만들기 및 구성'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Route Server를 만들고 구성하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: 7dfded652c14ec6805b7fa48004cf5dfe8d72b5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061108"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Route Server 만들기 및 구성

이 문서는 Azure Portal을 사용하여 가상 네트워크에서 NVA(네트워크 가상 어플라이언스)와 피어링하도록 Azure Route Server를 구성하는 데 도움이 됩니다. Azure Route Server는 NVA에서 경로를 학습하고 가상 네트워크의 가상 머신에 대해 프로그래밍합니다. 또한 Azure Route Server는 NVA에 가상 네트워크 경로를 보급합니다. 자세한 내용은 [Azure Route Server](overview.md)를 읽어보세요.

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure Portal을 사용하는 Route Server 배포 환경의 다이어그램." border="false":::

> [!IMPORTANT]
> 9월 1일 이전에 만든 Azure Route Server가 있고 공용 IP 주소가 분리되지 않은 경우 관리 목적으로 IP 주소를 가져올 수 있도록 Route Server를 다시 만들어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure Route Server에 대한 서비스 제한](route-server-faq.md#limitations)을 검토합니다.

## <a name="create-a-route-server"></a>Route Server 만들기

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

### <a name="create-a-route-server"></a>Route Server 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하여 **Route Server** 를 검색 및 선택합니다.

1. **+ 새 Route Server 만들기** 를 선택합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Route Server 방문 페이지의 스크린샷"::: 

1. **Route Server 만들기** 페이지에서 필요한 정보를 입력하거나 선택합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Route Server 만들기 페이지의 스크린샷":::     

    | 설정 | 값 |
    |----------|-------|
    | 구독 | Route Server를 배포하는 데 사용하려는 Azure 구독을 선택합니다. |
    | 리소스 그룹 | Route Server가 만들어질 리소스 그룹을 선택합니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다. |
    | Name | Route Server의 이름을 입력합니다. |
    | 지역 | Route Server가 만들어질 지역을 선택합니다. 이전에 만든 가상 네트워크와 동일한 지역을 선택하여 드롭다운에서 가상 네트워크를 확인합니다. |
    | Virtual Network | Route Server가 만들어질 가상 네트워크를 선택합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 사용할 수 있습니다. 기존 가상 네트워크를 사용하는 경우 Route Server 서브넷 요구 사항을 수용할 수 있는 최소 /27 서브넷에 충분한 공간이 기존 가상 네트워크에 있는지 확인합니다. 드롭다운에서 가상 네트워크가 표시되지 않으면 올바른 리소스 그룹 또는 지역을 선택했는지 확인합니다. |
    | 서브넷 | 가상 네트워크를 만들거나 선택하면 서브넷 필드가 표시됩니다. 이 서브넷은 Route Server 전용입니다. **서브넷 구성 관리** 를 선택하고, Azure Route Server 서브넷을 만듭니다. **+ 서브넷** 을 선택하고, 다음 지침에 따라 서브넷을 만듭니다.</br><br>- 서브넷 이름은 *RouteServerSubnet* 이어야 합니다.</br><br>- 서브넷은 최소 /27 이상이어야 합니다.</br> |
    | 공용 IP 주소 | Route Server에 할당할 새 리소스를 만들거나 기존 표준 공용 IP 리소스를 선택합니다. Route Server 구성을 관리하는 백 엔드 서비스에 대한 연결을 보장하려면 공용 IP 주소가 필요합니다. |

1. **검토 + 만들기** 를 선택하고, 요약을 검토한 다음, **만들기** 를 선택합니다. 

    > [!NOTE]
    > Route Server를 배포하는 데 약 20분이 걸립니다.

## <a name="set-up-peering-with-nva"></a>NVA와의 피어링 설정

이 섹션은 NVA와 BGP의 피어링을 구성하는 데 도움이 됩니다.

1. Azure Portal에서 [Route Server](./overview.md)로 이동한 다음, 구성하려는 Route Server를 선택합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Route Server 목록의 스크린샷"::: 

1. 왼쪽 탐색 패널의 *설정* 아래에서 **피어** 를 선택합니다. 그런 다음, **+ 추가** 를 선택하여 새 피어를 추가합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="피어 방문 페이지의 스크린샷"::: 

1. NVA 피어에 대한 다음 정보를 입력합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="피어 추가 페이지의 스크린샷":::

    | 설정 | 값 |
    |----------|-------|
    | Name | Route Server와 NVA 간의 피어링에 대한 이름을 지정합니다. |
    | ASN |  NVA의 ASN(자치 시스템 번호)을 입력합니다. |
    | IPv4 주소 | Route Server에서 BGP를 설정하기 위해 통신하는 NVA의 IP 주소를 입력합니다. |

1. **추가** 를 선택하여 이 피어를 추가합니다.

## <a name="complete-the-configuration-on-the-nva"></a>NVA에 대한 구성 완료

NVA에 대한 구성을 완료하여 BGP 세션을 설정하려면 Azure Route Server의 피어 IP 및 ASN이 필요합니다. 이 정보는 Route Server 개요 페이지에서 가져올 수 있습니다.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Route Server 개요 페이지의 스크린샷":::

## <a name="configure-route-exchange"></a>경로 교환 구성

ExpressRoute 게이트웨이 및/또는 VPN 게이트웨이가 있고 경로를 Route Server와 교환하도록 하려면 경로 교환을 사용하도록 설정할 수 있습니다.

1. Azure Portal에서 [Route Server](./overview.md)로 이동한 다음, 구성하려는 Route Server를 선택합니다.

1. 왼쪽 탐색 패널의 *설정* 아래에서 **구성** 을 선택합니다.

1. **분기 간** 설정에 대해 **사용** 을 선택한 다음, **저장** 을 선택합니다.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="경로 교환을 사용하도록 설정하는 방법의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

Azure Route Server가 더 이상 필요하지 않은 경우 개요 페이지에서 **삭제** 를 선택하여 Route Server의 프로비저닝을 해제합니다.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Route Server를 삭제하는 방법의 스크린샷":::

## <a name="next-steps"></a>다음 단계

Azure Route Server를 만든 후에 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법에 대해 계속 알아보세요. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)