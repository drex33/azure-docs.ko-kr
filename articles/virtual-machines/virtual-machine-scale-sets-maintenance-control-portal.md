---
title: Azure Portal을 사용하여 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어
description: 유지 관리 제어 및 Azure Portal을 사용하여 Azure 가상 머신 확장 집합에 자동 OS 이미지 업그레이드가 출시되는 시기를 제어하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 656d72663045b4604b9a94cbc686150925caca83
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112202043"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-portal"></a>Azure Portal을 사용하여 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어

유지 관리 제어를 통해 가상 머신 확장 집합에 자동 게스트 OS 이미지 업그레이드를 적용하는 시기를 결정할 수 있습니다. 이 문서에서는 Azure Portal의 유지 관리 제어 옵션에 관해 설명합니다. 유지 관리 제어를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 머신 확장 집합에 대한 유지 관리 제어](virtual-machine-scale-sets-maintenance-control.md)를 참조하세요.


## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

1. Azure Portal에 로그인합니다.

1. **유지 관리 구성** 을 검색합니다.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="유지 관리 구성을 여는 방법을 보여 주는 스크린샷":::

1. **추가** 를 선택합니다.

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add.png" alt-text="유지 관리 구성을 추가하는 방법을 보여 주는 스크린샷":::

1. 기본 사항 탭에서 구독 및 리소스 그룹을 선택하고, 구성의 이름을 제공하고, 지역을 선택하고, 범위에 대해 *OS 이미지 업그레이드* 를 선택합니다. **다음** 을 선택합니다.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="유지 관리 구성 기본 사항을 보여 주는 스크린샷":::

1. 일정 탭에서 Azure가 리소스에서 업데이트를 적용하는 예약된 기간을 선언합니다. 시작 날짜, 유지 관리 기간 및 되풀이를 설정합니다. 예약된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용하지 않아도 됩니다. **다음** 을 선택합니다. 

    > [!IMPORTANT]
    > 유지 관리 기간의 **기간** 은 ‘5시간’ 이상이어야 합니다. 유지 관리 **되풀이** 는 하루에 한 번 이상 반복되도록 설정해야 합니다. 

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="유지 관리 구성 일정을 보여 주는 스크린샷":::

1. 할당 탭에서 지금 리소스를 할당하거나 이 단계를 건너뛰고 유지 관리 구성 배포 후에 리소스를 할당합니다. **다음** 을 선택합니다.

1. 태그 및 값을 추가합니다. **다음** 을 선택합니다.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="유지 관리 구성에 태그를 추가하는 방법을 보여 주는 스크린샷":::

1. 요약을 검토합니다. **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.


## <a name="assign-the-configuration"></a>구성 할당

유지 관리 구성의 세부 정보 페이지에서 **할당** 을 선택한 다음, **리소스 할당** 을 선택합니다. 

![리소스를 할당하는 방법을 보여 주는 스크린샷](media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add-assignment.png)

유지 관리 구성에 할당하려는 가상 머신 확장 집합 리소스를 선택하고 **확인** 을 선택합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 실행되는 가상 머신의 유지 관리 및 업데이트에 대해 알아보기](maintenance-and-updates.md)
