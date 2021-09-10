---
title: Azure CLI를 사용하여 유연한 확장 집합에서 가상 머신 만들기
description: Azure CLI를 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합 만드는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699300"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>미리 보기: Azure CLI를 사용하여 유연한 확장 집합에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합


이 문서에서는 Azure CLI를 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 

최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치했고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인했는지 확인합니다.


> [!IMPORTANT]
> Flexible 오케스트레이션 모드의 가상 머신 확장 집합은 현재 공개 미리 보기로 제공됩니다. 아래에서 자세하게 설명하는 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저를 사용해야 합니다.
> 이 미리 보기 버전은 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="register-for-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 등록

Flexible 오케스트레이션 모드로 가상 머신 확장 집합을 배포하려면 먼저 미리 보기 기능에 대한 구독을 등록해야 합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

[az feature register](/cli/azure/feature#az_feature_register)를 사용하여 구독에서 미리 보기를 사용하도록 설정합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>유연한 확장 집합 시작

Azure CLI를 사용하여 Flexible 가상 머신 확장 집합 만들기

### <a name="add-multiple-vms-to-a-scale-set"></a>확장 집합에 여러 VM 추가

다음 예제에서는 가상 머신 프로필(VM 유형, 네트워킹 구성 등) 및 만들 인스턴스 수(인스턴스 수 = 2)를 지정합니다.  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
```

### <a name="add-a-single-vm-to-a-scale-set"></a>확장 집합에 단일 VM 추가

다음 예제에서는 장애 도메인 수가 1로 설정된 VM 프로필 없이 유연한 확장 집합을 만드는 모습을 보여 줍니다. 가상 머신이 만들어지고 유연한 확장 집합에 추가됩니다.

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Portal에서 유연한 확장 집합을 만드는 방법을 알아봅니다.](flexible-virtual-machine-scale-sets-portal.md)