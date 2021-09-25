---
title: 공용 IP 주소가 없는 Azure Batch 풀 만들기
description: 공용 IP 주소가 없는 풀을 만드는 방법에 대해 알아봅니다.
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: c229bd53dffa079b32d41f52b8450616e55498fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665590"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>공용 IP 주소가 없는 Azure Batch 풀 만들기

Azure Batch 풀을 만들 때 공용 IP 주소를 사용하지 않고 가상 머신 구성 풀을 프로비저닝 할 수 있습니다. 이 문서에서는 공용 IP 주소를 사용하지 않고 Batch 풀을 설정하는 방법을 설명합니다.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>공용 IP 주소가 없는 풀을 사용해야 하는 이유는 무엇인가요?

기본값으로 Azure Batch 가상 머신 구성 풀의 모든 컴퓨팅 노드에는 공용 IP 주소가 할당됩니다. 이 주소는 일괄 처리 서비스에서 작업을 예약하고 인터넷에 대한 아웃 바운드 액세스를 비롯하여 계산 노드와 통신하는 데 사용됩니다.

이러한 노드에 대한 액세스를 제한하고 인터넷에서 이러한 노드의 검색 가능성을 줄이려면 공용 IP 주소 없이 풀을 프로비저닝 할 수 있습니다.

> [!IMPORTANT]
> Azure Batch 공용 IP 주소가 없는 풀에 대한 지원은 현재 프랑스 중부, 동아시아, 미국 중서부, 미국 중남부, 미국 서부 2, 미국 동부, 북유럽, 미국 동부 2, 미국 중부, 서유럽, 미국 중북부, 미국 서부, 오스트레일리아 동부, 일본 동부, 일본 서부에 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **인증**. [가상 네트워크](./batch-virtual-network.md) 내에 공용 IP 주소가 없는 풀을 사용하려면 Batch 클라이언트 API에서 Azure AD(Active Directory) 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다. 가상 네트워크 내에 풀을 만들지 않는 경우 Azure AD 인증 또는 키 기반 인증을 사용할 수 있습니다.

- **Azure VNet**. [가상 네트워크](batch-virtual-network.md)에서 풀을 만드는 경우 다음 요구 사항 및 구성을 따릅니다. 하나 이상의 서브넷으로 VNet을 미리 준비하기 위해 Azure Portal, Azure PowerShell, Azure CLI(명령줄 인터페이스) 또는 기타 방법을 사용할 수 있습니다.

  - VNet은 풀을 만들 때 사용한 Batch 계정과 동일한 구독 및 지역에 있어야 합니다.

  - 풀에 대해 지정한 서브넷에는 풀에 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 즉 풀의 `targetDedicatedNodes` 및 `targetLowPriorityNodes` 속성 합계입니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 풀은 컴퓨팅 노드를 부분적으로 할당하고 크기 조정 오류를 반환합니다.

  - 프라이빗 링크 서비스 및 엔드포인트 네트워크 정책을 사용하지 않도록 설정해야 합니다. 이 작업은 Azure CLI를 사용하여 수행할 수 있습니다. 

    `az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resource-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies`

> [!IMPORTANT]
> Batch는 100개 전용 노드 또는 우선 순위가 낮은 노드 각각에 대해 프라이빗 링크 서비스 및 부하 분산 장치 하나를 할당합니다. 이러한 리소스는 구독의 [리소스 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 대형 풀의 경우 이러한 리소스 중 하나 이상에 대한 [할당량을 늘리도록 요청](batch-quota-limit.md#increase-a-quota)해야 할 수 있습니다. 또한 Batch에서 만든 리소스에는 리소스 잠금을 적용하지 않아야 합니다. 적용하는 경우 풀 삭제 또는 제로로 크기 조정과 같이 사용자가 시작한 작업으로 인해 리소스가 정리되지 않습니다.

## <a name="current-limitations"></a>현재 제한 사항

1. 공용 IP 주소가 없는 풀은 Cloud Services 구성이 아니라 가상 머신 구성을 사용해야 합니다.
1. 공용 IP 주소가 없는 풀에서는 Batch 컴퓨팅 노드에 대한 [사용자 지정 엔드포인트 구성](pool-endpoint-configuration.md)이 작동하지 않습니다.
1. 공용 IP 주소가 없기 때문에 이 유형의 풀에서 [사용자 고유의 지정된 공용 IP 주소를 사용](create-pool-public-ip.md)할 수 없습니다.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Azure Portal에 공용 IP 주소가 없는 풀 만들기

1. Azure Portal에서 Batch 계정으로 이동합니다.
1. 왼쪽의 **설정** 창에서 **풀** 을 선택합니다.
1. **풀** 창에서 **추가** 명령을 선택합니다.
1. **풀 추가** 창의 **이미지 형식** 드롭다운에서 사용할 옵션을 선택합니다.
1. 이미지에 올바른 **게시자/제품/SKU** 를 선택합니다.
1. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.
1. 사용하려는 가상 네트워크와 서브넷을 선택합니다. 이 가상 네트워크는 만들려는 풀과 동일한 리소스 그룹에 있어야 합니다.
1. **IP 주소 프로비저닝 유형** 에서 **NoPublicIPAddresses** 를 선택합니다.

![NoPublicIPAddresses가 선택된 풀 추가 화면의 스크린샷](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Batch REST API를 사용하여 공용 IP 주소가 없는 풀을 만듭니다.

아래 예제에서는 [Azure Batch REST API](/rest/api/batchservice/pool/add)를 사용하여 공용 IP 주소를 사용하는 풀을 만드는 방법을 보여 줍니다.

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>요청 본문

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>인터넷에 대한 아웃바운드 액세스

공용 IP 주소가 없는 풀에서는 [가상 네트워크 NAT](../virtual-network/nat-gateway/nat-overview.md)를 사용하는 등의 방법으로 네트워크 설정을 적절하게 구성하지 않는 한, 가상 머신에서 퍼블릭 인터넷에 액세스할 수 없습니다. NAT는 가상 네트워크의 가상 머신에서 인터넷에 대한 아웃바운드 액세스만 허용합니다. Batch로 만들어진 컴퓨팅 노드는 연결된 공용 IP 주소가 없기 때문에 퍼블릭으로 액세스할 수 없습니다.

아웃바운드 연결을 제공하는 또 다른 방법은 UDR(사용자 정의 경로)를 사용하는 것입니다. 이렇게 하면 퍼블릭 인터넷에 액세스할 수 있는 프록시 머신으로 트래픽의 경로를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크에서 풀 만들기](batch-virtual-network.md)에 대한 자세한 정보를 알아봅니다.
- [Batch 계정으로 프라이빗 엔드포인트를 사용](private-connectivity.md)하는 방법을 알아봅니다.
