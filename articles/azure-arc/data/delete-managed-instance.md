---
title: Azure Arc 지원 SQL Managed Instance 삭제
description: Azure Arc 지원 SQL Managed Instance 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e404235ee984f4d1744910307e2eaa95276ee2df
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956617"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance 삭제
이 문서에서는 Azure Arc 지원 SQL Managed Instance를 삭제하는 방법을 설명합니다.


## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>기존 Azure Arc 지원 SQL Managed Instance 보기
SQL Managed Instance를 보려면 다음 명령을 실행합니다.

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

출력은 다음과 비슷합니다.

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance 삭제
SQL Managed Instance를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az sql mi-arc delete -n <NAME_OF_INSTANCE> --k8s-namespace <namespace> --use-k8s
```

출력은 다음과 비슷합니다.

```azurecli
# az sql mi-arc delete -n demo-mi --k8s-namespace <namespace> --use-k8s
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes PVC(영구 볼륨 클레임) 회수

SQL Managed Instance를 삭제해도 연결된 [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)는 제거되지 않습니다. 이것은 의도적인 것입니다. 인스턴스가 실수로 삭제된 경우 사용자가 데이터베이스 파일에 액세스하도록 도와줍니다. PVC는 반드시 삭제할 필요는 없습니다. 그러나 권장됩니다. 해당 PVC를 회수하지 않으면 Kubernetes 클러스터의 디스크 공간이 부족해지므로 결국 오류가 발생합니다. PVC를 회수하려면 다음 단계를 수행합니다.

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 삭제한 서버 그룹의 PVC 나열
PVC를 나열하려면 다음 명령을 실행합니다.
```console
kubectl get pvc
```

아래 예제에서 삭제한 SQL Managed Instance의 PVC를 확인합니다.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. 각 PVC 삭제
삭제한 각 SQL Managed Instance의 데이터 및 로그 PVC를 삭제합니다.
이 명령의 일반적인 형식은 다음과 같습니다. 
```console
kubectl delete pvc <name of pvc>
```

예를 들면 다음과 같습니다.
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

이러한 각 kubectl 명령은 PVC가 성공적으로 삭제되었는지 확인합니다. 예를 들면 다음과 같습니다.
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 표시된 것처럼, PVC를 삭제하지 않으면 Kubernetes 클러스터에서 오류가 발생하게 될 수 있습니다. 해당 오류에는 Kubernetes API에서 리소스 만들기, 읽기, 업데이트, 삭제를 수행할 수 없거나 이 스토리지 문제로 인해 Kubernetes 노드에서 컨트롤러 Pod가 제거될 수 있으므로(정상적인 Kubernetes 동작) `az arcdata dc export`와 같은 명령을 실행할 수 있는 경우가 포함될 수 있습니다.
>
> 예를 들어, 다음과 유사한 로그의 메시지가 표시될 수 있습니다.  
> - Annotations:    microsoft.com/ignore-pod-health: true  
> - Status:         Failed  
> - Reason:         Evicted  
> - Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능](managed-instance-features.md)에 대한 자세한 정보

[데이터 컨트롤러를 생성하여 시작](create-data-controller-indirect-cli.md)

데이터 컨트롤러를 이미 만들었나요? [Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)
