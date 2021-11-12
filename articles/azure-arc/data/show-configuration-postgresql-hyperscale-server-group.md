---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시
titleSuffix: Azure Arc-enabled data services
description: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 0789e9bb28868ce4d75aa48beb956fcb4dd74031
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345806"
---
# <a name="show-the-configuration-of-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성 표시

이 문서에서는 서버 그룹의 구성을 표시하는 방법을 설명합니다. 문서에서는 사용자가 궁금해 할 수 있는 몇 가지 질문을 예상하여 설명하고 질문에 대한 답변을 제공합니다. 때때로 몇 가지 유효한 답변이 있을 수 있습니다. 이 문서에서는 가장 일반적이거나 유용한 답변을 제공합니다. 다음과 같이 테마에 따라 질문을 그룹화합니다.

- Kubernetes 관점에서
- Azure Arc 지원 데이터 서비스 관점에서

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes 관점에서

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-pods-are-they-using"></a>배포된 Postgres 서버 그룹은 무엇이며 사용하는 Pod는 몇 개입니까?

Postgres 형식의 Kubernetes 리소스를 나열합니다. 명령 실행:

```console
kubectl get postgresqls -n <namespace>
```

이 명령의 출력은 생성된 서버 그룹 목록을 보여 줍니다. 각각에 대해 Pod의 수를 나타냅니다. 예를 들면 다음과 같습니다.

```output
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          20.101.12.221:5432   12d
```

이 예제에서는 하나의 서버 그룹이 만들어지는 것을 보여 집니다. 5개의 Pod(코디네이터 1개와 작업자 4개)에서 실행됩니다.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 사용하는 Pod는 무엇인가요?

다음을 실행합니다.

```console
kubectl get pods -n <namespace>
```

명령은 Pod 목록을 반환합니다. 서버 그룹에 지정한 이름에 따라 서버 그룹에서 사용하는 Pod가 표시됩니다. 예를 들면 다음과 같습니다.

```console
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-4jrtl   1/1     Running   0          12d
control-kz8gh        2/2     Running   0          12d
controldb-0          2/2     Running   0          12d
logsdb-0             3/3     Running   0          12d
logsui-qjkgz         3/3     Running   0          12d
metricsdb-0          2/2     Running   0          12d
metricsdc-4jslw      2/2     Running   0          12d
metricsdc-4tl2g      2/2     Running   0          12d
metricsdc-fkxv2      2/2     Running   0          12d
metricsdc-hs4h5      2/2     Running   0          12d
metricsdc-tvz22      2/2     Running   0          12d
metricsui-7pcch      2/2     Running   0          12d
postgres01c0-0       3/3     Running   0          2d19h
postgres01w0-0       3/3     Running   0          2d19h
postgres01w0-1       3/3     Running   0          2d19h
postgres01w0-2       3/3     Running   0          2d19h
postgres01w0-3       3/3     Running   0          2d19h
```

### <a name="what-pod-is-used-for-what-role-in-the-server-group"></a>서버 그룹의 역할에 사용되는 Pod는 무엇인가요?

`c` 접미사가 붙은 모든 Pod 이름은  코디네이터 노드를 나타냅니다. 으로 접미사가 붙는 노드 이름은 `w`  서버 그룹을 호스트하는 5개의 Pod와 같은 작업자 노드입니다.

- `postgres01c0-0` 코디네이터 노드
- `postgres01w0-0` 작업자 노드
- `postgres01w0-1` 작업자 노드
- `postgres01w0-2` 작업자 노드
- `postgres01w0-3` 작업자 노드

지금은 `0` `c` 및 `w` (ServerGroupName `c0` -x 또는 ServerGroupName `w0` -x) 이후에 표시되는 문자를 무시할 수 있습니다. 제품이 고가용성 환경을 제공할 때 사용되는 표기입니다.

### <a name="what-is-the-status-of-the-pods"></a>Pod의 상태는 어떤가요?

`kubectl get pods -n <namespace>`를 실행하고 `STATUS` 열을 확인합니다.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>어떤 PVC(영구적 볼륨 클레임)를 사용 중인가요?

사용되는 PVC와 데이터, 로그 및 백업에 사용되는 PVC를 이해하려면 다음을 실행합니다.

