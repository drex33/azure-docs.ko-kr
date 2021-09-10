---
title: Azure Portal을 사용하여 Azure 가상 머신에 대한 유지 관리 제어
description: 유지 관리 제어 및 Azure Portal을 사용하여 Azure VM에 유지 관리가 적용되는 시기를 제어하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0db6f8d43a2492b59b407e43eee016d130915c77
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687744"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>유지 관리 제어 및 Azure Portal을 사용하여 업데이트 제어

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

유지 관리 제어를 사용하면 업데이트를 격리된 VM 및 Azure 전용 호스트에 적용할 시기를 결정할 수 있습니다. 이 문서에서는 Azure Portal의 유지 관리 제어 옵션에 관해 설명합니다. 유지 관리 제어 사용의 이점, 제한 사항 및 기타 관리 옵션에 관한 자세한 내용은 [유지 관리 제어를 사용하여 플랫폼 업데이트 관리](maintenance-control.md)를 참조하세요.

## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

1. Azure Portal에 로그인합니다.

1. **유지 관리 구성** 을 검색합니다.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="유지 관리 구성을 여는 방법을 보여주는 스크린샷":::

1. **추가** 를 클릭합니다.

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-2.png" alt-text="유지 관리 구성을 추가하는 방법을 보여주는 스크린샷":::

1. 기본 사항 탭에서 구독 및 리소스 그룹을 선택하고, 구성의 이름을 제공하고, 지역을 선택하고, 범위에 대해 *호스트* 를 선택합니다. **다음** 을 클릭합니다.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="유지 관리 구성 기본 사항을 보여주는 스크린샷":::

1. 일정 탭에서 Azure가 리소스에서 업데이트를 적용하는 예약된 기간을 선언합니다. 시작 날짜, 유지 관리 기간 및 되풀이를 설정합니다. 예약된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용할 필요가 없습니다. **다음** 을 클릭합니다. 

    > [!IMPORTANT]
    > 유지 관리 기간의 **기간** 은 *2시간* 이상이어야 합니다. 유지 관리 **되풀이** 는 35일에 한 번 이상 반복되도록 설정해야 합니다. 

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="유지 관리 구성 일정을 보여주는 스크린샷":::

1. 할당 탭에서 지금 리소스를 할당하거나 이 단계를 건너뛰고 유지 관리 구성 배포 후에 리소스를 할당합니다. **다음** 을 클릭합니다.

1. 태그 및 값을 추가합니다. **다음** 을 클릭합니다.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="유지 관리 구성에 태그를 추가하는 방법을 보여주는 스크린샷":::

1. 요약을 검토합니다. **만들기** 를 클릭합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.


## <a name="assign-the-configuration"></a>구성 할당

유지 관리 구성의 세부 정보 페이지에서 할당을 클릭한 다음, **리소스 할당** 을 클릭합니다. 

![리소스를 할당하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

유지 관리 구성에 할당하려는 리소스를 선택하고 **확인** 을 클릭합니다. **Type**(유형) 열은 리소스가 격리된 VM 또는 Azure 전용 호스트인지 여부를 표시합니다. 구성을 할당하려면 VM이 실행되고 있어야 합니다. 중지된 VM에 구성을 할당하려고 하면 오류가 발생합니다. 

<!---Shantanu to add details about the error case--->

![리소스를 선택하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>구성 확인

구성이 올바르게 적용되었는지 확인하거나 **유지 관리 구성** 을 사용하여 현재 할당된 유지 관리 구성을 확인할 수 있습니다. **Type**(유형) 열은 구성이 격리된 VM 또는 Azure 전용 호스트에 할당되었는지 여부를 표시합니다. 

![유지 관리 구성을 확인하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

해당 속성 페이지에서 특정 가상 머신의 구성을 확인할 수도 있습니다. **유지 관리** 를 클릭하여 해당 가상 머신에 할당된 구성을 확인합니다.

![호스트의 유지 관리를 확인하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

또한 유지 관리 구성에 대한 업데이트가 보류 중인지 확인하는 두 가지 방법이 있습니다. **유지 관리 구성** 의 구성에 대한 세부 정보에서 **할당** 을 클릭하고 **유지 관리 상태** 를 확인합니다.

![보류 중인 업데이트를 확인하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

**Virtual Machines** 또는 전용 호스트의 속성을 사용하여 특정 호스트를 확인할 수도 있습니다. 

![강조 표시된 유지 관리 상태를 보여주는 스크린샷.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>업데이트 적용

요청 시 보류 중인 업데이트를 적용할 수 있습니다. VM 또는 Azure Dedicated Host 세부 정보에서 **유지 관리** 를 클릭하고 **지금 유지 관리 적용** 을 클릭합니다. 업데이트 호출 적용은 완료하는 데 최대 2시간이 소요될 수 있습니다.

![보류 중인 업데이트를 적용하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>업데이트 적용 상태를 확인합니다. 

**유지 관리 구성** 또는 **Virtual Machines** 를 사용하여 구성에 대한 업데이트의 진행률을 확인할 수 있습니다. VM 세부 정보에서 **유지 관리** 를 클릭합니다. 다음 예제에서는 **유지 관리 상태** 에 업데이트가 **보류 중** 으로 표시됩니다.

![보류 중인 업데이트의 상태를 확인하는 방법을 보여주는 스크린샷](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>유지 관리 구성 삭제

구성을 삭제하려면 구성 세부 정보를 열고 **삭제** 를 클릭합니다.

![구성을 삭제하는 방법을 보여주는 스크린샷.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조하세요.
