---
title: Azure CLI를 사용하여 Azure Kubernetes 서비스에 대한 라우팅 기본 설정 구성
titlesuffix: Azure Virtual Network
description: Azure CLI를 사용하여 라우팅 기본 설정으로 AKS 클러스터를 구성하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 491572ceaac3e4bc401d5bd6c3a5401966d0c528
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463647"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Azure CLI를 사용하여 Kubernetes 클러스터에 대한 라우팅 기본 설정 구성

이 문서에서는 Azure CLI를 사용하여 Kubernetes 클러스터에 대해 ISP 네트워크(**인터넷** 옵션)를 통해 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정은 라우팅 기본 설정 유형 **Internet** 에 대한 공용 IP 주소를 만든 다음, 이를 AKS 클러스터를 만드는 동안 사용하는 방법으로 설정됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.49 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 **미국 동부** Azure 지역의 리소스 그룹을 만듭니다.

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 **인터넷** 유형의 라우팅 기본 설정으로 공용 IP 주소를 만듭니다.

다음 명령은 **미국 동부** Azure 지역에서 **인터넷** 라우팅 기본 설정으로 새 공용 IP를 만듭니다.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  현재 라우팅 기본 설정은 IPV4 공용 IP 주소만 지원합니다.

## <a name="get-the-id-of-public-ip-address"></a>공용 IP 주소의 ID 가져오기

다음 명령은 이전 섹션에서 만든 공용 IP 주소를 반환합니다.
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>공용 ip를 사용하여 Kubernetes 클러스터 만들기

다음 명령으로 이전 섹션에서 만든 공용 IP를 사용하여 AKS 클러스터를 만듭니다.

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>AKS 클러스터를 만드는 데 몇 분이 걸립니다.

유효성을 검사하려면 Azure Portal의 이전 단계에서 만든 공용 IP를 검색하고, 아래와 같이 Kubernetes 클러스터와 연결된 부하 분산 장치와 IP가 연결된 것을 확인합니다.

 ![Kubernetes에 대한 라우팅 기본 설정 공용 IP](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>다음 단계

- [공용 IP 주소의 라우팅 기본 설정](routing-preference-overview.md)에 대해 자세히 알아봅니다. 
- [Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정을 구성합니다](configure-routing-preference-virtual-machine-cli.md).
