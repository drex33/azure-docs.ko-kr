---
title: Azure Database for MySQL로 마이그레이션할 올바른 도구를 선택 합니다.
description: 이 항목에서는 고객이로 마이그레이션하는 데 적합 한 도구를 선택 하는 데 도움이 되는 의사 결정 테이블을 제공 Azure Database for MySQL
ms.service: mysql
author: shriram-muthukrishnan
ms.author: shriramm
ms.reviewer: maghan
ms.topic: how-to
ms.custom: ''
ms.date: 10/12/2021
ms.openlocfilehash: 686874492f44df1171a320aaacf8e1967f8cb060
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273769"
---
# <a name="select-the-right-tools-for-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 마이그레이션할 올바른 도구를 선택 합니다.

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>개요

마이그레이션은 끌어오기가 어려운 다단계 프로젝트입니다. 플랫폼 간에 데이터베이스 서버를 마이그레이션하려면 데이터와 스키마를 마이그레이션해야 합니다. 또한 서버 구성 매개 변수, 네트워킹, 액세스 제어 규칙 등의 몇 가지 다른 구성 요소를 이동할 수 있습니다. 새 대상 플랫폼에서 데이터베이스 서버의 기능이 원본을 모방 하는지 확인 하는 데 필요 합니다. 

데이터베이스를 Azure Database for MySQL로 마이그레이션하는 방법에 대 한 자세한 내용 및 사용 사례는 [데이터베이스 마이그레이션 가이드](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)를 참조 하세요. 이 문서에서는 Azure로의 MySQL 마이그레이션을 계획 하 고 실행 하는 데 도움이 되는 포인터를 제공 합니다. 

일반적으로 마이그레이션은 오프 라인 또는 온라인으로 분류 될 수 있습니다. 

- 오프 라인 마이그레이션을 사용 하면 원본 서버가 오프 라인 상태가 되 고 데이터베이스의 덤프 및 복원이 대상 서버에서 수행 됩니다. 

- 온라인 마이그레이션 (가동 중지 시간을 최소화 하는 마이그레이션)을 사용 하면 원본 서버에서 업데이트를 허용 하 고 마이그레이션 솔루션이 원본 서버와 대상 서버 간의 진행 중인 변경 내용을 대상의 초기 덤프 및 복원과 함께 복제 합니다. 

응용 프로그램에서 일부 가동 중지 시간을 수용할 수 있는 경우 간단 하 고 쉽게 실행할 수 있으므로 오프 라인 마이그레이션은 항상 기본 설정입니다. 그러나 응용 프로그램의 가동 중지 시간을 최소화할 수 있는 경우에는 온라인 마이그레이션을 선택 하는 것이 가장 좋습니다. 청구 처리 및 전자 상거래와 같은 대다수 OLTP 시스템의 마이그레이션은이 범주에 속합니다. 

## <a name="decision-table"></a>의사 결정 테이블

Azure Database for MySQL로 마이그레이션하기 위한 적절 한 도구를 선택 하는 데 도움이 필요 하면 다음 표의 세부 정보를 고려 하십시오. 

| 시나리오 | 권장 도구 | 링크 |
|-------|------|------------|
| 데이터베이스를 이동 하는 오프 라인 마이그레이션 >= 1tb | **MyDumper/MyLoader** + 높은 계산 VM을 사용 하 여 덤프 및 복원 | [mydumper/myloader를 사용하여 큰 데이터베이스를 Azure Database for MySQL로 마이그레이션](concepts-migrate-mydumper-myloader.md) <br><br> [대량 데이터베이스를 Azure Database for MySQL으로 마이그레이션하기 위한 모범 사례](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)|
| 데이터베이스 < 1TB로 이동 하는 오프 라인 마이그레이션  | 원본 및 대상 간의 네트워크 대역폭이 양호 하면 (예: Highspeed express 경로) **AZURE DMS** (database migration service)를 사용 합니다.  <br><br>   **디스크나** <br><br> 원본 및 Azure 간의 네트워크 대역폭이 낮은 경우 **Mydumper/Myloader + 높은 계산 VM** 을 사용 하 여 저속 네트워크를 통해 데이터를 효율적으로 이동 하는 압축 설정을 활용 합니다.  <br><br> **디스크나** <br><br> **Mysqldump** 및 **MySQL 워크 벤치 내보내기/가져오기** 유틸리티를 사용 하 여 더 작은 데이터베이스에 대해 오프 라인 마이그레이션을 수행 합니다.  | [자습서: DMS를 사용 하 여 Azure Database for MySQL 오프 라인으로 MySQL 마이그레이션-Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)<br><br>  [MySQL Workbench를 사용하여 Amazon RDS for MySQL를 Azure Database for MySQL로 마이그레이션](how-to-migrate-rds-mysql-workbench.md)<br><br>  [가져오기 및 내보내기 - Azure Database for MySQL](concepts-migrate-import-export.md)|
| 온라인 마이그레이션 |  **데이터 복제를 사용 하는 Mydumper/Myloader** <br><br> **데이터를 복제 하는 Mysqldump** 는 작은 데이터베이스 (100gb 미만)로 간주할 수 있습니다.  이러한 메서드는 외부 및 플랫폼 간 마이그레이션 모두에 적용 됩니다. | [입력 데이터 복제 구성 - Azure Database for MySQL Flexible Server](flexible-server/how-to-data-in-replication.md) <br><br> [자습서: 최소한의 가동 중지 시간으로 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션](howto-migrate-single-flexible-minimum-downtime.md) |
|단일에서 유연한 서버 마이그레이션 |  **오프 라인**:이 스크립트는 [GitHub](https://github.com/Azure/azure-mysql/tree/master/azuremysqltomysqlmigrate) 에서 호스트 되는 사용자 지정 셸 스크립트는 보안 설정 및 서버 매개 변수 구성과 같은 다른 서버 구성 요소도 이동 합니다. <br><br>**온라인**: **Mydumper/Myloader (데이터-복제 포함)** |  [간단한 5 단계를 통해 Azure Database for MySQL 단일 서버에서 유연한 서버로 마이그레이션!](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)<br><br>   [자습서: 최소한의 가동 중지 시간으로 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션](howto-migrate-single-flexible-minimum-downtime.md)| 

## <a name="next-steps"></a>다음 단계
* [MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)

<br><br>
