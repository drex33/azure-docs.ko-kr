---
title: 포털을 사용하여 Azure Spot Virtual Machines 배포
description: 포털을 사용하여 Spot Virtual Machines를 배포하는 방법
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: a80356cf7045fc079e71d429f89f3b4b0fff31ba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694378"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Spot Virtual Machines 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일 확장 집합

[Azure Spot Virtual Machines](spot-vms.md)를 사용하면 사용되지 않는 용량을 활용하여 비용을 크게 절감할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 따라서 Azure Spot Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같이 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

Azure Spot Virtual Machines의 가격 책정은 지역과 SKU에 따라 다릅니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대한 VM 가격 책정을 참조하세요. 최고 가격을 설정하는 방법에 대한 자세한 내용은 [Azure Spot Virtual Machines - 가격 책정](spot-vms.md#pricing)을 참조하세요.

VM에 대해 시간당 지불할 최고 가격을 설정하는 옵션이 있습니다. Azure 스폿 가상 머신 한 대당 최고 가격을 미국 달러(USD)로 최대 소수점 5자릿수까지 설정할 수 있습니다. 예를 들어 `0.05701` 값은 시간당 $0.05701 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하는 경우 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과하는 경우 스폿의 현재 가격과 표준 VM의 가격 중에서 더 작은 가격이 됩니다.

VM이 제거되면 VM 및 기본 디스크를 삭제하거나 VM의 할당을 취소하여 나중에 다시 시작할 수 있습니다.


## <a name="create-the-vm"></a>VM 만들기

VM을 배포할 때 Azure 스폿 인스턴스를 사용하도록 선택할 수 있습니다.


**기본** 탭의 **인스턴스 세부 정보** 섹션에서 **아니요** 는 Azure 스폿 인스턴스 사용에 대한 기본값입니다.

![아니요(Azure 스폿 인스턴스 사용 안 함)를 선택하는 화면 캡처](./media/spot-portal/no.png)

**예** 를 선택하면 섹션이 확장되고 [제거 형식 및 제거 정책](spot-vms.md#eviction-policy)을 선택할 수 있습니다. 

![예(Azure 스폿 인스턴스 사용)를 선택하는 화면 캡처](./media/spot-portal/yes.png)

**가격 책정 기록 보기 및 인접한 지역의 가격 비교** 를 선택하여 가격 책정 및 제거 비율을 다른 유사한 지역과 비교할 수도 있습니다.

이 예제에서 캐나다 중부 지역에서는 비용이 적게 들고 미국 동부 지역보다 제거 비율이 낮습니다.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="가격 책정 및 제거 비율에 차이가 있는 지역 옵션의 스크린샷":::

가장 적합한 옵션을 선택하고 **확인** 을 선택하여 지역을 변경할 수 있습니다.

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

애플리케이션이 갑작스러운 제거에 얼마나 잘 대응할 수 있는지 테스트하기 위해 Azure 스폿 가상 머신의 [제거를 시뮬레이션할](/rest/api/compute/virtualmachines/simulateeviction) 수 있습니다. 

다음을 내 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204`는 시뮬레이션이 성공적으로 완료되었음을 의미합니다. 

## <a name="next-steps"></a>다음 단계

[PowerShell](./windows/spot-powershell.md), [CLI](./linux/spot-cli.md) 또는 [템플릿](./linux/spot-template.md)을 사용하여 Azure Spot Virtual Machines를 만들 수도 있습니다.
