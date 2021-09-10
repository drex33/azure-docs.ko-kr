---
title: 가상 머신 확장 집합에서 사용한 기존 부하 분산 장치 업데이트 또는 삭제
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: 이 방법 가이드로 Azure 표준 Load Balancer와 가상 머신 확장 집합 이용을 시작해 보세요.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: f72ac3b3a799b97883586e5c2eebc4a42119ae6f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255713"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>가상 머신 확장 집합에서 사용된 부하 분산 장치 업데이트 또는 삭제

가상 머신 확장 집합과 Azure Load Balancer 인스턴스로 작업하는 경우 다음을 수행할 수 있습니다.

- 추가, 업데이트 및 삭제 규칙
- 구성 추가
- 부하 분산 장치 삭제

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>가장 머신 확장 집합을 확장하기 위한 부하 분산 장치 설정

Azure Load Balancer 인스턴스에 [인바운드 NAT 풀](/cli/azure/network/lb/inbound-nat-pool)이 설정되어 있으며, 가상 머신 확장 집합이 부하 분산 장치의 백엔드 풀에 배치되어 있는지 확인하세요. 새 가상 머신 인스턴스가 가상 머신 확장 집합에 추가되면 Load Balancer에서 인바운드 NAT 풀에 새 인바운드 NAT 규칙을 자동으로 만듭니다.

인바운드 NAT 풀이 올바르게 설정되었는지 확인하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다. 그런 다음 리소스 목록에서 **MyLoadBalancer** 를 선택합니다.
1. **설정** 에서 **인바운드 NAT 규칙** 을 선택합니다. 오른쪽 창에 가상 머신 확장 집합의 개별 인스턴스에 대해 생성된 규칙 목록이 표시되는 경우 언제든지 확장할 수 있습니다.

## <a name="add-inbound-nat-rules"></a>인바운드 NAT 규칙 추가

개별 인바운드 NAT 규칙은 추가할 수 없습니다. 그러나 가상 머신 확장 집합의 모든 인스턴스에 대해 정의된 프런트 엔드 포트 범위와 백엔드 포트를 사용하여 인바운드 NAT 규칙 집합을 추가할 수 있습니다.

가상 머신 확장 집합용 인바운드 NAT 규칙 전체 집합을 추가하려면 먼저 부하 분산 장치에 인바운드 NAT 풀을 만들어야 합니다. 그런 다음 가상 머신 확장 집합의 네트워크 프로필에서 인바운드 NAT 풀을 참조합니다. CLI를 사용하는 전체 예제가 표시되어 있습니다.

새 인바운드 NAT 풀에는 기존 인바운드 NAT 풀과 겹치는 프런트 엔드 포트 범위가 없어야 합니다. 설정된 기존 인바운드 NAT 풀을 보려면 이 [CLI 명령](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)을 사용합니다.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>인바운드 NAT 규칙 업데이트

개별 인바운드 NAT 규칙을 추가할 수 없습니다. 하지만 가상 머신 확장 집합의 모든 인스턴스에 대해 정의된 프런트 엔드 포트 범위와 백엔드 포트를 사용하여 인바운드 NAT 규칙 집합을 업데이트할 수 있습니다.

가상 머신 확장 집합에 대한 인바운드 NAT 규칙 전체 세트를 업데이트하려면 먼저 부하 분산 장치의 인바운드 NAT 풀을 업데이트합니다.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>인바운드 NAT 규칙 삭제

개별 인바운드 NAT 규칙은 삭제할 수 없지만 인바운드 NAT 풀을 삭제하여 전체 인바운드 NAT 규칙 집합을 삭제할 수 있습니다.

NAT 풀을 삭제하려면 먼저 확장 집합에서 NAT 풀을 제거합니다. CLI를 사용하는 전체 예제는 여기에 나타나 있습니다.

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       --lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>여러 IP 구성 추가

여러 IP 구성을 추가하려면 다음을 확인하십시오.

1. 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다. 그런 다음 리소스 목록에서 **MyLoadBalancer** 를 선택합니다.
1. **설정** 에서 **프런트 엔드 IP 구성** 을 선택합니다. 그런 다음, **추가** 를 선택합니다.
1. **프런트 엔드 IP 주소 추가** 페이지에서 값을 입력하고 **확인** 을 선택합니다.
1. 새 부하 분산 규칙이 필요한 경우 [Azure Load Balancer에 대한 규칙 관리 - Azure Portal](manage-rules-how-to.md)을 참조하세요.
1. 필요한 경우 새로 만든 프런트 엔드 IP 구성을 사용하여 새 인바운드 NAT 규칙 집합을 만듭니다. 예제는 이전 섹션에 나와 있습니다.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>단일 Load Balancer 뒤의 복합 Virtual Machine Scale Sets

Load Balancer에서 인바운드 NAT 풀을 만들고, 가상 머신 확장 집합의 네트워크 프로필에서 인바운드 NAT 풀을 참조하고, 마지막으로 변경 내용을 적용하기 위해 인스턴스를 업데이트합니다. 모든 Virtual Machine Scale Sets에 해당 절차를 반복합니다.

겹치지 않는 프런트 엔드 포트 범위를 사용하여 별도의 인바운드 NAT 풀을 만들어야 합니다.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용하는 프런트 엔드 IP 구성을 삭제합니다.

확장 집합에서 사용하는 프런트 엔드 IP 구성을 삭제하려면 다음을 수행합니다.

 1. 먼저 프런트 엔드 IP 구성을 참조하는 인바운드 NAT 풀(인바운드 NAT 규칙 집합)을 삭제합니다. 인바운드 규칙을 삭제하는 방법에 대한 지침은 이전 섹션에서 찾을 수 있습니다.
 1. 프런트 엔드 IP 구성을 참조하는 부하 분산 규칙을 삭제합니다.
 1. 프런트 엔드 IP 구성을 삭제합니다.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용되는 부하 분산 장치를 삭제합니다.

확장 집합에서 사용하는 프런트 엔드 IP 구성을 삭제하려면 다음을 수행합니다.

 1. 먼저 프런트 엔드 IP 구성을 참조하는 인바운드 NAT 풀(인바운드 NAT 규칙 집합)을 삭제합니다. 인바운드 규칙을 삭제하는 방법에 대한 지침은 이전 섹션에서 찾을 수 있습니다.
 1. 가상 머신 확장 집합을 포함하는 백 엔드 풀을 참조하는 부하 분산 규칙을 삭제합니다.
 1. `loadBalancerBackendAddressPool`가상 머신 확장 집합의 네트워크 프로필에서 참조를 제거합니다.
 
 CLI를 사용하는 전체 예제는 여기에 나타나 있습니다.

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
마지막으로, 부하 분산 장치 리소스를 삭제합니다.
 
## <a name="next-steps"></a>다음 단계

Azure Load Balancer 및 가상 머신 확장 집합에 대한 자세히 알아보려면 다음 개념을 참조하세요.

> [가상 머신 확장 집합과 Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)
