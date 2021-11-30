---
title: 서버 관리 - Azure CLI - Azure Database for PostgreSQL - 유연한 서버
description: Azure CLI에서 Azure Database for PostgreSQL - 유연한 서버를 관리하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: d3dda3106289a391470268fc711e61ca972cb649
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267739"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버 관리



이 문서에서는 Azure에 배포된 유연한 서버를 관리하는 방법을 보여 줍니다. 관리 작업에는 컴퓨팅 및 스토리지 크기 조정, 관리자 암호 재설정 및 서버 세부 정보 보기가 포함됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. 

Azure CLI 버전 2.0 이상을 로컬에서 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](/cli/azure/reference-index#az_login) 명령을 사용하여 계정에 로그인합니다. 

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) 명령을 사용하여 구독을 선택합니다. 다음 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 구독을 선택합니다. 모든 구독을 식별하려면 [az account list](/cli/azure/account#az_account_list) 명령을 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 아직 유연한 서버를 만들지 않았다면 이 방법 가이드를 따라야 합니다.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 크기 조정

다음 명령을 사용하여 컴퓨팅 계층, vCore 및 스토리지를 쉽게 스케일 업할 수 있습니다. 실행할 수 있는 모든 서버 작업 목록은 [az postgres flexible-server](/cli/azure/postgres/flexible-server) 개요를 참조하세요.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

다음은 이전 코드의 인수에 대한 세부 정보입니다.

**설정** | **샘플 값** | **설명**
---|---|---
name | mydemoserver | 서버의 고유 이름을 입력합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자를 포함해야 합니다.
resource-group | myresourcegroup | Azure 리소스 그룹의 이름을 입력합니다.
sku-name|Standard_D4ds_v3|컴퓨팅 계층 및 크기의 이름을 입력합니다. 값은 축약형의 *Standard_{VM size}* 규칙을 따릅니다. 자세한 내용은 [가격 책정 계층](../concepts-pricing-tiers.md)을 참조하세요.
storage-size | 6144 | 서버의 스토리지 용량을 메가바이트 단위로 입력합니다. 최솟값은 5120이며 1024씩 증가합니다.

> [!IMPORTANT]
> 스토리지를 스케일 다운할 수 없습니다. 

## <a name="manage-postgresql-databases-on-a-server"></a>서버에서 PostgreSQL 데이터베이스 관리

Azure Database for PostgreSQL 서버 연결하기 위해 사용할 수 있는 여러 애플리케이션이 있습니다. 클라이언트 컴퓨터에 PostgreSQL이 설치되어 있는 경우 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 로컬 인스턴스를 사용할 수 있습니다. 이제 psql 명령줄 도구를 사용하여 Azure Database for PostgreSQL 서버에 연결하겠습니다.

1. 다음 **psql** 명령을 실행합니다.

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   예를 들어, 다음 명령은 액세스 자격 증명을 통해 **mydemoserver.postgres.database.azure.com** PostgreSQL 서버의 **postgres** 라는 기본 데이터베이스에 연결합니다. 메시지가 표시되면 선택한 `<server_admin_password>`를 입력합니다.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   연결하면 psql 도구는 SQL 명령을 입력할 수 있는 **postgres** 프롬프트를 표시합니다. 사용 중인 psql 버전이 Azure Database for PostgreSQL 서버의 버전과 다른 경우 초기 연결 출력에 경고가 표시됩니다.

   psql 출력의 예:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 방화벽이 클라이언트의 IP 주소를 허용하도록 구성되지 않은 경우 다음 오류가 발생합니다.
   >
   > "psql: 오류: 호스트 `<IP address>`, 사용자 "myadmin", 데이터베이스 "postgres"에 대한 pg_hba.conf 항목이 없습니다. SSL 설정 오류: SSL 연결이 필요합니다. SSL 옵션을 지정하고 다시 시도하세요."
   >
   > 클라이언트의 IP 주소가 방화벽 규칙에서 허용되는지 확인합니다.

2. 프롬프트에서 다음 명령을 입력하여 **postgresdb** 라는 빈 데이터베이스를 만듭니다.

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 프롬프트에서 다음 명령을 실행하여 새로 만든 **postgresdb** 데이터베이스에 대한 연결로 전환합니다.

    ```bash
    \c postgresdb
    ```

4. `\q`를 입력하고 Enter 키를 선택하여 psql을 종료합니다.

이 섹션에서 psql을 통해 Azure Database for PostgreSQL 서버에 연결하고, 빈 사용자 데이터베이스를 만들었습니다.

## <a name="reset-the-admin-password"></a>관리자 암호 다시 설정

다음 명령을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 최소 8자에서 최대 128자의 암호를 선택합니다. 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 
> - 영어 대문자
> - 영어 소문자
> - 숫자
> - 영숫자가 아닌 문자

## <a name="delete-a-server"></a>서버 삭제

Azure Database for PostgreSQL 유연한 서버를 삭제하려면 [az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete) 명령을 실행합니다.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

- [백업 및 복원 개념 이해](concepts-backup-restore.md)
- [서버 튜닝 및 모니터링](concepts-monitoring.md)