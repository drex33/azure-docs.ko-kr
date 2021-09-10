---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 성능 기준'
description: 기존 MySQL 워크로드를 이해하는 일은 성공적인 마이그레이션을 위해 수행할 수 있는 가장 좋은 투자 중 하나입니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2077ef62ddabf7910d5a634c07262c9d29905cf4
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084969"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-performance-baselines"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 성능 기준

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[Test Plans](06-test-plans.md)

## <a name="overview"></a>개요

기존 MySQL 워크로드를 이해하는 일은 성공적인 마이그레이션을 위해 수행할 수 있는 가장 좋은 투자 중 하나입니다. 뛰어난 시스템 성능은 적절한 하드웨어 및 뛰어난 애플리케이션 디자인에 따라 좌우됩니다. CPU, 메모리, 디스크 및 네트워킹 같은 항목은 예상되는 부하에 맞게 적절하게 크기를 조정하고 구성해야 합니다. 하드웨어 및 구성은 시스템 성능 수식의 일부입니다. 개발자는 데이터베이스 쿼리 로드 및 실행하는 데 비용이 가장 많이 드는 쿼리를 파악해야 합니다. 비용이 가장 많이 드는 쿼리에 집중하면 전반적인 성능 메트릭에 상당한 영향을 줄 수 있습니다.

마이그레이션 프로젝트에는 쿼리 성능의 기준을 만드는 것이 중요합니다. 성능 기준은 마이그레이션된 데이터 워크로드에 대한 Azure 랜딩 존 구성을 확인하는 데 사용할 수 있습니다. 대부분의 시스템은 연중무휴로 실행되고 최대 로드 시간이 다릅니다. 기준의 최고 워크로드를 캡처하는 것이 중요합니다. 메트릭은 여러 번 캡처됩니다. 이 문서의 뒷부분에서는 원본 서버 매개 변수와 이러한 매개 변수가 전체 성능 기준을 파악하는 데 얼마나 중요한지를 알아봅니다. 마이그레이션 프로젝트 중에는 서버 매개 변수를 간과하면 안 됩니다.

## <a name="tools"></a>도구

