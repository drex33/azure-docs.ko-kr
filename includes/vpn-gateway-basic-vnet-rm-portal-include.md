---
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/23/2021
ms.openlocfilehash: 195a9665238a2deddd351c194a037271e0cbe997
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133034893"
---
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스, 서비스, 문서(G+/)** 에 **_가상 네트워크_ *_를 입력합니다. **Marketplace** 결과에서 _* 가상 네트워크** 를 선택하여 **가상 네트워크** 페이지를 엽니다.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png" alt-text="스크린샷은 Azure Portal 검색 창 결과와 Marketplace에서 Virtual Network를 선택하는 것을 보여줍니다." :::
1. **가상 네트워크** 페이지에서 **만들기** 를 클릭합니다. 그러면 **가상 네트워크 만들기** 페이지가 열립니다.
1. **기본** 탭에서 **프로젝트 세부 정보** 및 **인스턴스 세부 정보** 에 대한 VNet 설정을 구성합니다. 입력한 값의 유효성이 검사되면 녹색 확인 표시가 나타납니다. 예제에 표시된 값은 필요한 설정에 따라 조정할 수 있습니다.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png" alt-text="스크린샷은 기본 사항 탭을 보여줍니다." :::

   - **구독**: 나열된 구독이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 클릭하여 새로 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/management/overview.md#resource-groups)를 참조하세요.
   - **Name**: 가상 네트워크에 대한 이름을 입력합니다.
   - **지역**: VNet에 대한 위치를 선택합니다. 이 위치는 이 VNet에 배포하는 리소스가 상주하는 위치를 결정합니다.
1. **IP 주소** 를 클릭하여 IP 주소 탭으로 이동합니다. IP 주소 탭에서 설정을 구성합니다. 예제에 표시된 값은 필요한 설정에 따라 조정할 수 있습니다.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png" alt-text="스크린샷은 IP 주소 탭을 보여줍니다." :::

   - **IPv4 주소 공간**: 기본적으로 주소 공간은 자동으로 만들어집니다. 주소 공간을 클릭하여 자신의 값을 반영하도록 조정할 수 있습니다. 더 많은 주소 공간을 추가할 수도 있습니다.
   - **서브넷**: 기본 주소 공간을 사용하는 경우 기본 서브넷이 자동으로 만들어집니다. 주소 공간을 변경하는 경우 서브넷을 추가해야 합니다. **+ 서브넷 추가** 를 선택하면 **서브넷 추가** 창이 열립니다. 다음 설정을 구성한 다음, **추가** 를 선택하여 값을 추가합니다.
      - **서브넷 이름**: 이 예에서는 서브넷 이름을 "FrontEnd"로 지정했습니다.
      - **서브넷 주소 범위**: 이 서브넷의 주소 범위입니다.

1. **보안** 을 클릭하여 보안 탭으로 이동합니다. 이때 기본값을 그대로 둡니다.

   - **BastionHost**: 사용 안 함
   - **DDoS Protection 표준**: 사용 안 함
   - **방화벽**: 사용 안 함
1. **검토 + 만들기** 를 선택하여 가상 네트워크 설정의 유효성을 검사합니다.
1. 설정의 유효성을 검사한 후 **만들기** 를 클릭하여 가상 네트워크를 만듭니다.
