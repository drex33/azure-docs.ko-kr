---
title: '자습서: Azure CLI로 Azure Kubernetes 서비스에 대한 라우팅 기본 설정 구성'
titlesuffix: Azure virtual network
description: 이 자습서를 사용하여 Azure Kubernetes 서비스에 대한 라우팅 기본 설정을 구성하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369197"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure Kubernetes 서비스에 대한 라우팅 기본 설정 구성

이 문서에서는 Azure CLI를 사용하여 Kubernetes 클러스터에 대해 ISP 네트워크(**인터넷** 옵션)를 통해 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정은 라우팅 기본 설정 유형 **Internet** 에 대한 공용 IP 주소를 만든 다음, 이를 AKS 클러스터를 만드는 동안 사용하는 방법으로 설정됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * **인터넷** 라우팅 기본 설정을 사용하여 공용 IP 주소를 만듭니다.
> * **인터넷** 라우팅 기본 설정 공용 IP로 Azure Kubernetes 클러스터를 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- 이 문서에는 Azure CLI 버전 2.0.49 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 **미국 동부** Azure 지역의 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>인터넷 라우팅 기본 설정으로 공용 IP 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 **인터넷** 유형의 라우팅 기본 설정으로 공용 IP 주소를 만듭니다.

다음 명령은 **미국 동부** Azure 지역에서 **인터넷** 라우팅 기본 설정으로 새 공용 IP를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  현재 라우팅 기본 설정은 IPV4 공용 IP 주소만 지원합니다.

## <a name="create-kubernetes-cluster-with-public-ip"></a>공용 IP를 사용하여 Kubernetes 클러스터 만들기

나중에 사용하기 위해 이전에 만든 공용 IP의 ID를 변수에 배치합니다. VM의 공용 IP 주소를 검색하려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 명령을 사용합니다.

다음 명령은 공용 IP ID를 검색하고 다음 명령에서 사용할 변수에 배치합니다.

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

[az aks create](/cli/azure/aks#az_aks_create)를 사용하여 Kubernetes 클러스터를 만듭니다.

다음 명령은 Kubernetes 클러스터를 만들고 이전 단계에서 만든 공용 IP에 대한 변수를 사용합니다.

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>AKS 클러스터를 만드는 데 몇 분이 걸립니다.

유효성을 검사하려면 Azure Portal의 이전 단계에서 만든 공용 IP를 검색합니다. 이 공용 IP는 부하 분산 장치와 연결됩니다. 부하 분산 장치는 아래와 같이 Kubernetes 클러스터와 연결됩니다.

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="AKS 클러스터 공용 IP 주소의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 공용 IP, AKS 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 라우팅 기본 설정이 혼합된 가상 머신을 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기](routing-preference-mixed-network-adapter-portal.md)