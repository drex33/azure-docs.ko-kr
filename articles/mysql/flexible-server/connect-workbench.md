---
title: '빠른 시작: 연결 - MySQL Workbench - Azure Database for MySQL - 유연한 서버'
description: 이 빠른 시작에서는 Azure Database for MySQL - 유연한 서버에서 데이터를 연결하고 쿼리하는 데 MySQL Workbench를 사용하는 단계를 제공합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 7beb058e51010bc148d9cde31e4053d3de3cf598
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445524"
---
# <a name="use-mysql-workbench-with-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버와 함께 MySQL Workbench 사용

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 빠른 시작에서는 MySQL Workbench 애플리케이션을 사용하여 Azure Database for MySQL 유연한 서버에 연결하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기](./quickstart-create-server-portal.md)
- [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 만들기](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>클라이언트 워크스테이션 준비
- *프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다. [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-cli.md)를 참조하세요.
- *퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다. [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 방화벽 규칙 만들기 및 관리](./how-to-manage-firewall-cli.md)를 참조하세요.

- [MySQL 웹 사이트](https://dev.mysql.com/downloads/workbench/)에서 컴퓨터에 MySQL Workbench를 다운로드하여 설치합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기

유연한 서버에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 생성한 서버를 검색합니다(예: **mydemoserver**).
3. 서버 이름을 선택합니다.
4. 서버의 **개요** 패널에 있는 **서버 이름** 과 **서버 관리자 로그인 이름** 을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL Workbench를 사용하여 서버에 연결

MySQL Workbench를 사용하여 Azure Database for MySQL 유연한 서버에 연결하려면 다음을 수행합니다.

1. 컴퓨터에서 MySQL Workbench 애플리케이션을 시작합니다.

2. **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="새 연결 설정":::

    | **매개 변수** | **제안 값** | **필드 설명** |
    |---|---|---|
    |    연결 이름 | 데모 연결 | 이 연결에 대한 레이블을 지정합니다. |
    | 연결 방법 | 표준(TCP/IP) | 표준(TCP/IP)이면 충분합니다. |
    | Hostname | *서버 이름* | 이전에 Azure Database for MySQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 mydemoserver.mysql.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.mysql.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다.  |
    | 포트 | 3306 | Azure Database for MySQL에 연결 시 항상 포트 3306을 사용합니다. |
    | 사용자 이름 |  *서버 관리자 로그인 이름* | 이전에 Azure Database for MySQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 예제 사용자 이름은 myadmin입니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션의 단계에 따라 연결 정보를 가져옵니다.
    | 암호 | 사용자 암호 | **Store in Vault(자격 증명 모음에 저장)...** 단추를 클릭하여 암호를 저장합니다. |

3. **연결 테스트** 를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다.

4. **확인** 을 클릭하여 연결을 저장합니다.

5. **MySQL 연결** 목록에서 서버에 해당하는 타일을 클릭하고 연결이 설정될 때까지 대기합니다.

    새 SQL 탭이 쿼리를 입력할 수 있는 빈 편집기로 열립니다.

> [!NOTE]
> TLS 1.2를 사용한 암호화된 연결이 필요하며 Azure Database for MySQL 유연한 서버에서 암호화됩니다. 일반적으로 TLS/SSL 인증서가 있는 추가 구성은 MySQL Workbench에서 서버에 연결하는 데 필요하지 않지만, MySQL Workbench와 TLS/SSL CA 인증을 바인딩하는 것이 좋습니다. 자세한 내용은 [TLS/SSL을 사용한 연결](./how-to-connect-tls-ssl.md)을 참조하세요.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>테이블 만들기, 데이터 삽입, 데이터 읽기, 데이터 업데이트, 데이터 삭제

1. 몇 가지 샘플 데이터를 설명하기 위해 샘플 SQL 코드를 빈 SQL 탭에 복사하여 붙여넣습니다.

    이 코드는 quickstartdb라는 빈 데이터베이스를 만든 후 inventory라는 샘플 테이블을 만듭니다. 일부 행을 삽입한 후 행을 읽습니다. 데이터가 update 문으로 변경되고 다시 행을 읽습니다. 마지막으로 행을 삭제하고 다시 행을 읽습니다.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    스크린샷은 SQL Workbench에서의 SQL 코드 예제와 실행 후 출력을 보여 줍니다.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="샘플 SQL 코드를 실행하기 위한 MySQL Workbench SQL 탭":::

2. 샘플 SQL 코드를 실행하려면 **SQL 파일** 탭의 도구 모음에서 번갯불 아이콘을 클릭합니다.
3. 페이지 중앙의 **결과 그리드** 섹션에서 세 개의 탭 결과를 확인합니다.
4. 페이지 하단에서 **출력** 목록을 확인합니다. 각 명령의 상태가 표시됩니다.

이제 MySQL Workbench를 사용하여 Azure Database for MySQL 유연한 서버에 연결하고 SQL 언어를 사용하여 데이터를 쿼리해보았습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL -유연한 서버에서 TLS(Transport Layer Security) 1.2를 사용하는 암호화된 연결](./how-to-connect-tls-ssl.md).
- [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 방화벽 규칙 만들기 및 관리](./how-to-manage-firewall-portal.md).
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).
