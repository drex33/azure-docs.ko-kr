---
title: Azure Spot Virtual Machines 및 확장 집합 인스턴스 오류 코드
description: Azure Spot Virtual Machines 및 확장 집합 인스턴스 사용 중에 발생할 수 있는 오류 코드를 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: f035d7813dfcf84d21e5913fa4626188e125db9a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691088"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Azure Spot Virtual Machines 및 확장 집합 오류 메시지

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

다음은 Azure Spot Virtual Machines 및 확장 집합 사용 중에 나타날 수 있는 몇 가지 오류 코드입니다.


| 키 | 메시지 | Description |
|-----|---------|-------------|
| SkuNotAvailable | 리소스 ‘\<resource\>’에 대해 요청된 계층은 현재 구독’\<subscriptionID\>’에 대한 위치‘\<location\>’에서 사용할 수 없습니다. 다른 계층에 시도하거나 위치를 바꿔 배포하세요. | 해당 위치에는 VM이나 확장 집합 인스턴스를 만들 수 있는 Azure 스폿 가상 머신 용량이 부족합니다. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Azure Spot Virtual Machines에 대한 제거 정책만 설정할 수 있습니다. | 해당 VM은 Azure 스폿 가상 머신이 아니므로 제거 정책을 설정할 수 없습니다. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure 스폿 가상 머신은 가용성 집합에서 지원되지 않습니다. | Azure 스폿 가상 머신을 사용하거나 가용성 집합에서 VM을 사용해야 하며 둘 다 선택할 수는 없습니다. |
| AzureSpotFeatureNotEnabledForSubscription  |  Azure 스폿 가상 머신 기능을 사용하여 구독을 사용할 수 없습니다. | Azure Spot Virtual Machines를 지원하는 구독을 사용하세요. |
| VMPriorityCannotBeApplied  |  가상 머신을 만들 때 우선 순위를 지정하지 않았으므로 지정된 우선 순위 값인 '{0}'을 가상 머신인 '{1}'에 적용할 수 없습니다. | VM을 만들 때 우선 순위를 지정하세요. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Azure 스폿 가상 머신 크기 '{3}'에 대해 제공된 최대 가격인 '{1} USD'가 현재 스폿 가격인 '{2} USD'보다 낮으므로 '{0}' 작업을 수행할 수 없습니다. | 최대 가격을 더 높게 선택합니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)의 가격 정보를 참조하세요.|
| MaxPriceValueInvalid  |  최대 가격 값이 잘못되었습니다. 최대 가격에 대해 유일하게 지원하는 값은 -1이거나 0보다 큰 10진수입니다. 최대 가격 값이 -1인 경우, 가격을 이유로 Azure 스폿 가상 머신을 제거하지 않는다는 뜻입니다. | 유효한 최대 가격을 입력하세요. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)의 가격 책정을 참조하세요. |
| MaxPriceChangeNotAllowedForAllocatedVMs | VM '{0}'이 할당되어 있을 때는 최대 가격 변경을 할 수 없습니다. 할당을 취소한 다음 다시 시도하세요. | 최대 가격을 변경할 수 있도록 VM을 중지하거나 할당 취소합니다. |
| MaxPriceChangeNotAllowed | 최대 가격을 변경할 수 없습니다. | 해당 VM의 최대 가격을 변경할 수 없습니다. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  해당 API 버전에는 Azure 스폿 가상 머신이 지원되지 않습니다. | API 버전이 2019-03-01이어야 합니다. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure 스폿 가상 머신이 해당 VM 크기({0})에서 지원되지 않습니다. | 다른 VM 크기를 선택하세요. 자세한 내용은 [Azure Spot Virtual Machines](./spot-vms.md)을 참조하세요. |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  최대 가격이 Azure Spot Virtual Machines에 대해서만 지원됩니다. | 자세한 내용은 [Azure Spot Virtual Machines](./spot-vms.md)을 참조하세요. |
| MoveResourcesWithAzureSpotVMNotSupported  |  리소스 이동 요청에 Azure 스폿 가상 머신이 포함됩니다. 지원되지 않습니다. 가상 머신 ID에 대한 오류 세부 정보를 확인합니다. | Azure Spot Virtual Machines를 이동할 수 없습니다. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  리소스 이동 요청에 Azure 스폿 가상 머신 확장 집합이 포함됩니다. 지원되지 않습니다. 가상 머신 확장 집합 ID에 대한 오류 세부 정보를 확인합니다. | Azure 스폿 가상 머신 확장 집합 인스턴스를 이동할 수 없습니다. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Azure 스폿 가상 머신이 VM 오케스트레이션 모드를 사용하는 가상 머신 확장 집합에서 지원되지 않습니다. | Azure 스폿 가상 머신 인스턴스를 사용하려면 오케스트레이션 모드를 가상 머신 확장 집합으로 설정하세요. |


**다음 단계** 자세한 내용은 [Spot Virtual Machines](./spot-vms.md)를 참조하세요.