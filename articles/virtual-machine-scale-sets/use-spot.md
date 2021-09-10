---
title: Azure Spot Virtual Machines를 사용하는 확장 집합 만들기
description: Azure Spot Virtual Machines를 사용하여 비용을 절약하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b0c0ffdce85450900c0d4ca0da936b8675820f79
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690567"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 위한 Azure Spot Virtual Machines 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

확장 집합에 Azure Spot Virtual Machines를 사용하면 미사용 용량을 대폭 절감된 비용으로 활용할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 Azure 인프라에서 Azure Spot Virtual Machine 인스턴스를 제거합니다. 따라서 Azure Spot Virtual Machines 인스턴스는 일괄 처리 작업, 개발/테스트 환경, 대용량 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

사용 가능한 용량의 크기는 크기, 지역, 하루 중 시간 등에 따라 달라질 수 있습니다. 확장 집합에 Azure Spot Virtual Machine 인스턴스를 배포할 때 Azure는 사용 가능한 용량이 있는 경우에만 인스턴스를 할당하며 이 인스턴스에 대한 SLA는 없습니다. Azure Spot Virtual Machine 확장 집합은 단일 장애 도메인에 배포되며 고가용성을 보장하지 않습니다.


## <a name="pricing"></a>가격 책정

Azure Spot Virtual Machines 인스턴스의 가격 책정은 지역과 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)에 대한 가격 책정을 참조하세요. 


가변 가격 책정을 사용하면 최대 가격을 소수점 이하 다섯 자리까지 USD(미국 달러)로 설정할 수 있습니다. 예를 들어 `0.98765` 값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하면 가격을 토대로 인스턴스가 제거되지 않습니다. 사용 가능한 용량과 할당량이 있는 한, 인스턴스의 가격은 Azure Spot Virtual Machine의 현재 가격과 표준 인스턴스 가격 중에서 더 낮은 쪽이 됩니다.


## <a name="limitations"></a>제한 사항

Azure Spot Virtual Machines에 지원되지 않는 크기는 다음과 같습니다.
 - B 시리즈
 - 다양한 크기의 프로모션 버전(예: Dv2, NV, NC, H 프로모션 크기)

Azure Spot Virtual Machine은 Microsoft Azure 중국 21Vianet을 제외한 모든 지역에 배포할 수 있습니다.

<a name="channel"></a>

현재 지원되는 [제품 유형](https://azure.microsoft.com/support/legal/offer-details/)은 다음과 같습니다.

-   기업 계약
-   종량제 제품 코드(003P)
-   스폰서(0036P 및 0136P)
- CSP(클라우드 서비스 공급자)의 경우 [파트너 센터](/partner-center/azure-plan-get-started)를 참조하거나 파트너에게 직접 문의하세요.

## <a name="eviction-policy"></a>제거 정책

Azure Spot Virtual Machines를 사용하여 확장 집합을 만들 때 ‘할당 취소’(기본값) 또는‘삭제’하도록 제거 정책을 설정할 수 있습니다.  

‘할당 취소’ 정책은 제거된 인스턴스를 중지-할당 취소 상태로 전환하여 제거된 인스턴스를 다시 배포할 수 있습니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 확장 집합 인스턴스 할당량에 따라 계산되며 기본 디스크에 대한 요금이 청구됩니다. 

제거한 인스턴스를 삭제하려면 제거 정책을 ‘삭제’로 설정할 수 있습니다. 삭제하도록 제거 정책을 설정하면 확장 집합 인스턴스 수 속성을 늘려 새 VM을 만들 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 스토리지에 대한 요금이 청구되지 않습니다. 확장 집합의 자동 크기 조정 기능을 사용하여 제거된 VM을 자동으로 시도하고 보정할 수 있지만 성공적인 할당을 보장하지는 않습니다. 제거 정책을 삭제로 설정한 경우 디스크 비용을 피하고 할당량 한도에 도달하지 않기 위해서 Azure Spot Virtual Machine 확장 집합에서 자동 크기 조정 기능만 사용하는 것이 좋습니다. 

사용자는 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)를 통해 VM 내에서 알림을 받도록 옵트인할 수 있습니다. 이렇게 하면 VM을 제거하는 경우에 알림이 표시되며, 제거 전에 작업을 완료하고 종료 작업을 수행할 수 있도록 30초 정도의 시간 여유가 제공됩니다. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>시도 및 복원(미리 보기)

