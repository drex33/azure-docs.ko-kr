---
title: 가져오기 및 내보내기 - Azure Database for MySQL
description: 이 문서에서는 MySQL Workbench와 같은 도구를 사용하여 Azure Database for MySQL에서 데이터베이스를 가져오고 내보내는 일반적인 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: b26cfa516cc9080faac7d47c512b054e5909c1c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345246"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>가져오기 및 내보내기를 사용하여 MySQL 데이터베이스 마이그레이션

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 MySQL Workbench를 사용하여 Azure Database for MySQL 서버로 데이터를 가져오고 내보내는 두 가지 일반적인 방법을 설명합니다.

자세하고 포괄적인 마이그레이션 지침은 [마이그레이션 가이드 리소스](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)를 참조하세요. 

다른 마이그레이션 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

MySQL 데이터베이스 마이그레이션을 시작하려면 먼저 다음을 수행해야 합니다.

- [Azure Portal을 사용하여 Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)를 만듭니다.
- 가져오기 및 내보내기용 [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) 또는 다른 타사 MySQL 도구를 다운로드하여 설치합니다.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에서 데이터베이스 만들기

MySQL Workbench, Toad 또는 Navicat을 사용하여 Azure Database for MySQL 서버에 빈 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결하려면 다음을 수행합니다.

1. Azure Portal의 Azure Database for MySQL **개요** 창에서 연결 정보를 찾습니다.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Azure Portal의 Azure Database for MySQL 서버 연결 정보 스크린샷":::

1. MySQL Workbench에 연결 정보를 추가합니다.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench 연결 문자열의 스크린샷":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>가져오기 및 내보내기 기술을 사용할 시기 결정

> [!TIP]
> 전체 데이터베이스를 덤프하고 복원하려는 시나리오에서는 [덤프 및 복원](concepts-migrate-dump-restore.md) 방법을 대신 사용합니다.

다음 시나리오에서는 MySQL 도구를 사용하여 MySQL 데이터베이스로 데이터베이스를 가져오고 내보냅니다. 다른 도구의 경우 [MySQL to Azure Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)(MySQL과 Azure Database 간 마이그레이션 가이드)의 “마이그레이션 방법” 섹션(22페이지)으로 이동합니다. 

- 기존 MySQL 데이터베이스에서 Azure MySQL 데이터베이스로 몇 개의 테이블을 선택적으로 가져와야 하는 경우 가져오기 및 내보내기 기술을 사용하는 것이 가장 좋습니다. 이렇게 하면 시간과 리소스를 절약하도록 마이그레이션에서 불필요한 테이블을 생략할 수 있습니다. 예를 들어 `--include-tables` 또는 `--exclude-tables` 스위치를 [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)에서 사용하고, `--tables` 스위치를 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables)에서 사용합니다.
- 테이블 이외의 다른 데이터베이스 개체를 이동할 때는 해당 개체를 명시적으로 만듭니다. 마이그레이션하려는 제약 조건(기본 키, 외래 키, 인덱스), 뷰, 함수, 프로시저, 트리거 및 다른 모든 데이터베이스 개체를 포함합니다.
- MySQL 데이터베이스 이외의 외부 데이터 원본에서 데이터를 마이그레이션하는 경우 플랫 파일을 만들고 [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)를 사용하여 가져옵니다.

> [!Important]
> 단일 서버와 유연한 서버 모두 InnoDB 스토리지 엔진만 지원합니다. Azure Database for MySQL로 데이터를 로드할 때 데이터베이스의 모든 테이블이 InnoDB 스토리지 엔진을 사용하는지 확인합니다.
>
> 원본 데이터베이스에서 다른 스토리지 엔진을 사용하는 경우 InnoDB 엔진으로 변환한 후에 데이터베이스를 마이그레이션합니다. 예를 들어 MyISAM 엔진을 사용하는 WordPress 또는 웹앱이 있는 경우 먼저 데이터를 InnoDB 테이블로 마이그레이션하여 테이블을 변환합니다. `ENGINE=INNODB` 절을 사용하여 테이블을 만들기 위한 엔진을 설정한 다음 마이그레이션 전에 데이터를 호환되는 테이블로 전송합니다.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>가져오기 및 내보내기를 위한 성능 권장 사항

최적의 데이터 가져오기 및 내보내기 성능을 위해서는 다음을 수행하는 것이 좋습니다.

- 데이터를 로드하기 전에 클러스터형 인덱스 및 기본 키를 만듭니다. 기본 키 순서로 데이터를 로드합니다.
- 데이터가 로드될 때까지 보조 인덱스 만들기를 지연합니다.
- 데이터를 로드하기 전에 외래 키 제약 조건을 사용하지 않도록 설정합니다. 외래 키 검사 비활성화는 상당한 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
- 병렬로 데이터를 로드합니다. 리소스 제한에 도달하도록 하는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다.
- 적절한 경우 분할된 테이블을 사용합니다.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>MySQL Workbench를 사용하여 데이터 가져오기 및 내보내기

MySQL Workbench에서는 개체 브라우저 상황에 맞는 메뉴 또는 탐색기 창이라는 두 가지 방법을 통해 데이터를 내보내고 가져올 수 있습니다. 각 방법은 서로 다른 용도로 사용됩니다.

> [!NOTE]
> Mysql 워크 벤치에서 MySQL 단일 서버 또는 유연한 서버에 대 한 연결을 추가 하는 경우 다음을 수행 합니다.
>
> - MySQL 단일 서버에서는 사용자 이름이 *\<username@servername>* 형식인지 확인합니다.
> - MySQL 유연한 서버에서는 *\<username>* 만 사용합니다. *\<username@servername>* 을 사용하여 연결하면 연결이 실패합니다.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>개체 브라우저 상황에 맞는 메뉴에서 테이블 데이터 내보내기 및 가져오기 마법사 실행

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="개체 브라우저 상황에 맞는 메뉴에 있는 MySQL Workbench 내보내기 및 가져오기 마법사 명령의 스크린샷":::

