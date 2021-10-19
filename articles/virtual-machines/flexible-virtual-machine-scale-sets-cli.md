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
ms.openlocfilehash: a0c6d8dc1f9e032ca76393c52be76d1803873f8b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166407"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Azure CLI를 사용하여 유연한 확장 집합에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합


이 문서에서는 Azure CLI를 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 

최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치했고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인했는지 확인합니다.


> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="get-started-with-flexible-scale-sets"></a>유연한 확장 집합 시작

Azure CLI를 사용하여 Flexible 가상 머신 확장 집합 만들기

### <a name="add-multiple-vms-to-a-scale-set"></a>확장 집합에 여러 VM 추가

다음 예제에서는 가상 머신 프로필(VM 유형, 네트워킹 구성 등) 및 만들 인스턴스 수(인스턴스 수 = 2)를 지정합니다.  

```azurecli-interactive
az vmss create
--name $vmssName
--resource-group $rg
--image UbuntuLTS
--instance-count 2
--orchestration-mode flexible
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