이 새로운 플랫폼 수준 기능은 대상 인스턴스 수를 유지하기 위해 확장 집합 내에 제거된 Azure Spot Virtual Machine 인스턴스의 복원을 자동으로 시도하는 데에 AI를 사용합니다. 

> [!IMPORTANT]
> 시도 및 복원은 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

시도 및 복원 혜택은 다음과 같습니다.
- 용량 때문에 제거된 Azure Spot Virtual Machines의 복원을 시도합니다.
- 복원된 Azure Spot Virtual Machines는 더 오래 실행되고 용량으로 트리거된 제거의 가능성이 더 낮을 것으로 예상합니다.
- Azure Spot Virtual Machine의 수명을 개선하여 더 오랫동안 워크로드가 실행되게 합니다.
- Virtual Machine Scale Sets가 Azure Spot Virtual Machines의 대상 수를 유지하도록 하며, 이는 종량제 VM에 이미 존재하는 대상 수 유지 기능과 비슷합니다.

[자동 크기 조정](virtual-machine-scale-sets-autoscale-overview.md)을 사용하는 확장 집합에서는 시도 및 복원을 사용하지 않도록 설정됩니다. 확장 집합의 VM 수는 자동 크기 조정 규칙에 따라 결정됩니다.

### <a name="register-for-try--restore"></a>시도 및 복원 등록

시도 및 복원 기능을 사용하려면 먼저 미리 보기에 대한 구독을 등록해야 합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. Azure CLI 또는 PowerShell을 사용하여 기능 등록을 완료할 수 있습니다.


**CLI 사용**

[az feature register](/cli/azure/feature#az_feature_register)를 사용하여 구독에서 미리 보기를 사용하도록 설정합니다. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

기능이 구독에 등록되면 변경 내용을 컴퓨팅 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**PowerShell 사용** 

[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용하여 구독에서 미리 보기를 사용하도록 설정합니다. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

기능이 구독에 등록되면 변경 내용을 컴퓨팅 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>배치 그룹

배치 그룹은 자체 장애 도메인과 업그레이드 도메인이 있는 Azure 가용성 집합과 구조가 비슷합니다. 기본적으로 확장 집합은 최대 100대의 VM을 갖춘 단일 배치 그룹으로 구성됩니다. `singlePlacementGroup`이라 불리는 확장 집합 속성이 *false* 로 설정된 경우, 확장 집합은 다중 배치 그룹으로 구성될 수 있으며 0~1,000대의 VM을 가집니다. 

> [!IMPORTANT]
> HPC와 함께 Infiniband를 사용하는 경우가 아니라면, 확장 집합 속성 `singlePlacementGroup`을 *false* 로 설정하여 여러 지역 또는 영역에서 더 나은 스케일링을 위해 다중 배치 그룹을 사용합니다. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>확장 집합에 Azure Spot Virtual Machines 배포

확장 집합에 Azure Spot Virtual Machines를 배포하려면 새 ‘우선 순위’ 플래그를 ‘스폿’으로 설정합니다.  확장 집합의 모든 VM이 스폿으로 설정됩니다. Azure Spot Virtual Machines로 확장 집합을 만들려면 다음 방법 중 하나를 사용합니다.
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 리소스 관리자 템플릿](#resource-manager-templates)

## <a name="portal"></a>포털

Azure Spot Virtual Machines를 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에 설명된 프로세스와 동일합니다. 확장 집합을 배포할 때 스폿 플래그 및 제거 정책을 설정할 수 있습니다. ![Azure Spot Virtual Machines로 확장 집합 만들기](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Azure Spot Virtual Machines로 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에 설명된 프로세스와 동일합니다. ‘--Priority Spot’을 추가하고 `--max-price`를 추가합니다. 이 예제에서는 `--max-price`에 `-1`을 사용하여 가격에 따라 인스턴스가 제거되지 않도록 했습니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Azure Spot Virtual Machines로 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-powershell.md)에 설명된 프로세스와 동일합니다.
‘-Priority Spot’을 추가하고 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig)에 `-max-price`를 제공합니다.

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

Azure Spot Virtual Machines를 사용하여 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)를 위한 시작 문서에 설명된 프로세스와 동일합니다. 

Azure Spot Virtual Machine 템플릿 배포에는 `"apiVersion": "2019-03-01"` 이상을 사용합니다. 

템플릿에서 `priority`, `evictionPolicy`, `billingProfile` 속성을 `"virtualMachineProfile":` 섹션에 추가하고 `"singlePlacementGroup": false,` 속성을 `"Microsoft.Compute/virtualMachineScaleSets"` 섹션에 추가합니다.

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

