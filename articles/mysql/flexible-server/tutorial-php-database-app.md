---
title: '자습서: Azure Database for MySQL 유연한 서버를 사용하여 PHP(Laravel) 앱 빌드'
description: 이 자습서에서는 유연한 서버를 사용하여 PHP 앱을 빌드하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 154aa9ad4374456247d119902ccfb37803215cdb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442258"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-app-in-azure-app-service"></a>자습서: Azure App Service에서 PHP(Laravel) 및 MySQL 유연한 서버 앱 빌드

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="유연한 서버를 사용하는 Azure의 PHP 웹앱":::

[Azure App Service](../../app-service/overview.md)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Azure에서 PHP 앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여줍니다. 완료되면 [Laravel](https://laravel.com/) 앱이 Linux의 Azure App Service에서 실행됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
> * 로컬 MySQL을 사용하여 PHP(Laravel) 앱 설정
> * MySQL 유연한 서버 만들기
> * MySQL 유연한 서버에 PHP 앱 연결
> * 앱을 Azure App Service에 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure Portal에서 앱 관리


[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
2. [PHP 5.6.4 이상 설치](https://php.net/downloads.php)
3. [작성기 설치](https://getcomposer.org/doc/00-intro.md)
4. Laravel에 필요한 OpenSSL, PDO-MySQL, Mbstring, 토크나이저, XML 등의 PHP 확장 사용
5. [MySQL 설치 및 시작](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>로컬 MySQL 준비

이 단계에서는 이 자습서에서 사용할 데이터베이스를 로컬 MySQL 서버에 만듭니다.

### <a name="connect-to-local-mysql-server"></a>로컬 MySQL 서버에 연결

터미널 창에서 로컬 MySQL 서버에 연결합니다. 이 터미널 창을 사용하여 이 자습서의 모든 명령을 실행할 수 있습니다.

```bash
mysql -u root -p
```

암호를 묻는 메시지가 표시되면 `root` 계정에 대한 암호를 입력합니다. 루트 계정 암호가 기억나지 않는 경우 [MySQL: 루트 암호를 재설정하는 방법](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)을 참조하세요.

명령이 성공적으로 실행되면 MySQL 서버가 실행되고 있습니다. 그렇지 않은 경우 [MySQL 설치 후 단계](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)에 따라 로컬 MySQL 서버가 시작되었는지 확인합니다.

### <a name="create-a-database-locally"></a>로컬에서 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>로컬에서 PHP 앱 만들기
이 단계에서는 Laravel 샘플 애플리케이션 가져오고, 해당 데이터베이스를 구성한 후 로컬로 실행합니다.

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 샘플 애플리케이션을 복제할 수 있는 빈 디렉터리로 이동합니다.  다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd`를 사용하여 복제된 디렉터리로 이동합니다.
필요한 패키지를 설치합니다.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL 연결 구성

리포지토리 루트에서 *.env* 라는 파일을 만듭니다. 다음 변수를 *.env* 파일에 복사합니다. _&lt;root_password>_ 자리 표시자를 MySQL 루트 사용자의 암호로 바꿉니다.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Laravel에서 _.env_ 파일을 사용하는 방법에 대한 자세한 내용은 [Laravel 환경 구성](https://laravel.com/docs/5.4/configuration#environment-configuration)(영문)을 참조하세요.

### <a name="run-the-sample-locally"></a>로컬에서 샘플 실행

[Laravel 데이터베이스 마이그레이션](https://laravel.com/docs/5.4/migrations)(영문)을 실행하여 애플리케이션에 필요한 테이블을 만듭니다. 마이그레이션에서 만들어진 테이블을 보려면 Git 리포지토리의 _database/migrations_ 디렉터리를 살펴봅니다.

```bash
php artisan migrate
```

새 Laravel 애플리케이션 키를 생성합니다.

```bash
php artisan key:generate
```

애플리케이션을 실행합니다.

```bash
php artisan serve
```

브라우저에서 `http://localhost:8000` 으로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP가 MySQL 연결에 성공":::

PHP를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

## <a name="create-a-mysql-flexible-server"></a>MySQL 유연한 서버 만들기
이 단계에서는 [Azure Database for MySQL 유연한 서버](../index.yml)에서 MySQL 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결할 PHP 애플리케이션을 구성합니다. [Azure Cloud Shell](../../cloud-shell/overview.md)에서 [`az flexible-server create`](/cli/azure/mysql/server#az_mysql_flexible_server_create) 명령을 사용하여 서버를 만듭니다.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
>- 다음 단계에서 laravel 데이터 마이그레이션을 연결하고 실행하는 데 사용할 **servername** 및 **연결 문자열** 을 적어 둡니다.
> - **IP-Address** 인수에는 클라이언트 머신의 IP를 입력합니다. 생성된 서버는 잠겨 있으므로 서버를 로컬로 관리할 수 있도록 클라이언트 머신에 대한 액세스를 허용해야 합니다.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>웹앱이 서버에 연결할 수 있도록 서버 방화벽 구성

Cloud Shell에서 az mysql server firewall-rule create 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP와 끝 IP를 모두 ```0.0.0.0```으로 설정하면 서버에 연결할 고정 IP가 없는 다른 Azure 서비스에 대해서만 방화벽이 열립니다.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>로컬에서 프로덕션 MySQL 서버에 연결

로컬 터미널 창에서 Azure의 MySQL 서버에 연결합니다. 이전에 ```<admin-user>``` 및 ```<mysql-server-name>```에 대해 지정한 값을 사용합니다. 암호를 묻는 메시지가 표시되면 Azure에서 데이터베이스를 만들 때 지정한 암호를 사용합니다.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>프로덕션 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

_phpappuser_ 라는 데이터베이스 사용자를 만들고 `sampledb` 데이터베이스의 모든 권한을 부여합니다. 이 자습서에서는 편의상 _MySQLAzure2020_ 을 암호로 사용합니다.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

`quit`를 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>MySQL 유연한 서버에 앱 연결

이 단계에서는 Azure Database for MySQL에서 만든 MySQL 데이터베이스에 PHP 애플리케이션을 연결합니다.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

리포지토리 루트에서 _.env.production_ 파일을 만들고 다음 변수를 복사합니다. *DB_HOST* 및 *DB_USERNAME* 의 자리 표시자 _&lt;mysql-server-name>_ 을 바꿉니다.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

변경 내용을 저장합니다.

> [!TIP]
> MySQL 연결 정보를 보호하기 위해 이 파일은 이미 Git 리포지토리에서 제외됩니다(리포지토리 루트의 _.gitignore_ 참조). 나중에 Azure Database for MySQL에서 데이터베이스에 연결하도록 App Service에서 환경 변수를 구성하는 방법에 대해 알아봅니다. 환경 변수를 사용하면 App Service에서 *.env* 파일이 필요하지 않습니다.
>

### <a name="configure-tlsssl-certificate"></a>TLS/SSL 인증서 구성

기본적으로 MySQL 유연한 서버는 클라이언트의 TLS 연결을 적용합니다. Azure의 MySQL 데이터베이스에 연결하려면 [Azure Database for MySQL 유연한 서버에서 제공하는 _.pem_ 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)를 사용해야 합니다. [이 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem))를 다운로드하여 샘플 앱 리포지토리의 로컬 복사본에 있는 **SSL** 폴더에 배치합니다.

다음 코드와 같이 _config/database.php_ 를 열고 `sslmode` 및 `options` 매개 변수를 `connections.mysql`에 추가합니다.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>로컬에서 애플리케이션 테스트

_.env.production_ 을 환경 파일로 사용해서 Laravel 데이터베이스 마이그레이션을 실행하고 Azure Database for MySQL에서 MySQL 데이터베이스에 테이블을 만듭니다. _.env.production_ 에는 Azure의 MySQL 데이터베이스에 대한 연결 정보가 있습니다.

```bash
php artisan migrate --env=production --force
```

_.env.production_ 에는 아직 유효한 애플리케이션 키가 없습니다. 터미널에서 새 키를 생성합니다.

```bash
php artisan key:generate --env=production --force
```

_.env.production_ 을 환경 파일로 사용해서 샘플 애플리케이션을 실행합니다.

```bash
php artisan serve --env=production
```

`http://localhost:8000`로 이동합니다. 오류 없이 페이지가 로드되면 PHP 애플리케이션이 Azure의 MySQL 데이터베이스에 연결됩니다.

해당 페이지에서 몇 가지 작업을 추가합니다.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP가 Azure Database for MySQL에 성공적으로 연결됨":::

PHP를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="commit-your-changes"></a>변경 내용을 커밋합니다

다음 Git 명령을 실행하여 변경 내용을 커밋합니다.

```bash
git add .
git commit -m "database.php updates"
```

앱을 배포할 준비가 되었습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 MySQL에 연결된 PHP 애플리케이션을 Azure App Service에 배포합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

FTP 및 로컬 Git는 배포 사용자를 통해 Azure 웹앱에 배포할 수 있습니다. 일단 배포 사용자를 구성하면 모든 Azure 배포에 사용할 수 있습니다. 계정 수준 배포 사용자 이름 및 암호는 Azure 구독 자격 증명과 다릅니다.

배포 사용자를 구성하려면 Azure Cloud Shell에서 [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) 명령을 실행합니다. _&lt;username>_ 및 _&lt;password>_ 를 배포 사용자 이름 및 암호로 바꿉니다.

사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ‘@’ 기호를 포함하면 안 됩니다.
암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

JSON 출력에는 암호가 null로 표시됩니다. '충돌'. 상세 정보: 409 오류가 발생하면 사용자 이름을 변경합니다. '잘못된 요청'. 상세 정보: 400 오류가 발생하면 더 강력한 암호를 사용합니다. **웹앱을 배포할 때 사용할 수 있도록 사용자 이름 및 암호를 기록해 둡니다.**

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

Cloud Shell에서 [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 명령을 사용하여 리소스 그룹에 App Service 계획을 만듭니다. 다음 예제에서는 체험 가격 책정 계층(--sku F1) 및 Linux 컨테이너(--is-linux)에 myAppServicePlan이라는 App Service 요금제를 만듭니다.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>웹앱 만들기

myAppServicePlan App Service 요금제에 [웹앱](../../app-service/overview.md#app-service-on-linux)을 만듭니다.

Cloud Shell에서 [az webapp create](/cli/azure/webapp#az_webapp_create) 명령을 사용하면 됩니다. 다음 예제에서 _&lt;app-name>_ 을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`). 런타임은 `PHP|7.0`으로 설정됩니다. 지원되는 모든 런타임을 보려면 [az webapp list-runtimes --linux](/cli/azure/webapp#az_webapp_list_runtimes) 명령을 실행합니다.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

git 배포를 활성화하여 새 빈 웹앱을 만들었습니다.

> [!NOTE]
> Git 원격의 URL은 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 형식으로 deploymentLocalGitUrl 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

App Service에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 환경 변수를 _앱 설정_ 으로 설정합니다.

다음 명령에서는 `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` 및 `DB_PASSWORD` 앱 설정을 구성합니다. _&lt;app-name>_ 및 _&lt;mysql-server-name>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

PHP [getenv](https://www.php.net/manual/en/function.getenv.php) 메서드를 사용하여 설정에 액세스할 수 있습니다. Laravel 코드에서는 `getenv` PHP에 대해 [env()](https://laravel.com/docs/5.4/helpers#method-env) 래퍼를 사용합니다. 예를 들어 _config/database.php_ 의 MySQL 구성은 다음 코드와 같습니다.

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel 환경 변수 구성

Laravel에는 App Service의 애플리케이션 키가 필요합니다. 앱 설정을 사용하여 키를 구성할 수 있습니다.

로컬 터미널 창에서 `php artisan`을 사용하여 _.env_ 로 저장하지 않으면서 새 애플리케이션 키를 생성합니다.

```bash
php artisan key:generate --show
```

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 App Service 앱에서 애플리케이션 키를 설정합니다. 자리 표시자 _&lt;app-name>_ 및 _&lt;outputofphpartisankey:generate>_ 를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`는 배포된 앱에서 오류가 발생하면 디버깅 정보를 반환하도록 Laravel에 지시합니다. 프로덕션 애플리케이션을 실행할 때 더 안전한 `false`로 설정합니다.

### <a name="set-the-virtual-application-path"></a>가상 애플리케이션 경로 설정

[Laravel 애플리케이션 수명 주기](https://laravel.com/docs/5.4/lifecycle)는 애플리케이션의 루트 디렉터리 대신 _public_ 디렉터리에서 시작됩니다. App Service용 기본 PHP Docker 이미지는 Apache를 사용하며 Laravel에 맞게 `DocumentRoot`를 사용자 지정하는 것을 허용하지 않습니다. 하지만 `.htaccess`를 사용하여 루트 디렉터리 대신 _/public_ 을 가리키도록 모든 요청을 다시 쓸 수 있습니다. 리포지토리 루트에는 이 목적을 위해 `.htaccess`가 이미 추가되었습니다. 이를 통해 Laravel 애플리케이션을 배포할 수 있습니다.

자세한 내용은 [사이트 루트 변경](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root)을 참조하세요.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 터미널 창으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. _&lt;deploymentLocalGitUrl-from-create-step>_ 을 [웹앱 만들기](#create-a-web-app)에 저장된 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. Git Credential Manager에서 자격 증명을 묻는 메시지가 표시되면 Azure Portal에 로그인하는 데 사용하는 자격 증명이 아니라 **배포 사용자 구성** 에서 만든 자격 증명을 입력해야 합니다.

```bash
git push azure main
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

`http://<app-name>.azurewebsites.net`으로 이동한 후 목록에 몇 가지 작업을 추가합니다.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Azure의 PHP 웹앱":::

축하합니다! Azure App Service에서 데이터 기반 PHP 앱을 실행하고 있습니다.

## <a name="update-model-locally-and-redeploy"></a>로컬에서 모델 업데이트 및 다시 배포

이 단계에서는 `task` 데이터 모델과 웹앱을 간단히 변경한 다음 업데이트를 Azure에 게시합니다.

작업 시나리오의 경우 작업을 완료한 것으로 표시할 수 있도록 애플리케이션을 수정합니다.

### <a name="add-a-column"></a>열 추가

로컬 터미널 창에서 Git 리포지토리의 루트로 이동합니다.

`tasks` 테이블에 대한 새 데이터베이스 마이그레이션을 생성합니다.

```bash
php artisan make:migration add_complete_column --table=tasks
```

이 명령은 생성되는 마이그레이션 파일의 이름을 표시합니다. _database/migrations_ 에서 이 파일을 찾아서 엽니다.

`up` 메서드를 다음 코드로 바꿉니다.

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

앞의 코드는 `complete`라는 `tasks` 테이블에 부울 열을 추가합니다.

`down` 메서드를 롤백 작업에 대한 다음 코드로 바꿉니다.

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

로컬 터미널 창에서 Laravel 데이터베이스 마이그레이션을 실행하여 로컬 데이터베이스를 변경합니다.

```bash
php artisan migrate
```

[Laravel 명명 규칙](https://laravel.com/docs/5.4/eloquent#defining-models)에 따라 `Task`(_app/Task.php_ 참조) 모델은 기본적으로 `tasks` 테이블에 매핑됩니다.

### <a name="update-application-logic"></a>애플리케이션 논리 업데이트

*routes/web.php* 파일을 엽니다. 애플리케이션에서 해당 경로 및 비즈니스 논리를 여기에 정의합니다.

파일의 끝에 다음 코드를 포함하는 경로를 추가합니다.

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

앞의 코드는 `complete` 값을 설정/해제하여 데이터 모델을 간단히 업데이트합니다.

### <a name="update-the-view"></a>보기 업데이트

*resources/views/tasks.blade.php* 파일을 엽니다. `<tr>` 여는 태그를 찾아 다음으로 바꿉니다.

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

앞의 코드는 작업이 완료되었는지 여부에 따라 행 색을 변경합니다.

다음 줄에는 다음 코드가 있습니다.

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

전체 줄을 다음 코드로 바꿉니다.

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

앞의 코드는 이전에 정의한 경로를 참조하는 제출 단추를 추가합니다.

### <a name="test-the-changes-locally"></a>로컬에서 변경 내용 테스트

로컬 터미널 창에서 Git 리포지토리의 루트 디렉터리에서 개발 서버를 실행합니다.

```bash
php artisan serve
```

작업 상태 변경을 확인하려면 `http://localhost:8000`으로 이동하고 확인란을 선택합니다.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="작업에 확인란이 추가됨":::

PHP를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 프로덕션 연결 문자열로 Laravel 데이터베이스 마이그레이션을 실행하여 Azure 데이터베이스를 변경합니다.

```bash
php artisan migrate --env=production --force
```

Git에서 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

`git push`가 완료되면 Azure 앱으로 이동하여 새 기능을 테스트합니다.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Azure에 게시된 모델 및 데이터베이스 변경 내용":::

모든 작업을 추가했으면 데이터베이스에서 유지됩니다. 데이터 스키마를 업데이트하는 경우 기존 데이터는 그대로 유지됩니다.

## <a name="clean-up-resources"></a>리소스 정리
이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것 같으면 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Portal에서 리소스 관리](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [서버를 관리하는 방법](how-to-manage-server-cli.md)
