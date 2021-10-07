---
title: '자습서: 동일한 가상 네트워크에 Azure Database for PostgreSQL - 유연한 서버 및 Azure App Service 웹앱 만들기'
description: 웹앱을 사용하여 가상 네트워크에 Azure Database for PostgreSQL - 유연한 서버를 만드는 빠른 시작 가이드
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 146a3a252a0f8105245c2a6826876e1400efdc13
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766989"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>자습서: App Services 웹앱을 사용하여 가상 네트워크에 Azure Database for PostgreSQL - 유연한 서버 만들기

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 자습서에서는 Azure Database for PostgreSQL - 유연한 서버(미리 보기)를 사용하여 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 내에 Azure App Service 웹앱을 만드는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
>[!div class="checklist"]
> * 가상 네트워크에 PostgreSQL 유연한 서버 만들기
> * 웹앱 만들기
> * 가상 네트워크에 웹앱 추가
> * 웹앱에서 Postgres에 연결 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- [Azure CLI 버전 2.0 이상을 로컬에 설치](/cli/azure/install-azure-cli)합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 
- [az login](/cli/azure/authenticate-azure-cli) 명령을 사용하여 계정에 로그인합니다. 해당 구독 이름에 대한 명령 출력에서 **id** 속성을 참고합니다.

  ```azurecli
  az login
  ```
- 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>새 가상 네트워크에 PostgreSQL 유연한 서버 만들기

다음 명령을 사용하여 VNET(가상 네트워크) 내부에 프라이빗 유연한 서버를 만듭니다.

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 리소스 그룹이 없는 경우 생성합니다.
- 서버 이름을 입력하지 않은 경우 서버 이름을 생성합니다.
- 새 postgreSQL 서버를 위한 새 가상 네트워크를 만들고, 이 가상 네트워크 내에 데이터베이스 서버를 위한 서브넷을 만듭니다.
- 서버의 관리자 사용자 이름 및 암호를 입력하지 않은 경우 지금 만듭니다.
- **postgres** 라는 빈 데이터베이스 만들기

샘플 출력은 다음과 같습니다.

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>웹앱 만들기
이 섹션에서는 App Service 앱에서 앱 호스트를 만들고, 이 앱을 Postgres 데이터베이스에 연결한 다음, 코드를 해당 호스트에 배포합니다. 터미널에서 애플리케이션 코드의 리포지토리 루트에 있는지 확인합니다. 참고: 기본 요금제에서는 VNET 통합이 지원되지 않습니다. 표준 또는 프리미엄을 사용하세요. 

az webapp up 명령을 사용하여 App Service 앱(호스트 프로세스) 만들기

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - _--location_ 인수의 경우 이전 섹션의 데이터베이스에 사용한 것과 동일한 위치를 사용합니다.
> - \<app-name\>을 모든 Azure에서 고유한 이름으로 바꿉니다. \<app-name\>에 허용되는 문자는 A-Z, 0-9 및 -입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.

이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 리소스 그룹이 없는 경우 생성합니다. (이 명령에서는 이전에 데이터베이스를 만든 것과 동일한 리소스 그룹을 사용합니다.)
- 없는 경우 App Service 앱을 만듭니다.
- 아직 사용하도록 설정되지 않은 경우 기본 로깅을 앱에 사용하도록 설정합니다.
- 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.

### <a name="create-subnet-for-web-app"></a>웹앱을 위한 서브넷 만들기
VNET 통합을 사용하도록 설정하려면 App Service 웹앱에 위임된 서브넷이 있어야 합니다. 서브넷을 만들기 전에 데이터베이스 서브넷 주소를 확인하여 웹앱 서브넷에 동일한 주소 접두사가 사용되지 않도록 합니다. 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

다음 명령을 실행하여 데이터베이스 서버가 생성된 것과 동일한 가상 네트워크에 새 서브넷을 만듭니다. **데이터베이스 서브넷과의 충돌을 방지하기 위해 주소 접두사를 업데이트합니다.**

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>가상 네트워크에 웹앱 추가
[az webapp vnet-integration](/cli/azure/webapp/vnet-integration) 명령을 사용하여 지역 가상 네트워크 통합을 웹앱에 추가합니다. 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>데이터베이스를 연결하도록 환경 변수 구성
코드가 App Service에 배포되었으므로, 다음 단계는 Azure에서 앱을 유연한 서버에 연결하는 것입니다. 앱 코드는 다양한 환경 변수에서 데이터베이스 정보를 찾을 것으로 예상합니다. App Service에서 환경 변수를 설정하려면 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용합니다.

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- 새로 만든 유연한 서버 명령의 **postgres-server-name**, **username**, **password** 를 바꿉니다.
- **\<username\>** 및 **\<password\>** 를 명령에서 생성한 자격 증명으로 바꿉니다.
- 리소스 그룹 및 앱 이름은 .azure/config 파일의 캐시된 값에서 가져옵니다.
- 이 명령은 **DBHOST**, **DBNAME**, **DBUSER** _, _*DBPASS**라는 설정을 만듭니다. 애플리케이션 코드에서 데이터베이스 정보에 다른 이름을 사용하는 경우 코드에 설명된 대로 해당 이름을 앱 설정에 사용하세요.

가상 네트워크 내에서의 모든 아웃바운드 연결을 허용하도록 웹앱을 구성합니다.
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>리소스 정리

다음 명령을 사용하여 자습서에서 만든 모든 리소스를 정리합니다. 이 명령은 리소스 그룹에 있는 모든 리소스를 삭제합니다.

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](../../app-service/app-service-web-tutorial-custom-domain.md)
