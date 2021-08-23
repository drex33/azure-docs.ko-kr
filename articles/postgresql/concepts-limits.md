---
title: 제한 - Azure Database for PostgreSQL 단일 서버
description: 이 문서에서는 연결 수 및 스토리지 엔진 옵션과 같은 Azure Database for PostgreSQL - 단일 서버의 제한을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: d9d817077b1bdfb0bd53ec18f25def1c9615d2fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528708"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버의 제한
다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다. 리소스(컴퓨팅, 메모리, 스토리지) 계층에 대해 알아보려면 [가격 책정 계층](concepts-pricing-tiers.md) 문서를 참조하세요.


## <a name="maximum-connections"></a>최대 연결 수
가격 책정 계층 및 vCores당 최대 연결 수는 아래와 같습니다. Azure 시스템에는 Azure Database for PostgreSQL 서버를 모니터링하기 위해 5개의 연결이 필요합니다. 

|**가격 책정 계층**| **vCore**| **최대 연결** | **최대 사용자 연결** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|범용| 2| 150| 145|
|범용| 4| 250| 245|
|범용| 8| 480| 475|
|범용| 16| 950| 945|
|범용| 32| 1500| 1495|
|범용| 64| 1900| 1895|
|메모리 최적화| 2| 300| 295|
|메모리 최적화| 4| 500| 495|
|메모리 최적화| 8| 960| 955|
|메모리 최적화| 16| 1900| 1895|
|메모리 최적화| 32| 1987| 1982|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

> [!IMPORTANT]
> 최상의 환경을 위해 pgBouncer와 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

PostgreSQL 연결은 유효 상태라도 약 10MB의 메모리를 차지할 수 있습니다. 또한 새 연결을 만드는 데 시간이 걸립니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조하세요.

## <a name="functional-limitations"></a>기능 제한 사항
### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층 간의 동적 크기 조정은 현재 지원되지 않습니다.
- 서버 스토리지 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](./howto-migrate-using-dump-and-restore.md)합니다.

> PostgreSQL 버전 10 이전에는 [PostgreSQL 버전 관리 정책](https://www.postgresql.org/support/versioning/)에서 _주 버전_ 업그레이드를 첫 번째 _또는_ 두 번째 번호의 증가로 간주했습니다(예: 9.5에서 9.6은 _주_ 버전 업그레이드로 간주됨).
> 버전 10부터는 첫 번째 번호의 변경만 주 버전 업그레이드로 간주됩니다(예: 10.0에서 10.1은 _부_ 버전 업그레이드이고 10에서 11은 _주_ 버전 업그레이드).

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

### <a name="restoring-a-server"></a>서버 복원
- PITR 기능을 사용하면 기반으로 하는 서버와 동일한 가격 책정 계층 구성을 사용하여 새 서버가 만들어집니다.
- 복원 동안 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙이 없습니다. 방화벽 규칙은 새 서버에 대해 개별적으로 설정돼야 합니다.
- 삭제된 서버 복원은 지원되지 않습니다.

### <a name="utf-8-characters-on-windows"></a>Windows의 UTF-8 문자
- 일부 시나리오에서는 UTF-8 문자가 Windows의 오픈 소스 PostgreSQL에서 완전히 지원되지 않으며, Azure Database for PostgreSQL에 영향을 줍니다. 자세한 내용은 [postgresql-archive의 버그 #15476](https://www.postgresql.org/message-id/2101.1541220270%40sss.pgh.pa.us)에 대한 스레드를 참조하세요.

### <a name="gss-error"></a>GSS 오류
**GSS** 와 관련된 오류가 표시되면 Azure Postgres 단일 서버가 아직 완전히 지원하지 않는 최신 클라이언트/드라이버 버전을 사용하고 있을 수 있습니다. 이 오류는 [JDBC 드라이버 버전 42.2.15 및 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868)에 영향을 주는 것으로 알려져 있습니다.
   - 11월 말까지 업데이트를 완료할 예정입니다. 그동안 작업 중인 드라이버 버전을 사용해 보세요.
   - 또는 GSS 요청을 사용하지 않도록 설정하는 것이 좋습니다.  `gssEncMode=disable`와 같은 연결 매개 변수를 사용합니다.

### <a name="storage-size-reduction"></a>스토리지 크기 감소
스토리지 크기를 줄일 수 없습니다. 원하는 스토리지 크기로 새 서버를 만들고, 수동 [덤프 및 복원](./howto-migrate-using-dump-and-restore.md)을 수행하고, 새 서버로 데이터베이스를 마이그레이션해야 합니다.

## <a name="next-steps"></a>다음 단계
- [각 가격 책정 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md) 이해
- [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md) 알아보기
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](howto-restore-server-portal.md) 검토
