---
title: 가상 허브(미리 보기)를 사용하여 BGP 피어링 만들기 - Azure Portal
titleSuffix: Azure Virtual WAN
description: Virtual WAN 허브 라우터를 사용하여 BGP 피어링을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567296"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>가상 허브(미리 보기)를 사용하여 BGP 피어링을 만드는 방법 - Azure Portal

이 문서는 Azure Portal을 사용하여 가상 네트워크에서 NVA(네트워크 가상 어플라이언스)와 피어링하도록 Azure Virtual WAN 허브 라우터를 구성하는 데 도움이 됩니다. 가상 허브 라우터는 가상 WAN 허브에 연결된 스포크 VNet에서 NVA의 경로를 알아냅니다. 또한 가상 허브 라우터는 NVA에 가상 네트워크 경로를 보급합니다. 자세한 내용은 [시나리오: 가상 허브를 사용하여 BGP 피어링](scenario-bgp-peering-hub.md)을 참조하세요.

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="구성의 다이어그램":::

## <a name="prerequisites"></a>필수 구성 요소

> [!IMPORTANT]
> Virtual WAN 허브 기능을 사용하는 BGP 피어링은 현재 제어된 공개 미리 보기 상태입니다. 이 기능을 사용하려는 경우 Virtual WAN 리소스의 리소스 ID와 함께 이메일( **previewbgpwithvhub@microsoft.com** )을 보내세요. 
>
> 리소스 ID를 찾으려면 Azure Portal을 열고 Virtual WAN 리소스로 이동하고, **설정 > 속성 > 리소스 ID** 를 클릭합니다.<br> 예: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>허브 만들기

허브는 사이트 간, Express 경로 또는 지점 및 사이트 간 기능을 위한 게이트웨이를 포함할 수 있는 가상 네트워크입니다. 허브가 생성되면 사이트를 연결하지 않아도 허브 요금이 청구됩니다.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>허브에 VNet 연결

이 섹션에서는 허브와 VNet 간의 연결을 만듭니다.

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>BGP 피어 구성

1.  [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2)를 사용하여 [Azure 미리 보기 포털](https://aka.ms/azurecortexv2)을 엽니다. Virtual WAN 허브 기능을 사용하는 BGP 피어링은 현재 관리형 미리 보기 상태이며 일반 Azure Portal에서 구성 페이지를 사용할 수 없습니다.

1.  가상 WAN에 대한 포털 페이지의 **연결** 섹션에서 **허브** 를 선택하여 허브 목록을 봅니다. 허브를 클릭하여 BGP 피어를 구성합니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="허브의 스크린샷":::

1.  **가상 허브** 페이지의 **라우팅** 섹션에서 **BGP 피어** 를 선택하고 **+ 추가** 를 클릭하여 BGP 피어를 추가합니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  **BGP 피어 추가** 페이지에서 모든 필드를 완료합니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **이름** – 특정 BGP 피어를 식별하는 리소스 이름 
    * **ASN** - BGP 피어에 대한 ASN
    * **IPv4 주소** – BGP 피어의 IPv4 주소
    * **Virtual Network 연결** – BGP 피어를 호스트하는 가상 네트워크에 해당하는 연결 식별자를 선택합니다.

1.  **추가** 를 클릭하여 BGP 피어 구성을 완료하고 피어를 봅니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="추가된 피어의 스크린샷":::

## <a name="modify-a-bgp-peer"></a>BGP 피어 수정

1. **가상 허브** 리소스에서 **BGP 피어** 를 클릭하고 BGP 피어를 선택합니다. **...** 을 클릭합니다. 그런 다음, **편집** 을 클릭합니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="편집의 스크린샷":::

1. BGP 피어가 수정되면 **추가** 를 클릭하여 저장합니다.

## <a name="delete-a-bgp-peer"></a>BGP 피어 삭제

1. **가상 허브** 리소스에서 **BGP 피어** 를 클릭하고 BGP 피어를 선택합니다. **...** 을 클릭합니다. 그런 다음, **삭제** 를 클릭합니다.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="피어 삭제의 스크린샷":::

## <a name="next-steps"></a>다음 단계

* BGP 시나리오에 대한 자세한 내용은 [시나리오: 가상 허브를 사용하여 BGP 피어링](scenario-bgp-peering-hub.md)을 참조하세요.
