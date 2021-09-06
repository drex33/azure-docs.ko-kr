---
title: Kubernetes 클러스터 노드에 PostgreSQL 하이퍼스케일 서버 그룹 배치
description: PostgreSQL 하이퍼스케일 서버 그룹을 구성하는 PostgreSQL 인스턴스가 Kubernetes 클러스터 노드에 배치되는 방법을 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b99df2f95838fe1913876a3e6a138935806df836
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535569"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 배치

이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 PostgreSQL 인스턴스가 해당 인스턴스를 호스팅하는 Kubernetes 클러스터의 실제 노드에 배치되는 방법을 설명하는 예제를 사용합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>구성

이 예제에서는 4개의 실제 노드가 있는 AKS(Azure Kubernetes Service) 클러스터를 사용하고 있습니다. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure Portal의 4개 노드 AKS 클러스터":::

Kubernetes 클러스터의 실제 노드를 나열합니다. 명령 실행:

```console
kubectl get nodes
```

`kubectl`은 Kubernetes 클러스터 안에 있는 4개의 실제 노드를 반환합니다.

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

아키텍처는 다음과 같이 나타낼 수 있습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Kubernetes 클러스터에 그룹화된 4개 노드의 논리적 표현":::

Kubernetes 클러스터는 하나의 Azure Arc 데이터 컨트롤러와 하나의 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 호스팅합니다. 이 서버 그룹은 3개의 PostgreSQL 인스턴스(코디네이터 1개와 작업자 2개)로 구성됩니다.

다음 명령을 사용하여 pod를 나열합니다.

```console
kubectl get pods -n arc3
```
`kubectl`은 다음을 반환합니다.

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
이러한 각 pod는 PostgreSQL 인스턴스를 호스팅합니다. 이와 함께 Pod는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성합니다.

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>배치
Kubernetes가 서버 그룹의 pod을 배치하는 방법을 살펴보겠습니다. 각 pod를 설명하고 해당 pod가 배치된 Kubernetes 클러스터의 실제 노드를 식별합니다. 예를 들어 코디네이터의 경우 다음 명령을 실행합니다.

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl`은 다음을 반환합니다.

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

각 pod에 대해 이 명령을 실행하면 현재 배치가 다음과 같이 요약됩니다.

| 서버 그룹 역할|서버 그룹 pod|pod를 호스팅하는 Kubernetes 실제 노드 |
|--|--|--|
| 작업자|postgres01-1|aks-agentpool-42715708-vmss000002 |
| 작업자|postgres01-2|aks-agentpool-42715708-vmss000003 |

또한 pod 설명에서 각 pod가 호스팅하는 컨테이너의 이름을 확인합니다. 예를 들어 두 번째 작업자에 대해 다음 명령을 실행합니다.

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl`은 다음을 반환합니다.

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 일부인 각 Pod는 다음 세 가지 컨테이너를 호스팅합니다.

|컨테이너|설명
|----|----|
|`Fluentbit` |데이터 * 로그 수집기: https://fluentbit.io/
|`Postgres`|Azure Arc 지원 PosgreSQL 하이퍼스케일 서버 그룹의 PostgreSQL 인스턴스 부분
|`Telegraf` |메트릭 수집기: https://www.influxdata.com/time-series-platform/telegraf/

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="각각 별도의 노드에 배치된 3개의 pod":::

즉, 이 시점에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성하는 각 PostgreSQL 인스턴스는 Kubernetes 컨테이너 내의 특정 실제 호스트에서 호스팅됩니다. 이 구성은 각 역할(코디네이터 및 작업자)이 각 실제 노드의 리소스를 사용하므로 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 가장 높은 성능을 제공합니다. 이러한 리소스는 여러 PostgreSQL 역할 간에 공유되지 않습니다.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 스케일 아웃

이제 스케일 아웃하여 서버 그룹에 세 번째 작업자 노드를 추가하고 어떻게 되는지 살펴보겠습니다. 네 번째 pod에서 호스트되는 네 번째 PostgreSQL 인스턴스가 만들어집니다.
스케일 아웃하려면 다음 명령을 실행합니다.

