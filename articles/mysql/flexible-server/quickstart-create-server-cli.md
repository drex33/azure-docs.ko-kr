---
title: '빠른 시작: 서버 만들기 - Azure CLI - Azure Database for MySQL - 유연한 서버'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 Azure Database for MySQL 유연한 서버를 만드는 방법을 살펴봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli, mode-api
ms.openlocfilehash: 5dc10019c5ed06b49c9d51aea80cc626a1aceddc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043560"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 만들기

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


이 빠른 시작에서는 [Azure Cloud Shell](https://shell.azure.com)에서 [Azure CLI](/cli/azure/get-started-with-azure-cli) 명령을 사용하여 5분 안에 Azure Database for MySQL 유연한 서버를 만드는 방법을 보여 줍니다. 

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

[Azure Cloud Shell](../../cloud-shell/overview.md)은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[az login](/cli/azure/reference-index#az_login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **id** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](/cli/azure/account#az_account_list)를 사용합니다.

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>유연한 서버 만들기

`az group create` 명령을 사용하여 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md)을 만든 다음, 이 리소스 그룹 내에 MySQL 유연한 서버를 만듭니다. 고유한 이름을 제공해야 합니다. 다음 예제에서는 `eastus2` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

`az mysql flexible-server create` 명령을 사용하여 유연한 서버를 만듭니다. 서버는 여러 데이터베이스를 포함할 수 있습니다. 다음 명령은 Azure CLI의 [로컬 컨텍스트](/cli/azure/local-context)에 있는 서비스 기본값 및 값을 사용하여 서버를 만듭니다.

```azurecli-interactive
az mysql flexible-server create
```

생성된 서버에는 다음과 같은 특성이 있습니다.
- 자동 생성된 서버 이름, 관리자 이름, 관리자 암호, 리소스 그룹 이름(로컬 컨텍스트에서 아직 지정되지 않은 경우) 및 리소스 그룹과 동일한 위치.
- 나머지 서버 구성에 대한 서비스 기본값: 컴퓨팅 계층(버스트 가능), 컴퓨팅 크기/SKU(B1MS), 백업 보존 기간(7일) 및 MySQL 버전(5.7)
- 기본 연결 방법은 자동 생성된 가상 네트워크 및 서브넷을 사용하는 프라이빗 액세스(VNet 통합)입니다.

> [!NOTE]
> 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 예를 들어 만드는 동안 *프라이빗 액세스(VNet 통합)* 를 선택한 경우 만든 후에 *퍼블릭 액세스(허용된 IP 주소)* 로 변경할 수 없습니다. VNet 통합을 사용하여 서버에 안전하게 액세스하려면 프라이빗 액세스 권한이 있는 서버를 만드는 것이 좋습니다. [개념 문서](./concepts-networking.md)에서 프라이빗 액세스에 대해 자세히 알아보세요.

기본값을 변경하려는 경우 구성 가능한 CLI 매개 변수의 전체 목록에 대한 Azure CLI [참조 설명서](/cli/azure/mysql/flexible-server)를 참조하세요.

다음은 몇 가지 샘플 출력입니다.

```json
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

기본값을 변경하려는 경우 구성 가능한 CLI 매개 변수의 전체 목록에 대한 Azure CLI [참조 설명서](/cli/azure/mysql/flexible-server)를 참조하세요.

## <a name="create-a-database"></a>데이터베이스 만들기
아직 만들지 않은 경우 다음 명령을 실행하여 **newdatabase** 인 데이터베이스를 만듭니다.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다.

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin** 을 기록해 둡니다. 다음은 JSON 출력의 샘플입니다.

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Azure CLI를 사용하여 연결 및 연결 테스트

Azure Database for MySQL 유연한 서버를 사용하면 Azure CLI ```az mysql flexible-server connect``` 명령으로 mysql 서버에 연결할 수 있습니다. 이 명령을 사용하여 데이터베이스 서버에 대한 연결을 테스트하고, 빠른 시작 데이터베이스를 만들고, mysql.exe 또는 MySQL 워크벤치를 설치하지 않고도 서버에 대해 쿼리를 직접 실행할 수 있습니다.  여러 쿼리를 실행하기 위해 대화형 모드에서 명령 실행을 사용할 수도 있습니다.

다음 스크립트를 실행하여 개발 환경에서 데이터베이스에 대한 연결을 테스트하고 유효성을 검사합니다.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**예제:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
성공적인 연결을 위해 다음과 같은 출력이 표시되어야 합니다.

```output
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
연결에 실패하면 다음 솔루션을 시도해 보세요.
- 클라이언트 컴퓨터에서 포트 3306이 열려 있는지 확인합니다.
- 서버 관리자의 사용자 이름 및 암호가 올바른 경우
- 클라이언트 컴퓨터에 대한 방화벽 규칙을 구성한 경우
- 가상 네트워킹에서 프라이빗 액세스로 서버를 구성한 경우 클라이언트 컴퓨터가 동일한 가상 네트워크에 있는지 확인합니다.

다음 명령을 실행하여 ```--querytext``` 인수, ```-q```를 사용하여 단일 쿼리를 실행합니다.

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**예제:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
```az mysql flexible-server connect``` 명령 사용에 대해 자세히 알아보려면 [연결 및 쿼리](connect-azure-cli.md) 설명서를 참조하세요.

## <a name="connect-using-mysql-command-line-client"></a>mysql 명령줄 클라이언트를 사용하여 연결

프라이빗 액세스(VNet 통합)를 사용하여 유연한 서버를 만든 경우 서버와 동일한 가상 네트워크 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고, 유연한 서버를 사용하여 만든 가상 네트워크에 이를 추가할 수 있습니다. 자세한 정보는 [프라이빗 액세스 설명서](how-to-manage-virtual-network-portal.md) 구성을 참조하세요.

퍼블릭 액세스(허용된 IP 주소)를 사용하여 유연한 서버를 만든 경우 로컬 IP 주소를 서버의 방화벽 규칙 목록에 추가할 수 있습니다. 단계별 지침은 [방화벽 규칙 만들기 또는 관리 설명서](how-to-manage-firewall-portal.md)를 참조하세요.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 사용하여 로컬 환경에서 서버에 연결할 수 있습니다. Azure Database for MySQL 유연한 서버는 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)를 사용한 MySQL 서비스에 대한 클라이언트 애플리케이션 연결을 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있는 업계 표준 프로토콜입니다. MySQL 유연한 서버에 연결하려면 인증 기관 확인을 위해 [공용 SSL 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)를 다운로드해야 합니다. 암호화된 연결을 사용한 연결 또는 SSL을 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [암호화된 연결을 사용한 Azure Database for MySQL - 유연한 서버에 연결](how-to-connect-tls-ssl.md) 설명서를 참조하세요.

