---
title: '빠른 시작: Azure CLI를 사용하여 Azure Dedicated HSM 만들기'
description: Azure CLI를 사용하여 Azure Dedicated HSM을 만들고, 표시하고, 나열하고, 업데이트하고, 삭제합니다.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: 341a0d9f76194ff92bc481efd96f4a2cb2bd86f9
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050657"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Dedicated HSM 만들기

이 문서에서는 [az dedicated-hsm](/cli/azure/dedicated-hsm) Azure CLI 확장을 사용하여 Azure Dedicated HSM을 만들고 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 계정이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
  
  둘 이상의 Azure 구독이 있는 경우 Azure CLI [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 청구에 사용할 구독을 설정합니다.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- 프로비저닝에 사용할 가상 네트워크 및 가상 머신과 등록, 승인 등 전용 HSM에 대한 모든 요구 사항이 충족되었습니다. 전용 HSM 요구 사항 및 필수 구성 요소에 대한 자세한 내용은 [자습서: Azure CLI를 사용하여 기존 가상 네트워크에 HSM 배포](tutorial-deploy-hsm-cli.md)를 참조하세요.
  

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)은 Azure 리소스를 그룹으로 배포하고 관리하기 위한 논리적 컨테이너입니다. 아직 전용 HSM을 위한 리소스 그룹이 없는 경우 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `myRG`라는 리소스 그룹을 `westus` Azure 지역에 만듭니다.

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>전용 HSM 만들기

전용 HSM을 만들려면 [az dedicated-hsm create](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) 명령을 사용합니다. 다음 예제에서는 `hsm1`이라는 전용 HSM을 `westus` 지역, `myRG` 리소스 그룹 및 지정된 구독, 가상 네트워크 및 서브넷에 프로비저닝합니다. 필수 매개 변수는 `name`, `location` 및 `resource group`입니다.

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

배포를 완료하는 데 약 25~30분이 걸립니다.

## <a name="get-a-dedicated-hsm"></a>전용 HSM 가져오기

현재 전용 HSM을 가져오려면 [az dedicated-hsm show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) 명령을 실행합니다. 다음 예제에서는 리소스 그룹 `myRG`의 전용 HSM `hsm1`을 가져옵니다.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>전용 HSM 업데이트

[az dedicated-hsm update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) 명령을 사용하여 전용 HSM을 업데이트합니다. 다음 예제에서는 리소스 그룹 `myRG`의 전용 HSM `hsm1`과 태그를 업데이트합니다.

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>전용 HSM 나열

[az dedicated-hsm list](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) 명령을 실행하여 현재 전용 HSM에 대한 정보를 가져옵니다. 다음 예제에서는 리소스 그룹 `myRG`의 전용 HSM을 나열합니다.

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>전용 HSM 제거

전용 HSM을 제거하려면 [az dedicated-hsm delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) 명령을 사용합니다. 다음 예제에서는 리소스 그룹 `myRG`에서 전용 HSM `hsm1`을 삭제합니다.

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>리소스 그룹 삭제

전용 HSM에 대해 만든 리소스 그룹이 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 실행하여 삭제할 수 있습니다. 이 명령은 그룹과 해당 그룹에서 전용 HSM과 관련이 없는 모든 리소스를 삭제합니다. 다음 예제에서는 리소스 그룹 `myRG` 및 그 안에 포함된 모든 항목을 삭제합니다.

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>다음 단계

Azure Dedicated HSM에 대해 자세히 알아보려면 [Azure Dedicated HSM](overview.md)을 참조하세요.
