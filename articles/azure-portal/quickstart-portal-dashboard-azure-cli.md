---
title: Azure CLI를 사용하여 Azure Portal 대시보드 만들기
description: '빠른 시작: Azure CLI를 사용하여 Azure Portal에서 대시보드를 만드는 방법을 알아봅니다. 대시보드는 클라우드 리소스에 대해 집중적으로 구성된 보기입니다.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli, mode-api
ms.date: 12/4/2020
ms.openlocfilehash: 589f833b807ee526ae98bab9a836a55028daee2d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133073147"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Portal 대시보드 만들기

Azure Portal의 대시보드는 클라우드 리소스에 대해 집중적으로 구성된 보기입니다. 이 문서에서는 Azure CLI를 사용하여 대시보드를 만드는 과정을 중점적으로 설명합니다.
대시보드는 VM(가상 머신)의 성능뿐만 아니라 일부 정적 정보 및 링크도 보여 줍니다.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 여러 Azure 구독이 있는 경우 리소스에 대한 요금을 청구할 적절한 구독을 선택합니다.
[az account set](/cli/azure/account#az_account_set) 명령을 사용하여 구독을 선택합니다.

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만들거나 기존 리소스 그룹을 사용합니다.

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 가상 머신을 만듭니다.

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> 암호는 복잡해야 합니다.
> 이는 새 사용자 이름과 암호입니다.
> 예를 들어 Azure에 로그인하는 데 사용하는 계정이 아닙니다.
> 자세한 내용은 [사용자 이름 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-) 및 [암호 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 참조하세요.

배포가 시작되고 완료하는 데 일반적으로 몇 분 정도 걸립니다.
배포가 완료되면 다음 섹션으로 이동합니다.

## <a name="download-the-dashboard-template"></a>대시보드 템플릿 다운로드

Azure 대시보드는 리소스이므로 JSON으로 표현할 수 있습니다.
자세한 내용은 [Azure 대시보드의 구조](./azure-portal-dashboards-structure.md)를 참조하세요.

[portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json) 파일을 다운로드합니다.

다음 값을 사용자 값으로 변경하여 다운로드한 템플릿을 사용자 지정합니다.

* `<subscriptionID>`: 사용자의 구독
* `<rgName>`: 리소스 그룹(예: `myResourceGroup`)
* `<vmName>`: 가상 머신 이름(예 `SimpleWinVM`)
* `<dashboardTitle>`: 대시보드 제목(예: `Simple VM Dashboard`)
* `<location>`: Azure 지역(예: `centralus`)

자세한 내용은 [Microsoft 포털 대시보드 템플릿 참조](/azure/templates/microsoft.portal/dashboards)를 검토하세요.

## <a name="deploy-the-dashboard-template"></a>대시보드 템플릿 배포

이제 Azure CLI 내에서 템플릿을 배포할 수 있습니다.

1. [az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) 명령을 실행하여 템플릿을 배포합니다.

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. [az portal dashboard show](/cli/azure/portal/dashboard#az_portal_dashboard_show) 명령을 실행하여 대시보드가 성공적으로 만들어졌는지 확인합니다.

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

현재 구독에 대한 모든 대시보드를 보려면 [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list)를 사용합니다.

```azurecli
az portal dashboard list
```

리소스 그룹에 대한 모든 대시보드를 볼 수도 있습니다.

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

[az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) 명령을 사용하여 대시보드를 업데이트할 수 있습니다.

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>리소스 정리

가상 머신 및 관련 대시보드를 제거하려면 해당 가상 머신이 속한 리소스 그룹을 삭제합니다.

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurecli
az group delete --name myResourceGroup
```

대시보드만 제거하려면 [az portal dashboard delete](/cli/azure/portal/dashboard#az_portal_dashboard_delete) 명령을 사용합니다.

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>다음 단계

대시보드용 Azure CLI 지원에 대한 자세한 내용은 [az portal dashboard](/cli/azure/portal/dashboard)를 참조하세요.
