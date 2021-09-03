---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 삭제
description: Azure Arc 지원 Postgre 하이퍼스케일 서버 그룹 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 00387e190df3bcc6f654868d078a068e9196a635
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567340"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 삭제

이 문서에서는 Azure Arc 설정에서 서버 그룹을 삭제하는 단계를 설명합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>서버 그룹 삭제

예를 들어, 아래 설정에서 _postgres01_ 인스턴스를 삭제하려 한다고 가정해 보겠습니다.

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

delete 명령의 일반 형식은 다음과 같습니다.
```azurecli
az postgres arc-server delete -n <server group name> --k8s-namespace <namespace> --use-k8s
```
이 명령을 실행하면 서버 그룹 삭제를 확인하도록 요청됩니다. 스크립트를 사용하여 삭제를 자동화하는 경우에는 --force 매개 변수를 사용하여 확인 요청을 무시해야 합니다. 예를 들어, 다음과 같은 명령을 실행합니다. 
```azurecli
az postgres arc-server delete -n <server group name> --force --k8s-namespace <namespace> --use-k8s
```

delete 명령에 대한 자세한 내용을 보려면 다음을 실행합니다.
```azurecli
az postgres arc-server delete --help 
```

### <a name="delete-the-server-group-used-in-this-example"></a>이 예제에 사용된 서버 그룹 삭제

```azurecli
az postgres arc-server delete -n postgres01 --k8s-namespace <namespace> --use-k8s
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes PVC(영구 볼륨 클레임) 회수

서버 그룹을 삭제해도 연결된 [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)는 제거되지 않습니다. 이것은 의도적인 것입니다. 인스턴스가 실수로 삭제된 경우 사용자가 데이터베이스 파일에 액세스하도록 도와줍니다. PVC는 반드시 삭제할 필요는 없습니다. 그러나 권장됩니다. 이러한 PVC를 회수하지 않으면 Kubernetes 클러스터에서 디스크 공간이 부족하다고 간주하므로 오류가 발생합니다. PVC를 회수하려면 다음 단계를 수행합니다.

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 삭제한 서버 그룹의 PVC 나열

PVC를 나열하려면 다음 명령을 실행합니다.

```console
kubectl get pvc [-n <namespace name>]
```

PVC의 목록, 즉 삭제한 서버 그룹에 대한 PVC를 반환합니다. 예를 들면 다음과 같습니다.

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
이 서버 그룹에는 8개의 PVC가 있습니다.

### <a name="2-delete-each-of-the-pvcs"></a>2. 각 PVC 삭제

삭제한 서버 그룹의 각 PostgreSQL 하이퍼스케일 노드(코디네이터 및 작업자)에 대한 데이터 및 로그 PVC를 삭제합니다.

이 명령의 일반적인 형식은 다음과 같습니다. 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

예를 들면 다음과 같습니다.

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

이러한 각 kubectl 명령은 PVC가 성공적으로 삭제되었는지 확인합니다. 예를 들면 다음과 같습니다.

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> 표시된 것처럼, PVC를 삭제하지 않으면 Kubernetes 클러스터에서 오류가 발생하게 될 수 있습니다. 이러한 오류 중 일부에는 Kubernetes API에서 리소스를 만들기, 읽기, 업데이트, 삭제할 수 없거나 이 스토리지 문제(정상적인 Kubernetes 동작)로 인해 Kubernetes 노드에서 컨트롤러 Pod가 제거될 수 있으므로 `az arcdata dc export`와 같은 명령이 실행될 수 있는 것이 포함될 수 있습니다.
>
> 예를 들어, 다음과 유사한 로그의 메시지가 표시될 수 있습니다.  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>다음 단계
[Azure Arc 지원 PostgreSQL 하이퍼스케일](create-postgresql-hyperscale-server-group.md) 만들기
