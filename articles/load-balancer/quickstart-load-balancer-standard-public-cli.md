---
title: '빠른 시작: 공용 부하 분산 장치 만들기 - Azure CLI'
titleSuffix: Azure Load Balancer
description: 이 빠른 시작은 Azure CLI를 사용하여 공용 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli, mode-other
ms.openlocfilehash: d7d6f08143d859f7c19c2f664c7eb73eb712c62c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074434"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기

Azure CLI에서 Azure Load Balancer를 시작하여 공용 부하 분산 장치와 세 개의 가상 머신을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

* 이름을 **CreatePubLBQS-rg** 로 지정합니다. 
* 위치: **eastus**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="빠른 시작을 위해 만든 표준 부하 분산 장치 리소스." border="false":::

## <a name="configure-virtual-network---standard"></a>가상 네트워크 구성 - 표준

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.1.0.0/16** 의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.1.0.0/24** 의 서브넷 접두사.
* **CreatePubLBQS-rg** 리소스 그룹에서
* 위치: **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CCreatePubLBQS-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>베스천 서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 베스천 서브넷을 만듭니다.

* 이름은 **AzureBastionSubnet** 입니다.
* **10.1.1.0/24** 의 주소 접두사.
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreatePubLBQS-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>베스천 호스트 만들기

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 베스천 호스트를 만듭니다.

* 이름은 **myBastionHost** 입니다.
* **CreatePubLBQS-rg** 에서
* 공용 IP **myBastionIP** 와 연결됩니다.
* 가상 네트워크 **myVNet** 과 연결됩니다.
* 위치: **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 네트워크 보안 그룹에 속한 네트워크 인터페이스가 백 엔드 주소의 VM에 있어야 합니다. 

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹 **CreatePubLBQS-rg** 에서

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 원본: **(*)** .
* 대상: **(*)** .
* 대상 포트: **포트 80**.
* 액세스: **허용**
* 우선 순위: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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

## <a name="create-backend-servers---standard"></a>백 엔드 서버 만들기 - 표준

이 섹션에서는 다음을 만듭니다.

* 가상 머신에 대한 세 개의 네트워크 인터페이스.
* 부하 분산 장치의 백 엔드 서버로 사용할 가상 머신 세 개

### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 세 개의 네트워크 인터페이스를 만듭니다.

* 이름은 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 입니다.
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다.

### <a name="vm1"></a>VM1
* 이름: **myVM1**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 1**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* 이름: **myVM2**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM2** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 2**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 이름: **myVM3**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM3** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 3**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>공용 IP 주소 만들기 - 표준

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 다음을 수행합니다.

* **myPublicIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreatePubLBQS-rg** 에서

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

영역 1에서 공용 IP 주소를 만들려면:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* **myFrontEnd** 라는 프런트 엔드 풀
* **myBackEndPool** 이라는 백 엔드 풀
* 이전 단계에서 만든 공용 IP 주소 **myPublicIP** 에 연결됨 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용하여 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe** 를 사용합니다.
* 프로토콜: **TCP**
* **15분** 의 유휴 제한 시간.
* TCP 재설정을 활성화합니다.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀에 가상 머신 추가

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다.

* 백 엔드 주소 풀 **myBackEndPool** 에 있습니다.
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* **myLoadBalancer** 를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>아웃바운드 규칙 구성 만들기
부하 분산 디바이스 아웃바운드 규칙은 백 엔드 풀의 VM에 대한 아웃바운드 SNAT를 구성합니다. 

아웃바운드 연결에 대한 자세한 내용은 [Azure의 아웃바운드 연결](load-balancer-outbound-connections.md)을 참조하세요.

아웃바운드 구성에 공용 IP 또는 접두사를 사용할 수 있습니다.

### <a name="public-ip"></a>공용 IP

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 아웃바운드 연결에 대한 단일 IP를 만듭니다.  

* 이름: **myPublicIPOutbound**
* **CreatePubLBQS-rg** 에서

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

영역 1에서 영역 중복 공용 IP 주소를 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>공용 IP 접두사

[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)를 사용하여 아웃바운드 연결에 대한 공용 IP 접두사를 만듭니다.

* 이름: **myPublicIPPrefixOutbound**
* **CreatePubLBQS-rg** 에서
* 접두사 길이: **28**

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
영역 1에서 영역 중복 공용 IP 접두사를 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

아웃바운드 NAT 및 아웃바운드 연결 스케일링에 대한 자세한 내용은 [여러 IP 주소가 있는 아웃바운드 NAT 스케일링](load-balancer-outbound-connections.md)을 참조하세요.

### <a name="create-outbound-frontend-ip-configuration"></a>아웃바운드 프런트 엔드 IP 구성 만들기

[az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)를 사용하여 다음과 같은 새 프런트 엔드 IP 구성을 만듭니다.

이전 단계에서 선택한 내용에 따라 공용 IP 또는 공용 IP 접두사 명령을 선택합니다.

#### <a name="public-ip"></a>공용 IP

