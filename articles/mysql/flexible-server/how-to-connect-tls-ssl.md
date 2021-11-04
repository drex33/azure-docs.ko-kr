---
title: Azure Database for MySQL - 유연한 서버에서 TLS/SSL을 사용한 암호화된 연결
description: Azure Database for MySQL - 유연한 서버에서 TLS/SSL을 사용해 연결하는 방법에 대한 지침 및 정보입니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2f73d6c8969cd6915c1a571eb6196bcf8f901bd6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468141"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>암호화된 연결을 사용하여 Azure Database for MySQL - 유연한 서버에 연결

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버는 TLS(전송 계층 보안) 암호와 함께 SSL(Secure Sockets Layer)을 사용하여 MySQL 서버에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있도록 하는 업계 표준 프로토콜입니다.

Azure Database for MySQL 유연한 서버는 기본적으로 전송 계층 보안(TLS 1.2)을 사용하는 암호화된 연결을 지원하며 TLS 1.0 및 TLS 1.1을 사용하여 들어오는 연결은 기본적으로 모두 거부됩니다. 이 문서에서 설명하는 대로 유연한 서버에서 암호화된 연결 적용 또는 TLS 버전 구성을 변경할 수 있습니다.

다음은 유연한 서버에 사용할 수 있는 SSL 및 TLS 설정의 다양한 구성입니다.

| 시나리오   | 서버 매개 변수 설정      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL 적용 사용 안 함 | require_secure_transport = OFF |레거시 애플리케이션이 MySQL 서버에 대한 암호화된 연결을 지원하지 않는 경우 require_secure_transport=OFF를 설정하여 유연한 서버에 암호화된 연결 적용을 사용하지 않도록 설정할 수 있습니다.|
|TLS 버전 < 1.2로 SSL 적용 | require_secure_transport = ON 및 tls_version = TLSV1 또는 TLSV1.1| 레거시 애플리케이션에서 암호화된 연결을 지원하지만 TLS 버전 1.2 미만이 필요한 경우 암호화된 연결을 사용하도록 설정할 수 있지만 애플리케이션에서 지원하는 tls 버전(v1.0 또는 v1.1)과의 연결을 허용하도록 유연한 서버를 구성할 수 있습니다.|
|TLS 버전 = 1.2를 사용하여 SSL 적용(기본 구성)|require_secure_transport = ON 및 tls_version = TLSV1.2| 이는 유연한 서버에 권장되는 기본 구성입니다.|
|TLS 버전 = 1.3으로 SSL 적용(MySQL v8.0 이상에서 지원됨)| require_secure_transport = ON 및 tls_version = TLSV1.3| 이는 새 애플리케이션 개발에 유용하며 권장됩니다.|

> [!Note]
> 유연한 서버에서 SSL 암호화를 변경하는 것은 지원되지 않습니다. tls_version이 TLS 버전 1.2로 설정된 경우 FIPS 암호화 그룹은 기본적으로 적용됩니다. 버전 1.2이 아닌 TLS 버전의 경우 SSL 암호화는 MySQL 커뮤니티 설치와 함께 제공되는 기본 설정으로 설정됩니다.

이 아티클에서는 다음 방법을 설명합니다.

* 유연한 서버 구성
  * SSL 사용 안 함
  * SSL이 TLS 버전 < 1.2에서 적용됨
* mysql 명령줄을 사용하여 유연한 서버에 연결
  * 암호화된 연결 사용 안 함
  * 암호화된 연결 사용
* 연결에 대한 암호화 상태 확인
* 다양한 애플리케이션 프레임워크를 사용하여 암호화된 연결로 유연한 서버에 연결

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>유연한 서버에서 SSL 적용 사용 안 함

클라이언트 애플리케이션이 암호화된 연결을 지원하지 않는 경우 유연한 서버에서 암호화된 연결 적용을 사용하지 않도록 설정해야 합니다. 암호화된 연결 적용을 사용하지 않도록 설정하려면 스크린샷에 표시된 대로 require_secure_transport 서버 매개 변수를 OFF로 설정하고 서버 매개 변수 구성을 저장하여 적용해야 합니다. require_secure_transport는 즉시 적용되며 서버를 다시 시작하지 않아도 되는 **동적 서버 매개 변수** 입니다.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Azure Database for MySQL 유연한 서버에서 SSL을 사용하지 않도록 설정하는 방법을 보여 주는 스크린샷":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>SSL을 사용하지 않도록 설정된 mysql 명령줄 클라이언트를 사용하여 연결

다음 예제에서는 mysql 명령줄 인터페이스를 사용하여 서버에 연결하는 방법을 보여 줍니다. `--ssl-mode=DISABLED` 연결 문자열 설정을 사용하여 mysql 클라이언트에서 TLS/SSL 연결을 사용하지 않도록 설정합니다. 값을 실제 서버 이름 및 암호로 바꿉니다.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED
```

require_secure_transport를 OFF로 설정하는 것은 암호화된 연결이 서버 쪽에서 지원되지 않는다는 것을 의미하지 않습니다. 유연한 서버에서 require_secure_transport를 OFF로 설정한 경우 클라이언트에서 암호화된 연결을 사용하여 연결하는 경우에도 계속 허용됩니다. require_secure_transport=OFF로 구성된 유연한 서버에 mysql 클라이언트를 사용하는 다음 연결은 아래와 같이 작동합니다.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```

