---
title: 로그 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버에서 로깅을 구성하는 방법, 스토리지와 분석에 대해 설명합니다
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 7c269c26710711c23dd200f688bf1eb55d3925fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597971"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 로그



Azure Database for PostgreSQL를 사용하여 Postgres의 표준 로그를 구성하고 액세스할 수 있습니다. 로그는 구성 오류 및 비정상적인 성능 문제를 인지하고, 해결하며, 복구하는 데 사용됩니다. 구성 및 액세스할 수 있는 로깅 정보에는 오류, 쿼리 정보, autovacuum 레코드, 연결 및 검사점이 있습니다. (트랜잭션 로그 액세스는 불가능합니다).

감사 로깅은 `pgaudit` Postgres 확장을 통해 가능합니다. 자세히 알아보려면 [감사 개념](concepts-audit.md) 문서를 참조하세요.

## <a name="configure-logging"></a>로깅 구성

로깅 서버 매개 변수를 사용하면 서버에서 로깅을 구성할 수 있습니다. Postgres 로그 매개 변수에 대한 자세한 내용을 알아보려면 Postgres 설명서의 [로그할 시기](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 및 [로그할 항목](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 섹션을 참조하세요. Azure Database for PostgreSQL에서 전부는 아니지만 대부분의 Postgres 로깅 매개 변수를 구성할 수 있습니다.

Azure Database for PostgreSQL 매개 변수를 구성하는 방법을 알아보려면 [포털 설명서](howto-configure-server-parameters-using-portal.md) 또는 [CLI 설명서](howto-configure-server-parameters-using-cli.md)를 참조하세요.

> [!NOTE]
> 가령 명령문 로깅과 같이 많은 양의 로그를 구성하면 상당한 성능 오버헤드가 추가될 수 있습니다. 

## <a name="accessing-logs"></a>로그 액세스

Azure Database for PostgreSQL은 Azure Monitor 진단 로그와 통합됩니다. 진단 설정을 사용하면 Postgres 로그를 JSON 형식으로 분석 및 경고를 위해 Azure Monitor Logs로 보내고, 스트리밍을 위해서는 Event Hubs로, 아카이빙을 위해서는 Azure Storage로 보냅니다. 

### <a name="log-format"></a>로그 형식

다음 표에서는 **PostgreSQLLogs** 형식의 필드를 설명합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 선택한 출력 엔드포인트에 따라 달라질 수 있습니다. 

|**필드** | **설명** |
|---|---|
| TenantId | 테넌트 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| 유형 | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| SubscriptionId | 서버가 속한 구독의 GUID |
| ResourceGroup | 서버가 속한 리소스 그룹의 이름 |
| ResourceProvider | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORPOSTGRESQL`입니다. |
| ResourceType | `Servers` |
| ResourceId | 리소스 URI |
| 리소스 | 서버의 이름 |
| 범주 | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 로깅 수준(예: LOG, ERROR, NOTICE) |
| 메시지 | 기본 로그 메시지 | 
| 도메인 | 서버 버전(예: postgres-10) |
| 세부 정보 | 보조 로그 메시지(해당하는 경우) |
| ColumnName | 열 이름(해당하는 경우) |
| SchemaName | 스키마 이름(해당하는 경우) |
| DatatypeName | 데이터 형식 이름(해당하는 경우) |
| LogicalServerName | 서버의 이름 | 
| _ResourceId | 리소스 URI |
| 접두사 | 로그 줄의 prefix |


## <a name="next-steps"></a>다음 단계

- [로그를 구성하고 액세스하는 방법](howto-configure-and-access-logs.md)에 대해 자세히 알아봅니다.
- [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.
- [감사 로그](concepts-audit.md)에 대해 자세히 알아봅니다
