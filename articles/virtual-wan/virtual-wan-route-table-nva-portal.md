---
title: 'Virtual WAN: NVA에 대한 가상 허브 경로 테이블 만들기: Azure portal'
description: 포털을 사용하여 네트워크 가상 어플라이언스에 대한 트래픽을 조정하기 위한 Virtual WAN 가상 허브 경로 테이블.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: e57f419daeb112be0925158109697bb2a9b399e8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779982"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>NVA에 대한 Virtual WAN 허브 경로 테이블 만들기: Azure portal

이 문서에서는 NVA(네트워크 가상 어플라이언스)를 통해 가상 WAN 허브에 연결된 분기(온-프레미스 사이트)에서 스포크 VNet(가상 네트워크)으로 트래픽을 조정하는 방법을 보여 줍니다.

![Virtual WAN 다이어그램](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>시작하기 전에

다음 기준을 충족하는지 확인합니다.

*  NVA(네트워크 가상 어플라이언스)가 있습니다. 네트워크 가상 어플라이언스는 일반적으로 가상 네트워크의 Azure Marketplace에서 프로비저닝되는 사용자가 선택한 타사 소프트웨어입니다.

    * 개인 IP 주소는 NVA 네트워크 인터페이스에 할당되어야 합니다.

    * NVA는 가상 허브에 배포되지 않습니다. 별도의 가상 네트워크에 배포해야 합니다.

    *  NVA 가상 네트워크에는 하나 이상의 가상 네트워크가 연결되어 있을 수 있습니다. 이 문서에서는 NVA 가상 네트워크를 '간접 스포크 VNet'이라고 합니다. 이러한 가상 네트워크는 VNet 피어링을 사용하여 NVA VNet에 연결할 수 있습니다. VNet 피어링 링크는 위의 그림에서 VNet 1, VNet 2 및 NVA VNet 사이의 검은색 화살표로 표시됩니다.
*  두 개의 가상 네트워크를 만들었습니다. 스포크 VNet으로 사용됩니다.

    * VNet 스포크 주소 공간은 다음과 같습니다. VNet1: 10.0.2.0/24 및 VNet2: 10.0.3.0/24 가상 네트워크를 만드는 방법에 관한 정보가 필요하면 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조하세요.

    * VNet에 가상 네트워크 게이트웨이가 없는지 확인합니다.

    * VNet에는 게이트웨이 서브넷이 필요하지 않습니다.

## <a name="1-sign-in"></a><a name="signin"></a>1. 로그인

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. 가상 WAN 만들기

가상 WAN 만들기 다음 예제 값을 사용하거나 원하는 값으로 바꿀 수 있습니다.

* **Virtual WAN 이름:** myVirtualWAN
* **리소스 그룹:** testRG
* **위치:** 미국 서부

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. 허브 만들기

허브를 만듭니다. 다음 예제 값을 사용하거나 원하는 값으로 바꿀 수 있습니다.

* **위치:** 미국 서부
* **이름:** westushub
* **허브 프라이빗 주소 공간:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. 허브 경로 테이블 만들기 및 적용

허브 경로 테이블로 허브를 업데이트합니다. 다음 예제 값을 사용합니다.

* **스포크 VNet 주소 공간:** (VNet1 및 VNet2) 10.0.2.0/24 및 10.0.3.0/24
* **DMZ NVA 네트워크 인터페이스 개인 IP 주소:** 10.0.4.5

1. 가상 WAN으로 이동합니다.
2. 경로 테이블을 만들려는 허브를 클릭합니다.
3. **...** 를 클릭한 다음, **가상 허브 편집** 을 클릭합니다.
4. **가상 허브 편집** 페이지에서 아래로 스크롤하여 **라우팅에 표 사용** 확인란을 선택합니다.
5. **대상 접두사가 다음인 경우** 열에서 주소 공간을 추가합니다. **다음 홉으로 보내기** 열에서 DMZ NVA 네트워크 인터페이스 개인 IP 주소를 추가합니다.

   > [!NOTE]
   > DMZ NVA 네트워크는 로컬 허브에 적용됩니다.
   
6. **확인** 을 클릭하여 허브 리소스를 경로 테이블 설정으로 업데이트합니다.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. VNet 연결 만들기

각 간접 스포크 VNet(VNet1 및 VNet2)에서 허브로의 가상 네트워크 연결을 만듭니다. 이러한 가상 네트워크 연결은 위의 그림에서 파란색 화살표로 표시됩니다. 그런 다음, NVA VNet에서 허브로 VNet 연결을 만듭니다(그림의 검은색 화살표).

 이 단계에서는 다음과 같은 값을 사용할 수 있습니다.

| 가상 네트워크 이름| 연결 이름|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

연결하려는 각 가상 네트워크에 대해 다음 절차를 반복합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결** 을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가** 를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인** 을 클릭하여 연결을 만듭니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