```azurecli
az postgres arc-server edit --name postgres01 --workers 3 --k8s-namespace <namespace> --use-k8s
```

다음 출력이 생성됩니다.

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Azure Arc 데이터 컨트롤러에 배포된 서버 그룹을 나열하고 이제 서버 그룹이 작업자 3개와 함께 실행되는지 확인합니다. 명령 실행:

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

그리고 작업자 2개에서 3개로 스케일 아웃했는지 확인합니다.

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

앞에서 설명한 것처럼 서버 그룹은 이제 총 4개의 pod를 사용합니다.

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

그리고 새 pod를 설명하여 해당 pod가 호스트되는 Kubernetes 클러스터의 실제 노드를 식별합니다.
명령 실행:

```console
kubectl describe pod postgres01w-2 -n arc3
```

호스팅 노드의 이름을 식별하려면 다음을 수행합니다.

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

이제 클러스터의 실제 노드에서 PostgreSQL 인스턴스의 배치는 다음과 같습니다.

|서버 그룹 역할|서버 그룹 pod|pod를 호스팅하는 Kubernetes 실제 노드
|-----|-----|-----
|코디네이터|postgres01-0|aks-agentpool-42715708-vmss000000
|작업자|postgres01-1|aks-agentpool-42715708-vmss000002
|작업자|postgres01-2|aks-agentpool-42715708-vmss000003
|작업자|postgres01-3|aks-agentpool-42715708-vmss000000

새 작업자(postgres01w-2)의 pod가 코디네이터와 동일한 노드에 배치되었는지 확인합니다. 

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="코디네이터와 동일한 노드의 네 번째 pod":::

새 작업자/pod가 Kubernetes 클러스터 aks-agentpool-42715708-vmss000003의 나머지 실제 노드에 배치되지 않는 이유는 무엇인가요?

그 이유는 Kubernetes 클러스터의 마지막 실제 노드가 Azure Arc 지원 Data Services를 실행하는 데 필요한 추가 구성 요소를 호스트하는 여러 Pod를 호스트하기 때문입니다. Kubernetes는 예약 시점에 추가 작업자를 호스트할 가장 적합한 후보가 aks-agentpool-42715708-vmss000000 실제 노드라고 평가했습니다. 

위와 동일한 명령을 사용하여 각 실제 노드가 무엇을 호스트하는지 확인합니다.

|다른 pod 이름\* |사용량|pod를 호스트하는 Kubernetes 실제 노드
|----|----|----
|bootstrapper-jh48b|SQL 관리되는 인스턴스, PostgreSQL 하이퍼스케일 서버 그룹 및 데이터 컨트롤러와 같은 사용자 지정 리소스를 생성, 편집 및 삭제하기 위해 들어오는 요청을 처리하는 서비스|aks-agentpool-42715708-vmss000003
|control-gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0|데이터 컨트롤러의 구성 및 상태를 저장하는 데 사용되는 컨트롤러 데이터 저장소|aks-agentpool-42715708-vmss000001
|controlwd-zzjp7|데이터 컨트롤러의 가용성을 감시하는 컨트롤러 "워치독" 서비스|aks-agentpool-42715708-vmss000000
|logsdb-0|모든 Arc Data Services pod에서 수집된 모든 로그를 저장하는 데 사용되는 Elastic Search 인스턴스 Elasticsearch, 각 pod의 `Fluentbit` 컨테이너에서 데이터 수신|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|로그 분석 GUI를 제공하기 위해 Elastic Search 데이터베이스 위에 있는 Kibana 인스턴스|aks-agentpool-42715708-vmss000003
|metricsdb-0|모든 Arc Data Services pod에서 수집된 모든 메트릭을 저장하는 데 사용되는 InfluxDB 인스턴스 InfluxDB, 각 pod의 `Telegraf` 컨테이너에서 데이터 수신|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|노드에 대한 노드 수준 메트릭을 수집하기 위해 클러스터의 모든 Kubernetes 노드에 배포된 디먼 집합|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|노드에 대한 노드 수준 메트릭을 수집하기 위해 클러스터의 모든 Kubernetes 노드에 배포된 디먼 집합|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|노드에 대한 노드 수준 메트릭을 수집하기 위해 클러스터의 모든 Kubernetes 노드에 배포된 디먼 집합|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|노드에 대한 노드 수준 메트릭을 수집하기 위해 클러스터의 모든 Kubernetes 노드에 배포된 디먼 집합|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|모니터링 대시보드 GUI를 제공하기 위해 InfluxDB 데이터베이스 위에 있는 Grafana 인스턴스|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Grafana 및 Kibana 인스턴스 앞에 있는 웹 애플리케이션 프록시 계층|aks-agentpool-42715708-vmss000002

