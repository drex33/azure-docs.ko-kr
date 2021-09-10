---
title: CLI를 사용하여 Azure Spot Virtual Machines 배포
description: CLI를 사용하여 비용 절감을 위해 Azure Spot Virtual Machines를 배포하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 81753681776ab4fd8ae42081ac3d5b0998d4f219
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692067"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Spot Virtual Machines 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

[Azure Spot Virtual Machines](../spot-vms.md)를 사용하면 사용되지 않는 용량을 활용하여 비용을 크게 절감할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 따라서 Azure Spot Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같이 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

Azure Spot Virtual Machines의 가격 책정은 지역과 SKU에 따라 다릅니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대한 VM 가격 책정을 참조하세요. 

VM에 대해 시간당 지불할 최고 가격을 설정하는 옵션이 있습니다. Azure Spot Virtual Machine 한 대당 최고 가격을 미국 달러(USD)로 최대 소수 자릿수 5자리까지 설정할 수 있습니다. 예를 들어 `0.98765` 값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하는 경우 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과하는 경우 Azure Spot Virtual Machines의 현재 가격과 표준 VM의 가격 중에서 더 작은 가격이 됩니다. 최고 가격을 설정하는 방법에 대한 자세한 내용은 [Azure Spot Virtual Machines - 가격 책정](../spot-vms.md#pricing)을 참조하세요.

Azure CLI를 사용하여 Azure Spot Virtual Machines를 만드는 프로세스는 [빠른 시작 문서](./quick-create-cli.md)에 자세히 설명된 것과 같습니다. '--priority Spot' 매개 변수를 추가하고 `--eviction-policy`를 할당 취소(기본값) 또는 `Delete`로 설정한 후 최대 가격 또는 `-1`을 제공합니다. 


## <a name="install-azure-cli"></a>Azure CLI 설치

Azure Spot Virtual Machines를 만들려면 Azure CLI 버전 2.0.74 이상을 실행해야 합니다. 버전을 찾으려면 **az --version** 을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[az login](/cli/azure/reference-index#az_login)을 사용하여 Azure에 로그인합니다.

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure Spot Virtual Machines 만들기

이 예제에서는 가격에 따라 제거되지 않을 Linux Azure Spot Virtual Machines를 배포하는 방법을 보여 줍니다. 제거 정책은 나중에 다시 시작할 수 있도록 VM의 할당을 취소하도록 설정됩니다. VM을 제거할 때 VM 및 기본 디스크를 삭제하려면 `--eviction-policy`를 `Delete`로 설정합니다.

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM을 만든 후에 쿼리를 통해 리소스 그룹에 속한 모든 VM에 대한 최고 청구 가격을 확인할 수 있습니다.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

REST, PowerShell 또는 CLI를 사용하여 Azure Spot Virtual Machine 제거를 시뮬레이션하여 애플리케이션이 갑작스러운 제거에 얼마나 잘 대응하는지 테스트할 수 있습니다.

대부분의 경우 REST API [가상 머신 - 제거 시뮬레이션](/rest/api/compute/virtualmachines/simulateeviction)을 사용하면 애플리케이션의 자동화된 테스트에 도움이 됩니다. REST의 경우 `Response Code: 204`가 나타나면 제거 시뮬레이션이 성공적으로 완료되었음을 의미합니다. 제거 시뮬레이션을 [ 서비스](scheduled-events.md)와 결합하면 VM 제거 시 앱의 대응 방식을 자동화할 수 있습니다.

작동 중인 예약된 이벤트를 보려면 [Azure Friday - Azure Scheduled Events를 사용하여 VM 유지 관리에 대비](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)합니다.


### <a name="quick-test"></a>빠른 테스트

제거 시뮬레이션의 사용 방법을 간략히 테스트하기 위해, 예약된 이벤트 서비스를 쿼리하여 Azure CLI를 통해 제거를 시뮬레이션하면 어떻게 되는지 확인하는 방법을 살펴보겠습니다.

예약된 이벤트는 이벤트에 대한 요청을 처음 수행하는 서비스에 대해 사용할 수 있습니다. 

VM에 원격으로 연결한 다음 명령 프롬프트를 엽니다. 

VM의 명령 프롬프트에서 다음을 입력합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

이 첫 번째 응답은 최대 2분 정도 걸릴 수 있습니다. 지금부터 출력이 거의 즉시 표시되어야 합니다.

Azure CLI가 설치된 컴퓨터(예: 로컬 머신)에서 [az vm simulate-eviction](/cli/azure/vm#az_vm_simulate_eviction)을 사용하여 제거를 시뮬레이션합니다. 리소스 그룹 이름 및 VM 이름을 고유의 이름으로 바꿉니다. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

요청이 성공적으로 수행되면 응답 출력에 `Status: Succeeded`가 나타납니다.

신속하게 Spot Virtual MachineS에 대한 원격 연결로 돌아가서 Scheduled Events 엔드포인트를 다시 쿼리합니다. 추가 정보를 포함하는 출력이 반환될 때까지 다음 명령을 반복합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

예약된 이벤트 서비스에 제거 알림이 수신되면 다음과 유사한 응답이 반환됩니다.

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

`"EventType":"Preempt"`가 있으며 리소스는 VM 리소스 `"Resources":["myspotvm"]`입니다. 

`"NotBefore"`를 선택하여 VM이 제거되는 경우를 확인할 수도 있습니다. 애플리케이션이 정상적으로 종료될 수 있는 기간에 해당하는 지정된 시간 전에는 VM이 제거되지 않습니다.


## <a name="next-steps"></a>다음 단계

[Azure PowerShell](../windows/spot-powershell.md), [Portal](../spot-portal.md) 또는 [템플릿](spot-template.md)을 사용하여 Azure Spot Virtual Machines를 만들 수도 있습니다.

Azure Spot Virtual Machines에 대한 자세한 내용은 [Azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices)를 사용하여 현재 가격 책정 정보를 쿼리하세요. `meterName`과 `skuName`에는 둘 다 `Spot`이 포함됩니다.

오류가 발생하는 경우 [오류 코드](../error-codes-spot.md)를 참조하세요.