```console
kubectl get pvc -n <namespace>
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

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-server-group"></a>사용 중인 메모리 및 vCore의 양과 서버 그룹에 대해 생성된 확장은 무엇인가요?

kubectl을 사용하여 Postgres 리소스를 설명합니다. 이렇게 하려면 해당 종류(Azure Arc Postgres에 대한 CRD(Kubernetes 리소스)의 이름) 및 서버 그룹의 이름이 필요합니다.

이 명령의 일반적인 형식은 다음과 같습니다.

```console
kubectl describe <CRD name>/<server group name> -n <namespace>
```

예를 들면 다음과 같습니다.

```console
kubectl describe postgresql/postgres01 -n arc
```

이 명령은 서버 그룹의 구성을 보여 줍니다.

```output
Name:         postgres01
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1beta2
Kind:         PostgreSql
Metadata:
  Creation Timestamp:  2021-10-13T01:09:25Z
  Generation:          29
  Managed Fields:
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:dev:
        f:engine:
          .:
          f:extensions:
          f:version:
        f:scale:
          .:
          f:replicas:
          f:workers:
        f:scheduling:
          .:
          f:default:
            .:
            f:resources:
              .:
              f:requests:
                .:
                f:memory:
          f:roles:
            .:
            f:coordinator:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
            f:worker:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
        f:services:
          .:
          f:primary:
            .:
            f:port:
            f:type:
        f:storage:
          .:
          f:backups:
            .:
            f:volumes:
          f:data:
            .:
            f:volumes:
          f:logs:
            .:
            f:volumes:
    Manager:      OpenAPI-Generator
    Operation:    Update
    Time:         2021-10-22T22:37:51Z
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:IsValid:
      f:spec:
        f:scale:
          f:syncReplicas:
      f:status:
        .:
        f:logSearchDashboard:
        f:metricsDashboard:
        f:observedGeneration:
        f:podsStatus:
        f:primaryEndpoint:
        f:readyPods:
        f:state:
    Manager:         unknown
    Operation:       Update
    Time:            2021-10-22T22:37:53Z
  Resource Version:  1541521
  UID:               23565e53-2e7a-4cd6-8f80-3a79397e1d7a
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
    Roles:
      Coordinator:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
      Worker:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
  Services:
    Primary:
      Port:  5432
      Type:  LoadBalancer
  Storage:
    Backups:
      Volumes:
        Size:  5Gi
    Data:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
    Logs:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
Status:
  Log Search Dashboard:  https://12.235.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
  Metrics Dashboard:     https://12.346.578.99:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01
  Observed Generation:   29
  Pods Status:
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-1
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01c0-0
    Role:                    coordinator
    Conditions:
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-3
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-0
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-2
    Role:                    worker
  Primary Endpoint:          20.101.12.221:5432
  Ready Pods:                5/5
  Running Version:           v1.1.0_2021-10-12_patching_0bcb7bcaf
  State:                     Ready
Events:                      <none>
```

#### <a name="interpret-the-configuration-information"></a>구성 정보 해석

위에 표시된 `servergroup`의 설명에서 특정 관심 사항을 확인해 보겠습니다. 이 서버 그룹에 대해 어떤 점을 알려주나요?

- Postgres 버전 12이며 Citus 확장을 실행합니다.

   ```output
   Spec:
     Dev:  false
     Engine:
       Extensions:
         Name:   citus
       Version:  12
   ```

- 2021년 10월 13일에 생성되었습니다.

   ```output
     Metadata:
     Creation Timestamp:  2021-10-13T01:09:25Z
   ```

- 4개의 작업자 노드를 사용합니다.

   ```output
        Scale:
          Replicas:       1
          Sync Replicas:  0
          Workers:        4
   ```

- 리소스 구성: 이 예제에서 코디네이터와 작업자는 256Mi 메모리를 보장합니다. 코디네이터와 작업자 노드는 1Gi 메모리를 더 많이 사용할 수 없습니다. 코디네이터와 작업자는 둘 다 하나의 vCore를 보장하며 두 개 이상의 vCore를 사용할 수 없습니다.

   ```console
        Scheduling:
       Default:
         Resources:
           Requests:
             Memory:  256Mi
       Roles:
         Coordinator:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
         Worker:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
   ```

- 서버 그룹의 상태는 무엇인가요? 내 애플리케이션에 사용할 수 있나요?

   예, 모든 Pod(코디네이터 노드 및 4개의 작업자 노드가 모두 준비되었습니다.)

   ```console
   Ready Pods:                5/5
   ```

## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Azure Arc 지원 데이터 서비스 관점에서

Az CLI 명령을 사용합니다.

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-workers-are-they-using"></a>배포된 Postgres 서버 그룹은 무엇이며 얼마나 많은 작업자를 사용하고 있나요?

다음 명령을 실행합니다.

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s
   ```

배포된 서버 그룹을 나열합니다.

   ```output
   [
     {
       "name": "postgres01",
       "replicas": 1,
       "state": "Ready",
       "workers": 4
     }
   ]
   ```

또한 서버 그룹에서 사용하는 작업자 노드의 수도 나타냅니다. 각 작업자 노드는 코디네이터 노드를 호스트하는 데 사용되는 하나의 Pod를 추가해야 하는 하나의 Pod에 배포됩니다.

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>메모리 및 vCore가 얼마나 사용되고 그룹에 대해 어떤 확장이 생성되었나요?

다음 명령 중 하나를 실행합니다.

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

kubectl에서 반환한 것과 유사한 형식 및 콘텐츠로 정보를 반환합니다. 선택한 도구를 사용하여 시스템과 상호 작용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Arc 지원 PostgreSQL 하이퍼스케일의 개념에 대해 읽어보기](concepts-distributed-postgres-hyperscale.md)
- [서버 그룹을 확장(작업자 노드 추가)하는 방법에 대해 읽어보기](scale-out-in-postgresql-hyperscale-server-group.md)
- [서버 그룹을 확장/축소(메모리 및/또는 vCore 증가 또는 감소)하는 방법에 대해 읽어보기](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [스토리지 구성에 대해 읽어보기](storage-configuration.md)
- [데이터베이스 인스턴스를 모니터링하는 방법 읽어보기](monitor-grafana-kibana.md)
- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 PostgreSQL 확장 사용](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성](configure-security-postgres-hyperscale.md)