제거된 인스턴스를 삭제하려면 `evictionPolicy` 매개 변수를 `Delete`로 변경합니다.


## <a name="simulate-an-eviction"></a>제거 시뮬레이션

Azure Spot Virtual Machine [제거를 시뮬레이션](/rest/api/compute/virtualmachines/simulateeviction)하여 애플리케이션이 갑작스러운 제거에 얼마나 잘 대응하는지 테스트할 수 있습니다. 

다음을 내 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204`는 시뮬레이션이 성공적으로 완료되었음을 의미합니다. 

자세한 내용은 [시뮬레이션된 제거 알림 테스트](../virtual-machines/windows/spot-powershell.md#simulate-an-eviction)를 참조하세요.

## <a name="faq"></a>FAQ

**Q:** 일단 만들어지면, Azure Spot Virtual Machine 인스턴스는 표준 인스턴스와 동일한가요?

**A:** 예, 다만 Azure Spot Virtual Machines는 SLA가 없으며 언제든지 제거될 수 있습니다.


**Q:** 제거되었지만 여전히 용량이 필요한 경우에는 어떻게 해야 하나요?

**A:** 용량이 당장 필요하다면 Azure Spot Virtual Machines 대신 표준 VM을 사용하는 것이 좋습니다.


**Q:** Azure Spot Virtual Machine의 할당량은 어떻게 관리되나요?

**A:** Azure Spot Virtual Machine 인스턴스와 표준 인스턴스는 별도의 할당량 풀을 갖습니다. Azure Spot Virtual Machine 할당량은 VM과 확장 집합 인스턴스 간에 공유됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.


**Q:** Azure Spot Virtual Machine에 대한 추가 할당량을 요청할 수 있나요?

**A:** 예, [표준 할당량 요청 프로세스](../azure-portal/supportability/per-vm-quota-requests.md)를 통해 Azure Spot Virtual Machines의 할당량 추가 요청을 제출할 수 있습니다.


**Q:** 기존 확장 집합을 Azure Spot Virtual Machine 확장 집합으로 변환할 수 있나요?

**A:** 아니요, 생성 시에만 `Spot` 플래그 설정이 지원됩니다.


**Q:** 낮은 우선 순위 확장 집합에 `low`를 사용하고 있었다면, 이제 대신 `Spot`을 사용해야 하나요?

**A:** 지금은 `low`와 `Spot`이 모두 작동하지만 `Spot` 사용으로 전환하기 시작해야 합니다.


**Q:** 일반 VM과 Azure Spot Virtual Machines를 모두 사용하여 확장 집합을 만들 수 있나요?

**A:** 아니요. 확장 집합은 두 개 이상의 우선 순위 형식을 지원할 수 없습니다.


**Q:** Azure Spot Virtual Machine 확장 집합으로 자동 크기 조정을 사용할 수 있나요?

**A:** 예, Azure Spot Virtual Machine 확장 집합에 자동 크기 조정 규칙을 설정할 수 있습니다. VM이 제거된 경우, 자동 크기 조정이 새 Azure Spot Virtual Machines를 만들려고 시도할 수 있습니다. 그렇지만 이 용량은 보장되지 않습니다. 


**Q:** 자동 크기 조정은 두 가지 제거 정책(할당 취소 및 삭제)과 함께 사용될 수 있나요?

**A:** 예, 하지만 자동 크기 조정을 사용하는 경우 제거 정책을 삭제하도록 설정하는 것이 좋습니다. 할당 취소된 인스턴스가 확장 집합에서 용량 수에 따라 계산되기 때문입니다. 자동 크기 조정을 사용할 때 할당 취소되고 제거된 인스턴스로 인해 신속하게 대상 인스턴스 수를 달성할 수 있을 것 같습니다. 또한, 크기 조정 작업은 스폿 제거에 영향을 받을 수 있습니다. 예를 들어 가상 머신 확장 집합 인스턴스는 크기 조정 작업 중 다중 스폿 제거로 인해 설정된 최소 개수 아래로 떨어질 수 있습니다. 


**Q:** 질문은 어디에 게시할 수 있나요?

**A:** [Q&A](/answers/topics/azure-spot.html)에서 `azure-spot`을 사용하여 질문을 게시하고 태그를 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

가격 책정에 대한 자세한 내용은 [가상 머신 확장 집합 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)를 확인하세요.
