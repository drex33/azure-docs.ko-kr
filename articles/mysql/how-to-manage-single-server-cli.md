---
title: 서버 관리 - Azure CLI - Azure Database for MySQL
description: Azure CLI에서 Azure Database for MySQL 서버를 관리하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 51e0913404fd0cf853eaeae39a91f648ece17341
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642161"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL 단일 서버 관리

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure에 배포된 단일 서버를 관리하는 방법을 보여줍니다. 관리 작업에는 컴퓨팅 및 스토리지 스케일링, 관리자 암호 재설정, 서버 세부 정보 보기 등이 포함됩니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. 이 문서에서는 Azure CLI 버전 2.0 이상을 로컬로 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](/cli/azure/reference-index#az_login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **id** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](/cli/azure/account#az_account_list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

서버를 아직 만들지 않은 경우 [빠른 시작](quickstart-create-mysql-server-database-using-azure-cli.md)을 참조하여 새로 만듭니다.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 크기 조정
다음 명령을 사용하여 가격 책정 계층, 컴퓨팅 및 스토리지를 스케일 업할 수 있습니다. [az mysql server overview](/cli/azure/mysql/server)를 수행할 수 있는 모든 서버 작업을 볼 수 있습니다.

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

위의 인수에 대한 세부 정보는 다음과 같습니다.

**설정** | **샘플 값** | **설명**
---|---|---
name | mydemoserver | 고유한 Azure Database for MySQL 서버 이름을 입력합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
resource-group | myresourcegroup | Azure 리소스 그룹의 이름을 입력합니다.
sku-name|GP_Gen5_2|가격 책정 계층 및 컴퓨팅 구성의 이름을 입력합니다. {가격 책정 계층} _{계산 세대}_ {vCores} 규칙을 축약형으로 따릅니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.
storage-size | 6144 | 서버의 스토리지 용량입니다(단위는 메가바이트). 최솟값은 5120이고 1024씩 증분하여 늘어납니다.

> [!Important]
> - 스토리지는 스케일 업할 수 있지만 스케일 다운할 수는 없습니다.
> - 기본에서 범용 또는 메모리 최적화 가격 책정 계층으로 스케일 업하는 것은 지원되지 않습니다. [Bash 스크립트를 사용](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)하거나 [MySQL Workbench를 사용](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)하여 수동으로 스케일 업할 수 있습니다.


## <a name="manage-mysql-databases-on-a-server"></a>서버에서 MySQL 데이터베이스 관리
다음 명령 중 하나를 사용하여 서버에 있는 데이터베이스의 데이터베이스 속성을 만들고, 삭제하고, 나열하고, 볼 수 있습니다.

| Cmdlet | 사용| Description |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az_mysql_db_create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |데이터베이스 만들기|
|[az mysql db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|서버에서 데이터베이스를 삭제합니다. 이 명령은 서버를 삭제하지 않습니다. |
|[az mysql db list](/cli/azure/sql/db#az_mysql_db_list)|```az mysql db list -g myresourcegroup -s mydemoserver```|서버의 모든 데이터베이스를 나열합니다.|
|[az mysql db show](/cli/azure/sql/db#az_mysql_db_show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|데이터베이스에 대한 자세한 정보를 표시합니다.|

## <a name="update-admin-password"></a>관리자 암호 업데이트
다음 명령을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  암호는 최소 8자, 최대 128자여야 합니다.
> 암호에는 영어 대문자, 영어 소문자, 숫자, 영숫자가 아닌 문자 중 세 가지에 해당하는 문자가 포함되어야 합니다.

## <a name="delete-a-server"></a>서버 삭제
새로 만든 MySQL 단일 서버를 삭제하려면 [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) 명령을 실행할 수 있습니다.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계
- [서버 다시 시작](howto-restart-server-cli.md)
- [상태가 잘못된 서버 복원](howto-restore-server-cli.md)
- [서비스 모니터링 및 튜닝](concepts-monitoring.md)