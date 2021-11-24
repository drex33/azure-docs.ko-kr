---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Private Link 서비스 만들기
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Private Link 서비스를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.custom: mode-api
ms.openlocfilehash: a772f434325eb7d5625eea52136a107ee45306cc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055690"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Private Link 서비스 만들기

서비스를 참조하는 Private Link 서비스 만들기를 시작합니다.  Azure 표준 Load Balancer 배후에 배포된 서비스 또는 리소스에 대한 Private Link 액세스를 제공합니다.  서비스 사용자는 가상 네트워크에서 비공개로 액세스할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- 이 빠른 시작에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

* 이름을 **CreatePrivLinkService-rg** 로 지정합니다. 
* 위치: **eastus**

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>내부 부하 분산 장치 만들기

이 섹션에서는 가상 네트워크 및 내부 Azure Load Balancer를 만듭니다.

### <a name="virtual-network"></a>가상 네트워크

이 섹션에서는 Private Link 서비스에 액세스하는 부하 분산 장치를 호스팅할 가상 네트워크와 서브넷을 만듭니다.

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.1.0.0/16** 의 주소 접두사.
* 이름이 **mySubnet** 인 서브넷.
* **10.1.0.0/24** 의 서브넷 접두사.
* **CreatePrivLinkService-rg** 리소스 그룹에 있습니다.
* 위치: **eastus2**.
* 서브넷에서 프라이빗 링크 서비스에 대한 네트워크 정책을 사용하지 않도록 설정합니다.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

프라이빗 링크 서비스 네트워크 정책을 사용하지 않도록 서브넷을 업데이트하려면 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용합니다.

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

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
* 가상 네트워크 **myVNet** 과 연결됩니다.
* 백 엔드 서브넷 **mySubnet** 과 연결됩니다.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
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
    --resource-group CreatePrivLinkService-rg \
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
    --resource-group CreatePrivLinkService-rg \
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

## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기

이 섹션에서는 이전 단계에서 만든 Azure Load Balancer를 사용하는 프라이빗 링크 서비스를 만듭니다.

[az network private-link-service create](/cli/azure/network/private-link-service#az_network_private_link_service_create)를 통해 표준 부하 분산 장치 프런트 엔드 IP 구성을 사용하여 프라이빗 링크 서비스를 만듭니다.

* 이름을 **myPrivateLinkService** 로 지정합니다.
* 가상 네트워크: **myVNet**
* 표준 부하 분산 장치 **myLoadBalancer** 및 프런트 엔드 구성 **myFrontEnd** 와 연결됩니다.
* **eastus2** 위치에 있습니다.
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

프라이빗 링크 서비스가 생성되고 트래픽을 수신할 수 있습니다. 트래픽 흐름을 보려면 표준 부하 분산 장치 뒤에 애플리케이션을 구성합니다.


## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 프라이빗 링크 서비스를 프라이빗 엔드포인트에 매핑합니다. 가상 네트워크는 프라이빗 링크 서비스에 대한 프라이빗 엔드포인트를 포함합니다. 이 가상 네트워크에는 프라이빗 링크 서비스에 액세스하는 리소스가 포함되어 있습니다.

### <a name="create-private-endpoint-virtual-network"></a>프라이빗 엔드포인트 가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

* 이름을 **myVNetPE** 로 지정합니다.
* **11.1.0.0/16** 의 주소 접두사.
* 이름이 **mySubnetPE** 인 서브넷.
* **11.1.0.0/24** 의 서브넷 접두사.
* **CreatePrivLinkService-rg** 리소스 그룹에 있습니다.
* 위치: **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 업데이트하려면 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용합니다.

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>엔드포인트 및 연결 만들기

* [az network private-link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show)를 사용하여 프라이빗 링크 서비스의 리소스 ID를 가져옵니다. 이 명령은 나중에 사용할 수 있도록 리소스 ID를 변수에 배치합니다.

* [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)를 사용하여 이전에 만든 가상 네트워크에 프라이빗 엔드포인트를 만듭니다.

* 이름을 **MyPrivateEndpoint** 로 지정합니다.
* **CreatePrivLinkService-rg** 리소스 그룹에 있습니다.
* 연결 이름 **myPEconnectiontoPLS**.
* 위치: **eastus2**.
* 가상 네트워크 **myVNetPE** 및 서브넷 **mySubnetPE** 에 있습니다.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 프라이빗 링크 서비스, 부하 분산 장치 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음 작업을 수행했습니다.

* 가상 네트워크 및 내부 Azure Load Balancer를 만들었습니다.
* 프라이빗 링크 서비스를 만들었습니다.

Azure 프라이빗 엔드포인트에 대해 자세히 알아보려면 계속 진행하세요.
> [!div class="nextstepaction"]
> [빠른 시작: Azure CLI를 사용하여 프라이빗 엔드포인트 만들기](create-private-endpoint-cli.md)
