---
title: 'Azure ExpressRoute: ExpressRoute Direct 구성: Portal'
description: 이 페이지는 Portal을 사용하여 ExpressRoute Direct를 구성하는 데 도움이 됩니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/05/2021
ms.author: duau
ms.openlocfilehash: d5b7bba4774eb81c684875a4db9ffb1afb2a60aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811226"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Azure Portal을 사용하여 ExpressRoute Direct 만들기

이 문서에서는 Azure Portal을 사용하여 ExpressRoute Direct를 만드는 방법을 보여줍니다.
ExpressRoute Direct를 사용하면 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결할 수 있습니다. 자세한 내용은 [ExpressRoute Direct 정보](expressroute-erdirect-about.md)를 참조하세요.

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

ExpressRoute Direct를 사용하려면 먼저 구독을 등록해야 합니다. 등록하려면 Azure PowerShell을 통해 다음을 수행하세요.
1.  Azure에 로그인하고 등록할 구독을 선택합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 다음 명령을 사용하여 퍼블릭 미리 보기에 대한 구독을 등록합니다.
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

등록한 후에는 **Microsoft.Network** 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다.

1. [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)에 설명된 대로 구독 설정에 액세스합니다.
1. 구독에서 **리소스 공급자** 에 대해 **Microsoft.Network** 공급자가 **등록됨** 상태로 표시되는지 확인합니다. Microsoft.Network 리소스 공급자가 등록된 공급자 목록에 없는 경우 추가합니다.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>ExpressRoute Direct 만들기

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **_마켓플레이스 검색_*필드에* ExpressRoute Direct** 를 입력한 다음, **Enter** 를 선택하여 검색 결과로 이동합니다.

1. 결과에서 **ExpressRoute Direct** 를 선택합니다.

1. **ExpressRoute Direct** 페이지에서 **만들기** 를 선택하여 **ExpressRoute Direct 만들기** 페이지를 엽니다.

1. **기본** 페이지의 필드를 작성하여 시작합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="기본 페이지":::

    * **구독**: 새 ExpressRoute 리소스를 만드는 데 사용할 Azure 구독입니다. ExpressRoute Direct 리소스와 ExpressRoute 회로가 동일한 구독에 있어야 합니다.
    * **리소스 그룹**: 새 ExpressRoute Direct 리소스가 만들어질 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **지역**: 리소스가 만들어질 Azure 공용 지역입니다.
    * **이름**: 새 ExpressRoute Direct 리소스의 이름입니다.