```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

요약하면 require_secure_transport=OFF 설정은 유연한 서버에서 암호화된 연결의 적용을 완화하고 암호화된 연결 외에도 클라이언트에서 서버로의 암호화되지 않은 연결을 허용합니다.

## <a name="enforce-ssl-with-tls-version--12"></a>TLS 버전 < 1.2로 SSL 적용

애플리케이션에서 SSL을 사용하여 MySQL 서버에 대한 연결을 지원하지만 TLS 버전 < 1.2를 지원하는 경우에는 유연한 서버에서 TLS 버전 서버 매개 변수를 설정해야 합니다. 유연한 서버에서 지원하도록 할 TLS 버전을 설정하려면 스크린샷에 표시된 것처럼 tls_version 서버 매개 변수를 TLSV1, TLSV 1.1 또는 TLSV1 및 TLSV 1.1로 설정하고 이를 적용하려면 서버 매개 변수 구성을 저장해야 합니다. tls_version은 매개 변수를 적용하려면 서버를 다시 시작해야 하는 **정적 서버 매개 변수** 입니다.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Azure Database for MySQL 유연한 서버에 대한 tls 버전을 설정하는 방법을 보여 주는 스크린샷":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>TLS/SSL과 함께 mysql 명령줄 클라이언트를 사용하여 연결

### <a name="download-the-public-ssl-certificate"></a>공용 SSL 인증서 다운로드

클라이언트 애플리케이션에서 암호화된 연결을 사용하려면 아래 스크린샷에 표시된 것처럼 Azure Portal 네트워킹 블레이드에서 사용할 수 있는 [공용 SSL 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)를 다운로드해야 합니다.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Azure Portal에서 공용 SSL 인증서를 다운로드하는 방법을 보여 주는 스크린샷":::

인증서 파일을 원하는 위치에 저장합니다. 예를 들어 이 자습서에서는 애플리케이션이 호스트되는 로컬 환경 또는 클라이언트 환경에서 `c:\ssl` 또는 `\var\www\html\bin`을 사용합니다. 이렇게 하면 애플리케이션에서 SSL을 통해 데이터베이스에 안전하게 연결할 수 있습니다.

*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다.

*퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 선택하여 로컬 환경에서 서버에 연결할 수 있습니다.

다음 예제에서는 mysql 명령줄 인터페이스를 사용하여 서버에 연결하는 방법을 보여 줍니다. `--ssl-mode=REQUIRED` 연결 문자열 설정을 사용하여 TLS/SSL 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `--ssl-ca` 매개 변수에 전달합니다. 값을 실제 서버 이름 및 암호로 바꿉니다.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

> [!Note]
> `--ssl-ca`에 전달된 값이 저장한 인증서의 파일 경로와 일치하는지 확인합니다.

암호화되지 않은 연결을 사용하여 서버에 연결하려고 하면 보안되지 않은 전송을 사용한 연결이 아래와 유사하게 금지되었다는 오류가 표시됩니다.

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>TLS/SSL 연결 확인

mysql **status** 명령을 실행하여 TLS/SSL을 통해 MySQL 서버에 연결되어 있는지 확인합니다.

```dos
mysql> status
```

출력을 검토하여 연결이 암호화되었는지 확인합니다. 다음이 표시되어야 합니다. **SSL: Cipher in use is** 이 암호화 그룹은 예제를 표시하고 클라이언트를 기반으로 다른 암호화 그룹을 볼 수 있습니다.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>다양한 애플리케이션 프레임워크를 사용하여 암호화된 연결로 유연한 서버에 연결

Azure Portal의 해당 서버에 사용 가능한 "연결 문자열" 페이지에서 미리 정의된 연결 문자열에는 TLS/SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. TLS/SSL 매개 변수는 커넥터에 따라 달라집니다. 예: "useSSL=true", "sslmode=required" 또는 "ssl_verify_cert=true" 및 기타 변형

애플리케이션에서 TLS/SSL을 통해 유연한 서버에 대한 암호화된 연결을 설정하려면 다음 코드 샘플을 참조하세요.

### <a name="wordpress"></a>WordPress

[SSL 공용 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)를 다운로드하고 ```// **MySQL settings - You can get this info from your web host** //``` 줄 뒤의 wp-config.php에 다음 줄을 추가합니다.

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP(PDO 사용)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python(MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python(PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django(PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java(Java용 MySQL 커넥터)

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java(Java용 MariaDB 커넥터)

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET(MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

### <a name="nodejs"></a>Node.js

```node
var fs = require('fs');
var mysql = require('mysql');
const serverCa = [fs.readFileSync("/var/www/html/DigiCertGlobalRootCA.crt.pem", "utf8")];
var conn=mysql.createConnection({
    host:"mydemoserver.mysql.database.azure.com",
    user:"myadmin",
    password:"yourpassword",
    database:"quickstartdb",
    port:3306,
    ssl: {
        rejectUnauthorized: true,
        ca: serverCa
    }
});
conn.connect(function(err) {
  if (err) throw err;
});
```

## <a name="next-steps"></a>다음 단계

* [MySQL Workbench를 사용하여 Azure Database for MySQL 유연한 서버에서 데이터 연결 및 쿼리](./connect-workbench.md)
* [PHP를 사용하여 Azure Database for MySQL 유연한 서버에서 데이터 연결 및 쿼리](./connect-php.md)
* [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
* [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
* [Azure Database for MySQL 유연한 서버 방화벽 규칙](./concepts-networking-public.md#public-access-allowed-ip-addresses)에 대해 자세히 알아보기
