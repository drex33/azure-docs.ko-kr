---
title: 포털을 사용하여 근접 배치 그룹 만들기
description: Azure Portal을 사용하여 근접 배치 그룹 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: 37e959615ea9e5d483a081c7059556d36b5fc8ff
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694869"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Azure Portal을 사용하여 근접 배치 그룹 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

VM을 최대한 가깝게 유지하고, 대기 시간을 최대한 줄이려면, VM을 [근접 배치 그룹](../co-location.md#proximity-placement-groups) 내에 배포해야 합니다.

근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가까운 위치에 있도록 하는 데 사용되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구하는 작업에 유용합니다.

> [!NOTE]
> 근접 배치 그룹은 전용 호스트와 함께 사용할 수 없습니다.
>
> 배치 그룹과 함께 가용성 영역을 사용하려는 경우 배치 그룹의 VM도 모두 동일한 가용성 영역에 있는지 확인해야 합니다.
>

## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기

1. 검색에 **근접 배치 그룹** 을 입력합니다.
1. 검색 결과의 **서비스** 에서 **근접 배치 그룹** 을 선택합니다.
1. **근접 배치 그룹** 페이지에서 **추가** 를 선택합니다.
1. **기본** 탭의 **프로젝트 세부 정보** 에서 올바른 구독이 선택되어 있는지 확인합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택하여 새 그룹을 만들거나 드롭다운에서 이미 존재하는 빈 리소스 그룹을 선택합니다. 
1. **지역** 에서 근접 배치 그룹을 만들 위치를 선택합니다.
1. **근접 배치 그룹 이름** 에 이름을 입력한 다음 **검토 + 만들기** 를 선택합니다.
1. 유효성 검사가 통과되면 **만들기** 를 선택하여 가상 근접 배치 그룹을 만듭니다.

    ![근접 배치 그룹을 만드는 스크린샷](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM 만들기

1. 포털에서 VM을 만드는 동안 **고급** 탭으로 이동합니다. 
1. **근접 배치 그룹** 선택에서 올바른 배치 그룹을 선택합니다. 

    ![포털에서 새 VM을 만들 때 근접 배치 그룹 섹션의 스크린샷](./media/ppg/vm-ppg.png)

1. 필요한 다른 모든 항목을 선택했으면 **검토 + 만들기** 를 선택합니다.
1. 유효성 검사를 통과한 후 **만들기** 를 선택하여 배치 그룹에 VM을 배포합니다.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>가용성 집합의 VM을 근접 배치 그룹에 추가

VM이 가용성 집합의 일부인 경우 VM을 추가하기 전에 가용성 집합을 배치 그룹에 추가해야 합니다.

1. [포털](https://portal.azure.com)에서 *가용성 집합* 을 검색하고 결과에서 가용성 집합을 선택합니다.
1. VM을 선택한 다음 VM 페이지에서 **중지** 를 선택한 다음 **확인** 을 선택하여 VM을 중지하여 가용성 집합의 각 VM을 중지/할당 취소합니다.
1. 가용성 집합 페이지에서 모든 VM의 **상태** 가 **중지됨(할당 취소됨)** 으로 나열되어 있는지 확인합니다.
1. 왼쪽 메뉴에서 **구성** 을 선택합니다.
1. **근접 배치 그룹** 의 드롭다운에서 배치 그룹을 선택한 다음 **저장** 을 선택합니다.
1. VM 목록을 다시 보려면 왼쪽 메뉴에서 **개요** 를 선택합니다. 
1. 가용성 세트에서 각 VM을 선택한 다음 각 VM 페이지에서 **시작** 을 선택합니다. 


## <a name="add-existing-vm-to-placement-group"></a>배치 그룹에 기존 VM 추가 


1. VM 페이지에서 **중지** 를 선택합니다.
1. VM 상태가 **중지됨(할당 취소됨)** 으로 표시되면 왼쪽 메뉴에서 **구성** 을 선택합니다.
1. **근접 배치 그룹** 의 드롭다운에서 배치 그룹을 선택한 다음 **저장** 을 선택합니다.
1. 왼쪽 메뉴에서 **개요** 를 선택한 다음 **시작** 를 선택하여 VM을 다시 시작합니다.

 

## <a name="next-steps"></a>다음 단계

또한 [Azure PowerShell](proximity-placement-groups.md)을 사용하여 근접 배치 그룹을 만들 수도 있습니다.