* 이름: **myFrontEndOutbound**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 공용 IP 주소 **myPublicIPOutbound** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>공용 IP 접두사

* 이름: **myFrontEndOutbound**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 공용 IP 접두사 **myPublicIPPrefixOutbound** 에 연결됨
* 부하 분산 장치 **myLoadBalancer** 에 연결됨

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>아웃바운드 풀 만들기

[az network lb address pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)를 사용하여 새 아웃바운드 풀을 만듭니다.

* 이름: **myBackEndPoolOutbound**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 부하 분산 장치 **myLoadBalancer** 에 연결됨

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>아웃바운드 규칙 만들기

[az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create)를 사용하여 아웃바운드 백 엔드 풀에 대한 새 아웃바운드 규칙을 만듭니다.

* 이름: **myOutboundRule**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 부하 분산 장치 **myLoadBalancer** 에 연결됨
* 프런트 엔드 **myFrontEndOutbound** 에 연결됨
* 프로토콜: **모두**
* 유휴 시간 제한: **15**
* 아웃바운드 포트: **10000**
* 백 엔드 풀 **myBackEndPoolOutbound** 에 연결됨

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>아웃바운드 풀에 가상 머신 추가

[az network nic ip config address pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 아웃바운드 풀에 가상 머신을 추가합니다.


* 백 엔드 주소 풀: **myBackEndPoolOutbound**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* **myLoadBalancer** 를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="빠른 시작에서 만든 기본 부하 분산 장치 리소스." border="false":::

## <a name="configure-virtual-network---basic"></a>가상 네트워크 구성 - 기본

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.1.0.0/16** 의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.1.0.0/24** 의 서브넷 접두사.
* **CreatePubLBQS-rg** 리소스 그룹에서
* 위치: **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreatePubLBQS-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>베스천 서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 베스천 서브넷을 만듭니다.

* 이름은 **AzureBastionSubnet** 입니다.
* **10.1.1.0/24** 의 주소 접두사.
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreatePubLBQS-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>베스천 호스트 만들기

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 베스천 호스트를 만듭니다.

* 이름은 **myBastionHost** 입니다.
* **CreatePubLBQS-rg** 에서
* 공용 IP **myBastionIP** 와 연결됩니다.
* 가상 네트워크 **myVNet** 과 연결됩니다.
* 위치: **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 네트워크 보안 그룹에 속한 네트워크 인터페이스가 백 엔드 주소의 VM에 있어야 합니다. 

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹 **CreatePubLBQS-rg** 에서

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 원본: **(*)** .
* 대상: **(*)** .
* 대상 포트: **포트 80**.
* 액세스: **허용**
* 우선 순위: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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

## <a name="create-backend-servers---basic"></a>백 엔드 서버 만들기 - 기본

이 섹션에서는 다음을 만듭니다.

* 가상 머신에 대한 세 개의 네트워크 인터페이스.
* 가상 머신에 대한 가용성 집합
* 부하 분산 장치의 백 엔드 서버로 사용할 가상 머신 세 개


### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](/cli/azure/network/nic#az_network_nic_create)를 사용하여 세 개의 네트워크 인터페이스를 만듭니다.


* 이름은 **myNicVM1**, **myNicVM2** 및 **myNicVM3** 입니다.
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>가상 머신에 대한 가용성 집합 만들기

[az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)를 사용하여 가용성 집합을 만듭니다.

* 이름: **myAvSet**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 위치: **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다.

### <a name="vm1"></a>VM1
* 이름: **myVM1**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM1** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 1**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* 이름: **myVM2**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM2** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 2**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 이름: **myVM3**
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 네트워크 인터페이스 **myNicVM3** 에 연결됨
* 가상 머신 이미지 **win2019datacenter**.
* 위치: **영역 3**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>공용 IP 주소 만들기 - 기본

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 다음을 수행합니다.

* **myPublicIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreatePubLBQS-rg** 에서

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](/cli/azure/network/lb#az_network_lb_create)를 사용하여 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* **myFrontEnd** 라는 프런트 엔드 풀
* **myBackEndPool** 이라는 백 엔드 풀
* 이전 단계에서 만든 공용 IP 주소 **myPublicIP** 에 연결됨 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)를 사용하여 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd** 의 **포트 80** 에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool** 에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe** 를 사용합니다.
* 프로토콜: **TCP**
* **15분** 의 유휴 제한 시간.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
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

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀에 가상 머신 추가

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다.

* 백 엔드 주소 풀 **myBackEndPool** 에 있습니다.
* 리소스 그룹 **CreatePubLBQS-rg** 에서
* 부하 분산 장치 **myLoadBalancer** 에 연결됨

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

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
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

부하 분산 장치의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 명령을 사용합니다. 

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="부하 분산 장치 테스트" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음 작업을 수행했습니다.

* 표준 또는 공용 부하 분산 장치를 만들었습니다.
* 가상 머신을 연결했습니다. 
* 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성했습니다.
* 부하 분산 장치를 테스트했습니다.

Azure Load Balancer에 대해 자세히 알아보려면 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure Load Balancer란?](load-balancer-overview.md)
