---
title: Azure 가상 머신 확장 집합에서 장애 도메인 관리
description: 가상 머신 확장 집합을 만드는 동안 적절한 수의 FD를 선택하는 방법을 알아봅니다.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: ff513925647e5233afd6056677343dca88b8977b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697405"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>가상 머신 확장 집합에 대해 적절한 수의 장애 도메인 선택

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

가상 머신 확장 집합은 영역 없는 Azure 지역에서 기본적으로 5개의 장애 도메인으로 만들어집니다. 가상 머신 확장 집합의 영역 배포를 지원하고 해당 옵션이 선택된 지역에서 장애 도메인의 기본 개수는 영역별로 1개입니다. 이 경우 FD=1은 확장 집합에 속하는 VM 인스턴스가 최선의 결과를 얻기 위해 여러 랙에 걸쳐 분산될 것임을 의미합니다.

Managed Disks 장애 도메인 수에 맞게 확장 집합 장애 도메인 수를 조정하는 것이 바람직할 수도 있습니다. 이러한 조정을 통해 전체 Managed Disks 장애 도메인이 다운될 경우에도 쿼럼 손실을 방지할 수 있습니다. FD 수는 각 지역에서 사용할 수 있는 Managed Disks 장애 도메인 수보다 작거나 같게 설정할 수 있습니다. 이 [문서](../virtual-machines/availability.md)를 참조하여 지역별 Managed Disks 장애 도메인 수에 대해 자세히 알아보세요.

## <a name="rest-api"></a>REST API
속성 `properties.platformFaultDomainCount`를 1, 2, 3 중 하나(지정하지 않을 경우 기본값 3)로 설정할 수 있습니다. [여기](/rest/api/compute/virtualmachinescalesets/createorupdate)에서 REST API 설명서를 참조하세요.

## <a name="azure-cli"></a>Azure CLI
매개 변수 `--platform-fault-domain-count`를 1, 2, 3 중 하나(지정하지 않을 경우 기본값 3)로 설정할 수 있습니다. [여기](/cli/azure/vmss#az_vmss_create)에서 Azure CLI 설명서를 참조하세요.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계
- Azure 환경의 [가용성 및 중복성 기능](../virtual-machines/availability.md)에 대해 자세히 알아봅니다.
