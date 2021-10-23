---
title: 리디렉션을 사용하여 연결 - Azure Database for MariaDB
description: 이 문서에서는 리디렉션을 사용하여 Azure Database for MariaDB에 연결하도록 애플리케이션을 구성하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 0e99d08e638569c68043bb67f090bc7ce41982ca
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215037"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>리디렉션을 사용하여 Azure Database for MariaDB에 연결

이 항목에서는 리디렉션 모드를 사용하여 애플리케이션을 Azure Database for MariaDB 서버에 연결하는 방법을 설명합니다. 리디렉션은 애플리케이션이 백 엔드 서버 노드에 직접 연결할 수 있도록 하여 클라이언트 애플리케이션과 MariaDB 서버 간의 네트워크 대기 시간을 줄이는 것을 목표로 합니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure Portal](https://portal.azure.com)에 로그인합니다. 엔진 버전 10.2 또는 10.3을 사용하여 Azure Database for MariaDB 서버를 만듭니다. 

자세한 내용은 [Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md) 또는 [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)를 사용하여 Azure Database for MariaDB 서버를 만드는 방법을 참조하세요.

> [!IMPORTANT]
> 현재는 [Azure Database for MariaDB에 대 한 개인 링크](concepts-data-access-security-private-link.md)를 사용 하 여 리디렉션을 지원 하지 않습니다.

## <a name="enable-redirection"></a>리디렉션 사용

Azure Database for MariaDB 서버에서 리디렉션 모드로 연결을 허용할 수 있도록 `redirect_enabled` 매개 변수를 `ON`으로 구성합니다. 해당 서버 매개 변수를 업데이트하려면 [Azure Portal](howto-server-parameters.md) 또는 [Azure CLI](howto-configure-server-parameters-cli.md)를 사용합니다.

## <a name="php"></a>PHP

PHP 애플리케이션의 리디렉션 지원은 Microsoft에서 개발한 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장을 통해 제공됩니다. 

Mysqlnd_azure 확장은 PECL을 통해 PHP 애플리케이션에 추가할 수 있으며 공식적으로 게시된 [PECL 패키지](https://pecl.php.net/package/mysqlnd_azure)를 통해 확장을 설치하고 구성하는 것이 좋습니다.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장의 리디렉션에 대한 지원은 현재 미리 보기 상태입니다.

### <a name="redirection-logic"></a>리디렉션 논리

>[!IMPORTANT]
> 버전 1.1.0을 시작하는 리디렉션 논리/동작이 업데이트되었으며 **버전 1.1.0+를 사용하는 것이 좋습니다**.

리디렉션 동작은 `mysqlnd_azure.enableRedirect`의 값에 따라 결정됩니다. 아래 표에서는 **버전 1.1.0+** 부터 이 매개 변수의 값을 기반으로 하는 리디렉션의 동작을 간략하게 설명합니다.

이전 버전의 mysqlnd_azure 확장(버전 1.0.0-1.0.3)을 사용하는 경우 리디렉션 동작은 `mysqlnd_azure.enabled`의 값에 따라 결정됩니다. 유효한 값은 `off`(아래 표에 설명된 동작과 유사하게 동작) 및 `on`(아래 표의 `preferred`처럼 동작)입니다.  

|**mysqlnd_azure.enableRedirect 값**| **동작**|
|----------------------------------------|-------------|
|`off` 또는 `0`|리디렉션이 사용되지 않습니다. |
|`on` 또는 `1`|- 연결에서 드라이버 쪽의 SSL을 사용하지 않는 경우 연결되지 않습니다. 다음 오류가 반환됩니다. "mysqlnd_azure.enableRedirect가 설정되어 있지만 연결 문자열에 SSL 옵션이 설정되어 있지 않습니다. 리디렉션은 SSL로만 가능합니다."<br>- SSL이 드라이버 쪽에서 사용되지만 서버에서 리디렉션이 지원되지 않으면 첫 번째 연결이 중단되고 다음과 같은 오류가 반환됩니다. "리디렉션이 MariaDB 서버에서 사용하도록 설정되어 있지 않거나 네트워크 패키지가 리디렉션 프로토콜을 충족하지 않아 연결이 중단되었습니다."<br>- MariaDB 서버에서 리디렉션을 지원하지만 어떤 이유로든 리디렉션된 연결에 실패한 경우 첫 번째 프록시 연결도 중단됩니다. 리디렉션된 연결의 오류를 반환합니다.|
|`preferred` 또는 `2`<br> (기본값)|- 가능한 경우 mysqlnd_azure에서 리디렉션을 사용합니다.<br>- 프록시 연결이 유효한 경우 연결에서 드라이버 쪽의 SSL을 사용하지 않거나, 서버에서 리디렉션을 지원하지 않거나, 치명적이지 않은 이유로 인해 리디렉션된 연결에 실패하면, 첫 번째 프록시 연결로 대체됩니다.|

문서의 이후 섹션에서는 PECL을 사용하여 `mysqlnd_azure` 확장을 설치하고 이 매개 변수의 값을 설정하는 방법을 간략하게 설명합니다.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15+ 및 7.3.2+
- PHP PEAR 
- php-mysql
- Azure Database for MariaDB 서버

1. [PECL](https://pecl.php.net/package/mysqlnd_azure)을 사용하여 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)를 설치합니다. 버전 1.1.0+를 사용하는 것이 좋습니다.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 다음을 실행하여 확장 디렉터리(`extension_dir`)를 찾습니다.

    ```bash
    php -i | grep "extension_dir"
    ```

3. 반환된 폴더로 디렉터리를 변경하고 `mysqlnd_azure.so`이 폴더에 있는지 확인합니다. 

4. 다음을 실행하여 .ini 파일의 폴더를 찾습니다. 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 이 반환된 폴더로 디렉터리를 변경합니다. 

6. `mysqlnd_azure`에 대한 새 .ini 파일을 만듭니다. 모듈이 ini 파일의 이름 순서에 따라 로드되므로 알파벳 이름은 mysqnld의 뒤에 있어야 합니다. 예를 들어 `mysqlnd` .ini 이름이 `10-mysqlnd.ini`인 경우 mysqlnd ini 이름을 `20-mysqlnd-azure.ini`로 지정합니다.

7. 새 .ini 파일 내에서 리디렉션을 사용하도록 설정하려면 다음 줄을 추가합니다.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15+ 및 7.3.2+
- php-mysql
- Azure Database for MariaDB 서버

1. 다음 명령을 실행하여 x64 또는 x86 버전의 PHP를 실행하고 있는지 확인합니다.

    ```cmd
    php -i | findstr "Thread"
    ```

2. 해당 버전의 PHP와 일치하는 [PECL](https://pecl.php.net/package/mysqlnd_azure)에서 해당하는 x64 또는 x86 버전의 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL을 다운로드합니다. 버전 1.1.0+를 사용하는 것이 좋습니다.

3. Zip 파일을 추출하고 `php_mysqlnd_azure.dll`라는 DLL을 찾습니다.

4. 다음 명령을 실행하여 확장 디렉터리(`extension_dir`)를 찾습니다.

    ```cmd
    php -i | find "extension_dir"
    ```

5. `php_mysqlnd_azure.dll` 파일을 4단계에서 반환된 디렉터리에 복사합니다. 

6. 다음 명령을 사용하여 `php.ini` 파일을 포함하는 PHP 폴더를 찾습니다.

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini` 파일을 수정하고 다음 추가 줄을 추가하여 리디렉션을 사용하도록 설정 합니다. 

    동적 확장 섹션에서 다음을 수행합니다. 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    모듈 설정 섹션에서 다음을 수행합니다.     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>리디렉션 확인

다음 샘플 PHP 코드를 사용하여 리디렉션이 구성되었는지 확인할 수도 있습니다. `mysqlConnect.php`라는 PHP 파일을 만들고 아래 코드를 붙여넣습니다. 서버 이름, 사용자 이름 및 암호를 사용자 고유의 것으로 업데이트합니다. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>다음 단계
연결 문자열에 대한 자세한 내용은 [연결 문자열](howto-connection-string.md)을 참조하세요.