테이블 데이터 마법사는 CSV 및 JSON 파일을 사용하여 가져오기 및 내보내기 작업을 지원합니다. 마법사에는 구분 기호, 열 선택, 인코딩 선택 등의 여러 가지 구성 옵션이 있습니다. 로컬 또는 원격으로 연결된 MySQL 서버에 대해 각 마법사를 실행할 수 있습니다. 가져오기 작업은 테이블, 열 및 형식 매핑을 포함합니다.

개체 브라우저 상황에 맞는 메뉴에서 이러한 마법사에 액세스하려면 테이블을 마우스 오른쪽 단추로 클릭한 다음, **테이블 데이터 내보내기 마법사** 또는 **테이블 데이터 가져오기 마법사** 를 선택합니다.

#### <a name="the-table-data-export-wizard"></a>테이블 데이터 내보내기 마법사

테이블을 CSV 파일로 내보내려면 다음을 수행합니다.

1. 내보낼 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다.
1. **테이블 데이터 내보내기 마법사** 를 선택합니다. 내보낼 열, 행 오프셋(있는 경우) 및 개수(있는 경우)를 선택합니다.
1. **내보낼 데이터 선택** 창에서 **다음** 을 선택합니다. 파일 경로, CSV 또는 JSON 파일 형식을 선택합니다. 또한 선 구분 기호, 문자열을 묶는 방법 및 필드 구분 기호를 선택합니다.
1. **출력 파일 위치 선택** 창에서 **다음** 을 선택합니다.
1. **데이터 내보내기** 창에서 **다음** 을 선택합니다.

#### <a name="the-table-data-import-wizard"></a>테이블 데이터 가져오기 마법사

CSV 파일에서 테이블을 가져오려면 다음을 수행합니다.

1. 가져올 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다.
1. 가져올 CSV 파일을 찾아서 선택한 후 **다음** 을 선택합니다.
1. 대상 테이블(신규 또는 기존)을 선택하고 **가져오기 전에 테이블 자르기** 확인란을 선택하거나 선택 취소한 후 **다음** 을 선택합니다.
1. 가져올 인코딩 및 열을 선택하고 **다음** 을 선택합니다.
1. **데이터 가져오기** 창에서 **다음** 을 선택합니다. 마법사에서 데이터를 가져옵니다.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>탐색기 창에서 SQL 데이터 내보내기 및 가져오기 마법사 실행

마법사를 사용하여 MySQL Workbench 또는 mysqldump 명령에서 생성된 SQL 데이터를 내보내거나 가져옵니다. **탐색기** 창에서 마법사에 액세스하거나 주 메뉴에서 **서버** 를 선택할 수 있습니다.

#### <a name="export-data"></a>데이터 내보내기

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="탐색기 창을 사용하여 MySQL Workbench에 데이터 내보내기 창을 표시하는 스크린샷":::

**데이터 내보내기** 창을 사용하여 MySQL 데이터를 내보낼 수 있습니다.

1. MySQL Workbench의 **탐색기** 창에서 **데이터 내보내기** 를 선택합니다.

1. **데이터 내보내기** 창에서 내보내려는 각 스키마를 선택합니다.
 
   각 스키마에 대해 내보낼 특정 스키마 개체 또는 테이블을 선택할 수 있습니다. 구성 옵션에는 프로젝트 폴더 또는 자체 포함된 SQL 파일로 내보내기, 저장된 루틴 및 이벤트 덤프, 테이블 데이터 건너뛰기 등이 포함됩니다.

   또는 **결과 집합 내보내기** 를 사용하여 SQL 편집기에서 CSV, JSON, HTML 및 XML 등의 다른 형식으로 특정 결과 집합을 내보냅니다.

1. 내보낼 데이터베이스 개체를 선택하고 관련 옵션을 구성합니다.
1. **새로 고침** 을 선택하여 현재 개체를 로드합니다.
1. 필요에 따라 오른쪽 위에 있는 **고급 옵션** 을 선택하여 내보내기 작업을 구체화합니다. 예를 들어 테이블 잠금을 추가하고, `insert` 문 대신 `replace` 문을 사용하고, 백틱 문자로 식별자를 묶습니다.
1. **내보내기 시작** 을 선택하여 내보내기 프로세스를 시작합니다.


#### <a name="import-data"></a>데이터 가져오기

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="탐색기 창을 사용하여 MySQL Workbench에 데이터 가져오기 창을 표시하는 스크린샷":::

**데이터 가져오기** 창을 사용하여 데이터 내보내기 작업 또는 mysqldump 명령에서 내보낸 데이터를 가져오거나 복원할 수 있습니다.

1. MySQL Workbench의 **탐색기** 창에서 **데이터 내보내기/복원** 을 선택합니다.
1. 프로젝트 폴더 또는 자체 포함된 SQL 파일을 선택하거나, 가져올 스키마를 선택하거나, **새로 만들기** 단추를 선택하여 새 스키마를 정의합니다.
1. **가져오기 시작** 을 선택하여 가져오기 프로세스를 시작합니다.

## <a name="next-steps"></a>다음 단계

- 다른 마이그레이션 방법은 [덤프 및 복원을 사용하여 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션](concepts-migrate-dump-restore.md)을 참조하세요.
- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)을 참조하세요.