> \* pod 이름의 접미사는 다른 배포에 따라 다릅니다. 또한 Azure Arc 데이터 컨트롤러의 Kubernetes 네임스페이스 내에서 호스트되는 pod만 여기에 나열됩니다.

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="다양한 노드의 네임스페이스에 있는 모든 pod":::

위에서 설명한 대로 Azure Arc 지원 Postgres 하이퍼스케일 서버 그룹의 코디네이터 노드(Pod 1)는 서버 그룹의 세 번째 작업자 노드(Pod 4)와 동일한 물리적 리소스를 공유합니다. 이는 코디네이터 노드가 일반적으로 작업자 노드가 사용할 수 있는 것과 비교할 때 매우 적은 리소스를 사용하기 때문에 허용됩니다. 이러한 이유로 신중하게 다음을 선택합니다.
- Kubernetes 클러스터의 크기 및 각 실제 노드(메모리, vCore)의 특징
- Kubernetes 클러스터 안에 있는 실제 노드의 수
- Kubernetes 클러스터에서 호스트하는 애플리케이션 또는 워크로드

Kubernetes 클러스터에서 너무 많은 워크로드를 호스트한다는 것은 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대해 제한이 발생할 수 있다는 의미입니다. 이러한 경우 수평 크기 조정 기능이 크게 유용하지 않습니다. 시스템에서 얻을 수 있는 성능은 실제 노드 또는 스토리지 시스템의 배치 또는 물리적 특징에 대한 것이 아닙니다. 또한 얻을 수 있는 성능은 Kubernetes 클러스터(Azure Arc 지원 PostgreSQL 하이퍼스케일 포함) 내에서 실행되는 각 리소스(예: 메모리 및 vCore에 대해 설정한 요청 및 제한)를 구성하는 방법에 대한 것입니다. 지정된 Kubernetes 클러스터에서 호스트할 수 있는 워크로드의 양은 Kubernetes 클러스터의 특징, 워크로드의 특성, 사용자 수, Kubernetes 클러스터의 작업이 수행되는 방식 등에 따라 다릅니다.

## <a name="scale-out-aks"></a>AKS 스케일 아웃

AKS 클러스터와 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 모두 수평으로 크기 조정하는 것이 Azure Arc 지원 PostgreSQL 하이퍼스케일의 고성능을 최대한 활용하는 방법임을 보여 드리겠습니다.
AKS 클러스터에 다섯 번째 노드를 추가해보겠습니다.

:::row:::
    :::column:::
        이전
    :::column-end:::
    :::column:::
        After
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="이전의 Azure Portal 레이아웃":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="이후의 Azure Portal 레이아웃":::
    :::column-end:::
:::row-end:::

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="업데이트 후 Kubernetes 클러스터의 논리적 레이아웃":::

다음 명령을 실행하여 새 AKS 실제 노드에서 호스트되는 Arc 데이터 컨트롤러 네임스페이스의 pod를 살펴보겠습니다.

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

시스템 아키텍처의 표현을 업데이트해 보겠습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="클러스터의 논리적 다이어그램에 있는 모든 pod":::

Kubernetes 클러스터의 새 실제 노드가 Azure Arc Data Services에 필요한 메트릭 pod만 호스트하고 있음을 확인할 수 있습니다. 이 예에서는 Arc 데이터 컨트롤러의 네임스페이스에만 초점을 맞추고 있으며 다른 pod는 나타내지 않습니다.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 다시 스케일 아웃