1. 그런 다음, **구성** 페이지의 필드를 작성합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="'구성' 탭이 선택된 'ExpressRoute Direct 만들기' 페이지가 표시된 스크린샷":::

    * **피어링 위치**: ExpressRoute Direct 리소스에 연결하는 피어링 위치입니다. 피어링 위치에 대한 자세한 내용은 [ExpressRoute 위치](expressroute-locations-providers.md)를 검토하세요.
   * **대역폭**: 예약할 포트 쌍 대역폭입니다. ExpressRoute Direct는 10Gb 대역폭 옵션과 100Gb 대역폭 옵션을 모두 지원합니다. 지정된 피어링 위치에서 원하는 대역폭을 사용할 수 없는 경우 [Azure Portal에서 지원 요청을 엽니다](https://aka.ms/azsupt).
   * **캡슐화**: ExpressRoute Direct는 QinQ 및 Dot1Q 캡슐화를 둘 다 지원합니다.
     * QinQ를 선택한 경우 ExpressRoute Direct 리소스 전체에서 고유하게 식별되는 S-Tag가 각 ExpressRoute 회로에 동적으로 할당됩니다.
     *  회로의 각 C-Tag는 회로에서 고유해야 하지만 ExpressRoute Direct 전체에서 고유할 필요는 없습니다.
     * Dot1Q 캡슐화가 선택된 경우 전체 ExpressRoute Direct 리소스에서 C-Tag(VLAN)의 고유성을 관리해야 합니다.
     >[!IMPORTANT]
     >ExpressRoute Direct에는 한 가지 유형으로만 캡슐화할 수 있습니다. ExpressRoute Direct를 만든 후에는 캡슐화를 변경할 수 없습니다.
     >

1. 리소스 태그를 지정한 다음, **검토 + 만들기** 를 선택하여 ExpressRoute Direct 리소스 설정의 유효성을 검사합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="'검토 + 만들기' 탭이 선택된 'ExpressRoute 만들기' 페이지를 보여주는 스크린샷":::

1. **만들기** 를 선택합니다. 배포가 진행되고 있음을 알리는 메시지가 표시됩니다. 리소스가 생성되면 이 페이지에 상태가 표시됩니다. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>LOA(승인 문서) 생성

1. ExpressRoute Direct 리소스의 개요 페이지로 이동하고 **LOA(승인 문서) 생성** 을 선택합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/overview.png" alt-text="개요 페이지의 LOA(승인 문서) 생성 단추 스크린샷":::

1. 회사 이름을 입력하고 **다운로드** 를 선택하여 LOA(승인 문서)를 생성합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/letter-of-authorization-page.png" alt-text="LOA(승인 문서) 페이지의 스크린샷":::

## <a name="change-admin-state-of-links"></a><a name="state"></a>링크의 관리 상태 변경

이 프로세스를 사용하여 계층 1 테스트를 수행하고 각 교차 연결이 1차 및 2차 포트에 대한 각 라우터에 제대로 패치되도록 합니다.

1. ExpressRoute Direct 리소스 **개요** 페이지의 **링크** 섹션에서 **link1** 을 선택합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="링크 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. **관리 상태** 설정을 **사용** 으로 전환한 다음, **저장** 을 선택합니다.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="관리 상태":::

    >[!IMPORTANT]
    >두 링크에서 관리 상태를 사용하도록 설정하면 청구가 시작됩니다.
    >

1. **link2** 에 대해 동일한 프로세스를 반복합니다.

## <a name="create-a-circuit"></a><a name="circuit"></a>회로 생성

기본적으로 ExpressRoute Direct 리소스가 있는 구독에서 10개의 회로를 만들 수 있습니다. 이 수는 지원을 통해 늘릴 수 있습니다. 사용자는 프로비전된 대역폭과 사용된 대역폭을 둘 다 추적할 책임이 있습니다. 프로비저닝된 대역폭은 ExpressRoute Direct 리소스의 모든 회로 대역폭 합계입니다. 사용된 대역폭은 기본 실제 인터페이스의 실제 사용량입니다.

* 위에 설명된 시나리오를 지원에 한해 ExpressRoute Direct에서 사용할 수 있는 추가 회로 대역폭은 40Gbps 및 100Gbps입니다.

* SkuTier는 로컬, 표준 또는 프리미엄일 수 있습니다.

* SkuFamily는 MeteredData여야 합니다. ExpressRoute Direct에서는 무제한이 지원되지 않습니다.

다음 단계는 ExpressRoute Direct 워크플로에서 ExpressRoute 회로를 만드는 데 도움이 됩니다. 대신 일반 회로 워크플로를 사용하여 회로를 만들 수도 있지만, 이 구성에 대한 일반 회로 워크플로 단계를 사용하는 데는 이점이 없습니다. [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)을 참조하세요.

1. ExpressRoute Direct **설정** 섹션에서 **회로** 를 선택한 다음, **+추가** 를 선택합니다. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="회로가 선택되고 추가가 강조 표시된 ExpressRoute 설정을 보여주는 스크린샷" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. **구성** 페이지에서 설정을 구성합니다.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="구성 페이지 - ExpressRoute Direct":::

1. 리소스 태그를 지정하고 **검토 + 만들기** 를 선택하여 리소스를 만들기 전에 값의 유효성을 검사합니다.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="검토 및 만들기 - ExpressRoute Direct":::

1. **만들기** 를 선택합니다. 배포가 진행되고 있음을 알리는 메시지가 표시됩니다. 리소스가 생성되면 이 페이지에 상태가 표시됩니다. 

## <a name="next-steps"></a>다음 단계

Express 경로 회로를 만든 후 [가상 네트워크를 express 경로 회로에 연결할](expressroute-howto-add-gateway-portal-resource-manager.md)수 있습니다.
