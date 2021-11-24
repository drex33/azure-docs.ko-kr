---
title: '빠른 시작: PHP를 사용하여 연결 - Azure Database for MySQL'
description: 이 빠른 시작에서는 MySQL용 Azure Database의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 여러 PHP 코드 샘플을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, mode-other
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: b6eab2d8a5cf13cb249a5c5f1a89094abfbe4699
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043892"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>빠른 시작: PHP를 사용하여 Azure Database for MySQL에서 데이터 연결 및 쿼리

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
이 빠른 시작에서는 [PHP](https://secure.php.net/manual/intro-whatis.php) 애플리케이션을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작에는 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free).
- [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)을 사용하여 Azure Database for MySQL 단일 서버 만들기 <br/> 또는 [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)를 만듭니다(없는 경우).
- 퍼블릭 또는 프라이빗 액세스를 사용하는지 여부에 따라 아래 작업 중 **하나** 를 완료하여 연결을 활성화합니다.

    |작업| 연결 방법|방법 가이드|
    |:--------- |:--------- |:--------- |
    | **방화벽 규칙 구성** | 공용 | [포털](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **서비스 엔드포인트 구성** | 공용 | [포털](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **프라이빗 링크 구성** | Private | [포털](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [데이터베이스 및 비관리 사용자 만들기](./howto-create-users.md?tabs=single-server)
- 운영 체제에 최신 PHP 버전 설치
    - [macOS의 PHP](https://secure.php.net/manual/install.macosx.php)
    - [Linux의 PHP](https://secure.php.net/manual/install.unix.php)
    - [Windows의 PHP](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> 이 빠른 시작에서는 [MySQLi](https://www.php.net/manual/en/book.mysqli.php) 라이브러리를 사용하여 연결을 관리하고 서버를 쿼리합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기
다음 단계에 따라 Azure Portal에서 데이터베이스 서버 연결 정보를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Databases for MySQL 페이지로 이동합니다. **Azure Database for MySQL** 을 검색하여 선택할 수 있습니다.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL 찾기":::

2. MySQL 서버(예: **mydemoserver**)를 선택합니다.
3. **개요** 페이지에서 **서버 이름** 옆에 정규화된 서버 이름을 복사하고 **서버 관리자 로그인 이름** 옆에 관리 사용자 이름을 복사합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

> [!IMPORTANT]
> - 암호를 잊어버린 경우 [암호를 재설정](./howto-create-manage-server-portal.md#update-admin-password)할 수 있습니다.
> - **host, username, password** 및 **db_name** 매개 변수를 고유한 값**으로 바꿉니다.

## <a name="step-1-connect-to-the-server"></a>1단계: 서버에 연결
SSL은 기본적으로 활성화되어 있습니다. 로컬 환경에서 연결하려면 [DigiCertGlobalRootG2 SSL 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)를 다운로드해야 할 수도 있습니다. 이 코드는 다음을 호출합니다.
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php): MySQLi를 초기화합니다.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php): SSL 인증서 경로를 가리킵니다. 이는 로컬 환경에 필요하지만 App Service 웹앱 또는 Azure 가상 머신에는 필요하지 않습니다.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php): MySQL에 연결합니다.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php): 연결을 닫습니다.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno())
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[문제가 있나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>2단계: 테이블 만들기
다음 코드를 사용하여 연결합니다. 이 코드는 다음을 호출합니다.
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php): 쿼리를 실행합니다.
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>3단계: 데이터 삽입
**INSERT** SQL 문을 사용하여 데이터를 삽입하려면 다음 코드를 사용합니다. 이 코드는 다음 메서드를 사용합니다.
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php): 준비된 insert 문을 만듭니다.
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php): 삽입된 각 열 값의 매개 변수를 바인딩합니다.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php): 메서드를 사용하여 명령문을 닫습니다.


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>4단계: 데이터 읽기
**SELECT** SQL 문을 사용하여 데이터를 읽으려면 다음 코드를 사용하세요.  이 코드는 다음 메서드를 사용합니다.
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php)는 **SELECT** 쿼리를 실행합니다.
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php): 결과 행을 페치합니다.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>5단계: 데이터 삭제
다음 코드를 통해 **DELETE** SQL 문을 사용하여 행을 삭제합니다. 이 코드는 다음 메서드를 사용합니다.
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php): 준비된 delete 문을 만듭니다.
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)은 매개 변수를 바인딩합니다.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)는 준비된 delete 문을 실행합니다.
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)는 명령문을 닫습니다.

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 사용된 모든 리소스를 정리하려면 다음 명령을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [포털을 사용하여 Azure Database for MySQL 서버 관리](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI를 사용하여 Azure Database for MySQL 서버 관리](./how-to-manage-single-server-cli.md)

[원하는 항목을 찾을 수 없나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)
