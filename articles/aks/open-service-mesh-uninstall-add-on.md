---
title: OSM(Open Service Mesh) 추가 기능 제거
description: Azure CLI 사용하여 AKS(Azure Kubernetes Service Open Service Mesh) 배포
services: container-service
ms.topic: article
ms.date: 11/10/2021
ms.author: pgibson
ms.openlocfilehash: f5b90fc5486c660e68608aef581226c2565d63a7
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132765143"
---
# <a name="uninstall-the-open-service-mesh-osm-add-on-from-your-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터에서 OSM(Open Service Mesh) 추가 기능 제거

이 문서에서는 AKS 클러스터에서 OMS 추가 기능 및 관련 리소스를 제거하는 방법을 보여줍니다.

## <a name="disable-the-osm-add-on-from-your-cluster"></a>클러스터에서 OSM 추가 기능을 사용하지 않도록 설정

를 사용하여 클러스터에서 OSM 추가 기능을 사용하지 않도록 `az aks disable-addon` 설정합니다. 다음은 그 예입니다.

```azurecli-interactive
az aks disable-addons \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --addons open-service-mesh
```

위의 예제에서는 *myResourceGroup* 의 *myAKSCluster에서* OSM 추가 기능 을 제거합니다.

## <a name="remove-additional-osm-resources"></a>추가 OSM 리소스 제거

OSM 추가 설정을 사용하지 않도록 설정하면 다음 리소스가 클러스터에 남아 있습니다.

1. OSM meshconfig 사용자 지정 리소스
2. OSM 컨트롤 플레인 비밀
3. OSM 변경 webhook 구성
4. OSM 유효성 검사 webhook 구성
5. OSM CRD

> [!IMPORTANT]
> OSM 추가 기능을 사용하지 않도록 설정하면 이러한 추가 리소스를 제거해야 합니다. 나중에 OSM 추가 기능을 다시 사용하도록 설정하면 클러스터에 이러한 리소스를 남겨 두면 문제가 발생할 수 있습니다.

이러한 나머지 리소스를 제거하려면 다음을 수행합니다.

1. meshconfig 구성 리소스 삭제
```azurecli-interactive
kubectl delete --ignore-not-found meshconfig -n kube-system osm-mesh-config
```

2. OSM 컨트롤 플레인 비밀 삭제
```azurecli-interactive
kubectl delete --ignore-not-found secret -n kube-system osm-ca-bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
```

3. OSM 변경 웹후크 구성 삭제
```azurecli-interactive
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-injector --ignore-not-found
```

4. 웹후크 구성의 유효성을 검사하는 OSM 삭제
```azurecli-interactive
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-controller --ignore-not-found
```

5. OSM CRD 삭제: OSM의 CRD 및 삭제 방법에 대한 지침은 [이 설명서를 참조하세요.](https://docs.openservicemesh.io/docs/getting_started/uninstall/#removal-of-osm-cluster-wide-resources)
