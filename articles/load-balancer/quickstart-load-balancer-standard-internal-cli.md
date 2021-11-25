---
title: '빠른 시작: 내부 부하 분산 장치 만들기 - Azure CLI'
titleSuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure CLI를 사용하여 내부 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli, mode-other
ms.openlocfilehash: 74d15f974bdb9091f14e15503dae5615bbe3afd8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074595"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 내부 부하 분산 장치 만들기

Azure CLI를 사용하여 내부 부하 분산 장치와 세 개의 가상 머신을 만들어 Azure Load Balancer를 시작합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

이 빠른 시작에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 리소스 그룹 이름을 **CreateIntLBQS-rg** 로 지정하고 위치를 **eastus** 로 지정합니다.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

이 섹션에서는 가상 머신의 부하를 분산하는 부하 분산 장치를 만듭니다. 내부 부하 분산 장치를 만들 때 가상 네트워크는 부하 분산 장치의 네트워크로 구성됩니다. 다음 다이어그램은 이 빠른 시작에서 만든 리소스를 보여줍니다.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="빠른 시작을 위해 만든 표준 부하 분산 장치 리소스." border="false":::

### <a name="configure-the-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 배포하기 전에 지원되는 가상 네트워크 리소스부터 만듭니다.

#### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음을 지정합니다.

* **myVNet** 으로 이름 지정
* **10.1.0.0/16** 의 주소 접두사
* **myBackendSubnet** 으로 서브넷 이름 지정
* **10.1.0.0/24** 의 서브넷 접두사
* **CreateIntLBQS-rg** 리소스 그룹에서
* **eastus** 위치

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 Azure Bastion 호스트에 대한 공용 IP 주소를 만듭니다. 다음을 지정합니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreateIntLBQS-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure Bastion 서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 서브넷을 만듭니다. 다음을 지정합니다.

* **AzureBastionSubnet** 으로 이름 지정
* **10.1.1.0/24** 의 주소 접두사
* 가상 네트워크 **myVNet** 에서
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion 호스트 만들기

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 호스트를 만듭니다. 다음을 지정합니다.

