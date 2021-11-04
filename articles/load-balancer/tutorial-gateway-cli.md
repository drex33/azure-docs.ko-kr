---
title: '자습서: 게이트웨이 부하 분산 장치 - Azure CLI 만들기'
titleSuffix: Azure Load Balancer
description: 이 자습서에서는 Azure CLI를 사용하여 게이트웨이 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 13435307985f3471800a6bfc3697c7bc0fc58ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101463"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 게이트웨이 부하 분산 장치 만들기

게이트웨이 부하 분산 장치는 표준, 기본 및 게이트웨이 SKU로 구성됩니다. 게이트웨이 부하 분산 장치는 NVA(네트워크 가상 어플라이언스)를 사용자가 인식하지 못하는 새 삽입하는 데 사용됩니다. NVA의 고성능 및 높은 확장성이 필요한 시나리오의 경우 게이트웨이 부하 분산 장치를 사용합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 미리 보기 기능을 등록합니다.
> * 가상 네트워크를 만듭니다.
> * 네트워크 보안 그룹을 만듭니다.
> * 게이트웨이 부하 분산 장치를 만듭니다.
> * 부하 분산 장치 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다.

> [!IMPORTANT]
> 현재 Azure 게이트웨이 부하 분산 장치는 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- 기존 공용 표준 SKU Azure Load Balancer. 부하 분산 장치 만들기에 대한 자세한 내용은 **[Azure CLI 사용하여 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-cli.md)** 를 참조하세요.
    - 이 자습서에서는 예제의 기존 부하 분산 장치 이름을 **myLoadBalancer** 로 지정합니다.

## <a name="register-preview-feature"></a>미리 보기 기능 등록

게이트웨이 부하 분산 장치의 공개 미리 보기의 일부로 공급자를 Azure 구독에 등록해야 합니다.

[az feature register](/cli/azure/feature#az_feature_register)를 사용하여 **AllowGatewayLoadBalancer** 공급자 기능을 등록합니다.

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

[az provider register](/cli/azure/provider#az_provider_register)를 사용하여 **Microsoft.Network** 리소스 공급자를 등록합니다.

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name TutorGwLB-rg \
    --location eastus

```

## <a name="configure-virtual-network"></a>가상 네트워크 구성

가상 네트워크는 게이트웨이 부하 분산 장치의 백 엔드 풀에 있는 리소스에 필요합니다.  

### <a name="create-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --resource-group TutorGwLB-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-bastion-public-ip-address"></a>베스천 공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 Azure Bastion 호스트에 대한 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorGwLB-rg \
    --name myBastionIP \
    --sku Standard \
    --zone 1 2 3
```

### <a name="create-bastion-subnet"></a>배스천 서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 베스천 서브넷을 만듭니다.

```azurecli-interactive
az network vnet subnet create \
    --resource-group TutorGwLB-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/27
```

### <a name="create-bastion-host"></a>베스천 호스트 만들기

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 가상 네트워크의 리소스를 안전하게 관리하기 위한 배스천 호스트를 배포합니다.

```azurecli-interactive
az network bastion create \
    --resource-group TutorGwLB-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="configure-nsg"></a>NSG 구성

다음 예제를 사용하여 네트워크 보안 그룹을 만듭니다. 이전에 만든 가상 네트워크의 네트워크 트래픽에 필요한 NSG 규칙을 구성합니다.

### <a name="create-nsg"></a>NSG 만들기

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 NSG를 만듭니다.

```azurecli-interactive
  az network nsg create \
    --resource-group TutorGwLB-rg \
    --name myNSG
```

### <a name="create-nsg-rules"></a>NSG 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 NSG 규칙을 만듭니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100

  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll-TCP-Out \
    --protocol 'TCP' \
    --direction outbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100
```

## <a name="configure-gateway-load-balancer"></a>게이트웨이 부하 분산 장치 구성

이 섹션에서는 구성을 만들고 게이트웨이 부하 분산 장치를 배포합니다.  

### <a name="create-gateway-load-balancer"></a>게이트웨이 부하 분산 장치 만들기

[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 부하 분산 장치를 만듭니다.

```azurecli-interactive
  az network lb create \
    --resource-group TutorGwLB-rg \
    --name myLoadBalancer-gw \
    --sku Gateway \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --backend-pool-name myBackendPool \
    --frontend-ip-name myFrontEnd
```

### <a name="create-tunnel-interface"></a>터널 인터페이스 만들기

내부 인터페이스는 **`--identifier`** 가 **900** 이고, **`--port`** 가 **10800** 인 Azure CLI를 사용하여 자동으로 만들어집니다.

[az network lb address-pool tunnel-interface add](/cli/azure/network/lb/address-pool/tunnel-interface#az_network_lb_address_pool_tunnel_interface_add)를 사용하여 부하 분산 장치의 외부 터널 인터페이스를 만듭니다. 

```azurecli-interactive
  az network lb address-pool tunnel-interface add \
    --address-pool myBackEndPool \
    --identifier '901' \
    --lb-name myLoadBalancer-gw \
    --protocol VXLAN \
    --resource-group TutorGwLB-rg \
    --type External \
    --port '10801'
```

### <a name="create-health-probe"></a>상태 프로브 만들기
부하 분산 장치에서 백 엔드 인스턴스의 상태를 모니터링하려면 상태 프로브가 필요합니다. TCP 상태 프로브를 만들려면 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용합니다.

```azurecli-interactive
  az network lb probe create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myHealthProbe \
    --protocol http \
    --port 80 \
    --path '/' \
    --interval '5' \
    --threshold '2'
    
```

### <a name="create-load-balancing-rule"></a>부하 분산 규칙 만들기

백 엔드 인스턴스로 향하는 트래픽은 부하 분산 규칙을 통해 라우팅됩니다. [az network lb rule create](/cli/azure/network/lb/probe#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

```azurecli-interactive
  az network lb rule create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myLBRule \
    --protocol All \
    --frontend-port 0 \
    --backend-port 0 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>게이트웨이 부하 분산 장치 백 엔드 풀에 네트워크 가상 어플라이언스 추가
Azure Marketplace 통해 NLA를 배포합니다. 배포되면 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>게이트웨이 부하 분산 장치에 부하 분산 장치 프런트 엔드 연결

이 예제에서는 표준 부하 분산 장치의 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다. 

구독에 있는 기존 부하 분산 장치의 프런트 엔드 IP에 프런트 엔드를 추가합니다.

[az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_az_network_lb_frontend_ip_show)를 사용하여 게이트웨이 부하 분산 장치 프런트 엔드의 리소스 ID를 변수에 배치합니다.

[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update)를 사용하여 게이트웨이 부하 분산 장치 프런트 엔드를 기존 부하 분산 장치에 연결합니다.

```azurecli-interactive
  feid=$(az network lb frontend-ip show \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myFrontend \
    --query id \
    --output tsv)

  az network lb frontend-ip update \
    --resource-group CreatePubLBQS-rg \
    --name myFrontendIP \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIP \
    --gateway-lb $feid

```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 남아 있는 리소스를 제거할 수 있습니다.

```azurecli-interactive
  az group delete \
    --name TutorGwLB-rg
```

## <a name="next-steps"></a>다음 단계

Azure에서 네트워크 가상 어플라이언스를 만듭니다. 

NVA를 만들 때 이 자습서에서 만든 리소스를 선택합니다.

* 가상 네트워크

* 서브넷

* 네트워크 보안 그룹

* 게이트웨이 부하 분산 장치

영역 간 Azure Load Balancer를 만드는 방법에 대해 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [영역 간 부하 분산 장치](tutorial-cross-region-powershell.md)
