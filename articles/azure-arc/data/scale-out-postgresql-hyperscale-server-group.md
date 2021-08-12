---
title: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃
description: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350092"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>작업자 노드를 더 추가하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃
이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 스케일 아웃하는 방법을 이 문서에서는 시나리오를 따라가는 방식을 취합니다. **시나리오를 거치지 않고 스케일 아웃 방법을 살펴보기만 하려는 경우 [스케일 아웃](#scale-out) 단락으로 이동하세요.**

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>시작하기
Azure Arc 지원 PostgreSQL 하이퍼스케일 또는 Azure Database for PostgreSQL 하이퍼스케일(Citus)의 스케일링 모델에 이미 익숙한 경우 이 단락을 건너뛸 수 있습니다. 익숙하지 않은 경우 먼저 Azure Database for PostgreSQL 하이퍼스케일(Citus)의 설명서 페이지에서 해당 스케일링 모델에 대해 읽어 보는 것이 좋습니다. Azure Database for PostgreSQL 하이퍼스케일(Citus)은 Azure Arc 지원 데이터 서비스의 일부로 제공되는 대신 Azure에서 서비스(PAAS라고도 하는 Platform As A Service)로 호스트되는 동일한 기술입니다.
- [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
- [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
- [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
- [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* 위 문서에서는 **Azure Portal에 로그인** 과 **Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)** 섹션을 건너뛰었습니다. Azure Arc 배포의 나머지 단계를 구현합니다. 이 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

## <a name="scenario"></a>시나리오
이 시나리오는 [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 설명서에서 예제로 생성된 PostgreSQL 하이퍼스케일 서버 그룹을 참조합니다.

### <a name="load-test-data"></a>테스트 데이터 로드
이 시나리오에서는 [Citus Data 웹 사이트](https://www.citusdata.com/)(Citus Data는 Microsoft의 일부임)에서 공개적으로 제공하는 GitHub 데이터의 샘플을 사용합니다.

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 연결

##### <a name="list-the-connection-information"></a>연결 정보 나열
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 연결하려면 먼저 연결 정보를 가져옵니다. 이 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres endpoint list -n <server name>
```
예를 들면 다음과 같습니다.
```console
azdata arc postgres endpoint list -n postgres01
```

예제 출력:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>원하는 클라이언트 도구를 사용하여 연결

다음 쿼리를 실행하여 현재 하이퍼스케일 작업자 노드가 두 개 이상이고 각 노드가 Kubernetes Pod와 상응하는지 확인합니다.

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>샘플 스키마 만들기
다음 쿼리를 실행하여 두 개의 테이블을 만듭니다.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

PostgreSQL에서 JSONB는 이진 형식의 JSON 데이터 형식이며 단일 열에 유연한 스키마를 저장합니다. 스키마는 내부에 모든 키와 값을 인덱싱할 수 있는 GIN 인덱스를 갖게 됩니다. GIN 인덱스가 있으면 직접 해당 페이로드에서 다양한 조건을 사용하여 쉽고 빠르게 쿼리를 실행할 수 있습니다. 따라서 데이터를 로드하기 전에 몇 가지 인덱스를 만들어 보겠습니다.

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

표준 테이블을 분할하기 위해 테이블별로 쿼리를 실행합니다. 분할하려는 테이블 및 분할 기준으로 사용할 키를 지정합니다. 여기서는 user_id를 기준으로 이벤트 및 사용자 테이블을 분할할 것입니다.

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>샘플 데이터 로드
COPY ... FROM PROGRAM을 사용하여 데이터를 로드합니다.

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>데이터 쿼리
이제 노드가 2개일 때 단순 쿼리에 소요되는 시간을 측정합니다.

```sql
SELECT COUNT(*) FROM github_events;
```
쿼리 실행 시간을 적어 놓습니다.


## <a name="scale-out"></a>확장
스케일 아웃 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> 미리 보기 릴리스에서는 크기 조정을 지원하지 않습니다. 예를 들어 아직 작업자 노드 수를 줄일 수는 없습니다. 이 작업을 수행해야 하는 경우 데이터를 추출/백업하고, 서버 그룹을 삭제하고, 작업자 노드가 더 적은 새 서버 그룹을 만든 다음 데이터를 가져와야 합니다.

이 예제에서는 다음 명령을 실행하여 작업자 노드 수를 2에서 4로 늘립니다.

```console
azdata arc postgres server edit -n postgres01 -w 4
```

노드를 추가하면 서버 그룹에 대해 보류 중 상태가 표시됩니다. 예를 들면 다음과 같습니다.
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

노드를 사용할 수 있게 되면 하이퍼스케일 분할 리밸런서가 자동으로 실행되고 데이터를 새 노드에 재배포합니다. 스케일 아웃 작업은 온라인 작업입니다. 노드가 추가되고 데이터가 노드 간에 재배포되는 동안 데이터는 쿼리에 사용할 수 있는 상태로 유지됩니다.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>서버 그룹의 새로운 모양 확인(선택 사항)
아래 방법 중 하나를 사용하여, 서버 그룹이 앞서 추가한 작업자 노드를 현재 사용하고 있는지 확인합니다.

#### <a name="with-azdata"></a>azdata 사용:
명령 실행:
```console
azdata arc postgres server list
```

이 명령은 네임스페이스에 생성된 서버 그룹의 목록을 반환하고 해당 서버 그룹의 작업자 노드 수를 나타냅니다. 예를 들면 다음과 같습니다.
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Kubectl 사용:
명령 실행:
```console
kubectl get postgresql-12
```

이 명령은 네임스페이스에 생성된 서버 그룹의 목록을 반환하고 해당 서버 그룹의 작업자 노드 수를 나타냅니다. 예를 들면 다음과 같습니다.
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> PostgreSQL 버전 12 대신 버전 11의 서버 그룹을 만든 경우 대신 _kubectl get postgresql-11_ 명령을 실행합니다.

#### <a name="with-a-sql-query"></a>SQL 쿼리 사용:
선택한 클라이언트 도구를 사용하여 서버 그룹에 연결하고 다음 쿼리를 실행합니다.

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>시나리오로 돌아가기

선택한 카운트 쿼리의 실행 시간을 샘플 데이터 세트와 비교하려면 동일한 카운트 쿼리를 사용합니다. 카운트 쿼리는 SQL 문을 변경하지 않고도 4개의 작업자 노드에 걸쳐 사용할 수 있습니다.

```sql
SELECT COUNT(*) FROM github_events;
```
실행 시간을 확인합니다.


> [!NOTE]
> 환경에 따라(예: 단일 노드 VM에서 `kubeadm`을 사용하여 테스트 서버 그룹을 배포한 경우) 실행 시간이 상당히 단축될 수 있습니다. Azure Arc 지원 PostgreSQL 하이퍼스케일을 통해 달성할 수 있는 성능 개선 유형에 대해 자세히 알아보려면 다음의 짧은 동영상을 시청하세요.
>* [Azure PostgreSQL 하이퍼스케일(Citus)을 사용한 고성능 HTAP](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Python 및 Azure PostgreSQL 하이퍼스케일(Citus)을 사용한 HTAP 애플리케이션 빌드](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>다음 단계

- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 스케일 업 및 스케일 다운(메모리, vCore)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)하는 방법에 대해 알아보세요.
- Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 서버 매개 변수를 설정하는 방법에 대해 알아보세요.
- PostgreSQL 하이퍼스케일용 Azure Database의 개념 및 방법 가이드를 읽고, 여러 PostgreSQL 하이퍼스케일 노드에 데이터를 분산하고, Postgres 하이퍼스케일용 Azure Database의 모든 기능을 활용해 보세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* 위 문서에서는 **Azure Portal에 로그인** 과 **Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)** 섹션을 건너뛰었습니다. Azure Arc 배포의 나머지 단계를 구현합니다. 이 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
