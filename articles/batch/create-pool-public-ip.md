---
title: 지정한 공용 IP 주소를 사용하여 풀 만들기
description: 사용자 고유의 공용 IP 주소를 사용하는 Batch 풀을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: fc652a7f4719d5cc23e84caaef63ec9d1c5f7ff1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255701"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>지정한 공용 IP 주소를 사용하여 Azure Batch 풀 만들기

Azure Batch 풀을 만들 때 지정한 [Azure VNet(가상 네트워크)의 서브넷에서 풀을 프로비저닝](batch-virtual-network.md)할 수 있습니다. Batch 풀의 가상 머신은 Batch에서 생성된 공용 IP 주소를 통해 액세스합니다. 공용 IP 주소는 풀의 수명 동안 변경될 수 있습니다. 즉, IP 주소를 새로 고치지 않으면 네트워크 설정이 오래된 상태가 될 수 있습니다.

풀의 가상 머신에 사용할 고정 공용 IP 주소 목록을 만들 수 있습니다. 이렇게 하면 공용 IP 주소 목록을 제어하고 예기치 않게 변경되지 않도록 할 수 있습니다. 이 기능은 특정 IP 주소에 대한 액세스를 제한하는 외부 서비스(예: 데이터베이스)로 작업하는 경우에 특히 유용할 수 있습니다.

공용 IP 주소가 없는 풀을 만드는 방법에 대한 자세한 내용은 [공용 IP 주소가 없는 Azure Batch 풀 만들기](./batch-pool-no-public-ip-address.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **인증**. 공용 IP 주소를 사용하려면 Batch 클라이언트 API에서 [Azure AD(Active Directory) 인증](batch-aad-auth.md)을 사용해야 합니다.

- **Azure VNet**. 풀과 IP 주소를 만드는 Azure 구독의 [가상 네트워크](batch-virtual-network.md)를 사용해야 합니다. Azure Resource Manager 기반 VNet만 사용할 수 있습니다. VNet이 [일반적인 요구 사항](batch-virtual-network.md#vnet-requirements)을 모두 충족하는지 확인합니다.

- **하나 이상의 Azure 공용 IP 주소**. 하나 이상의 공용 IP 주소를 만들려면 [Azure Portal](../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address), [Azure CLI(명령줄 인터페이스)](/cli/azure/network/public-ip#az_network_public_ip_create) 또는 [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)을 사용할 수 있습니다. 아래에 나열된 요구 사항을 준수해야 합니다.

> [!NOTE]
> Batch는 공용 IP 주소를 포함하는 리소스 그룹에 추가 네트워킹 리소스를 자동으로 할당합니다. Batch는 일반적으로 100개 전용 노드마다 NSG(네트워크 보안 그룹)와 부하 분산 장치를 하나씩 할당합니다. 리소스는 구독의 리소스 할당량으로 제한됩니다. 대형 풀을 사용하는 경우 하나 이상의 리소스에 대해 [할당량 증가를 요청](batch-quota-limit.md#increase-a-quota)해야 할 수도 있습니다.

## <a name="public-ip-address-requirements"></a>공용 IP 주소 요구 사항

공용 IP 주소를 만들 때는 다음 요구 사항을 염두에 두어야 합니다.

- 공용 IP 주소는 풀을 만들 때 사용한 Batch 계정과 동일한 구독 및 지역에 있어야 합니다.
- **IP 주소 할당** 은 **고정** 으로 설정해야 합니다.
- **SKU** 는 **표준** 으로 설정해야 합니다.
- DNS 이름을 지정해야 합니다.
- 공용 IP 주소는 가상 머신 구성 풀에만 사용해야 합니다. IP 주소를 사용하는 다른 리소스가 없어야 합니다. 그렇지 않으면 풀에서 할당 오류가 발생할 수 있습니다.
- 사용자의 공용 IP 주소 액세스를 제한하는 보안 정책 또는 리소스 잠금이 없어야 합니다.
- 풀에 대해 지정한 공용 IP 주소 개수가 풀을 대상으로 하는 VM 수를 수용할 만큼 충분히 커야 합니다. 이 값은 풀의  **targetDedicatedNodes** 및 **targetLowPriorityNodes** 속성 합계 이상이어야 합니다. IP 주소가 충분하지 않으면 풀에서 컴퓨팅 노드를 부분적으로 할당하고 크기 조정 오류가 발생합니다. 현재 Batch는 100개 VM마다 하나의 공용 IP 주소를 사용합니다.
- 항상 공용 IP 주소의 추가 버퍼를 포함합니다. 하나 이상의 추가 공용 IP 주소나 풀에 추가하는 총 공용 IP 주소의 약 10% 중에서 더 큰 개수를 추가하는 것이 좋습니다. 이 추가 버퍼는 스케일 다운 시 내부 최적화와 스케일 업 또는 스케일 다운에 실패한 후 보다 신속한 스케일 업에 도움이 됩니다.
- 풀을 만든 후에는 풀에서 사용하는 공용 IP 주소 목록을 추가하거나 변경할 수 없습니다. 목록을 수정해야 하는 경우 풀을 삭제하고 다시 만들어야 합니다.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>공용 IP 주소를 사용하여 Batch 풀 만들기

아래 예제에서는 [Azure Batch Service REST API](/rest/api/batchservice/pool/add)를 통해 공용 IP 주소를 사용하는 풀을 만드는 방법을 보여 줍니다.

### <a name="batch-service-rest-api"></a>Batch 서비스 REST API

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

요청 본문

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure 가상 네트워크의 서브넷에서 풀을 만드는 방법](batch-virtual-network.md)을 알아봅니다.
- [공용 IP 주소가 없는 Azure Batch 풀을 만드는 방법](./batch-pool-no-public-ip-address.md)을 알아봅니다.