서버 메트릭 및 데이터베이스 워크로드 정보를 수집하는 데 사용되는 도구는 다음과 같습니다. 캡처한 메트릭을 사용하여 적절한 Azure Database for MySQL 계층과 관련된 스케일링 옵션을 결정합니다.

  - [MySQL Enterprise Monitor:](https://www.mysql.com/products/enterprise/monitor.html) 이 무료 엔터프라이즈 버전 도구는 비용이 많이 드는 쿼리, 서버 메트릭, 파일 I/O 및 토폴로지 정보에 대한 정렬된 목록을 제공할 수 있습니다.

  - [PMM(Percona Monitoring and Management)](https://www.percona.com/software/database-tools/percona-monitoring-and-management): 최고의 오픈 소스 데이터베이스 모니터링 솔루션입니다. 배포된 위치에 관계없이 복잡성을 줄이고, 성능을 최적화하고, 중요 비즈니스용 데이터베이스 환경의 보안을 개선하는 데 유용합니다.

## <a name="server-parameters"></a>서버 매개 변수

MySQL 서버 기본 구성은 워크로드를 적절하게 지원하지 않을 수 있습니다. MySQL에는 다양한 서버 매개 변수가 있지만 대부분의 경우 마이그레이션 팀은 까다로운 매개 변수에 집중해야 합니다. **원본** 및 **대상** 환경에서 다음 매개 변수를 평가해야 합니다. 구성이 잘못되면 마이그레이션 속도에 영향을 줄 수 있습니다. 마이그레이션 단계를 실행할 때 이러한 매개 변수를 다시 확인합니다.

  - **innodb\_buffer\_pool\_size**: 큰 값을 지정하면 디스크 I/O를 활용하기 전에 메모리 내 리소스가 먼저 사용됩니다. 일반적인 값의 범위는 사용 가능한 메모리의 80-90%입니다. 예를 들어 메모리가 8GB인 시스템은 풀 크기에 대해 5-6GB를 할당해야 합니다.

  - **innodb\_log\_file\_size**: 다시 실행 로그는 빠르고 지속 가능한 쓰기를 보장하는 데 사용됩니다. 이 트랜잭션 백업은 시스템 작동이 중단되는 동안 유용합니다. Innodb\_log\_file\_size = 512M(다시 실행 로그 1GB 제공)부터 시작하면 쓰기를 위한 충분한 공간이 제공됩니다. MySQL 5.6 이상을 사용하는 쓰기 집약적 애플리케이션은 innodb\_log\_file\_size = 4G로 시작해야 합니다.

  - **max\_connections**: 이 매개 변수를 통해 `Too many connections` 오류를 완화할 수 있습니다. 기본값은 151개의 연결입니다. 애플리케이션 수준에서 연결 풀을 사용하는 것이 좋지만 서버 연결 구성도 늘려야 할 수 있습니다.

  - **innodb\_file\_per\_table**: 이 설정은 공유 테이블스페이스 또는 각 테이블의 별도의 .ibd 파일에 데이터 및 인덱스를 저장해야 하는 경우 InnoDB에 알려 줍니다. 테이블별로 하나의 파일을 만들면 테이블을 삭제하거나, 자르거나, 다시 작성할 때 서버에서 공간을 회수할 수 있습니다. 많은 수의 테이블을 포함하는 데이터베이스는 파일 구성별로 테이블을 사용하면 안 됩니다. MySQL 5.6에서 기본값은 ON입니다. 이전 데이터베이스 버전은 데이터를 로드하기 전에 구성을 ON으로 설정해야 합니다. 이 설정은 새로 만든 테이블에만 영향을 줍니다.

  - **innodb\_flush\_log\_at\_trx\_commit**: 기본 설정인 1은 InnoDB가 완전히 ACID 규격임을 의미합니다. 이러한 낮은 위험 수준의 트랜잭션 구성에서는 다시 실행 로그에 각 변경 내용을 플러시하는 데 추가 fsyncs가 필요하므로 디스크 속도가 느린 시스템에서는 상당한 오버헤드가 발생할 수 있습니다. 커밋된 트랜잭션이 다시 실행 로그에 1초에 한 번만 플러시되기 때문에 매개 변수를 2로 설정하면 안정성이 약간 떨어집니다. 이러한 위험은 일부 마스터 상황에서 허용될 수 있으며, 복제본에 사용하기에 적절한 값입니다. 값이 0이면 시스템 성능을 향상시킬 수 있지만 오류 발생 시 데이터베이스 서버에서 일부 데이터를 손실할 가능성이 높습니다. 결과적으로 0 값은 복제본에 대해서만 사용해야 합니다.

  - **innodb\_flush\_method**: 이 설정은 데이터 및 로그가 디스크에 플러시되는 방식을 제어합니다. 배터리로 보호된 쓰기 저장 캐시를 사용하는 하드웨어 RAID 컨트롤러가 있는 경우 `O_DIRECT`를 사용합니다. 다른 시나리오에는 `fdatasync`(기본값)를 사용합니다.

  - **innodb\_log\_buffer\_size**: 이 설정은 아직 커밋되지 않은 트랜잭션의 버퍼 크기입니다. 기본값(1MB)은 일반적으로 적절합니다. 대형 Blob/텍스트 필드가 있는 트랜잭션은 버퍼를 빠르게 채우고 추가 I/O 로드를 트리거할 수 있습니다. `Innodb_log_waits` 상태 변수를 확인하고 0이 아니면 `innodb_log_buffer_size`를 늘립니다.

  - **query\_cache\_size**: 쿼리 캐시는 보통의 동시성 상황에서 볼 수 있는 잘 알려진 병목 상태입니다. 캐시를 사용하지 않으려면 초기값을 0으로 설정해야 합니다. 예: `query_cache_size = 0`. MySQL 5.6 이상에 대한 기본값입니다.

  - **log\_bin**: 이 설정은 이진 로깅을 사용하도록 설정합니다. 서버가 복제 마스터 역할을 하는 경우 이진 로깅을 반드시 사용하도록 설정해야 합니다.

  - **server\_id**: 이 설정은 복제 토폴로지의 ID 서버에 대한 고유 값이 됩니다.

  - **expire\_logs\_days**: 이 설정은 이진 로그가 자동으로 제거되는 기간(일)을 제어합니다.

  - **skip\_name\_resolve**: 클라이언트 호스트 이름을 확인할 사용자입니다. DNS 속도가 느리면 연결이 느려집니다. 이름 확인을 사용하지 않도록 설정하는 경우 GRANT 문은 IP 주소만 사용해야 합니다. IP를 사용하려면 이전에 만든 GRANT 문을 다시 실행해야 합니다.

검토하려면 다음 명령을 실행하여 서버 매개 변수를 파일로 내보냅니다. Azure Database for MySQL 서버에 적절한 경우 마이그레이션 후에 몇 가지 간단한 구문 분석을 통해 출력을 사용하여 동일한 서버 매개 변수를 다시 적용할 수 있습니다. [Azure Portal을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성](../../howto-server-parameters.md)을 참조하세요.

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

MySQL 5.5.60 기본 설치된 서버 매개 변수는 [부록](15-appendix.md#default-server-parameters-mysql-55-and-azure-database-for-mysql)에서 찾을 수 있습니다.

마이그레이션을 시작하기 전에 원본 MySQL 구성 설정을 내보냅니다. 마이그레이션 후에 이러한 값을 Azure 랜딩 존 인스턴스 설정과 비교합니다. 대상 Azure 랜딩 존 인스턴스의 기본값에서 설정이 수정된 경우 마이그레이션 후에 다시 설정되었는지 확인합니다. 또한 마이그레이션 사용자는 마이그레이션 전에 서버 매개 변수를 설정할 수 있는지 확인해야 합니다.

구성할 수 없는 서버 매개 변수 목록은 [구성할 수 없는 서버 매개 변수](../../concepts-server-parameters.md#non-configurable-server-parameters)를 참조하세요.

### <a name="egress-and-ingress"></a>송신 및 수신

각각의 데이터 마이그레이션 도구 및 경로에 대해 가능한 가장 빠른 송신 및 수신을 지원하도록 원본 및 대상 MySQL 서버 매개 변수를 수정해야 합니다. 도구에 따라 매개 변수는 다를 수 있습니다. 예를 들어 병렬로 마이그레이션을 수행하는 도구에는 단일 스레드 도구에 비해 원본 및 대상에 대해 더 많은 연결이 필요할 수 있습니다.

데이터 세트의 영향을 받을 수 있는 제한 시간 매개 변수를 검토합니다. 이러한 위협은 다음과 같습니다.

  - [connect\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

또한 최댓값에 영향을 주는 매개 변수를 검토합니다.

  - [max\_allowed\_packet](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> 일반적인 마이그레이션 오류는 `MySQL server has gone away`입니다. 여기에 설명된 매개 변수는 이 오류를 해결하기 위한 일반적인 원인입니다.

## <a name="wwi-scenario"></a>WWI 시나리오

WWI는 해당 Conference 데이터베이스 워크로드를 검토하고 매우 작은 부하가 발생했음을 확인했습니다. 기본 계층 서버를 작동하고 있지만 나중에 다른 계층으로 마이그레이션하는 작업을 수행하지 않으려고 합니다. 배포 중인 서버는 결과적으로 다른 MySQL 데이터 워크로드를 호스트하게 될 예정이므로 `General Performance` 계층을 선택했습니다.

MySQL 데이터베이스를 검토하면 MySQL 5.5 서버가 초기 설치 중에 설정된 기본값 서버 매개 변수를 사용하여 실행되고 있는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데이터 마이그레이션](./08-data-migration.md)
