---
title: Azure VMware Solution 리소스 공급자 등록
description: Azure VMware Solution 리소스 공급자를 등록하는 단계입니다.
ms.topic: include
ms.date: 02/17/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: a4f24e503c06f4f7650334f8c513466eb4dff546
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638584"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution을 사용하려면 먼저 구독에 리소스 공급자를 등록해야 합니다. 리소스 공급자에 관한 자세한 내용은 [Azure 리소스 공급자 및 유형](../../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.


### <a name="portal"></a>[포털](#tab/azure-portal)
 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.

1. **모든 서비스** 상자에서 **구독** 을 입력한 다음, **구독** 을 선택합니다.

1. 구독 목록에서 보려는 구독을 선택합니다.

1. **리소스 공급자** 를 선택하고 검색에 **Microsoft.AVS** 를 입력합니다. 
 
1. 리소스 공급자가 등록되지 않은 경우 **등록** 을 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI를 통해 Azure VMware Solution 배포에 사용하는 Azure 구독에 로그인합니다. [az provider register](/cli/azure/provider#az_provider_register) 명령으로 `Microsoft.AVS` 리소스 제공자를 등록합니다.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

[az provider list](/cli/azure/provider#az_provider_list) 명령을 사용하여 사용 가능한 모든 공급자를 볼 수 있습니다.

---


 