다음 예제에서는 mysql 명령줄 인터페이스를 사용하여 유연한 서버에 연결하는 방법을 보여 줍니다. 아직 설치 하지 않은 경우 먼저 mysql 명령줄을 설치합니다. SSL 연결에 필요한 DigiCertGlobalRootCA 인증서를 다운로드합니다. TLS/SSL 인증서 확인을 적용하려면 --ssl-mode=REQUIRED 연결 문자열 설정을 사용합니다. 로컬 인증서 파일 경로를 --ssl-ca 매개 변수에 전달합니다. 값을 실제 서버 이름 및 암호로 바꿉니다.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

**퍼블릭 액세스** 를 사용하여 유연한 서버를 프로비저닝한 경우 아래와 같이 미리 설치된 mysql 클라이언트를 사용하여 유연한 서버에 연결할 때 [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

Azure Cloud Shell을 사용하여 유연한 서버에 연결하려면 Azure Cloud Shell에서 유연한 서버로의 네트워킹 액세스를 허용해야 합니다. 이를 위해 Azure Portal의 MySQL 유연한 서버 **네트워킹** 블레이드로 이동하고, 아래 스크린샷에 나온 대로 **방화벽** 섹션 아래에 “Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)” 확인란을 선택하고 저장을 클릭하여 설정을 유지합니다.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="퍼블릭 액세스 네트워크 구성에 대해 MySQL 유연한 서버에 Azure Cloud Shell 액세스를 허용하는 방법을 보여 주는 스크린샷":::


> [!NOTE]
> 개발 또는 테스트하는 경우에만 **Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)** 를 선택해야 합니다. 다른 고객의 구독에서 들어오는 연결을 포함하여 Azure 서비스 또는 자산에 할당된 IP 주소에서 들어오는 연결을 허용하도록 방화벽을 구성합니다.

**사용해 보기** 를 클릭하여 Azure Cloud Shell을 시작하고 다음 명령을 사용하여 유연한 서버에 연결합니다. 명령에서는 서버 이름, 사용자 이름 및 암호를 사용합니다.

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
>Azure Cloud Shell을 사용하여 유연한 서버에 연결하는 동안 --ssl=true 매개 변수를 사용해야 하고 --ssl-mode=REQUIRED는 필요하지 않습니다.
> 주된 이유는 Azure Cloud Shell에는 Oracle 배포 환경의 mysql 클라이언트에는 --ssl-mode 매개 변수가 필요한 반면, --ssl 매개 변수가 필요한 MariaDB 배포 환경의 mysql.exe 클라이언트가 미리 설치되어 있기 때문입니다.

이전 명령을 실행한 후에 유연한 서버에 연결하는 동안 다음과 같은 오류 메시지가 표시되는 경우 앞에서 언급한 "Allow public access from any Azure service within Azure to this server(Azure 내 모든 Azure 서비스에서 이 서버에 대한 퍼블릭 액세스 허용)"를 사용하여 방화벽 규칙을 설정하지 않았거나 옵션이 저장되지 않은 것입니다. 방화벽을 다시 설정하고 다시 시도해 보세요.

오류 2002(HY000): \<servername\>의 MySQL 서버에 연결할 수 없음(115)

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음 명령을 수행하여 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버만 삭제하려면 `az mysql server delete` 명령을 실행할 수 있습니다.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
> [Azure CLI를 사용하여 연결 및 쿼리](connect-azure-cli.md)
> [암호화된 연결을 사용하여 Azure Database for MySQL - 유연한 서버에 연결](how-to-connect-tls-ssl.md)
> [MySQL을 사용하여 PHP(Laravel) 웹앱 빌드](tutorial-php-database-app.md)
