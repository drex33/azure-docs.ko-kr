---
title: 감사 로깅 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버의 pgAudit 로깅 개념
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d4659e44475c09a1a42c06041e3f180357af9ee2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556036"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 감사 로깅

Azure Database for PostgreSQL - 유연한 서버의 데이터베이스 작업 감사 로깅은 PostgreSQL Audit 확장: [pgAudit](https://www.pgaudit.org/)를 통해 사용할 수 있습니다. pgAudit는 자세한 세션 및/또는 개체 감사 로깅을 제공합니다.

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

컴퓨팅 및 스토리지 스케일링과 같은 작업에 대한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

## <a name="usage-considerations"></a>용도 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL - 유연한 서버에서 나중에 Log Analytics에서 분석하기 위해 Azure Monitor 로그 저장소로 보낼 모든 로그를 구성할 수 있습니다. Azure Monitor 리소스 로깅을 사용하도록 설정하면 사용자의 선택에 따라 로그가 자동으로(JSON 형식으로) Azure Storage, Event Hubs 또는 Azure Monitor 로그에 전송됩니다.

Azure Storage, Event Hubs 또는 Azure Monitor 로그에 로깅을 설정하는 방법을 알아보려면 [서버 로그 문서](concepts-logging.md)의 리소스 로그 섹션을 참조하세요.

## <a name="installing-pgaudit"></a>pgAudit 설치

pgAudit를 설치하려면 서버의 공유 미리 로드 라이브러리에 포함시켜야 합니다. Postgres의 `shared_preload_libraries` 매개 변수를 변경하려면 서버를 다시 시작해야 합니다. 매개 변수는 [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md) 또는 [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate)를 사용하여 바꿀 수 있습니다.

[Azure Portal](https://portal.azure.com) 사용:

   1. Azure Database for PostgreSQL 유연한 서버를 선택 합니다.
   2. 사이드바에서 **서버 매개 변수** 를 선택합니다.
   3. `shared_preload_libraries` 매개 변수를 검색합니다.
   4. **pgaudit** 를 선택합니다.
     :::image type="content" source="./media/concepts-audit/shared-preload-libraries.png" alt-text=" Pgaudit에 대 한 Azure Database for PostgreSQL 사용 shared_preload_libraries를 보여 주는 스크린샷 ":::
   5. Psql에서 다음 쿼리를 실행 하 여 shared_preload_libraries에서 **pgaudit** 가 로드 되는지 확인할 수 있습니다.
        ```SQL
      show shared_preload_libraries;
      ```
      반환 되는 쿼리 결과에 **pgaudit** 가 표시 됩니다 shared_preload_libraries

   6. 클라이언트(예: psql)를 사용하여 서버에 연결하고 pgAudit 확장을 사용하도록 설정합니다.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 오류가 표시되면 `shared_preload_libraries`를 저장한 후 서버를 다시 시작했는지 확인합니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

pgAudit를 사용하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)은 실행된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)은 특정 관계로 범위가 지정된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정하도록 선택할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 전역으로 지정되며, 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.

[pgAudit를 사용하도록 설정](#installing-pgaudit)한 후 해당 매개 변수를 구성하여 로깅을 시작할 수 있습니다. PgAudit를 구성 하려면 아래 지침을 따를 수 있습니다. 다음과 같이 [Azure Portal](https://portal.azure.com)을 사용합니다.

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   2. 사이드바에서 **서버 매개 변수** 를 선택합니다.
   3. `pg_audit`매개 변수를 검색 합니다.
   4. 편집할 적절 한 설정 매개 변수를 선택 합니다. 예를 들어 `pgaudit.log` Azure Database for PostgreSQL을 보여 주는 스크린샷으로 설정 된 로깅을 시작 하려면 `WRITE` :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="pgaudit를 사용 하 여 로깅을 구성"::: 합니다.
   5. **저장** 단추를 클릭 하 여 변경 내용을 저장 합니다.


[pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)는 각 매개 변수의 정의를 제공합니다. 먼저 매개 변수를 테스트하고 예상대로 동작하는지 확인합니다.

> [!NOTE]
> `pgaudit.log_client`를 On으로 설정하면 로그를 파일에 기록하는 대신 psql과 같은 클라이언트 프로세스로 리디렉션합니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level`은 `pgaudit.log_client`가 on인 경우에만 사용하도록 설정됩니다.

> [!NOTE]
> Azure Database for PostgreSQL - 유연한 서버에서는 pgAudit 설명서에 설명된 대로 `-`(빼기) 기호 바로 가기를 사용하여 `pgaudit.log`를 설정할 수 없습니다. 모든 필수 문 클래스(READ, WRITE 등)는 개별적으로 지정해야 합니다.

## <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 로그 줄 시작 부분 근처의 `AUDIT:`로 표시됩니다. 항목 나머지 부분의 형식은 [pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명되어 있습니다.

## <a name="getting-started"></a>시작
빠르게 시작하려면 `pgaudit.log`를 `WRITE`로 설정하고, 서버 로그를 열어 출력을 검토합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
로그에 액세스하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage의 경우에는 [로그 스토리지 계정](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) 문서를 참조하세요. Event Hub의 경우에는 [Azure 로그 스트리밍](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) 문서를 참조하세요.

Azure Monitor 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로, AzureDiagnostics 테이블에서 쿼리될 수 있습니다. 테이블의 필드는 아래에 설명되어 있습니다. 쿼리와 경고에 대한 자세한 정보는 [Azure Monitor 로그 쿼리](../../azure-monitor/logs/log-query-overview.md) 개요에서 알아 보세요.

이 쿼리를 사용하여 시작할 수 있습니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대한 모든 pgAudit 항목을 검색합니다
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL - 유연한 서버의 로깅에 대해 알아보기](concepts-logging.md)
- [Azure Database for PostgreSQL - 유연한 서버에서 로깅을 설정하는 방법 및 로그에 액세스하는 방법에 대해 알아보기](howto-configure-and-access-logs.md)