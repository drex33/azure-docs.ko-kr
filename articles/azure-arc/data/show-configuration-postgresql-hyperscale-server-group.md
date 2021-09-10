---
title: Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시
titleSuffix: Azure Arc-enabled data services
description: Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 727c792daa20c392a87f7c57100b72ae79a971f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566626"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시

이 문서에서는 서버 그룹의 구성을 표시하는 방법을 설명합니다. 문서에서는 사용자가 궁금해 할 수 있는 몇 가지 질문을 예상하여 설명하고 질문에 대한 답변을 제공합니다. 때로는 유효한 답변이 여러 개 있을 수 있습니다. 이 문서에서는 가장 일반적이거나 유용한 답변을 제공합니다. 다음과 같이 테마에 따라 질문을 그룹화합니다.

- Kubernetes 관점에서
- Azure Arc 지원 데이터 서비스 관점에서

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes 관점에서

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에서 사용하는 Pod는 몇 개인가요?

Postgres 형식의 Kubernetes 리소스를 나열합니다. 명령 실행:

```console
kubectl get postgresqls [-n <namespace name>]
```

이 명령의 출력은 생성된 서버 그룹 목록을 보여 줍니다. 각각에 대해 Pod의 수를 나타냅니다. 예를 들면 다음과 같습니다.

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

이 예에서는 2개의 서버 그룹이 만들어지고 각각 3개의 Pod(코디네이터 1개 + 작업자 2개)에서 실행되는 것을 보여 줍니다. 즉, 이 Azure Arc 데이터 컨트롤러에서 만들어진 서버 그룹은 6개의 Pod를 사용합니다.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 사용하는 Pod는 무엇인가요?

다음을 실행합니다.

```console
kubectl get pods [-n <namespace name>]
```

그러면 Pod 목록이 반환됩니다. 서버 그룹에 지정한 이름에 따라 서버 그룹에서 사용하는 Pod가 표시됩니다. 예를 들면 다음과 같습니다.

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

이 예에서, 생성된 두 서버 그룹을 호스트하는 6개의 Pod는 다음과 같습니다.
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>어떤 서버 그룹 Pod가 어떤 서버 그룹 역할에 사용되나요?

`-0` 접미사가 붙은 모든 Pod 이름은  코디네이터 노드를 나타냅니다. 1 이상인 `-x`가 접미사로 붙은 모든 노드 이름은 작업자 노드입니다. 위의 예제에서는 다음과 같습니다.
- 코디네이터는 다음과 같습니다. `postgres01-0`, `postgres02-0`
- 작업자는 다음과 같습니다. `postgres01-2`, `postgres01-2`, `postgres02-1`, `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Pod의 상태는 어떤가요?

`kubectl get pods`를 실행하고 `STATUS` 열을 확인합니다.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>어떤 PVC(영구적 볼륨 클레임)를 사용 중인가요? 

사용되는 PVC와 데이터, 로그 및 백업에 사용되는 PVC를 이해하려면 다음을 실행합니다. 

```console
kubectl get pvc [-n <namespace name>]
```

기본적으로 PVC 이름의 접두사는 해당 사용량을 나타냅니다.

- `backups-`...: 백업에 사용되는 PVC
- `data-`...: 데이터 파일에 사용되는 PVC
- `logs-`...: 트랜잭션 로그/WAL 파일에 사용되는 PVC

예를 들면 다음과 같습니다.

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Azure Arc 지원 데이터 서비스 관점에서

* Arc 데이터 컨트롤러에서 생성되는 서버 그룹은 몇 개입니까?
* 이름은 무엇인가요?
* 사용되는 작업자 노드 수는 몇 개인가요?
* 실행되는 Postgres 버전은 무엇인가요?

다음 명령 중 하나를 실행합니다.
- **kubectl 사용:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   예를 들어, 각 줄이 `servergroup`인 아래 출력을 생성합니다. 아래 표시에서 이름의 구조는 다음과 같이 구성됩니다.

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   위의 출력은 Postgres 버전이 12인 2개의 서버 그룹을 보여 줍니다. 버전이 Postgres 11인 경우에는 CRD 이름이 대신 postgresql-11.arcdata.microsoft.com이 됩니다.

   각 항목은 3개의 노드/Pod: 1개의 코디네이터와 2개의 작업자에서 실행됩니다.

- **Azure CLI(az) 사용:**

다음 명령을 실행합니다. 출력은 다음과 같이 kubectl에서 표시하는 것과 비슷한 정보를 표시합니다.

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>메모리 및 vCore가 얼마나 사용되고 그룹에 대해 어떤 확장이 생성되었나요?

다음 명령 중 하나를 실행합니다.

**Kubectl 사용:**

kubectl를 사용하여 Postgres 리소스를 설명합니다. 이렇게 하려면 해당하는 종류(위에 표시된 해당하는 Postgres 버전의 Kubernetes 리소스(CRD)의 이름)와 서버 그룹의 이름이 필요합니다.
이 명령의 일반적인 형식은 다음과 같습니다.

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

예를 들면 다음과 같습니다.

```console
kubectl describe postgresql-12/postgres02
```

이 명령은 서버 그룹의 구성을 보여 줍니다.

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>2020년 10월 릴리스 이전에 `Workers`는 이전 예제에서 `Shards`였습니다. 자세한 내용은 [릴리스 정보 - Azure Arc 지원 데이터 서비스(미리 보기)](release-notes.md)를 참조하세요.

위에 표시된 `servergroup`의 설명에서 특정 관심 사항을 확인해 보겠습니다. 이 서버 그룹에 대해 어떤 점을 알려주나요?

- 이것은 Postgres 버전 12입니다. 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- 2020년 8월에 생성되었습니다.
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- 이 서버 그룹에서 두 개의 Postgres 확장 `citus`와 `pg_stat_statements`가 생성되었습니다.
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- 두 작업자 노드를 사용합니다.
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- 노드당 하나의 1개의 cpu/vCore 및 512MB Ram을 사용하도록 보장합니다. 5개 이상의 cpu/vCore 및 1024MB의 메모리를 사용합니다.
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - 쿼리에 사용할 수 있으며 문제가 발생하지 않습니다. 모든 노드가 실행되고 있습니다.
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Azure CLI(az) 사용:**

이 명령의 일반적인 형식은 다음과 같습니다.

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server show -n postgres02 --k8s-namespace <namespace> --use-k8s
```

kubectl에서 반환하는 것과 매우 유사한 형식 및 내용으로 아래 출력을 반환합니다.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>다음 단계
- [Azure Arc 지원 PostgreSQL 하이퍼스케일의 개념에 대해 읽어보기](concepts-distributed-postgres-hyperscale.md)
- [서버 그룹을 확장(작업자 노드 추가)하는 방법에 대해 읽어보기](scale-out-in-postgresql-hyperscale-server-group.md)
- [서버 그룹을 확장/축소(메모리 및/또는 vCore 증가 또는 감소)하는 방법에 대해 읽어보기](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [스토리지 구성에 대해 읽어보기](storage-configuration.md)
- [데이터베이스 인스턴스를 모니터링하는 방법 읽어보기](monitor-grafana-kibana.md)
- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 PostgreSQL 확장 사용](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성](configure-security-postgres-hyperscale.md)
