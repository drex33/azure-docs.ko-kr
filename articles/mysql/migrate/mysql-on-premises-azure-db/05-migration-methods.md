---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 마이그레이션 방법'
description: 원본에서 대상으로 데이터를 가져오려면 MySQL 도구 또는 기능을 사용하여 마이그레이션을 수행해야 합니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 6a4b0e2dc084168e1bffa539924ad897ac93961e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085005"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-migration-methods"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 마이그레이션 방법

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[계획](04-planning.md)

## <a name="overview"></a>개요

원본에서 대상으로 데이터를 가져오려면 MySQL 도구 또는 기능을 사용하여 마이그레이션을 수행해야 합니다.

다음 단계를 시작하기 전에 전체 평가 및 계획 단계를 완료하는 것이 중요합니다. 수집되는 의사 결정 및 데이터는 마이그레이션 경로 및 도구 선택 의존성입니다.

이 섹션에서는 일반적으로 사용되는 다음 도구를 살펴봅니다.

  - MySQL Workbench

  - mysqldump

  - mydumper 및 myloader

  - 입력 데이터 복제(binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/)는 개발자와 관리자가 MySQL 인스턴스를 디자인, 개발 및 관리할 수 있는 풍부한 GUI 환경을 제공합니다.

최신 버전의 MySQL Workbench는 데이터베이스를 원본에서 대상으로 이동할 때 정교한 [개체 마이그레이션 기능](https://www.mysql.com/products/workbench/migrate/)을 제공합니다.

#### <a name="data-import-and-export"></a>데이터 가져오기 및 내보내기

MySQL Workbench는 테이블 및 데이터베이스 개체의 전체 또는 부분 내보내기 및 가져오기를 수행하는 마법사 기반 UI를 제공합니다. MySQL Workbench를 사용하는 방법의 예제는 [가져오기 및 내보내기를 사용하여 MySQL 데이터베이스 마이그레이션](../../concepts-migrate-import-export.md)을 참조하세요.

### <a name="dump-and-restore-mysqldump"></a>덤프 및 복원(mysqldump)

`mysqldump`는 일반적으로 MySQL 설치의 일부로 제공됩니다. 데이터베이스를 특정 시점으로 다시 빌드하기 위해 재생할 수 있는 SQL 문 세트와 같은 논리적 백업을 만들기 위해 실행할 수 있는 [클라이언트 유틸리티](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)입니다. `mysqldump`는 대량의 데이터를 백업하거나 마이그레이션하기 위한 빠르고 스케일링 가능한 솔루션으로 사용되지 않습니다. 큰 SQL 삽입 문 세트를 실행하면 인덱스를 업데이트하는 데 필요한 디스크 I/O로 인해 성능이 저하될 수 있습니다. 그러나 원래 스키마가 필요한 다른 도구와 결합되는 경우 `mysqldump`는 데이터베이스 및 테이블 스키마를 생성할 수 있는 적합한 도구입니다. 스키마는 대상 랜딩 존 환경을 만들 수 있습니다.

`mysqldump` 유틸리티는 데이터 마이그레이션 단계에서 유용한 기능을 제공합니다. 유틸리티를 실행하기 전에 성능 고려 사항을 평가해야 합니다. [성능 고려 사항](../../concepts-migrate-dump-restore.md#performance-considerations)을 참조하세요.

### <a name="mydumper-and-myloader"></a>mydumper 및 myloader

빠른 마이그레이션이 필요한 대규모 데이터베이스가 있는 환경에서는 [mydumper 및 myloader](https://github.com/maxbube/mydumper)를 사용해야 합니다. 이 도구는 C++로 작성되며 다중 스레드 기술을 이용하여 대상 MySQL 인스턴스에 최대한 빠르게 데이터를 전송합니다. `mydumper` 및 `myloader`는 병렬 처리를 활용하며 마이그레이션 속도를 10배 이상 높일 수 있습니다.

퍼블릭 다운로드에 사용할 수 있는 도구의 이진 릴리스는 Linux용으로 컴파일되었습니다. Windows에서 이 도구를 실행하려면 오픈 소스 프로젝트를 다시 컴파일해야 합니다. 소스 코드를 컴파일하고 릴리스를 만드는 것은 대부분 사용자에게 간단한 작업이 아닙니다.

### <a name="data-in-replication-binlog"></a>입력 데이터 복제(binlog)

다른 데이터베이스 관리 시스템과 마찬가지로 MySQL은 [binlog 복제](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)라는 로그 복제 기능을 제공합니다. `binlog` 복제 기능은 데이터 마이그레이션 및 읽기 복제본 생성에 도움이 됩니다.

binlog 복제를 이용하여 온라인 시나리오에서 [데이터를 Azure Database for MySQL로 마이그레이션](../../concepts-data-in-replication.md)합니다. 데이터 복제는 최종 대상 데이터를 변경하는 데 필요한 가동 중지 시간을 줄이는 데 도움이 됩니다.

`binlog` 복제 기능을 사용하기 위해 몇 가지 설정 [요구 사항](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)이 있습니다.

  - 그런 다음, 마스터 서버는 MySQL InnoDB 엔진을 사용하는 것이 좋습니다. InnoDB 이외의 스토리지 엔진을 사용하는 경우 해당 테이블을 InnoDB로 마이그레이션해야 합니다.

  - 마이그레이션 사용자는 이진 로깅을 구성하고 마스터 서버에서 새 사용자를 만들 수 있는 권한이 있어야 합니다.

  - 마스터 서버에서 SSL을 사용하도록 설정한 경우 도메인에 제공된 SSL CA 인증서가 mysql.az\_replication\_change\_master 저장 프로시저에 포함되어 있는지 확인합니다. 다음 [예제](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) 및 master\_ssl\_ca 매개 변수를 참조하세요.

  - 마스터 서버의 IP 주소가 Azure Database for MySQL 복제본 서버의 방화벽 규칙에 추가되었는지 확인합니다. Azure Portal 또는 Azure CLI를 사용하여 방화벽 규칙을 업데이트합니다.

  - 마스터 서버를 호스트하는 컴퓨터에서 포트 3306에 대한 인바운드 및 아웃바운드 트래픽을 둘 다 허용하는지 확인합니다.

  - 마스터 서버에 원본에서 대상으로 액세스 가능한 IP 주소(공용 또는 개인)가 있는지 확인합니다.

복제를 사용하여 마이그레이션을 수행하기 위한 자세한 내용은 [Azure Database for MySQL 입력 데이터 복제를 구성하는 방법](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)을 검토합니다.

`binlog` 복제 방법에는 높은 CPU 및 추가 스토리지 요구 사항이 있습니다. 마이그레이션 사용자는 온라인 마이그레이션 중에 원본 시스템에 배치된 부하를 테스트하고 부하가 허용 가능한지 확인해야 합니다.

### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

[Azure DMS(Database Migration Services)](https://azure.microsoft.com/services/database-migration/)는 관리자가 마이그레이션의 다양한 설정을 추적하고 필요한 경우 다시 사용할 수 있는 Azure 클라우드 기반 도구입니다. DMS를 사용하려면 다양한 원본 및 대상을 가리키는 설정을 사용하여 마이그레이션 프로젝트를 만듭니다. DMS는 [오프라인 마이그레이션](../../../dms/tutorial-mysql-azure-mysql-offline-portal.md)을 지원합니다. 또한 온-프레미스 데이터 워크로드 및 클라우드 기반 워크로드(예: Amazon RDS(Relational Database Service) MySQL)를 지원합니다.

DMS 서비스는 온라인 도구이지만 MySQL의 `binlog` 복제 기능을 사용하여 작업을 완료합니다. 현재 DMS는 오프라인 마이그레이션 프로세스를 부분적으로 자동화합니다. DMS는 대상 Azure Database for MySQL 인스턴스에서 일치하는 스키마를 생성하고 적용해야 합니다. `mysqldump` 클라이언트 유틸리티를 사용하여 스키마를 내보낼 수 있습니다.

### <a name="fastestminimum-downtime-migration"></a>가장 빠른/최소 가동 중지 시간 마이그레이션

데이터를 마이그레이션하기 위한 다양한 경로가 있습니다. 사용할 경로를 결정하는 것은 마이그레이션 팀 기술 세트 및 데이터베이스와 애플리케이션 소유자가 수락할 수 있는 가동 중지 시간의 함수입니다. 일부 도구는 다중 스레드 병렬 데이터 마이그레이션 접근 방식을 지원하지만 다른 도구는 테이블 데이터의 간단한 마이그레이션용으로만 디자인되었습니다.

가장 빠르고 완전한 경로는 `binlog` 복제를 사용하는 것이지만(MySQL에서 직접 또는 DMS를 통해) 기본 키를 추가하는 비용이 발생합니다.

### <a name="decision-table"></a>의사 결정 테이블

WWI가 MySQL 워크로드를 마이그레이션하는 데 사용할 수 있는 여러 경로가 있습니다. 다음과 같이 잠재적 경로 및 각 경로의 장단점이 나와 있는 표를 제공했습니다.

| Objective | Description | 도구 | 사전 요구 사항 | 장점 | 단점 |
|-----------|-------------|------|---------------|------------|---------------|
| **가능한 가장 빠른 마이그레이션** | 병렬 접근 방식| mydumper 및 myloader | Linux | 고도로 병렬 처리됨 | 대상 제한 |
| **온라인 마이그레이션** | 원본을 최대한 오랫동안 가동 상태로 유지 | binlog | 없음 | 원활 | 추가 처리 및 스토리지 |
| **오프라인 마이그레이션** | 원본을 최대한 오랫동안 가동 상태로 유지 | DMS(Database Migration Service)| 없음 | 반복 가능한 프로세스 | 데이터로만 제한되고 모든 MySQL 버전 지원 |
| **고도로 사용자 지정된 오프라인 마이그레이션** | 선택적으로 개체 내보내기 | mysqldump | 없음 | 고도로 사용자 지정 기능 | 수동 |
| **오프라인 마이그레이션 반자동화됨** | UI 기반 내보내기 및 가져오기 | MySQL Workbench | 다운로드 및 설치 | 반자동화됨 | 일반적인 스위치 세트만 지원됩니다. |

### <a name="wwi-scenario"></a>WWI 시나리오

WWI는 해당 컨퍼런스 데이터베이스를 첫 번째 마이그레이션 워크로드로 선택했습니다. 연간 컨퍼런스 일정의 차이로 인해 위험이 가장 적고 사용 가능한 가동 중지 시간이 가장 크기 때문에 해당 워크로드가 선택되었습니다. 또한 마이그레이션 팀의 평가에 따라 MySQL Workbench를 사용하여 오프라인 마이그레이션을 수행하려고 시도했음을 확인했습니다.

### <a name="migration-methods-checklist"></a>마이그레이션 방법 검사 목록

  - 대상 및 원본 환경에서 적합한 방법이 선택되었는지 확인합니다.

  - 해당 방법이 비즈니스 요구 사항을 충족할 수 있는지 확인합니다.

  - 데이터 워크로드가 해당 방법을 지원하는지 항상 확인합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Test Plans](./06-test-plans.md)