다섯 번째 실제 노드는 워크로드를 아직 호스트하고 있지 않습니다. Azure Arc 지원 PostgreSQL 하이퍼스케일을 스케일 아웃하면 Kubernetes는 새 PostgreSQL Pod의 배치를 최적화하며 이미 더 많은 워크로드를 호스트하고 있는 실제 노드에 이를 배치해서는 안 됩니다. 다음 명령을 실행하여 Azure Arc 지원 PostgreSQL 하이퍼스케일을 작업자 3개에서 4개로 조정합니다. 작업이 끝나면 서버 그룹이 구성되고 PostgreSQL 인스턴스 5개, 코디네이터 1개 및 작업자 4개에 배포됩니다.

```azurecli
az postgres arc-server edit --name postgres01 --workers 4 --k8s-namespace <namespace> --use-k8s
```

다음 출력이 생성됩니다.

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

데이터 컨트롤러에 배포된 서버 그룹을 나열하고 이제 서버 그룹이 작업자 4개와 함께 실행되는지 확인합니다.

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

그리고 작업자 3개에서 4개로 스케일 아웃했는지 관찰합니다. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

이전처럼 서버 그룹이 이제 4개의 pod를 사용함을 확인합니다.

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

이제 서버 그룹의 모양은 다음과 같습니다.

|서버 그룹 역할|서버 그룹 pod
|----|-----
|코디네이터|postgres01c-0
|작업자|postgres01w-0
|작업자|postgres01w-1
|작업자|postgres01w-2
|작업자|postgres01w-3

postgres01w-3 pod를 설명하여 pod가 호스트되는 실제 노드를 식별합니다.

```console
kubectl describe pod postgres01w-3 -n arc3
```

그리고 이 노드에서 실행하는 pod를 관찰합니다.

|서버 그룹 역할|서버 그룹 pod| Pod
|----|-----|------
|코디네이터|postgres01c-0|aks-agentpool-42715708-vmss000000
|작업자|postgres01w-0|aks-agentpool-42715708-vmss000002
|작업자|postgres01w-1|aks-agentpool-42715708-vmss000003
|작업자|postgres01w-2|aks-agentpool-42715708-vmss000000
|작업자|postgres01w-3|aks-agentpool-42715708-vmss000004

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes에서 사용량이 가장 적은 노드의 최신 pod 예약":::

Kubernetes가 Kubernetes 클러스터의 부하가 가장 적은 실제 노드에서 새 PostgreSQL pod를 예약했습니다.

## <a name="summary"></a>요약

Azure Arc 지원 서버 그룹의 수평 크기 조정 성능과 확장성을 최대한 활용하려면 다음의 Kubernetes 클러스터 내에서 리소스 경합이 발생하지 않도록 해야 합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹과 동일한 Kubernetes 클러스터에서 호스트되는 다른 워크로드 간
- Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성하는 모든 PostgreSQL 인스턴스 간

방법은 여러 가지입니다.
- Kubernetes 및 Azure Arc 지원 Postgres 하이퍼스케일 모두 스케일 아웃: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 크기를 조정하는 것과 동일한 방식으로 Kubernetes 클러스터 크기를 수평으로 조정하는 것을 고려합니다. 서버 그룹에 추가하는 각 작업자에 대한 실제 노드를 클러스터에 추가합니다.
- Kubernetes를 스케일 아웃하지 않고 Azure Arc 지원 Postgres 하이퍼스케일 스케일 아웃: Kubernetes(Azure Arc 지원 PostgreSQL 하이퍼스케일 포함)에서 호스트되는 워크로드에 올바른 리소스 제약 조건(메모리와 vCore에 대한 요청 및 제한)을 설정하여 Kubernetes에서 워크로드의 공동 배치를 사용하고 리소스 경합의 위험을 줄입니다. Kubernetes 클러스터의 실제 노드에 대한 물리적 특징이 사용자가 정의한 리소스 제약 조건을 준수하는지 확인해야 합니다. 또한 시간 경과에 따라 워크로드가 진화하거나 더 많은 워크로드가 Kubernetes 클러스터에 추가될 때 균형이 유지되도록 해야 합니다.
- Kubernetes 메커니즘(pod 선택기, 선호도, 반선호도)을 사용하여 pod 배치에 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

[작업자 노드를 더 추가하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-in-postgresql-hyperscale-server-group.md)
