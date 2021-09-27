---
title: 감사 로깅 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버의 pgAudit 감사 로깅 개념.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: f15ffe95f002ac74553363485fe90d16f8864347
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552530"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버의 감사 로깅

Azure Database for PostgreSQL - 단일 서버의 데이터베이스 작업 감사 로깅은 PostgreSQL Audit 확장: [pgAudit](https://www.pgaudit.org/)를 통해 사용할 수 있습니다. pgAudit는 자세한 세션 및/또는 개체 감사 로깅을 제공합니다.

> [!NOTE]
> pgAudit는 Azure Database for PostgreSQL에서 미리 보기 상태입니다.
> 이 확장은 범용 및 메모리 액세스에 최적화된 서버에서만 사용하도록 설정할 수 있습니다.

컴퓨팅 및 스토리지 크기 조정과 같은 작업에 대한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

## <a name="usage-considerations"></a>용도 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL에서 이러한 .log 파일은 Azure Portal 또는 CLI를 통해 다운로드할 수 있습니다. 파일 컬렉션의 최대 스토리지는 1GB이고, 각 파일은 최대 7일 동안 사용할 수 있습니다(기본값은 3일). 이 서비스는 단기 스토리지 옵션입니다.

또는 나중에 Log Analytics에서 분석하기 위해 Azure Monitor 로그 저장소로 전송되도록 모든 로그를 구성할 수 있습니다. Azure Monitor 리소스 로깅을 사용하도록 설정하면 사용자의 선택에 따라 로그가 자동으로(JSON 형식으로) Azure Storage, Event Hubs 또는 Azure Monitor 로그에 전송됩니다.

pgAudit를 사용하도록 설정하면 서버에서 많은 양의 로깅이 생성되어 성능 및 로그 스토리지에 영향을 줍니다. Azure Monitor 로그를 사용하는 것이 좋습니다. 이 서비스는 분석 및 경고 기능뿐만 아니라 장기 스토리지 옵션도 제공합니다. 추가 로깅의 성능 영향을 줄이려면 표준 로깅을 해제하는 것이 좋습니다.

   1. `logging_collector` 매개 변수를 Off로 설정합니다. 
   2. 서버를 다시 시작하여 이 변경 내용을 적용합니다.

Azure Storage, Event Hubs 또는 Azure Monitor 로그에 로깅을 설정하는 방법을 알아보려면 [서버 로그 문서](concepts-server-logs.md)의 리소스 로그 섹션을 참조하세요.

## <a name="installing-pgaudit"></a>pgAudit 설치

pgAudit를 설치하려면 서버의 공유 미리 로드 라이브러리에 포함시켜야 합니다. Postgres의 `shared_preload_libraries` 매개 변수를 변경하려면 서버를 다시 시작해야 합니다. 매개 변수는 [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md) 또는 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate)를 사용하여 바꿀 수 있습니다.

[Azure Portal](https://portal.azure.com) 사용:

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   2. 사이드바에서 **서버 매개 변수** 를 선택합니다.
   3. `shared_preload_libraries` 매개 변수를 검색합니다.
   4. **pgaudit** 를 선택합니다.
     :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Azure Database for PostgreSQL 보여 shared_preload_libraries 사용하도록 설정하는 스크린샷 ":::
   5. 서버를 다시 시작하여 변경 내용을 적용합니다.
   6. psql에서 다음 쿼리를 실행하여 **shared_preload_libraries pgaudit가** 로드되어 있는지 확인할 수 있습니다.
        ```SQL
      show shared_preload_libraries;
      ```
      쿼리 결과에는 shared_preload_libraries 반환되는 **pgaudit가** 표시됩니다.

   7. 클라이언트(예: psql)를 사용하여 서버에 연결하고 pgAudit 확장을 사용하도록 설정합니다.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 오류가 표시되면 `shared_preload_libraries`를 저장한 후 서버를 다시 시작했는지 확인합니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

pgAudit를 사용하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)은 실행된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)은 특정 관계로 범위가 지정된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정하도록 선택할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 전역적으로 지정되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.

