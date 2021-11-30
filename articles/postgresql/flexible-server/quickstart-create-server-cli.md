---
title: '빠른 시작: 서버 만들기 - Azure CLI - Azure Database for PostgreSQL - 유연한 서버'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 Azure Database for PostgreSQL 유연한 서버를 만드는 방법을 살펴봅니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/30/2021
ms.custom: mvc, devx-track-azurecli, mode-api
ms.openlocfilehash: b777d4197544a30dbf35c68881c673d4e453a27b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266941"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Database for PostgreSQL 유연한 서버 만들기

이 빠른 시작에서는 [Azure Cloud Shell](https://shell.azure.com)에서 [Azure CLI](/cli/azure/get-started-with-azure-cli) 명령을 사용하여 5분 안에 Azure Database for PostgreSQL 유연한 서버를 만드는 방법을 보여 줍니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.



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

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>유연한 서버 만들기

`az group create` 명령을 사용하여 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md)을 만든 다음, 이 리소스 그룹 내에 PostgreSQL 유연한 서버를 만듭니다. 고유한 이름을 제공해야 합니다. 다음 예제에서는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

`az postgres flexible-server create` 명령을 사용하여 유연한 서버를 만듭니다. 서버는 여러 데이터베이스를 포함할 수 있습니다. 다음 명령은 Azure CLI의 [로컬 컨텍스트](/cli/azure/local-context)에 있는 서비스 기본값 및 값을 사용하여 서버를 만듭니다. 

```azurecli
az postgres flexible-server create
```

생성된 서버에는 다음과 같은 특성이 있습니다. 
- 자동 생성된 서버 이름, 관리자 이름, 관리자 암호, 리소스 그룹 이름(로컬 컨텍스트에서 아직 지정되지 않은 경우) 및 리소스 그룹과 동일한 위치. 
- 나머지 서버 구성에 대한 서비스 기본값: 컴퓨팅 계층(범용), 컴퓨팅 크기/SKU(D2s_v3 - 2 vCore, 8GB RAM), 백업 보존 기간(7일) 및 PostgreSQL 버전(12)
- 기본 연결 방법은 자동 생성된 가상 네트워크 및 서브넷을 사용하는 프라이빗 액세스(VNet 통합)입니다.

> [!NOTE] 
> 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 예를 들어 만드는 동안 *프라이빗 액세스(VNet 통합)* 를 선택한 경우 만든 후에 *퍼블릭 액세스(허용된 IP 주소)* 로 변경할 수 없습니다. VNet 통합을 사용하여 서버에 안전하게 액세스하려면 프라이빗 액세스 권한이 있는 서버를 만드는 것이 좋습니다. [개념 문서](./concepts-networking.md)에서 프라이빗 액세스에 대해 자세히 알아보세요.

기본값을 변경하려면 Azure CLI 참조 문서에서 <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요. 

> [!NOTE]
> Azure Database for PostgreSQL에 대한 연결은 포트 5432를 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 5432 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 5432포트를 열지 않으면 서버에 연결할 수 없습니다.

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin** 을 기록해 둡니다.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>PostgreSQL 명령줄 클라이언트를 사용하여 연결

*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고, 생성된 가상 네트워크에 추가할 수 있습니다. 

VM을 만든 후에는 머신에 SSH를 수행할 수 있으며 **[psql](https://www.postgresql.org/download/)** 명령줄 도구를 설치할 수 있습니다.

psql을 사용하면 다음 명령으로 연결합니다. 값을 실제 서버 이름 및 암호로 바꿉니다. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```
>[!Note]
> `The parameter PrivateDnsZoneArguments is required, and must be provided by customer` 오류가 발생하면 이전 버전의 Azure CLI를 실행 중일 수 있습니다. [Azure CLI를 업그레이드](/cli/azure/update-azure-cli)하고 작업을 다시 시도하세요.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음 명령을 수행하여 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버만 삭제하려면 `az postgres flexible-server delete` 명령을 실행할 수 있습니다.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[App Service 및 PostgreSQL를 사용하여 Django 앱 배포](tutorial-django-app-service-postgres.md)
