---
title: PostgreSQL 데이터베이스의 데이터를 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹으로 마이그레이션
titleSuffix: Azure Arc-enabled database services
description: PostgreSQL 데이터베이스의 데이터를 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹으로 마이그레이션
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ec41b338acbf055e8fa499ff6b4e867844b30e4d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553060"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL 데이터베이스를 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹으로 마이그레이션

이 문서에서는 기존 PostgreSQL 데이터베이스(Azure Arc 지원 Data Services에서 호스트되지 않은 데이터베이스)를 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹으로 가져오는 단계를 설명합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>고려 사항

Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹은 PostgreSQL의 커뮤니티 버전이며 CitusData 확장이 활성화된 상태로 실행됩니다. Azure Arc 외부 PostgreSQL에서 작동하는 모든 도구는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 작동합니다.


따라서 Postgres에 대해 지금 사용하는 도구 세트로 다음을 수행할 수 있습니다.
1. Azure Arc 외부에 호스트된 인스턴스에서 Postgres 데이터베이스를 백업합니다.
2. Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 데이터베이스를 복원합니다.

사용자가 수행할 수 있는 작업은 다음과 같습니다.
- 서버 매개 변수 재설정
- 보안 컨텍스트 재설정: 사용자, 역할 재생성 및 권한 재설정...

이 백업/복원 작업을 수행하기 위해 Postgres에 대해 백업/복원을 수행할 수 있는 모든 도구를 사용할 수 있습니다. 예를 들면 다음과 같습니다.
- Azure Data Studio 및 해당 Postgres 확장
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>예제

`pgAdmin` 도구를 사용하여 단계를 설명해 보겠습니다.
다음 설정을 고려하세요.
- **원본:**  
    운영 체제 미설치 서버에서 온-프레미스로 실행되고 이름이 JEANYDSRV인 Postgres 서버 버전 12이고 1개의 행 :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="마이그레이션-원본":::을 포함한 T1 테이블이 있는 MyOnPremPostgresDB라는 데이터베이스를 호스트합니다.

- **대상:**  
    Azure Arc 환경에서 실행되고 이름이 postgres01인 Postgres 서버 버전 12입니다. 표준 Postgres 데이터베이스를 제외한 데이터베이스는 없습니다.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="마이그레이션-대상":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>원본 데이터베이스의 백업을 온-프레미스로 가져오기

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="마이그레이션-원본-백업":::

구성하려면:
1. 파일 이름 지정: **MySourceBackup**
2. 형식을 **사용자 지정**
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="마이그레이션-원본-백업-구성":::으로 설정합니다.

백업이 성공적으로 완료됩니다.  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="마이그레이션-원본-백업-완료됨":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 대상 시스템에서 빈 데이터베이스 만들기

> [!NOTE]
> `pgAdmin` 도구에 Postgres 인스턴스를 등록하려면 Kubernetes 클러스터에서 인스턴스의 공용 IP를 사용하고 포트 및 보안 컨텍스트를 적절하게 설정해야 합니다. 다음 명령을 실행한 후 `psql` 엔드포인트 줄에서 해당 세부 정보를 찾을 수 있습니다.

```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```
다음과 같은 출력을 반환합니다.
```console
{
  "instances": [
    {
      "endpoints": [
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
],
"engine": "PostgreSql",
"name": "postgres01"
}
  ],
  "namespace": "arc"
}
```

대상 데이터베이스의 이름을 **RESTORED_MyOnPremPostgresDB** 로 지정합니다.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="마이그레이션-대상-데이터베이스-만들기" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Arc 설정에서 데이터베이스 복원

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="마이그레이션-데이터베이스-복원":::

복원 구성:
1. **MySourceBackup** 라는 복원할 백업이 포함된 파일을 가리킵니다.
2. **사용자 지정 또는 tar**
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="마이그레이션-데이터베이스-복원-구성":::으로 설정된 형식 유지

3. **복원** 을 클릭합니다.  

   복원에 성공했습니다.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="마이그레이션-데이터베이스-복원-완료됨":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>데이터베이스가 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 성공적으로 복원되었는지 확인

다음 방법 중 하나를 사용할 수 있습니다.

**`pgAdmin`에서:**  

Azure Arc 설정에서 호스트되는 Postgres 인스턴스를 확장합니다. 복원된 데이터베이스의 테이블이 표시되고 데이터를 선택하면 온-프레미스 인스턴스에 있는 것과 동일한 행이 표시됩니다.

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="마이그레이션-데이터베이스-복원-확인":::

**Azure Arc 설정 내 `psql`에서:**  

Arc 설정 내에서 `psql`를 사용하여 Postgres 인스턴스에 연결하고 데이터베이스 컨텍스트를 `RESTORED_MyOnPremPostgresDB`로 설정하고 데이터를 쿼리할 수 있습니다.

1. 연결 문자열을 구성 하는 데 도움이 되는 끝점을 나열 합니다 `psql` .

   ```Az CLI
   az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   ```Az CLI
   {
     "instances": [
       {
         "endpoints": [
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
   ],
   "engine": "PostgreSql",
   "name": "postgres01"
   }
     ],
     "namespace": "arc"
   }
   ```

1. `psql` 연결 문자열에서 `-d` 매개 변수를 사용하여 데이터베이스 이름을 표시합니다. 아래의 명령을 사용하여 암호를 입력하라는 메시지가 표시됩니다.

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` 연결

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. 테이블을 선택하면 온-프레미스 Postgres 인스턴스에서 복원한 데이터가 표시됩니다.

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - 스케일 아웃하고 PostgreSQL 하이퍼스케일 서버 그룹의 작업자 노드에 데이터를 분할/분산하기 전에는 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 실행해도 성능이 크게 향상되지 않습니다. [다음 단계](#next-steps)를 참조하세요.
>
> - 현재 온-프레미스 또는 기타 클라우드에서 실행되는 기존 Postgres 인스턴스를 “Azure Arc에 온보딩”할 수 없습니다. 즉, 기존 Postgres 인스턴스에 일종의 “Azure Arc 에이전트”를 설치하여 Azure Arc에서 사용할 수 있는 Postgres 설정으로 만들 수 없습니다. 대신 새 Postgres 인스턴스를 만들고 여기에 데이터를 전송해야 합니다. 위에 표시된 방법을 사용하여 이 작업을 수행하거나 원하는 ETL 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- PostgreSQL 하이퍼스케일용 Azure Database의 개념 및 방법 가이드를 읽고, 여러 PostgreSQL 하이퍼스케일 노드에 데이터를 분산하고 PostgreSQL 하이퍼스케일용 Azure Database의 모든 기능을 활용하세요.
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *이 문서에서는 **Azure Portal에 로그인** 섹션을 건너뛰고 **Postgres-하이퍼스케일(Citus)에 대한 Azure 데이터베이스를 만듭니다**. Azure Arc 배포의 나머지 단계를 구현합니다. 이 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-in-postgresql-hyperscale-server-group.md)
