---
title: Azure Database for PostgreSQL - 단일 서버의 감사 로깅
description: Azure Database for PostgreSQL - 단일 서버의 pgAudit 감사 로깅 개념.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458703"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버의 감사 로깅

Azure Database for PostgreSQL에서 데이터베이스 활동에 대 한 감사 로깅은 PostgreSQL Audit 확장 ( [pgaudit](https://www.pgaudit.org/))을 통해 사용할 수 있습니다. PgAudit 확장은 자세한 세션 및 개체 감사 로깅을 제공 합니다.

> [!NOTE]
> PgAudit 확장은 Azure Database for PostgreSQL 미리 보기 상태입니다. 범용 및 메모리 최적화 서버 에서만이 기능을 사용 하도록 설정할 수 있습니다.

계산 및 저장소 크기 조정과 같은 작업에 대 한 Azure 리소스 수준 로그를 원하는 경우 [azure platform Logs 개요](../azure-monitor/essentials/platform-logs-overview.md)를 참조 하세요.

## <a name="usage-considerations"></a>용도 고려 사항

기본적으로 pgAudit log 문은 Postgres 표준 로깅 기능을 사용 하 여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL Azure Portal 또는 Azure CLI를 통해 이러한 로그 파일을 다운로드할 수 있습니다. 파일 컬렉션에 대 한 최대 저장소는 1gb입니다. 각 파일은 최대 7 일 동안 사용할 수 있습니다. 기본값은 3 일입니다. 이 서비스는 단기 스토리지 옵션입니다.

또는 나중에 Log Analytics에서 분석을 위해 Azure Monitor 로그 저장소로 보낼 모든 로그를 구성할 수 있습니다. 리소스 로깅 모니터링을 사용 하도록 설정 하면 사용자의 선택에 따라 로그가 Azure Storage, Azure Event Hubs 또는 모니터 로그에 자동으로 전송 됩니다.

PgAudit를 사용 하도록 설정 하면 서버에서 많은 양의 로깅이 생성 되어 성능 및 로그 저장소에 영향을 줍니다. 더 장기적인 저장소 옵션과 분석 및 경고 기능을 제공 하는 모니터 로그를 사용 하는 것이 좋습니다. 추가 로깅의 성능 영향을 줄이려면 표준 로깅을 해제 합니다.

   1. 매개 변수를 `logging_collector` **OFF** 로 설정 합니다.
   1. 서버를 다시 시작하여 이 변경 내용을 적용합니다.

로그를 설정 하는 방법에 대 한 자세한 내용은 로그 Storage, Event Hubs 또는 모니터링 하는 방법에 대 한 자세한 내용은 [Azure Database for PostgreSQL 단일 서버에서 로그](concepts-server-logs.md)의 리소스 로그 섹션을 참조 하세요.

## <a name="install-pgaudit"></a>PgAudit 설치

PgAudit를 설치 하려면 서버에서 공유 하는 미리 로드 된 라이브러리에이를 포함 해야 합니다. Postgres 매개 변수를 변경 하려면 `shared_preload_libraries` 서버를 다시 시작 해야 적용 됩니다. [포털](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate)를 사용 하 여 매개 변수를 변경할 수 있습니다.

[포털](https://portal.azure.com)을 사용 하려면 다음을 수행 합니다.

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   1. 왼쪽의 **설정** 에서 **서버 매개 변수** 를 선택 합니다.
   1. **Shared_preload_libraries** 를 검색 합니다.
   1. **Pgaudit** 를 선택 합니다.
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="PGAUDIT에 대 한 shared_preload_libraries 활성화 Azure Database for PostgreSQL 보여 주는 스크린샷":::

   1. 서버를 다시 시작하여 변경 내용을 적용합니다.
   1. `pgaudit` `shared_preload_libraries` Psql에서 다음 쿼리를 실행 하 여가에 로드 되었는지 확인 합니다.
   
        ```SQL
      show shared_preload_libraries;
      ```
      을 `pgaudit` 반환 하는 쿼리 결과에가 표시 되어야 합니다 `shared_preload_libraries` .

   1. Psql과 같은 클라이언트를 사용 하 여 서버에 커넥트 하 고 pgAudit 확장을 사용 하도록 설정 합니다.
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 오류가 표시 되 면 저장 한 후 서버를 다시 시작 했는지 확인 `shared_preload_libraries` 합니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

PgAudit를 사용 하 여 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)은 실행된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)은 특정 관계로 범위가 지정된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정하도록 선택할 수 있습니다.

> [!NOTE]
> PgAudit 설정은 전역적으로 지정 되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.

[PgAudit를 설치한](#install-pgaudit)후에는 해당 매개 변수를 구성 하 여 로깅을 시작할 수 있습니다.

PgAudit를 구성 하려면 [포털](https://portal.azure.com)에서 다음을 수행 합니다.

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   1. 왼쪽의 **설정** 에서 **서버 매개 변수** 를 선택 합니다.
   1. **Pg_audit** 매개 변수를 검색 합니다.
   1. 편집할 적절 한 설정 매개 변수를 선택 합니다. 예를 들어 로깅을 시작 하려면 **pgaudit .log** 를 **WRITE** 로 설정 합니다.
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="PgAudit를 사용 하 여 로깅을 구성 Azure Database for PostgreSQL 보여 주는 스크린샷":::
   1. **저장** 을 선택하여 변경 내용을 저장합니다.

[pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)는 각 매개 변수의 정의를 제공합니다. 먼저 매개 변수를 테스트 하 고 예상 되는 동작이 있는지 확인 합니다. 예를 들어:

- **Pgaudit.log_client** 설정 되 면 로그는 파일에 기록 되는 대신 psql과 같은 클라이언트 프로세스로 리디렉션됩니다. 일반적으로이 설정을 사용 하지 않도록 설정 된 상태로 둡니다.
- **Pgaudit.log_level** 매개 변수는 **pgaudit.log_client** on 인 경우에만 사용할 수 있습니다.

> [!NOTE]
> Azure Database for PostgreSQL에서 pgAudit 설명서에 설명 된 대로 빼기 기호 바로 가기 ()를 사용 하 여 **pgaudit .log** 를 설정할 수 없습니다. `-` 읽기 및 쓰기와 같은 모든 필수 문 클래스는 개별적으로 지정 해야 합니다.

### <a name="audit-log-format"></a>감사 로그 형식

각 감사 항목은 로그 줄 시작 부분 근처의 `AUDIT:`로 표시됩니다. 항목 나머지 부분의 형식은 [pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명되어 있습니다.

감사 요구 사항을 충족하기 위해 다른 필드가 필요한 경우 Postgres 매개 변수 `log_line_prefix`를 사용하세요. 문자열은 `log_line_prefix` 모든 Postgres 로그 줄의 시작 부분에 출력 됩니다. 예를 들어 다음 `log_line_prefix` 설정은 타임스탬프, 사용자 이름, 데이터베이스 이름 및 프로세스 ID를 제공합니다.

```
t=%m u=%u db=%d pid=[%p]:
```

에 대해 자세히 알아보려면 `log_line_prefix` [PostgreSQL 설명서](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)를 참조 하세요.

### <a name="get-started"></a>시작하기

신속 하 게 시작 하려면 **pgaudit .log** 를 **WRITE** 로 설정 합니다. 그런 다음 로그를 열어 출력을 검토 합니다.

## <a name="view-audit-logs"></a>감사 로그 보기

.Log 파일을 사용 하는 경우 PostgreSQL 오류 로그와 동일한 파일에 감사 로그가 포함 됩니다. [포털](howto-configure-server-logs-in-portal.md) 또는 [CLI](howto-configure-server-logs-using-cli.md)에서 로그 파일을 다운로드할 수 있습니다.

Azure 리소스 로깅을 사용 하는 경우 로그에 액세스 하는 방법은 선택한 끝점에 따라 다릅니다. Storage [Azure 리소스 로그](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)를 참조 하세요. Event Hubs [Azure 리소스 로그](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)도 참조 하세요.

모니터 로그의 경우 로그는 선택한 작업 영역으로 전송 됩니다. Postgres 로그는 `AzureDiagnostics` 컬렉션 모드를 사용 하므로 표시 된 것 처럼 테이블에서 쿼리할 수 있습니다 `AzureDiagnostics` . 쿼리 및 경고에 대해 자세히 알아보려면 [Azure Monitor의 로그 쿼리](../azure-monitor/logs/log-query-overview.md)를 참조 하세요.

이 쿼리를 사용 하 여 시작 하세요. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날에 특정 서버에 대 한 모든 Postgres 로그를 검색 합니다.

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 로그인에 대해 알아봅니다](concepts-server-logs.md).
- [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate)를 사용 하 여 매개 변수를 설정 하는 방법에 대해 알아봅니다.
