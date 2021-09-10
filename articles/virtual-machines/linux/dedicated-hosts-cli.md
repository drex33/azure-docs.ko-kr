---
title: CLI를 사용해 전용 호스트에 VM 및 확장 집합 인스턴스 배포하기
description: Azure CLI를 사용해 전용 호스트에 VM 및 확장 집합 인스턴스를 배포합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: 3fbc398a937e1b36e88cfd14b0c123d554ca2cd7
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687912"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Azure CLI를 사용하여 전용 호스트에 배포하기
 
**적용 대상:** :heavy_check_mark: Linux VMs  :heavy_check_mark: Windows VMs 

이 문서에서는 VM(가상 머신)을 호스팅하는 Azure [전용 호스트](../dedicated-hosts.md)를 만드는 방법을 안내합니다.

Azure CLI 2.16.0 이상 버전을 설치했고 `az login`을 사용해 Azure 계정에 로그인했는지 확인합니다.


## <a name="limitations"></a>제한 사항

- 전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="create-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. az group create을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *myDHResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus
```

## <a name="list-available-host-skus-in-a-region"></a>지역에서 사용 가능한 호스트 SKU 나열

모든 호스트 SKU를 모든 지역 및 가용성 영역에서 사용할 수 있는 것은 아닙니다.

전용 호스트의 프로비저닝을 시작하기 전에 호스트 가용성 및 모든 제품 제한을 나열하세요.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹** 은 전용 호스트의 모음을 나타내는 리소스입니다. 영역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다.
- 여러 가용성 영역으로 확장. 이 경우에는 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑된 여러 장애 도메인으로 확장.

두 경우 모두 호스트 그룹의 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 확장하지 않으려면 장애 도메인 수 1을 사용합니다.

가용성 영역 및 장애 도메인을 모두 사용하도록 결정할 수도 있습니다.


이 예제에서는 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 사용하여 가용성 영역 및 장애 도메인을 모두 사용하는 호스트 그룹을 만듭니다.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

VM 및 확장 집합 인스턴스가 하나의 호스트 그룹 내에서 자동으로 호스트에 배치할 수 있게 `--automatic-placement true` 매개 변수를 추가합니다. 자세한 내용은 [수동 및 자동 배치](../dedicated-hosts.md#manual-vs-automatic-placement)를 참조하세요.


### <a name="other-examples"></a>다른 예제

[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 사용하여 가용성 영역 1에 호스트 그룹을 만들 수도 있습니다(장애 도메인은 없음).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 통해 장애 도메인만 사용하며 가용성 영역이 지원되지 않는 지역에서 사용될 호스트 그룹을 만드는 예제는 다음과 같습니다.

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

## <a name="create-a-host"></a>호스트 만들기

이제 호스트 그룹에서 전용 호스트를 만들어 보겠습니다. 호스트 이름 외에 호스트의 SKU도 제공해야 합니다. 호스트 SKU는 지원되는 VM 시리즈뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.

호스트 SKU 및 가격 책정에 대한 자세한 내용은 [Azure Dedicated Host 가격 책정](https://aka.ms/ADHPricing)을 참조하세요.

[az vm host create](/cli/azure/vm/host#az_vm_host_create)를 사용하여 호스트를 만듭니다. 호스트 그룹의 장애 도메인 수를 설정하면 호스트의 장애 도메인을 지정하라는 메시지가 표시됩니다.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```



## <a name="create-a-virtual-machine"></a>가상 머신 만들기
[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 전용 호스트 내에 가상 머신을 만듭니다. 호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 머신을 만들 때 동일한 영역을 사용해야 합니다.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

특정 호스트에 VM을 배치하려면 `--host-group`으로 대상 호스트 그룹을 지정하는 대신 `--host`를 사용합니다.

> [!WARNING]
> 리소스가 부족한 호스트에 가상 머신을 만드는 경우 가상 머신이 FAILED 상태로 생성됩니다.

## <a name="create-a-scale-set"></a>확장 집합 만들기

확장 집합을 배포하는 경우, 호스트 그룹을 지정합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

확장 집합을 배포할 호스트를 수동으로 선택하려면 `--host` 및 해당 호스트 이름을 추가합니다.


## <a name="check-the-status-of-the-host"></a>호스트의 상태 확인

[az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view)를 사용하여 호스트 상태와 호스트에 배포할 수 있는 가상 머신 수를 확인할 수 있습니다.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 출력은 다음과 유사합니다.

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

## <a name="export-as-a-template"></a>템플릿으로 내보내기
동일한 매개 변수를 사용하여 추가 개발 환경을 만들려고 하거나 일치하는 프로덕션 환경을 만들려는 경우 템플릿을 내보낼 수 있습니다. Resource Manager는 사용자 환경에 대한 모든 매개 변수를 정의하는 JSON 템플릿을 사용합니다. 이 JSON 템플릿을 참조하여 전체 환경을 빌드합니다. JSON 템플릿을 수동으로 빌드하거나 기존 환경을 내보내 JSON 템플릿을 만들 수 있습니다. [az group export](/cli/azure/group#az_group_export)를 사용하여 리소스 그룹을 내보냅니다.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json
```

이 명령을 실행하면 `myDHResourceGroup.json` 파일이 현재 작업 디렉터리에 만들어집니다. 이 템플릿에서 환경을 만들면 모든 리소스 이름을 입력하라는 메시지가 표시됩니다. `az group export` 명령에 `--include-parameter-default-value` 매개 변수를 추가하여 템플릿 파일에 이러한 이름을 입력할 수 있습니다. JSON 템플릿을 편집하여 리소스 이름을 지정하거나 리소스 이름을 지정하는 parameters.json 파일을 만듭니다.

템플릿에서 환경을 만들려면 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용합니다.

```azurecli-interactive
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myDHResourceGroup.json
```


## <a name="clean-up"></a>정리

가상 머신이 배포되지 않은 경우에도 전용 호스트에 대한 요금이 청구됩니다. 비용 절약을 위해 현재 사용하지 않는 호스트를 모두 삭제해야 합니다.

호스트를 사용하는 가상 머신이 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [az vm delete](/cli/azure/vm#az_vm_delete)를 사용하여 VM을 삭제합니다.

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

VM을 삭제한 후 [az vm host delete](/cli/azure/vm/host#az_vm_host_delete)를 사용하여 호스트를 삭제할 수 있습니다.

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

모든 호스트를 삭제한 후 [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete)를 사용하여 호스트 그룹을 삭제할 수 있습니다.

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에 생성된 모든 리소스가 삭제됩니다.

```azurecli-interactive
az group delete -n myDHResourceGroup
```

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](../dedicated-hosts.md) 개요를 참조하세요.

- 또한 [Azure Portal](../dedicated-hosts-portal.md)을 사용하여 전용 호스트를 만들 수도 있습니다.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 최대화하기 위해 영역과 오류 도메인을 모두 사용합니다.