[pgAudit를 설치](#installing-pgaudit)한 후 해당 매개 변수를 구성하여 로깅을 시작할 수 있습니다. pgAudit를 구성하려면 아래 지침을 따르면 됩니다. 다음과 같이 [Azure Portal](https://portal.azure.com)을 사용합니다.

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   2. 사이드바에서 **서버 매개 변수** 를 선택합니다.
   3. `pg_audit`매개 변수를 검색합니다.
   4. 편집할 적절한 설정 매개 변수를 선택합니다. 예를 들어 로깅을 시작하려면 Azure Database for PostgreSQL 보여 주는 `pgaudit.log` `WRITE` :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="스크린샷 - pgaudit를 사용하여 로깅 구성 ":::
   5. **저장** 단추를 클릭하여 변경 내용을 저장합니다.



[pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)는 각 매개 변수의 정의를 제공합니다. 먼저 매개 변수를 테스트하고 예상대로 동작하는지 확인합니다.


> [!NOTE]
> `pgaudit.log_client`를 On으로 설정하면 로그를 파일에 기록하는 대신 psql과 같은 클라이언트 프로세스로 리디렉션합니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level`은 `pgaudit.log_client`가 on인 경우에만 사용하도록 설정됩니다.

> [!NOTE]
> Azure Database for PostgreSQL에서는 pgAudit 설명서에 설명된 것처럼 `-` 기호 바로 가기를 사용하여 `pgaudit.log`를 설정할 수 없습니다. 모든 필수 문 클래스(READ, WRITE 등)는 개별적으로 지정해야 합니다.

### <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 로그 줄 시작 부분 근처의 `AUDIT:`로 표시됩니다. 항목 나머지 부분의 형식은 [pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명되어 있습니다.

감사 요구 사항을 충족하기 위해 다른 필드가 필요한 경우 Postgres 매개 변수 `log_line_prefix`를 사용하세요. `log_line_prefix`는 모든 Postgres 로그 줄의 시작 부분에 출력되는 문자열입니다. 예를 들어 다음 `log_line_prefix` 설정은 타임스탬프, 사용자 이름, 데이터베이스 이름 및 프로세스 ID를 제공합니다.

```
t=%m u=%u db=%d pid=[%p]:
```

`log_line_prefix`에 대해 자세히 알아보려면 [PostgreSQL 설명서](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)를 참조하세요.

### <a name="getting-started"></a>시작
빠르게 시작하려면 `pgaudit.log`를 `WRITE`로 설정하고, 로그를 열어 출력을 검토합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
.log 파일을 사용하는 경우 PostgreSQL 오류 로그와 동일한 파일에 감사 로그가 포함됩니다. 로그 파일은 Azure [Portal](howto-configure-server-logs-in-portal.md) 또는 [CLI](howto-configure-server-logs-using-cli.md)에서 다운로드할 수 있습니다. 

Azure 리소스 로깅을 사용하는 경우 로그에 액세스하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage의 경우에는 [로그 스토리지 계정](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) 문서를 참조하세요. Event Hub의 경우에는 [Azure 로그 스트리밍](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) 문서를 참조하세요.

Azure Monitor 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로, AzureDiagnostics 테이블에서 쿼리될 수 있습니다. 테이블의 필드는 아래에 설명되어 있습니다. 쿼리와 경고에 대한 자세한 정보는 [Azure Monitor 로그 쿼리](../azure-monitor/logs/log-query-overview.md) 개요에서 알아 보세요.

이 쿼리를 사용하여 시작할 수 있습니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대한 모든 Postgres 로그를 검색합니다
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL의 로깅에 대해 알아보기](concepts-server-logs.md)
- [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md) 또는 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate)를 사용하여 매개 변수를 설정하는 방법을 알아봅니다.
