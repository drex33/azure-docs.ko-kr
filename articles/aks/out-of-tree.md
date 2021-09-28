---
title: 클라우드 컨트롤러 관리자 사용 (미리 보기)
description: 트리 외부 클라우드 공급자를 사용 하도록 설정 하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 98f8fe2e00a7671078da7dae2174401b6c33fd30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667619"
---
# <a name="enable-cloud-controller-manager-preview"></a>클라우드 컨트롤러 관리자 사용 (미리 보기)

클라우드 공급자는 Microsoft Azure Kubernetes 커뮤니티와 긴밀 하 게 협력 하 여 사용자를 대신 하 여 인프라를 지원 합니다.

현재 Kubernetes 내의 클라우드 공급자 통합은 "트리 내" 이며, 클라우드 관련 기능에 대 한 모든 변경 내용은 표준 Kubernetes 릴리스 주기를 따라야 합니다.  문제를 찾거나 수정 하거나 향상 된 기능을 출시 해야 하는 경우 Kubernetes 커뮤니티의 릴리스 주기 내에서이 작업을 수행 해야 합니다.

Kubernetes 커뮤니티는 이제 클라우드 공급자가 [클라우드 공급자-azure][cloud-provider-azure] 구성 요소를 통해 핵심 Kubernetes 릴리스 일정과 별개로 릴리스를 제어 하는 "트리 외부" 모델을 채택 하 고 있습니다.  Kubernetes 버전 1.21 이상에서는 CSI (Cloud Storage Interface) 드라이버가 기본값으로 이미 출시 되었습니다.

> [!Note]
> AKS 클러스터에서 클라우드 컨트롤러 관리자를 사용 하도록 설정 하는 경우 트리 CSI 드라이버를 사용할 수 있습니다.

클라우드 컨트롤러 관리자는 AKS에서 지 원하는 Kubernetes 1.22의 기본 컨트롤러가 됩니다.


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

* Azure CLI
* `aks-preview` 확장 버전 0.5.5 이상
* Kubernetes 버전 1.20 이상


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>`EnableCloudControllerManager`기능 플래그 등록

클라우드 컨트롤러 관리자 기능을 사용 하려면 `EnableCloudControllerManager` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다. 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
[az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>클라우드 컨트롤러 관리자를 사용 하 여 AKS 클러스터 만들기

클라우드 컨트롤러 관리자를 사용 하 여 클러스터를 만들려면 `EnableCloudControllerManager=True` Azure CLI를 사용 하 여 AZURE API에 고객 헤더로 전달 합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>AKS 클러스터를 클라우드 컨트롤러 관리자로 업그레이드

클라우드 컨트롤러 관리자를 사용 하도록 클러스터를 업그레이드 하려면 Azure CLI을 `EnableCloudControllerManager=True` 사용 하 여 AZURE API에 고객 헤더로 전달 합니다.

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>다음 단계

- CSI 드라이버 및 1.21 위의 Kubernetes 버전에 대 한 기본 동작에 대 한 자세한 내용은 [설명서][csi-docs]를 참조 하세요.

- [커뮤니티 블로그 게시물][community-blog]에서 트리 공급자와 관련 하 여 Kubernetes 커뮤니티 방향에 대 한 자세한 정보를 찾을 수 있습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure
