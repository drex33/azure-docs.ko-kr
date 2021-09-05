---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 데이터 마이그레이션'
description: 데이터를 업그레이드하거나 마이그레이션하기 전 신중한 단계로, 업그레이드 전에 MySQL Workbench를 사용하거나 mysqldump 명령을 통해 수동으로 데이터베이스를 내보냅니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 9e53243424f4cb0b289c0ebba9c82cb9fb4853cc
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084945"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 데이터 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[성능 기준](07-performance-baselines.md)

## <a name="back-up-the-database"></a>데이터베이스 백업

데이터를 업그레이드하거나 마이그레이션하기 전 신중한 단계로, 업그레이드 전에 MySQL Workbench를 사용하거나 `mysqldump` 명령을 통해 수동으로 데이터베이스를 내보냅니다.

## <a name="offline-vs-online"></a>오프라인 대 온라인

마이그레이션 도구를 선택하기 전에 마이그레이션이 온라인인지 오프라인인지를 결정해야 합니다.

  - **오프라인 마이그레이션** 을 사용하면 마이그레이션이 진행되는 동안 시스템이 중단됩니다. 이 옵션을 사용하면 트랜잭션이 발생하지 않으며 데이터 상태가 Azure에서 복원될 때 예상되는 상태와 정확히 일치합니다.

  - **온라인 마이그레이션** 은 데이터를 거의 실시간으로 마이그레이션합니다. 이 옵션은 데이터 워크로드를 사용하는 사용자 또는 애플리케이션에 대한 가동 중지 시간이 거의 없는 경우에 적합합니다. 프로세스에는 `binlog` 또는 유사한 복제 방법을 사용하여 데이터를 복제하는 작업이 포함됩니다.

WWI의 경우 해당 환경에는 대상 마이그레이션 시간 프레임의 인바운드 및 아웃바운드 연결에 적절한 변경 내용을 허용하지 않는 몇 가지 복잡한 네트워킹 및 보안 요구 사항이 있습니다. 이러한 복잡성과 요구 사항은 기본적으로 온라인 접근 방식을 고려 대상에서 제외합니다.

> [!NOTE]
> 오프라인 및 온라인 마이그레이션에 대한 자세한 내용은 계획 및 평가 섹션을 검토하세요.

## <a name="data-drift"></a>데이터 드리프트

오프라인 마이그레이션 전략을 사용하면 데이터 드리프트가 발생할 수 있습니다. 새로 수정된 원본 데이터가 마이그레이션된 데이터와 동기화되지 않는 경우 데이터 드리프트가 발생합니다. 이 경우 전체 내보내기 또는 델타 내보내기가 필요합니다. 데이터베이스에 대한 모든 트래픽을 중지한 다음, 내보내기를 수행하여 이 문제를 완화할 수 있습니다. 모든 데이터 수정 트래픽을 중지할 수 없는 경우 드리프트를 고려해야 합니다.

데이터베이스 테이블에 숫자 기반 기본 키와 같은 열이 없거나 마이그레이션해야 하는 모든 테이블에서 일부 유형의 수정 및 생성 날짜가 없는 경우 변경 내용을 확인하는 것이 복잡해질 수 있습니다.

예를 들어 숫자 기반 기본 키가 있고 마이그레이션을 정렬 순서대로 가져오는 경우 가져오기가 중지된 위치를 확인하고 해당 위치에서 다시 시작하는 것이 비교적 간단합니다. 숫자 기반 키가 없는 경우 수정 및 만들기 날짜를 활용할 수 있으며 대상에 표시된 마지막 날짜부터 마이그레이션을 다시 시작할 수 있도록 정렬 방식으로 가져와야 합니다.

## <a name="performance-recommendations"></a>성능 권장 사항

### <a name="exporting"></a>내보내기

  - mydumper와 같은 다중 스레드 모드에서 실행할 수 있는 내보내기 도구 사용

  - MySQL 8.0을 사용할 때 해당되는 경우 [분할된 테이블](https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html)을 사용하여 내보내기 속도를 높입니다.

### <a name="importing"></a>가져오기

  - 데이터를 로드한 후 클러스터형 인덱스 및 기본 키를 만듭니다. 데이터를 기본 키 순서로 로드하거나, 기본 키인 경우 일부 날짜 열(예: 수정 날짜 또는 생성 날짜)을 정렬된 순서로 로드합니다.

  - 데이터가 로드될 때까지 보조 인덱스 만들기를 연기합니다. 로드한 후 모든 보조 인덱스를 만듭니다.

  - 로드하기 전에 외래 키 제약 조건을 비활성화합니다. 외래 키 검사 비활성화는 상당한 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.

  - 병렬로 데이터를 로드합니다. 리소스 경합을 일으킬 수 있는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다.

## <a name="perform-the-migration"></a>마이그레이션 수행

  - 데이터베이스 백업

  - Azure 랜딩 존 만들기 및 확인

  - 원본 서버 매개 변수 구성

  - 대상 서버 매개 변수 구성

  - 데이터베이스 개체(스키마, 사용자 등) 내보내기

  - 데이터 내보내기

  - 데이터베이스 개체 가져오기

  - 데이터 가져오기

  - 유효성 검사

  - 대상 서버 매개 변수 다시 설정

  - 애플리케이션 마이그레이션

## <a name="common-steps"></a>공통 단계

어떤 경로를 따르든 수행해야 하는 일반적인 단계가 있습니다.

  - 지원되는 Azure MySQL 버전으로 업그레이드

  - 인벤토리 데이터베이스 개체

  - 사용자 및 권한 내보내기

## <a name="migrate-to-latest-mysql-version"></a>최신 MySQL 버전으로 마이그레이션

WWI Conference 데이터베이스가 5.5를 실행 중이므로 업그레이드를 수행해야 합니다. CIO가 최신 버전의 MySQL(현재 8.0)로 업그레이드하도록 요청했습니다.

8\.0으로 업그레이드하는 두 가지 방법이 있습니다.

  - 현재 위치

  - 내보내기/가져오기

업그레이드를 수행하기로 결정할 때 **업그레이드 검사기** 도구를 실행하여 충돌이 있는지 확인하는 것이 중요합니다. 예를 들어 MySQL Server 8.0으로 업그레이드할 때 도구는 다음과 같은 충돌을 확인합니다.

  - MySQL 8.0의 예약어와 충돌하는 데이터베이스 개체 이름

  - utf8mb3 문자 집합 사용

  - ZEROFILL/표시 길이 형식 특성 사용

  - 8\.0의 테이블과 충돌하는 테이블 이름

  - 임시 형식 사용

  - 64자보다 긴 외래 키 제약 조건 이름

업그레이드 검사기에서 문제가 보고되지 않으면 MySQL 이진을 대체하여 현재 위치 업그레이드를 안전하게 수행할 수 있습니다. 문제가 있는 데이터베이스를 내보내고 문제를 해결해야 합니다.

## <a name="wwi-scenario"></a>WWI 시나리오

MySQL 인스턴스를 8.0으로 성공적으로 마이그레이션한 후 WWI 마이그레이션 팀은 원래 [DMS(Database Migration Service)](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql?step=1) 마이그레이션 경로를 더 이상 사용할 수 없음을 깨달았습니다. DMS 도구가 현재 5.6 및 5.7만 지원하기 때문입니다. DMS에는 네트워크 액세스가 필요했습니다. WWI 마이그레이션 팀이 복잡한 네트워크 문제를 처리할 준비가 되지 않았습니다. 이러한 환경 문제로 마이그레이션 도구 선택이 MySQL Workbench로 축소되었습니다.

## <a name="database-objects"></a>데이터베이스 개체

Test Plans 섹션에서 설명한 대로 모든 것을 마이그레이션했는지 확인하기 위해 마이그레이션 전후에 데이터베이스 개체의 인벤토리를 수행해야 합니다.

저장 프로시저를 실행하여 이 정보를 생성하려는 경우 다음과 유사한 내용을 사용할 수 있습니다.

```
DELIMITER // 
CREATE PROCEDURE `Migration_PerformInventory`(IN schemaName CHAR(64)) 
BEGIN 

        DECLARE finished INTEGER DEFAULT 0; 
          DECLARE tableName varchar(100) DEFAULT ""; 

        #get all tables 
            DECLARE curTableNames 
                CURSOR FOR 
                    SELECT TABLE_NAME FROM information_schema.tables where TABL
E_SCHEMA = schemaName; 
        
            -- declare NOT FOUND handler 
            DECLARE CONTINUE HANDLER 
                FOR NOT FOUND SET finished = 1; 
        
            DROP TABLE IF EXISTS MIG_INVENTORY; 

                CREATE TABLE MIG_INVENTORY 
                ( 
                      REPORT_TYPE VARCHAR(1000), 
                      OBJECT_NAME VARCHAR(1000), 
                  PARENT_OBJECT_NAME VARCHAR (1000), 
                      OBJECT_TYPE VARCHAR(1000), 
                      COUNT INT
                ) 
                ROW_FORMAT=DYNAMIC, 
                ENGINE='InnoDB';
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT
                     'OBJECTCOUNT', 'TABLES', 'TABLES', COUNT(*)
              FROM 
                     information_schema.tables 
                where 
                     TABLE_SCHEMA = schemaName;
                #### Constraints
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'STATISTICS', 'STATISTICS', COUNT(*) 
                FROM 
                      information_schema.STATISTICS 
                WHERE 
                      TABLE_SCHEMA = schemaName; 
                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'VIEWS', 'VIEWS', COUNT(*) 
                FROM 
                      information_schema.VIEWS 
                WHERE 
                      ROUTINE_TYPE = 'FUNCTION' and 
                      ROUTINE_SCHEMA = schemaName;

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'PROCEDURES', 'PROCEDURES', COUNT(*) 
                FROM 
                      information_schema.ROUTINES 
                WHERE 
                      ROUTINE_TYPE = 'PROCEDURE' and 
                      ROUTINE_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'EVENTS', 'EVENTS', COUNT(*) 
                FROM 
                       information_schema.EVENTS 
                WHERE 
                       EVENT_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'USER DEFINED FUNCTIONS', 'USER DEFINED FUNCTIONS'
        , COUNT(*) 
                FROM 
                        mysql.func; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                        'OBJECTCOUNT', 'USERS', 'USERS', COUNT(*) 
                FROM 
                        mysql.user 
                WHERE 
                        user <> '' order by user; 

                OPEN curTableNames; 
        
                getTableName: LOOP 
                        FETCH curTableNames INTO tableName; 
                        IF finished = 1 THEN 
                              LEAVE getTableName; 
                        END IF; 

                   SET @s = CONCAT('SELECT COUNT(*) into @TableCount FROM ', schemaName, 
'.', tableName); 
        #SELECT @s; 
            PREPARE stmt FROM @s; 
        EXECUTE stmt;
        INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
            
                SELECT 
                    'TABLECOUNT', tableName, 'TABLECOUNT', @TableCount; 
        DEALLOCATE PREPARE stmt; 
        
     END LOOP getTableName; 
     CLOSE curTableNames; 
          
   SELECT * FROM MIG_INVENTORY; 
END // 

DELIMITER ; 

CALL Migration_PerformInventory('reg_app');
```

  - 원본 DB에서 이 프로시저를 호출하는 경우 다음(잘린 출력)이 표시됩니다.

