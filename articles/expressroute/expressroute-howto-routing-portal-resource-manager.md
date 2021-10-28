---
title: '자습서: ExpressRoute 회로에 대한 피어링 구성 - Azure Portal'
description: 이 자습서는 Azure Portal을 사용하여 ExpressRoute 개인 및 Microsoft 피어링을 만들고 프로비저닝하는 방법을 보여줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: cc7eba43ad9706fd76526f99a9d041053b735179
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246797"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 ExpressRoute 회로의 피어링 만들기 및 수정

이 자습서에서는 Azure Portal을 사용하여 Azure Resource Manager ExpressRoute 회로에 대한 라우팅 구성을 만들고 관리하는 방법을 보여줍니다. ExpressRoute 회로에 대한 피어링의 상태를 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행할 수도 있습니다. 회로를 사용하는 다른 메서드를 사용하려는 경우 다음 목록에서 문서를 선택합니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [공용 피어링](about-public-peering.md)
> * [비디오 - 프라이빗 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-routing-classic.md)
> 

ExpressRoute 회로에 대해 개인 피어링 및 Microsoft 피어링을 구성할 수 있습니다(Azure 공용 피어링은 새 회로에서 사용되지 않음). 피어링은 선택하는 순서에 관계없이 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [ExpressRoute 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요. 공용 피어링에 대한 자세한 내용은 [ExpressRoute 공용 피어링](about-public-peering.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 회로에 대한 Microsoft 피어링 구성, 업데이트 및 삭제
> - 회로에 대한 Azure 개인 피어링 구성, 업데이트 및 삭제

## <a name="prerequisites"></a>필수 구성 요소

* 구성을 시작하기 전에 다음 페이지를 검토했는지 확인합니다.
    * [필수 구성 요소](expressroute-prerequisites.md) 
    * [라우팅 요구 사항](expressroute-routing.md)
    * [워크플로](expressroute-workflows.md)
* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침에 따라 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-portal-resource-manager.md), 계속하기 전에 연결 공급자가 회로를 사용하도록 설정합니다. 피어링을 구성하려면 ExpressRoute 회로는 프로비저닝되고 활성화된 상태여야 합니다. 
* 공유 키/MD5 해시를 사용하려면 터널 양쪽에 있는 키를 사용해야 합니다. 제한은 최대 25자의 영숫자입니다. 특수 문자는 지원되지 않습니다. 

이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다. 

> [!IMPORTANT]
> 현재는 서비스 관리 포털을 통해 서비스 공급자가 구성한 피어링을 보급하지 않습니다. 이 기능을 곧 사용할 수 있도록 개발 중입니다. BGP 피어링을 구성하기 전에 서비스 공급자에게 확인하세요.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft 피어링

이 섹션은 ExpressRoute 회로에 Microsoft 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다. 자세한 내용은 [Microsoft 피어링에 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면

1. ExpressRoute 회로를 구성합니다. 계속하기 전에 연결 공급자에 의해 회로가 완전이 프로비저닝되었는지 **공급자 상태** 를 확인합니다.

   연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Microsoft 피어링을 사용하도록 할 수 있습니다. 다음 섹션에 나열된 지침을 따를 필요는 없습니다. 그러나 연결 공급자가 라우팅을 관리하지 않는 경우 회로를 만든 후에 다음 단계를 계속합니다.

   **회로 - 공급자 상태: 프로비저닝되지 않음**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="공급자 상태가 프로비저닝되지 않음으로 설정되어 있음을 빨간색 상자로 강조 표시한 ExpressRoute 데모 회로의 개요 페이지를 보여주는 스크린샷":::

   **회로 - 공급자 상태: 프로비저닝됨**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="공급자 상태가 프로비저닝됨으로 설정되어 있음을 빨간색 상자로 강조 표시한 ExpressRoute 데모 회로의 개요 페이지를 보여주는 스크린샷":::

2. 회로에 Microsoft 피어링을 구성합니다. 계속하기 전에 다음 정보가 있는지 확인합니다.

   * 사용자가 소유하고 RIR/IRR에 등록된 서브넷의 쌍입니다. 한 서브넷은 기본 링크에 사용되고 다른 서브넷은 보조 링크에 사용됩니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다. 이 서브넷 쌍에 대한 세 가지 옵션이 있습니다.
       * IPv4: 2개의 /30 서브넷 이러한 접두사는 유효한 공용 IPv4 접두사여야 합니다.
       * IPv6: 2개의 /126 서브넷 이러한 접두사는 유효한 공용 IPv6 접두사여야 합니다.
       * 둘 다: 두 개의 /30 서브넷과 두 개의 /126 서브넷
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사의 목록을 제공합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.
   * **선택 사항 -** 고객 ASN: 피어링 AS 숫자에 등록되지 않은 접두사를 보급하는 경우 등록된 AS 번호를 지정할 수 있습니다.
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.
1. 다음 예제와 같이 구성하려는 피어링을 선택할 수 있습니다. Microsoft 피어링 행을 선택합니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Microsoft 피어링 행 선택":::

4. Microsoft 피어링을 구성합니다. 모든 매개 변수를 지정한 후에 구성을 **저장** 합니다. 다음 이미지는 구성 예제를 보여줍니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="필요한 Microsoft 피어링 유효성 검사 구성":::

> [!IMPORTANT]
> Microsoft는 인터넷 라우팅 레지스트리에서 지정된 '보급된 공용 접두사' 및 '피어 ASN'(또는 '고객 ASN')이 사용자에게 할당되었는지 확인합니다. 다른 엔터티에서 공용 접두사를 가져오는 경우, 라우팅 레지스트리에 할당이 기록되지 않은 경우 자동 유효성 검사가 완료되지 않으며 수동 유효성 검사가 필요합니다. 자동 유효성 검사에 실패하면 '유효성 검사 필요' 메시지가 표시됩니다. 
>
> '유효성 검사 필요' 메시지가 표시되면 라우팅 레지스트리에서 접두사의 소유자로 나열된 엔터티에서 조직에 할당한 공용 접두사를 보여주는 문서를 수집하고, 지원 티켓을 열어 수동 유효성 검사를 위해 이러한 문서를 제출합니다. 
>

   회로가 유효성 검사가 필요한 상태가 되면, 지원 팀에 접두사에 대한 소유권의 증거를 보여주는 지원 티켓을 열어야 합니다. 다음 예제와 같이 포털에서 바로 지원 티켓을 열 수 있습니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="유효성 검사 필요 - 티켓 지원":::

5. 구성이 성공적으로 수락되고 나면 다음 이미지와 유사한 내용을 볼 수 있습니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="피어링 상태: 구성됨":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft 피어링 세부 정보를 보려면

피어링에 대한 행을 선택하면 Microsoft 피어링의 속성을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Microsoft 피어링 속성 보기":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft 피어링 구성을 업데이트하려면

수정할 피어링에 대한 행을 선택한 다음, 피어링 속성을 수정하고 수정 사항을 저장할 수 있습니다.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="피어링 행 선택":::

## <a name="azure-private-peering"></a><a name="private"></a>Azure 프라이빗 피어링

이 섹션은 ExpressRoute 회로에 Azure 프라이빗 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

### <a name="to-create-azure-private-peering"></a>Azure 프라이빗 피어링을 만들려면

1. ExpressRoute 회로를 구성합니다. 계속하기 전에 연결 공급자에 의해 회로가 완전이 프로비전되었는지 확인합니다. 

   연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 다음 섹션에 나열된 지침을 따를 필요는 없습니다. 그러나 연결 공급자가 라우팅을 관리하지 않는 경우 회로를 만든 후에 다음 단계를 계속합니다.

   **회로 - 공급자 상태: 프로비저닝되지 않음**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="공급자 상태가 프로비저닝되지 않음으로 설정되어 있음을 빨간색 상자로 강조 표시한 ExpressRoute 데모 회로의 개요 페이지를 보여주는 스크린샷":::

   **회로 - 공급자 상태: 프로비저닝됨**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="공급자 상태가 프로비저닝됨으로 설정되어 있음을 빨간색 상자로 강조 표시한 ExpressRoute 데모 회로의 개요 페이지를 보여주는 스크린샷":::

2. 회로에 Azure 프라이빗 피어링을 구성합니다. 다음 단계를 계속하기 전에 다음 항목이 있는지 확인합니다.

   * 가상 네트워크에 예약된 주소 공간의 일부가 아닌 서브넷 쌍입니다. 한 서브넷은 기본 링크에 사용되고 다른 서브넷은 보조 링크에 사용됩니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다. 이 서브넷 쌍에 대한 세 가지 옵션이 있습니다.
       * IPv4: 2개의 /30 서브넷
       * IPv6: 2개의 /126 서브넷
       * 둘 다: 두 개의 /30 서브넷과 두 개의 /126 서브넷
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에는 65515에서 65520까지의 숫자를 제외한 프라이빗 AS 숫자를 사용할 수 있습니다.
   * 개인 피어링을 구성할 때 BGP를 통해 온-프레미스 Edge 라우터에서 Azure로의 경로를 보급해야 합니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.
3. 다음 예제와 같이 Azure 개인 피어링 행을 선택합니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="개인 피어링 행 선택":::

4. 프라이빗 피어링을 구성합니다. 모든 매개 변수를 지정한 후에 구성을 **저장** 합니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="프라이빗 피어링 구성":::

5. 구성이 성공적으로 수락되고 나면 다음 예와 유사한 내용을 볼 수 있습니다.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="저장된 프라이빗 피어링":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure 프라이빗 피어링 세부 정보를 보려면

피어링을 선택하면 Azure 프라이빗 피어링의 속성을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="개인 피어링 속성 보기":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure 프라이빗 피어링 구성을 업데이트하려면

피어링의 행을 선택하고 피어링 속성을 수정할 수 있습니다. 업데이트한 후 변경 내용을 저장합니다.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="프라이빗 피어링 업데이트":::

## <a name="clean-up-resources"></a>리소스 정리

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft 피어링을 삭제하려면

다음 이미지와 같이 피어링을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택하여 Microsoft 피어링 구성을 제거할 수 있습니다.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Microsoft 피어링 삭제":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure 프라이빗 피어링을 삭제하려면

다음 이미지와 같이 피어링을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택하여 프라이빗 피어링 구성을 제거할 수 있습니다.

> [!WARNING]
> 이 작업을 실행하기 전에 모든 가상 네트워크 및 ExpressRoute Global Reach 연결이 제거되었는지 확인해야 합니다. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="프라이빗 피어링 삭제":::

## <a name="next-steps"></a>다음 단계

Azure 개인 피어링이 구성되면 ExpressRoute 게이트웨이를 만들어 가상 네트워크를 회로에 연결할 수 있습니다. 

> [!div class="nextstepaction"]
> [ExpressRoute에 대한 가상 네트워크 게이트웨이 구성](expressroute-howto-add-gateway-resource-manager.md)
