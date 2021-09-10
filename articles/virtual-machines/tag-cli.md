---
title: Azure CLI를 사용하여 Azure 가상 머신에 태그를 지정하는 방법
description: Azure CLI를 사용하여 가상 머신에 태그를 지정하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10d5526b33b06867da267d61551cc4d6f16f1750
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695382"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Azure CLI를 사용하여 VM에 태그를 지정하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에서는 Azure CLI를 사용하여 VM에 태그를 지정하는 방법을 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. Azure [PowerShell](tag-powershell.md)을 사용하여 가상 머신에 태그를 지정할 수도 있습니다.


`az vm show`를 사용하여 지정된 VM의 태그를 포함한 모든 속성을 볼 수 있습니다.

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Azure CLI를 통해 새 VM 태그를 추가하는 데는 태그 매개 변수 `--set`와 함께 `azure vm update` 명령을 사용할 수 있습니다.

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

태그를 제거하는 데는 `azure vm update` 명령에 `--remove` 매개 변수를 사용할 수 있습니다.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Azure CLI 및 포털을 통해 리소스에 태그를 적용했으므로 이제 사용량 세부 정보를 확인하여 청구 포털에서 태그를 살펴보겠습니다.

### <a name="next-steps"></a>다음 단계

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그를 사용하여 Azure 리소스의 사용을 관리하는 방법을 알아보려면 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md)를 참조하세요.
