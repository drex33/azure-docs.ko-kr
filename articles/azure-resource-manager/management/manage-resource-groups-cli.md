---
title: 리소스 그룹 관리 - Azure CLI
description: Azure CLI를 사용하여 Azure Resource Manager를 통해 리소스 그룹을 관리합니다. 리소스 그룹을 만들고, 나열하고, 삭제하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2088f1b4f201f149fc4d51bf608cb5868802d4d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731126"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure Resource Manager 리소스 그룹 관리

[Azure Resource Manager](overview.md)와 함께 Azure CLI를 사용하여 Azure 리소스 그룹을 관리하는 방법을 알아봅니다. Azure 리소스를 관리하려면 [ Azure CLI를 사용하여 Azure 리소스 관리](manage-resources-cli.md)를 참조하세요.

리소스 관리에 관한 다른 문서:

- [Azure Portal를 사용하여 Azure 리소스 그룹 관리](manage-resources-portal.md)
- [Azure PowerShell을 사용하여 Azure 리소스 그룹 관리](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>리소스 그룹이란?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 조직에 가장 적합 한 것을 기준으로 리소스를 리소스 그룹에 추가 하는 방법을 결정 합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정하면 메타데이터가 저장되는 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create)를 사용 합니다.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>리소스 그룹 나열

구독의 리소스 그룹을 나열 하려면 [az group list](/cli/azure/group#az_group_list)를 사용 합니다.

```azurecli-interactive
az group list
```

리소스 그룹 하나를 가져오려면 [az group show](/cli/azure/group#az_group_show)를 사용 합니다.

```azurecli-interactive
az group show --name exampleGroup
```

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

리소스 그룹을 삭제 하려면 [az group delete](/cli/azure/group#az_group_delete)를 사용 합니다.

```azurecli-interactive
az group delete --name exampleGroup
```

Azure Resource Manager가 리소스 삭제를 지시하는 방법에 대한 자세한 내용은 [Azure Resource Manager 리소스 그룹 삭제](delete-resource-group.md)를 참조하세요.

## <a name="deploy-resources"></a>리소스 배포

Azure CLI를 사용 하거나 ARM (Azure Resource Manager) 템플릿 또는 Bicep 파일을 배포 하 여 Azure 리소스를 배포할 수 있습니다.

다음 예에서는 저장소 계정을 만듭니다. 저장소 계정에 대해 제공 하는 이름은 Azure에서 고유 해야 합니다.

```azurecli-interactive
az storage account create --resource-group exampleGroup --name examplestore --location westus --sku Standard_LRS --kind StorageV2
```

ARM 템플릿 또는 Bicep 파일을 배포 하려면 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용 합니다.

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-file storage.bicep
```

ARM 템플릿 배포에 대 한 자세한 내용은 [리소스 관리자 템플릿 및 Azure CLI를 사용 하 여 리소스 배포](../templates/deploy-cli.md)를 참조 하세요.

Bicep 파일을 배포 하는 방법에 대 한 자세한 내용은 [Bicep 및 Azure CLI를 사용 하 여 리소스 배포](../bicep/deploy-cli.md)를 참조 하세요.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금은 조직의 다른 사용자가 실수로 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다.

리소스 그룹 및 해당 리소스가 삭제 되지 않도록 하려면 [az lock create](/cli/azure/lock#az_lock_create)를 사용 합니다.

```azurecli-interactive
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleGroup
```

리소스 그룹에 대 한 잠금을 가져오려면 [az lock list](/cli/azure/lock#az_lock_list)를 사용 합니다.

```azurecli-interactive
az lock list --resource-group exampleGroup
```

잠금을 삭제 하려면 [az lock delete](/cli/azure/lock#az_lock_delete) 를 사용 합니다.

```azurecli-interactive
az lock delete --name exampleLock --resource-group exampleGroup
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](lock-resources.md)를 참조하세요.

## <a name="tag-resource-groups"></a>리소스 그룹 태그 지정

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](tag-resources.md#azure-cli)을 참조하세요.

## <a name="export-resource-groups-to-templates"></a>템플릿으로 리소스 그룹 내보내기

ARM 템플릿을 만드는 데 도움이 되도록 기존 리소스에서 템플릿을 내보낼 수 있습니다. 자세한 내용은 [Azure CLI를 사용 하 여 템플릿 내보내기](../templates/export-template-cli.md)를 참조 하세요. 

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대한 액세스 관리

리소스 그룹에 대 한 액세스를 관리 하려면 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 사용 합니다. 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조하세요.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/syntax.md)를 참조하세요.