---
title: 제한 사항 - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB에 대한 연결 수 및 스토리지 엔진 옵션과 같은 제한 사항을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664302"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량, 스토리지 엔진 지원, 권한 지원, 데이터 조작 명령문 지원 및 기능 제한 사항에 대해 설명합니다.

## <a name="server-parameters"></a>서버 매개 변수

> [!NOTE]
> 만약 `max_connections` 및 `innodb_buffer_pool_size`와 같은 서버 매개 변수에 대한 최솟값/최댓값을 찾고 있는 경우 이 정보는 **[서버 매개 변수](./concepts-server-parameters.md)** 문서로 이동되었습니다.

Azure Database for MariaDB는서버 매개 변수 값 조정을 지원합니다. 일부 매개 변수의 최솟값과 최댓값(예: `max_connections`, `join_buffer_size`, `query_cache_size`)은 서버의 가격 책정 계층 및 vCore에 의해 결정됩니다. 이러한 제한에 대한 자세한 내용은 [서버 매개 변수를](./concepts-server-parameters.md) 참조하세요.

초기 배포 시 Azure for MariaDB 서버는 표준 시간대 정보에 대한 시스템 테이블을 포함하지만 이 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

"validate_password" 및 "caching_sha2_password" 같은 암호 플러그인은 본 서비스에서 지원되지 않습니다.

## <a name="storage-engine-support"></a>스토리지 엔진 지원

### <a name="supported"></a>지원됨
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>권한 및 데이터 조작 지원

서버 매개 변수 및 설정이 많으면 의도치 않게 서버 성능이 저하되거나 MariaDB 서버의 ACID 속성이 무효화될 수 있습니다. 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 이 서비스는 여러 역할을 노출하지 않습니다. 

MariaDB 서비스는 기본 파일 시스템에 직접 액세스를 허용하지 않습니다. 일부 데이터 조작 명령은 지원되지 않습니다. 

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>지원되지 않음

다음은 지원되지 않습니다.
- DBA 역할: 제한됨. 또는 관리자 사용자(새 서버를 만드는 동안 만들어짐)를 사용하면 대부분의 DDL 및 DML 문을 수행할 수 있습니다. 
- SUPER 권한: 마찬가지로 [SUPER 권한](https://mariadb.com/kb/en/library/grant/#global-privileges) 도 제한됩니다.
- DEFINER: 생성하려면 SUPER 권한이 필요하며, 제한됩니다. 백업을 사용하여 데이터를 가져올 경우 mysqldump를 수행할 때 수동으로 또는 `--skip-definer` 명령을 사용하여 `CREATE DEFINER` 명령을 제거하세요.
- 시스템 데이터베이스: [mysql 시스템 데이터베이스](https://mariadb.com/kb/en/the-mysql-database-tables/) 는 읽기 전용이며 다양한 PaaS 기능을 지원하는 데 사용됩니다. 이는 `mysql` 시스템 데이터베이스로 변경할 수 없습니다.
- `SELECT ... INTO OUTFILE`: 서비스에서 지원되지 않습니다.

### <a name="supported"></a>지원됨
- `LOAD DATA INFILE`은 지원되지만 `[LOCAL]` 매개 변수를 지정하고 UNC 경로(SMB를 통해 탑재된 Azure Storage)로 전달해야 합니다.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층 간의 동적 크기 조정은 현재 지원되지 않습니다.
- 서버 스토리지 크기를 줄이는 것은 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
- PITR 기능을 사용하면 새 서버가 기반으로 하는 서버와 동일한 구성으로 새 서버가 만들어집니다.
- 삭제된 서버 복원은 지원되지 않습니다.

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 미리 생성된 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

### <a name="storage-size"></a>스토리지 크기
- 가격 책정 계층당 스토리지 크기 제한에 대한 [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="current-known-issues"></a>현재 알려진 문제
- 연결이 설정된 후에 MariaDB 서버 인스턴스가 잘못된 서버 버전을 표시합니다. 올바른 서버 인스턴스 엔진 버전을 설치하려면 `select version();` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계
- [각 서비스 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md)
- [지원되는 MariaDB 데이터베이스 버전](concepts-supported-versions.md)