![잘린 출력](./media/image4.jpg)

  - 마이그레이션을 완료한 후 대상 데이터베이스 프로시저 결과는 아래 이미지와 유사해야 합니다. DB에는 함수가 없습니다. 마이그레이션 전에 함수가 제거되었습니다.

![DB 함수](./media/image5.jpg)

## <a name="users-and-permissions"></a>사용자 및 사용 권한

마이그레이션을 성공적으로 수행하려면 연결된 사용자 및 사용 권한을 대상 환경으로 마이그레이션해야 합니다.

다음 PowerShell 스크립트를 사용하여 모든 사용자 및 권한 부여를 내보냅니다.

```
$username = "yourusername"; 
$password = "yourpassword"; 
mysql -u$username -p$password --skip-column-names -A -e"SELECT CONCAT('SHOW G
RANTS FOR ''',user,'''@''',host,''';') FROM mysql.user WHERE user<>''" > Show
Grants.sql; 

$lines = get-content "ShowGrants.sql" 

foreach ($line in $lines) 
{ 
mysql -u$username -p$password --skip-column-names -A -e"$line" >> AllGrants.sql 
}
```

  - PowerShell ISE를 사용하여 새 PowerShell 스크립트 만들기(설치 문서 참조)

  - **your username** 을 root로 설정하고 **your password** 를 root 사용자의 암호로 설정합니다

그런 다음, 새 Azure Database for MySQL을 대상으로 하는 `AllGrants.sql` 스크립트를 실행할 수 있습니다.

```
$username = "yourusername"; 
$password = "yourpassword"; 
$server = "serverDNSname"; 
$lines = get-content "AllGrants.sql" 

foreach ($line in $lines)
{ 
mysql -u$username -p$password -h$server --ssl-ca=c:\temp\BaltimoreCyberTrus
tRoot.crt.cer --skip-column-names -A -e"$line" 
}
```

PowerShell을 사용하여 Azure Database for MySQL에서 사용자를 만들 수도 있습니다(/en-us/azure/mysql/howto-create-users).

## <a name="execute-migration"></a>마이그레이션 실행

이제 기본 마이그레이션 구성 요소가 준비되었으므로 데이터 마이그레이션을 진행할 수 있습니다. 이전에 도입된 몇 가지 도구와 방법이 있었습니다. WWI의 경우 MySQL Workbench 경로를 활용하여 데이터를 내보낸 다음, Azure Database for MySQL로 가져오려고 합니다.

## <a name="data-migration-checklist"></a>데이터 마이그레이션 검사 목록

  - 환경의 복잡성과 온라인 접근 방식이 가능한지 이해합니다.

  - 데이터 드리프트를 고려합니다. 데이터베이스 서비스를 중지하면 잠재적인 데이터 드리프트가 제거될 수 있습니다.

  - 빠른 내보내기를 위한 원본 매개 변수를 구성합니다.

  - 빠른 가져오기를 위한 대상 매개 변수를 구성합니다.

  - 다른 원본 버전과 대상이 있는 모든 마이그레이션을 테스트합니다.

  - 사용자 이름과 권한 등 데이터 기반 이외의 개체를 마이그레이션합니다.

  - 마이그레이션이 실행될 때 모든 작업이 문서화되고 체크아웃되었는지 확인합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MySQL Workbench를 사용한 데이터 마이그레이션](./09-data-migration-with-mySQL-workbench.md)