* **myBastionHost** 로 이름 지정
* **CreateIntLBQS-rg** 에서
* 공용 IP **myBastionIP** 와 연결됨
* 가상 네트워크 **myVNet** 과 연결됨
* **eastus** 위치에서

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 VM에 네트워크 보안 그룹에 속한 네트워크 인터페이스가 있는지 확인합니다. [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음을 지정합니다.

* **myNSG** 로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음을 지정합니다.

* **myNSGRuleHTTP** 로 이름 지정
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 프로토콜 **(*)**
* 방향 **인바운드**
* 원본 **(*)**
* 대상 **(*)**
* 대상 포트 **포트 80**
* 액세스 **허용**
* 우선 순위 **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-back-end-servers"></a>백 엔드 서버 만들기

이 섹션에서는 다음을 만듭니다.

* 가상 머신에 대한 세 개의 네트워크 인터페이스.
* 부하 분산 장치의 서버로 사용할 가상 머신 세 개.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 세 개의 네트워크 인터페이스를 만듭니다. 다음을 지정합니다.

* **myNicVM1**, **myNicVM2**, **myNicVM3** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 가상 네트워크 **myVNet** 에서
* 서브넷 **myBackendSubnet** 에서
* 네트워크 보안 그룹 **myNSG** 에서

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다. 다음을 지정합니다.

* **myVM1**, **myVM2**, **myVM3** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**
* **영역 1**, **영역 2** 및 **영역 3** 에서

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

* 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 IP 풀.
* 첫 번째 풀이 부하 분산된 네트워크 트래픽을 보내는 두 번째 IP 풀.
* VM 인스턴스의 상태를 확인하는 상태 프로브.
* 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

#### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 공용 부하 분산 장치를 만듭니다. 다음을 지정합니다.

* **myLoadBalancer** 로 이름 지정
* **myFrontEnd** 로 풀 이름 지정
* **myBackEndPool** 로 풀 이름 지정
* 가상 네트워크 **myVNet** 과 연결됨
* 서브넷 **myBackendSubnet** 과 연결됨

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용하여 상태 프로브를 만듭니다. 다음을 지정합니다.

* 가상 머신의 상태 모니터링
* **myHealthProbe** 로 이름 지정
* 프로토콜 **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 IP 구성.
* 트래픽을 수신할 IP 풀.
* 필요한 원본 및 대상 포트 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음을 지정합니다.

* **myHTTPRule** 로 이름 지정
* 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80** 을 사용하여 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽 보내기 
* 상태 프로브 **myHealthProbe** 사용
* 프로토콜 **TCP**
* **15분** 의 유휴 제한 시간
* TCP 재설정 사용

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>VM을 부하 분산 장치에 추가

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다. 다음을 지정합니다.

* 주소 풀 **myBackEndPool** 에서
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 과 연결됨
* 부하 분산 장치 **myLoadBalancer** 와 연결됨

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

이 섹션에서는 가상 머신의 부하를 분산하는 부하 분산 장치를 만듭니다. 내부 부하 분산 장치를 만들 때 가상 네트워크는 부하 분산 장치의 네트워크로 구성됩니다. 다음 다이어그램은 이 빠른 시작에서 만든 리소스를 보여줍니다.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="빠른 시작을 위해 만든 기본 부하 분산 장치 리소스." border="false":::

### <a name="configure-the-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 배포하기 전에 지원되는 가상 네트워크 리소스부터 만듭니다.

#### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt)를 사용하여 가상 네트워크를 만듭니다. 다음을 지정합니다.

* **myVNet** 으로 이름 지정
* **10.1.0.0/16** 의 주소 접두사
* **myBackendSubnet** 으로 서브넷 이름 지정
* **10.1.0.0/24** 의 서브넷 접두사
* **CreateIntLBQS-rg** 리소스 그룹에서
* **eastus** 위치

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 Azure Bastion 호스트에 대한 공용 IP 주소를 만듭니다. 다음을 지정합니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreateIntLBQS-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure Bastion 서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 서브넷을 만듭니다. 다음을 지정합니다.

* **AzureBastionSubnet** 으로 이름 지정
* **10.1.1.0/24** 의 주소 접두사
* 가상 네트워크 **myVNet** 에서
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion 호스트 만들기

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 호스트를 만듭니다. 다음을 지정합니다.

* **myBastionHost** 로 이름 지정
* **CreateIntLBQS-rg** 에서
* 공용 IP **myBastionIP** 와 연결됨
* 가상 네트워크 **myVNet** 과 연결됨
* **eastus** 위치에서

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 VM에 네트워크 보안 그룹에 속한 네트워크 인터페이스가 있는지 확인합니다. [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음을 지정합니다.

* **myNSG** 로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음을 지정합니다.

* **myNSGRuleHTTP** 로 이름 지정
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 프로토콜 **(*)**
* 방향 **인바운드**
* 원본 **(*)**
* 대상 **(*)**
* 대상 포트 **포트 80**
* 액세스 **허용**
* 우선 순위 **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-back-end-servers"></a>백 엔드 서버 만들기

이 섹션에서는 다음을 만듭니다.

* 가상 머신에 대한 세 개의 네트워크 인터페이스.
* 가상 머신에 대한 가용성 집합.
* 부하 분산 장치의 서버로 사용할 가상 머신 세 개.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 세 개의 네트워크 인터페이스를 만듭니다. 다음을 지정합니다.

* **myNicVM1**, **myNicVM2**, **myNicVM3** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 가상 네트워크 **myVNet** 에서
* 서브넷 **myBackendSubnet** 에서
* 네트워크 보안 그룹 **myNSG** 에서

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>가상 머신에 대한 가용성 집합 만들기

[az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)를 사용하여 가용성 집합을 만듭니다. 다음을 지정합니다.

* **myAvailabilitySet** 로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 위치 **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다. 다음을 지정합니다.

* **myVM1**, **myVM2**, **myVM3** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**
* **myAvailabilitySet** 에서


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

* 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 IP 풀.
* 첫 번째 풀이 부하 분산된 네트워크 트래픽을 보내는 두 번째 IP 풀.
* VM 인스턴스의 상태를 확인하는 상태 프로브.
* 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

#### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 공용 부하 분산 장치를 만듭니다. 다음을 지정합니다.

* **myLoadBalancer** 로 이름 지정
* **myFrontEnd** 로 풀 이름 지정
* **myBackEndPool** 로 풀 이름 지정
* 가상 네트워크 **myVNet** 과 연결됨
* 서브넷 **myBackendSubnet** 과 연결됨

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용하여 상태 프로브를 만듭니다. 다음을 지정합니다.

* 가상 머신의 상태 모니터링
* **myHealthProbe** 로 이름 지정
* 프로토콜 **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 IP 구성.
* 트래픽을 수신할 IP 풀.
* 필요한 원본 및 대상 포트 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음을 지정합니다.

* **myHTTPRule** 로 이름 지정
* 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80** 을 사용하여 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽 보내기 
* 상태 프로브 **myHealthProbe** 사용
* 프로토콜 **TCP**
* **15분** 의 유휴 제한 시간

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
#### <a name="add-vms-to-the-load-balancer-pool"></a>VM을 부하 분산 장치에 추가

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다. 다음을 지정합니다.

* 주소 풀 **myBackEndPool** 에서
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 과 연결됨
* 부하 분산 장치 **myLoadBalancer** 와 연결됨

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 네트워크 인터페이스를 만듭니다. 다음을 지정합니다.

* **myNicTestVM** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 가상 네트워크 **myVNet** 에서
* 서브넷 **myBackendSubnet** 에서
* 네트워크 보안 그룹 **myNSG** 에서

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다. 다음을 지정합니다.

* **myTestVM** 으로 이름 지정
* 리소스 그룹 **CreateIntLBQS-rg** 에서
* 네트워크 인터페이스 **myNicTestVM** 에 연결됨
* 가상 머신 이미지 **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
가상 머신이 배포될 때까지 몇 분 정도 기다려야 할 수 있습니다.

## <a name="install-iis"></a>IIS 설치

[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set)를 사용하여 가상 머신에 IIS를 설치하고 기본 웹 사이트를 컴퓨터 이름으로 설정합니다.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>테스트

1. Azure Portal에 [로그인](https://portal.azure.com)합니다.

2. **개요** 화면에서 부하 분산 장치의 개인 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스** > **모든 리소스** > **myLoadBalancer** 를 선택합니다.

3. **myLoadBalancer** 의 개요에서 **개인 IP 주소** 옆에 있는 주소를 복사합니다.

4. 왼쪽 메뉴에서 **모든 서비스** > **모든 리소스** 를 선택합니다. 리소스 목록의 **CreateIntLBQS-rg** 리소스 그룹에서 **myTestVM** 을 선택합니다.

5. **개요** 페이지에서 **연결** > **Bastion** 을 선택합니다.

6. VM을 만들 때 입력한 사용자 이름 및 암호를 입력합니다.

7. **myTestVM** 에서 **Internet Explorer** 를 엽니다.

8. 이전 단계의 IP 주소를 브라우저의 주소 표시줄에 입력합니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="브라우저의 주소 표시줄에 있는 IP 주소의 스크린샷." border="true":::
   
부하 분산 장치가 세 VM 모두에 트래픽을 분산하는 것을 확인하려면 각 VM의 IIS 웹 서버에 대한 기본 페이지를 사용자 지정할 수 있습니다. 그런 다음, 클라이언트 컴퓨터에서 웹 브라우저를 수동으로 새로 고칩니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>다음 단계

Azure Load Balancer에 대한 개요를 보세요.
> [!div class="nextstepaction"]
> [Azure Load Balancer란?](load-balancer-overview.md)
