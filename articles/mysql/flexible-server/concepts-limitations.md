---
title: 제한 사항 - Azure Database for MySQL - 유연한 서버
description: '이 문서에서는 Azure Database for MySQL - 유연한 서버의 제한 사항(예: 연결 수 및 스토리지 엔진 옵션)을 설명합니다.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 85718f5c9f3705d5530a2528c07a5a5c40a971c5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468388"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 유연한 서버에서의 제한 사항

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 Azure Database for MySQL 유연한 서버 서비스의 제한 사항을 설명합니다. MySQL 데이터베이스 엔진의 [일반 제한 사항](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)도 적용됩니다. 리소스 제한 (계산, 메모리, 저장소)에 대해 알아보려면 [계산 및 저장소](concepts-compute-storage.md) 문서를 참조 하세요.

## <a name="server-parameters"></a>서버 매개 변수

> [!NOTE]
> `max_connections`, `innodb_buffer_pool_size` 같은 서버 매개 변수에 대한 최솟값/최댓값을 찾고 있는 경우, 이 정보는 서버 매개 변수 개념 [서버 매개 변수](./concepts-server-parameters.md) 문서로 이동되었습니다.

Azure Database for MySQL은 서버 매개 변수 값 조정을 지원합니다. 일부 매개 변수의 최솟값과 최댓값(예: `max_connections`, `join_buffer_size`, `query_cache_size`)은 서버의 컴퓨팅 계층 및 컴퓨팅 크기에 따라 결정됩니다. 이러한 제한에 대한 자세한 내용은 [서버 매개 변수](./concepts-server-parameters.md)를 참조하세요.

## <a name="storage-engines"></a>스토리지 엔진

MySQL은 많은 스토리지 엔진을 지원합니다. Azure Database for MySQL 유연한 서버에서 지원 되거나 지원 되지 않는 저장소 엔진 목록은 다음과 같습니다.

### <a name="supported"></a>지원됨
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>권한 및 데이터 조작 지원

서버 매개 변수 및 설정이 많으면 실수로 서버 성능이 저하되거나 MySQL 서버의 ACID 속성이 무효화될 수 있습니다. 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 이 서비스에서는 여러 역할이 노출되지 않습니다.

MySQL 서비스는 기본 파일 시스템에 대한 직접 액세스를 허용하지 않습니다. 일부 데이터 조작 명령은 지원되지 않습니다.

### <a name="unsupported"></a>지원되지 않음

다음 항목은 지원되지 않습니다.
- DBA 역할: 제한됨 또는 관리자 사용자(새 서버를 만드는 동안 만들어짐)를 사용하면 대부분의 DDL 및 DML 문을 수행할 수 있습니다.
- SUPER 권한: 마찬가지로 [SUPER 권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)도 제한됩니다.
- DEFINER: 생성하려면 SUPER 권한이 필요하며, 제한됩니다. 백업을 사용하여 데이터를 가져올 경우 mysqldump를 수행할 때 수동으로 또는 `--skip-definer` 명령을 사용하여 `CREATE DEFINER` 명령을 제거하세요.
- 시스템 데이터베이스: [mysql 시스템 데이터베이스](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)는 읽기 전용이며 다양한 PaaS 기능을 지원하는 데 사용됩니다. `mysql` 시스템 데이터베이스를 변경할 수 없습니다.
- `SELECT ... INTO OUTFILE`: 서비스에서 지원되지 않습니다.

### <a name="supported"></a>지원됨
- `LOAD DATA INFILE`은 지원되지만 `[LOCAL]` 매개 변수를 지정하고 UNC 경로(SMB를 통해 탑재된 Azure Storage)로 전달해야 합니다. 또한 MySQL 클라이언트 버전 >= 8.0을 사용하는 경우 연결 문자열에 `-–local-infile=1` 매개 변수를 포함해야 합니다.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="zone-redundant-ha"></a>영역 중복 HA
- 이 구성은 서버를 만드는 동안에만 설정할 수 있습니다.
- 버스트 가능 컴퓨팅 계층에서는 지원되지 않습니다.

### <a name="networking"></a>네트워킹
- 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 서버를 *개인 액세스(VNet 통합)* 를 사용하여 만든 경우에는 만든 후 *공용 액세스(허용된 IP 주소)* 로 변경할 수 없으며, 그 반대의 경우도 마찬가지입니다.

### <a name="stopstart-operation"></a>작업 중지/시작
- 읽기 복제본 구성(원본/복제본 모두)에서는 지원되지 않습니다.

### <a name="scale-operations"></a>크기 조정 작업
- 프로비저닝된 서버 스토리지를 줄이는 것은 지원되지 않습니다.

### <a name="read-replicas"></a>읽기 복제본
- 영역 중복 HA 구성(기본/대기 모두)에서는 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 지원되지 않습니다. 주 버전으로 업그레이드하려는 경우 새 엔진 버전으로 만든 서버에 주 버전을 [덤프 및 복원](../concepts-migrate-dump-restore.md)합니다.

### <a name="restoring-a-server"></a>서버 복원
- 지정 시간 복구를 사용하는 새 서버는 기반으로 하는 원본 서버와 동일한 컴퓨팅 및 스토리지 구성을 사용하여 만들어집니다. 서버를 만든 후 새로 복원된 서버의 컴퓨팅 규모를 축소할 수 있습니다.
- 삭제된 서버 복원은 지원되지 않습니다.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>단일 서버에서 사용할 수 있지만 유연한 서버에서는 아직 지원되지 않는 기능
Azure Database for MySQL - 단일 서버에서 사용할 수 있는 일부 기능은 아직 유연한 서버에서 사용할 수 없습니다. 단일 서버와 유연한 서버 간의 전체 기능 비교 목록은 [Azure 설명서에서 적절한 MySQL 서버 옵션 선택](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 설명서에서 적절한 MySQL Server 옵션을 선택하는 방법](../select-right-deployment-type.md) 알아보기
- [유연한 서버에서 컴퓨팅 및 스토리지 옵션에 사용할 수 있는 기능](concepts-compute-storage.md) 이해
- [지원되는 MySQL 버전](concepts-supported-versions.md) 알아보기
- 빠른 시작: [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기](quickstart-create-server-portal.md)
