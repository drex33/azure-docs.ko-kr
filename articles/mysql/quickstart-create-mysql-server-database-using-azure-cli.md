---
title: '빠른 시작: 서버 만들기 - Azure CLI - Azure Database for MySQL'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 MySQL용 Azure Database 서버를 만드는 방법을 살펴봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli, mode-api
ms.openlocfilehash: b84306cefc5f664a7b4ec8111f156cf8f5d962c2
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052662"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!TIP]
> 보다 단순한 [az mysql up](/cli/azure/mysql#az_mysql_up) Azure CLI 명령(현재 미리 보기로 제공)을 사용하는 것이 좋습니다. [빠른 시작](./quickstart-create-server-up-azure-cli.md)을 사용해 보세요.

이 빠른 시작에서는 [Azure Cloud Shell](https://shell.azure.com)에서 [Azure CLI](/cli/azure/get-started-with-azure-cli) 명령을 사용하여 5분 안에 Azure Database for MySQL 서버를 만드는 방법을 보여 줍니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 이 빠른 시작에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

 - [az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](/cli/azure/account#az_account_list)를 사용합니다.

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
[az group create](/cli/azure/group) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만든 다음, 이 리소스 그룹 내에 MySQL 서버를 만듭니다. 고유한 이름을 제공해야 합니다. 다음 예제에서는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) 명령을 사용하여 MySQL용 Azure Database 서버를 만듭니다. 서버는 여러 데이터베이스를 포함할 수 있습니다.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

위의 인수에 대한 세부 정보는 다음과 같습니다. 

**설정** | **샘플 값** | **설명**
---|---|---
name | mydemoserver | 고유한 Azure Database for MySQL 서버 이름을 입력합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
resource-group | myresourcegroup | Azure 리소스 그룹의 이름을 입력합니다.
위치 | westus | 서버에 대한 Azure 위치입니다.
admin-user | myadmin | 관리자 로그인에 대한 사용자 이름입니다. **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public** 을 사용할 수 없습니다.
admin-password | *보안 암호* | 관리자 사용자의 암호입니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자 및 영숫자가 아닌 문자
sku-name|GP_Gen5_2|가격 책정 계층 및 컴퓨팅 구성의 이름을 입력합니다. {가격 책정 계층} _{계산 세대}_ {vCores} 규칙을 축약형으로 따릅니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

>[!IMPORTANT] 
>- 서버의 기본 MySQL 버전은 5.7입니다. 현재 5.6 및 8.0 버전도 사용할 수 있습니다.
>- **az mysql server create** 명령에 대한 모든 인수를 보려면 이 [참조 문서](/cli/azure/mysql/server#az_mysql_server_create)를 참조하세요.
>- SSL은 서버에서 기본적으로 사용하도록 설정됩니다. SSL에 대한 자세한 내용은 [SSL 연결 구성](howto-configure-ssl.md) 참조

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성 
기본적으로 만들어진 새 서버는 방화벽 규칙으로 보호되며 공개적으로 액세스할 수 없습니다. [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule) 명령을 사용하여 서버에서 방화벽 규칙을 구성할 수 있습니다. 이렇게 하면 서버에 로컬로 연결할 수 있습니다.

다음 예제는 특정 IP 주소 192.168.0.1에서 연결하는 것을 허용하는 `AllowMyIP` 방화벽 규칙을 만듭니다. 연결할 IP 주소를 바꿉니다. 필요한 경우 IP 주소 범위를 사용할 수 있습니다. IP를 검색하는 방법을 모르는 경우 [https://whatismyipaddress.com/](https://whatismyipaddress.com/)으로 이동하여 IP 주소를 가져옵니다.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다.

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin** 을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>mysql 명령줄 클라이언트를 사용하여 Azure Database for MySQL 서버에 연결
널리 사용되는 클라이언트 도구인 **[mysql.exe](https://dev.mysql.com/downloads/)** 명령줄 도구를 [Azure Cloud Shell](../cloud-shell/overview.md)과 함께 사용하여 서버에 연결할 수 있습니다. 또는 로컬 환경에서 mysql 명령줄을 사용할 수도 있습니다.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>리소스 정리
다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음 명령을 수행하여 삭제할 수 있습니다. 

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버 하나만 삭제하려면 [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) 명령을 실행합니다.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[MySQL을 사용하여 Windows에서 PHP 앱 빌드](../app-service/tutorial-php-mysql-app.md)
