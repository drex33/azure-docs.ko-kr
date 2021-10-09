---
title: 서버 매개 변수 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버의 서버 매개 변수에 대해 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711897"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 서버 매개 변수

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL은 각 서버에 대해 구성 가능한 매개 변수의 하위 집합을 제공합니다. Postgres 매개 변수에 대한 자세한 내용은 [PostgreSQL 문서](https://www.postgresql.org/docs/13/config-setting.html)를 참조하세요.

## <a name="an-overview-of-postgresql-parameters"></a>PostgreSQL 매개 변수 개요 

Azure Database for PostgreSQL 서버는 생성 시 각 매개 변수에 대한 최적의 기본값으로 미리 구성됩니다. 정적 매개 변수는 서버를 다시 시작해야 하며 수퍼유저 액세스가 필요한 매개 변수는 사용자가 구성할 수 없습니다. 

보거나 수정할 수 있는 매개 변수를 검토하려면 Azure Portal 및 서버 매개 변수 페이지로 이동하는 것이 좋습니다. `ALTER DATABASE` 또는 `ALTER ROLE` 명령을 사용하여 사용자 또는 데이터베이스별로 매개 변수를 구성할 수도 있습니다.

>[!NOTE]
> Azure Database for PostgreSQL은 관리형 데이터베이스 서비스이므로 사용자에게 `postgresql.conf`와 같은 구성 파일을 보거나 수정할 수 있는 호스트 또는 OS 액세스 권한이 제공되지 않습니다. 파일 내용은 서버 매개 변수 페이지의 매개 변수 변경 사항에 따라 자동으로 업데이트됩니다.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="서버 매개 변수 - 포털":::

다음은 일부 매개 변수 목록입니다.


   | 매개 변수 이름             | 설명 |
|----------------------|--------|
| **max_connections** | 5,000개 연결로 설정할 수 있는 Postgres 유연한 서버에서 max_connections를 튜닝할 수 있습니다. 자세한 내용은 [제한 설명서](concepts-limits.md)를 참조하세요. | 
| **shared_buffers**    | 'shared_buffers' 설정은 선택한 SKU에 따라 변경됩니다(SKU에서 사용 가능한 메모리 결정). 범용 서버에는 2개의 vCore에 대해 2GB의 shared_buffers가 있습니다. 메모리 최적화 서버에는 2개의 vCore에 대해 4GB의 shared_buffers가 있습니다. shared_buffers 설정은 vCore가 계층에서 증가함에 따라 선형으로(대략) 조정됩니다. | 
| **shared_preload_libraries** | 이 매개 변수는 사전 정의된 지원 확장 세트로 구성에 사용할 수 있습니다. 항상 `azure` 확장(유지 관리 작업에 사용)과 `pg_stat_statements` 확장(pg_stat_statements.track 매개 변수를 사용하여 확장이 활성 상태인지 여부를 제어할 수 있음)을 로드합니다. |
| **connection_throttling** | 잘못된 암호 로그인 실패가 너무 많을 경우 IP당 임시 연결 제한을 사용하도록 설정하거나 사용하지 않도록 설정할 수 있습니다. |
 | **work_mem** | 이 매개 변수는 임시 디스크 파일에 쓰기 전에 내부 정렬 작업 및 해시 테이블에서 사용할 메모리 양을 지정합니다. 워크로드에 복잡한 정렬이 많은 쿼리가 적고 사용 가능한 메모리가 많은 경우 이 매개 변수를 늘리면 Postgres가 메모리 내 검사와 디스크로의 유출을 더 빠르게 수행할 수 있습니다.  그러나 하나의 복잡한 쿼리에 정렬 수, 해시 작업이 동시에 실행될 수 있기 때문에 주의해야 합니다. 이러한 각 작업은 디스크 기반 임시 파일에 쓰기를 시작하기 전에 값이 허용하는 만큼의 메모리를 사용합니다. 따라서 비교적 사용량이 많은 시스템에서 총 메모리 사용량은 개별 work_mem 매개 변수의 여러 번입니다. 이 값을 전역적으로 조정하려는 경우 초기 값으로 총 RAM * 0.25/ max_connections 수식을 사용할 수 있습니다. Azure Database for PostgreSQL - 유연한 서버는 이 매개 변수에 대해 4096-2097151바이트 범위를 지원합니다.|
| **effective_cache_size** |effective_cache_size 매개 변수는 운영 체제 및 데이터베이스 자체 내에서 디스크 캐싱에 사용할 수 있는 메모리 양을 예측합니다. PostgreSQL 쿼리 플래너는 RAM에서 고정되는지 여부를 결정합니다. 인덱스 검색은 더 높은 값에 대해 사용될 가능성이 높습니다. 그렇지 않으면 값이 낮은 경우 순차적 검색이 사용됩니다. 권장 사항 컴퓨터 총 RAM의 50%에서 Effective_cache_size 설정해야 합니다. |
| **maintenance_work_mem** | maintenance_work_mem 매개 변수는 기본적으로 진공, 인덱스 만들기 및 테이블 외래 키 추가 변경 작업과 같은 유지 관리 작업에 사용할 최대 메모리 양을 제공합니다.  해당 매개 변수의 기본값은 64KB입니다. 이 값은 work_mem보다 높게 설정하는 것이 좋습니다. 이렇게 하면 진공 성능이 향상될 수 있습니다. |
| **effective_io_concurrency** | PostgreSQL에서 동시에 실행할 수 있을 것으로 예상되는 동시 디스크 I/O 작업의 수를 설정합니다. 이 값을 높이면 개별 PostgreSQL 세션에서 병렬로 시작하려고 시도하는 I/O 작업 수가 증가합니다. 허용되는 범위는 1~1000이거나 비동기 I/O 요청의 발급을 사용하지 않도록 설정하는 0입니다. 현재 이 설정은 비트맵 힙 검사에만 영향을 미칩니다. |
 |**require_secure_transport** | 애플리케이션이 서버에 대한 SSL 연결을 지원하지 않는 경우 필요에 따라 이 매개 변수 값을 설정하여 클라이언트에서 보안 전송을 사용하지 않도록 설정할 수 `OFF` 있습니다. |

>[!NOTE]
> Azure Database for PostgreSQL - 유연한 서버 SUS를 확장 또는 축소하여 서버에 사용 가능한 메모리에 영향을 주면 위의 정보에 따라 메모리 전역 매개 변수(예: work_mem 또는 effective_cache_size)를 조정할 수 있습니다. 

 
## <a name="next-steps"></a>다음 단계

지원되는 PostgreSQL 확장에 대한 자세한 내용은 [확장 문서](concepts-extensions.md)를 참조하세요.
