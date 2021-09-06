---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: a06b4be25c5c342631c26bb5f1d3270a8851e71b
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122516339"
---
1. **Virtual WAN** 으로 이동합니다.

1. **가상 네트워크 연결** 을 선택합니다.

1. 가상 네트워크 연결 페이지에서 **+연결 추가** 를 선택합니다.

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="스크린샷은 추가를 보여줍니다.":::

1. **연결 추가** 페이지에서 필요한 설정을 구성합니다.
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="스크린샷은 VNet 연결 페이지를 보여줍니다.":::

   * **연결 이름**: 연결 이름을 지정합니다.
   * **Hubs** - 이 연결과 연결할 허브를 선택합니다.
   * **구독**: 구독을 확인합니다.
   * **리소스 그룹**: VNet을 포함하는 리소스 그룹.
   * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 선택한 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
   * **없음으로 전파**: 기본적으로 **아니요** 로 설정됩니다. 스위치를 **예** 로 변경하면 **경로 테이블로 전파** 및 **레이블로 전파** 에 대한 구성 옵션을 구성에 사용할 수 없게 됩니다.
   * **경로 테이블 연결**: 연결할 경로 테이블을 선택할 수 있습니다.
   * **고정 경로**: 이 설정을 사용하여 다음 홉을 지정할 수 있습니다.

1. 구성할 설정을 완료했으면 **만들기** 를 선택하여 연결을 만듭니다.