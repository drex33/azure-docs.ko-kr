---
title: Azure CLI 템플릿 내보내기
description: Azure CLI 사용하여 구독의 리소스에서 Azure Resource Manager 템플릿을 내보냅니다.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480879"
---
# <a name="use-azure-cli-to-export-a-template"></a>Azure CLI 사용하여 템플릿 내보내기

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

이 문서에서는 **Azure CLI** 통해 템플릿을 내보내는 방법을 보여줍니다. 다른 옵션은 다음을 참조하세요.

* [Azure Portal 템플릿 내보내기](export-template-portal.md)
* [Azure PowerShell 템플릿 내보내기](export-template-powershell.md)
* [REST API 리소스 그룹에서 내보내고](/rest/api/resources/resourcegroups/exporttemplate) [배포 기록 에서 내보내는 REST API.](/rest/api/resources/deployments/export-template)

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>리소스 그룹에서 템플릿 내보내기

리소스 그룹을 성공적으로 설정하면 리소스 그룹에 대한 Azure Resource Manager 템플릿을 내보낼 수 있습니다.

리소스 그룹의 모든 리소스를 내보내려면 [az group export](/cli/azure/group#az_group_export)를 사용하고 리소스 그룹 이름을 제공합니다.

```azurecli-interactive
az group export --name demoGroup
```

스크립트는 콘솔에 템플릿을 표시합니다. 파일에 저장하려면 다음을 사용합니다.

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

리소스 그룹의 모든 리소스를 내보내는 대신, 내보낼 리소스를 선택할 수 있습니다.

한 리소스를 내보내려면 해당 리소스 ID를 제공합니다.

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

둘 이상의 리소스를 내보내려면 공백으로 구분된 리소스 ID를 전달합니다. 모든 리소스를 내보내려면 이 인수를 지정하거나 "*"를 제공하지 마세요.

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
```

템플릿을 내보낼 때 템플릿에서 매개 변수를 사용할지 여부를 지정할 수 있습니다. 기본적으로 리소스 이름에 대한 매개 변수는 포함되지만 기본값은 없습니다.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

템플릿을 내보낼 때 `--skip-resource-name-params` 매개 변수를 사용하는 경우 리소스 이름에 대한 매개 변수가 템플릿에 포함되지 않습니다. 대신, 리소스 이름이 현재 값으로 리소스에 직접 설정됩니다. 배포하는 동안 이름을 사용자 지정할 수 없습니다.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

템플릿을 내보낼 때 `--include-parameter-default-value` 매개 변수를 사용하는 경우 템플릿 매개 변수는 현재 값으로 설정된 기본값을 포함합니다. 기본값을 사용하거나 다른 값을 제공하여 기본값을 덮어쓸 수 있습니다.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>배포 기록에서 템플릿 저장

배포 기록의 배포에서 템플릿을 저장할 수 있습니다. 배포에 사용된 것과 똑같은 템플릿을 얻을 수 있습니다.

리소스 그룹 배포에서 템플릿을 얻으려면 [az deployment group export](/cli/azure/deployment/group#az_deployment_group_export) 명령을 사용합니다. 검색할 배포의 이름을 지정합니다. 배포 이름을 얻는 데 대한 도움말은 Azure Resource Manager 를 통해 [배포 기록 보기를 참조하세요.](deployment-history.md)

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

템플릿이 콘솔에 표시됩니다. 파일을 저장하려면 다음을 사용합니다.

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

다른 수준에서 배포된 템플릿을 얻으려면 다음을 사용합니다.

* [구독에 배포하기](/cli/azure/deployment/sub#az_deployment_sub_export) 위한 az deployment sub export
* [az deployment mg export](/cli/azure/deployment/mg#az_deployment_mg_export) for deployments to management groups
* [테넌트 배포를](/cli/azure/deployment/tenant#az_deployment_tenant_export) 위한 az deployment tenant export


## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure PowerShell](export-template-portal.md) [또는](/rest/api/resources/resourcegroups/exporttemplate) [REST API](export-template-powershell.md)사용하여 템플릿을 내보내는 방법을 알아봅니다.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
- 템플릿을 개발하는 방법을 알아보려면 [단계별 자습서](../index.yml)를 참조하세요.