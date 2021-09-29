---
title: '자습서: Azure CLI를 사용하여 지역 간 부하 분산 장치 만들기'
titleSuffix: Azure Load Balancer
description: Azure CLI를 사용하여 지역 간 Azure Load Balancer를 배포하는 이 자습서를 시작하세요.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: b9c96a6434712ed3179fe25c1014dc1fdc6e062d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736583"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>자습서: Azure CLI를 사용하여 지역 간 Azure Load Balancer 만들기

지역 간 부하 분산 장치는 여러 Azure 지역에서 서비스를 전역적으로 사용할 수 있도록 합니다. 한 지역이 실패하는 경우 트래픽이 다음의 가장 가까운 정상적인 지역 부하 분산 장치로 라우팅됩니다.  

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 지역 간 부하 분산 장치 만들기
> * 부하 분산 장치 규칙 만들기
> * 두 개의 지역 부하 분산 장치를 포함하는 백 엔드 풀 만들기
> * 부하 분산 장치 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 두 개의 서로 다른 Azure 지역에 배포된 백 엔드 풀을 사용하는 두 개의 **표준** SKU Azure Load Balancer
    - 지역 표준 부하 분산 장치 및 백 엔드 풀에 대한 가상 머신을 만드는 방법에 대한 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-cli.md)를 참조하세요.
        - 각 지역의 부하 분산 장치 및 가상 머신의 이름을 **-R1** 및 **-R2** 를 사용하여 추가합니다. 
- 로컬로 설치된 Azure CLI 또는 Azure Cloud Shell.

로컬로 CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure CLI에 로그인합니다.

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>지역 간 부하 분산 장치 만들기

이 섹션에서는 지역 간 부하 분산 장치, 공용 IP 주소 및 부하 분산 규칙을 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

* 이름을 **myResourceGroupLB-CR** 로 지정합니다.
* **westus** 위치에 있습니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)를 사용하여 지역 간 부하 분산 장치를 만듭니다.

* 이름을 **myLoadBalancer-CR** 로 지정합니다.
* **myFrontEnd-CR** 이라는 프런트 엔드 풀.
* **myBackEndPool-CR** 이라는 백 엔드 풀.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* 이름을 **myHTTPRule-CR** 로 지정합니다.
* 프런트 엔드 풀 **myFrontEnd-CR** 의 **포트 80** 에서 수신 대기합니다.
* **포트 80** 을 사용하여 백 엔드 주소 풀 **myBackEndPool-CR** 에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 프로토콜: **TCP**

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>백 엔드 풀 만들기

이 섹션에서는 지역 간 부하 분산 장치의 백 엔드 풀에 두 개의 지역 표준 부하 분산 장치를 추가합니다.

> [!IMPORTANT]
> 이러한 단계를 완료하려면 백 엔드 풀을 사용하는 두 개의 지역 부하 분산 장치가 구독에 배포되어 있는지 확인합니다.  자세한 내용은 **[빠른 시작: Azure CLI를 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-cli.md)** 를 참조하세요.

### <a name="add-the-regional-frontends-to-load-balancer"></a>부하 분산 장치에 지역 프런트 엔드 추가

이 섹션에서는 두 지역 부하 분산 장치 프런트 엔드의 리소스 ID를 변수에 배치합니다.  그런 다음, 변수를 사용하여 지역 간 부하 분산 장치의 백 엔드 주소 풀에 프런트 엔드를 추가합니다.

[az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show)를 사용하여 리소스 ID를 검색합니다.

[az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add)를 사용하여 지역 간 부하 분산 장치의 백 엔드 풀에 있는 변수에 배치된 프런트 엔드를 추가합니다.

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

이 섹션에서는 지역 간 부하 분산 장치를 테스트합니다. 웹 브라우저에서 공용 IP 주소에 연결합니다.  지역 부하 분산 장치 백 엔드 풀 중 하나에서 가상 머신을 중지하고 장애 조치(failover)를 관찰합니다.

1. 부하 분산 장치의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용합니다.

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

3. 지역 부하 분산 장치 중 하나의 백 엔드 풀에서 가상 머신을 중지합니다.

4. 웹 브라우저를 새로 고치고 다른 지역 부하 분산 장치에 대한 연결의 장애 조치(failover)를 관찰합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행했습니다.

* 지역 간 부하 분산 장치 만들기
* 부하 분산 규칙 만들기
* 지역 간 부하 분산 장치의 백 엔드 풀에 지역 부하 분산 장치 추가
* 부하 분산 장치를 테스트했습니다.

아래 내용에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [가용성 영역에 VM 부하 분산](./quickstart-load-balancer-standard-public-portal.md)