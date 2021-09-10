---
title: 감사 로깅 - Azure Database for PostgreSQL - 하이퍼스케일(Citus)
description: Azure Database for PostgreSQL의 pgAudit 감사 로깅 개념 - 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 330f102dffb392540879d8d583e02a68fbe436c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536593"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL의 감사 로깅 - 하이퍼스케일(Citus)

> [!IMPORTANT]
> 하이퍼스케일(Citus) pgAudit 확장은 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 데이터베이스 작업 감사 로깅은 PostgreSQL Audit 확장: [pgAudit](https://www.pgaudit.org/)을 통하여 사용할 수 있습니다. pgAudit은 자세한 세션 및/또는 개체 감사 로깅을 제공합니다.

컴퓨팅 및 스토리지 스케일링과 같은 작업에 대한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

## <a name="usage-considerations"></a>용도 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 나중에 Log Analytics에서 분석하기 위하여 Azure Monitor 로그 저장소로 보낼 모든 로그를 구성할 수 있습니다. Azure Monitor 리소스 로깅을 사용하도록 설정하면 사용자의 선택에 따라 로그가 자동으로(JSON 형식으로) Azure Storage, Event Hubs 또는 Azure Monitor 로그에 전송됩니다.

## <a name="enabling-pgaudit"></a>pgAudit 사용

pgAudit 확장은 모든 하이퍼스케일(Citus) 서버 그룹 노드에서 미리 설치되어 사용할 수 있습니다. 노드에서 사용하도록 설정되지 않은 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

pgAudit를 사용하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)은 실행된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)은 특정 관계로 범위가 지정된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정하도록 선택할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 전역적으로 지정되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.
>
> 또한, pgAudit 설정은 서버 그룹의 노드별로 지정됩니다. 모든 노드에서 변경을 적용하려면 개별 노드마다 적용하여야 합니다.

로깅을 시작하려면 pgAudit 매개 변수를 구성하여야 합니다. [pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)는 각 매개 변수의 정의를 제공합니다. 먼저 매개 변수를 테스트하고 예상대로 동작하는지 확인합니다.

> [!NOTE]
> `pgaudit.log_client`를 On으로 설정하면 로그를 파일에 기록하는 대신 psql과 같은 클라이언트 프로세스로 리디렉션합니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level`은 `pgaudit.log_client`가 on인 경우에만 사용하도록 설정됩니다.

> [!NOTE]
> Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서는 pgAudit 설명서에 설명된 대로 `-`(빼기) 기호 바로 가기를 사용하여 `pgaudit.log`를 설정할 수 없습니다. 모든 필수 문 클래스(READ, WRITE 등)는 개별적으로 지정하여야 합니다.

## <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 로그 줄 시작 부분 근처의 `AUDIT:`로 표시됩니다. 항목 나머지 부분의 형식은 [pgAudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명되어 있습니다.

## <a name="getting-started"></a>시작
빠르게 시작하려면 `pgaudit.log`를 `WRITE`로 설정하고, 서버 로그를 열어 출력을 검토합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
로그에 액세스하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage의 경우에는 [로그 스토리지 계정](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) 문서를 참조하세요. Event Hub의 경우에는 [Azure 로그 스트리밍](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) 문서를 참조하세요.

Azure Monitor 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로, AzureDiagnostics 테이블에서 쿼리될 수 있습니다. 테이블의 필드는 아래에 설명되어 있습니다. 쿼리와 경고에 대한 자세한 정보는 [Azure Monitor 로그 쿼리](../azure-monitor/logs/log-query-overview.md) 개요에서 알아 보세요.

이 쿼리를 사용하여 시작할 수 있습니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대한 모든 pgAudit 항목을 검색합니다
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 로깅을 설정하는 방법 및 로그에 액세스하는 방법에 대하여 알아보기](howto-hyperscale-logging.md)
