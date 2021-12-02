---
title: '자습서: 포털의 지역 복제 및 장애 조치(Failover)'
description: Azure Portal 또는 Azure CLI를 사용하여 SQL 데이터베이스에 대해 지역 복제를 구성하고 장애 조치(failover)를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: 5e2022f5b1afd6fce06e2704c17652c10692469f
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133437337"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>자습서: 활성 지역 복제 및 장애 조치(failover) 구성(Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 [Azure Portal](https://portal.azure.com) 또는 Azure CLI를 사용하여 [Azure SQL Database에 대한 활성 지역 복제](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)를 구성하고 장애 조치(Failover)를 시작하는 방법을 보여줍니다.

자동 장애 조치(Failover) 그룹을 사용하는 모범 사례는 [Azure SQL Database에 대한 모범 사례](auto-failover-group-overview.md#best-practices-for-sql-database) 및 [Azure SQL Managed Instance에 대한 모범 사례](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)를 참조하세요. 



## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal을 사용하여 활성 지역 복제를 구성하려면 다음 리소스가 필요합니다.

* Azure SQL Database의 데이터베이스: 다른 지역으로 복제하려는 주 데이터베이스입니다.

> [!Note]
> Azure Portal을 사용하는 경우 기본 데이터베이스와 동일한 구독 내에서만 보조 데이터베이스를 만들 수 있습니다. 보조 데이터베이스가 다른 구독에 있어야 하는 경우 [데이터베이스 만들기 REST API](/rest/api/sql/databases/createorupdate) 또는 [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql)를 사용하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

활성 지역 복제를 구성하려면 Azure SQL Database에 데이터베이스가 있어야 합니다. 이것은 다른 지리적 지역으로 복제하려는 주 데이터베이스입니다.

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>보조 데이터베이스 추가

다음 단계에서는 지역에서 복제 파트너 관계에 새 보조 데이터베이스를 만듭니다.  

보조 데이터베이스를 추가하려면 구독 소유자 또는 공동 소유자여야 합니다.

보조 데이터베이스는 주 데이터베이스와 이름이 같고, 기본적으로 서비스 계층과 컴퓨팅 크기가 동일합니다. 보조 데이터베이스는 단일 데이터베이스 또는 풀링된 데이터베이스가 될 수 있습니다. 자세한 내용은 [DTU 기반 구매 모델](service-tiers-dtu.md) 및 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.
보조가 만들어지고 시드된 후 데이터는 주 데이터베이스에서 새로운 보조 데이터베이스로 복제되기 시작합니다.

> [!NOTE]
> 파트너 데이터베이스가 이미 있는 경우(예: 이전 지역에서 복제 관계를 종료한 결과) 명령이 실패합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 지역에서 복제를 위해 설치하려는 데이터베이스를 찾습니다.
2. SQL Database 페이지에서 데이터베이스를 선택하고, **데이터 관리** 로 이동하고, **복제본** 을 선택한 다음, **복제본 만들기** 를 선택합니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="지역에서 복제 구성":::

3. 보조 데이터베이스에 대해 서버를 선택하거나 만들고 필요에 따라 **컴퓨팅 + 스토리지** 옵션을 구성합니다. 보조 서버에 대해 지역을 선택할 수 있지만 [쌍을 이루는 지역](../../availability-zones/cross-region-replication-azure.md)이 권장됩니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{대체 텍스트}":::

    필요에 따라 탄력적 풀에 보조 데이터베이스를 추가할 수 있습니다. 풀에 보조 데이터베이스를 만들려면 **SQL 탄력적 풀 사용 여부?** 옆에서 **예** 를 선택하고 대상 서버에서 풀을 선택합니다. 대상 서버에 풀이 이미 있어야 합니다. 이 워크플로는 풀을 만들지 않습니다.

4. **검토 + 만들기** 를 클릭하고, 정보를 검토한 다음, **만들기** 를 클릭합니다.
5. 보조 데이터베이스가 생성되고 배포 프로세스가 시작됩니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="보조 데이터베이스의 배포 상태를 보여주는 스크린샷입니다.":::

6. 배포가 완료되면 보조 데이터베이스에 해당 상태가 표시됩니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="배포 후 보조 데이터베이스 상태를 보여주는 스크린샷입니다.":::

7. 주 데이터베이스 페이지로 돌아간 후 **복제본** 을 선택합니다. 보조 데이터베이스가 **지역 복제본** 아래에 나열됩니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="SQL 데이터베이스 주 및 지역 복제본을 보여주는 스크린샷입니다.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

지역 복제에 대해 설정하려는 데이터베이스를 선택합니다. 다음 정보가 필요합니다.
- 원래 Azure SQL 데이터베이스 이름
- Azure SQL Server 이름
- 리소스 그룹 이름
- 새 복제본을 만들 서버의 이름

> [!NOTE]
> 보조 데이터베이스는 서비스 계층이 주 데이터베이스와 동일해야 합니다.

보조 서버에 대해 지역을 선택할 수 있지만 [쌍을 이루는 지역](../../availability-zones/cross-region-replication-azure.md)이 권장됩니다.

[az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create) 명령을 실행합니다.

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

필요에 따라 탄력적 풀에 보조 데이터베이스를 추가할 수 있습니다. 풀에서 보조 데이터베이스를 만들려면 `--elastic-pool` 매개 변수를 사용합니다. 대상 서버에 풀이 이미 있어야 합니다. 이 워크플로는 풀을 만들지 않습니다.

보조 데이터베이스가 생성되고 배포 프로세스가 시작됩니다.

배포가 완료되면 [az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links) 명령을 실행하여 보조 데이터베이스의 상태를 확인할 수 있습니다.
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>장애 조치(failover) 시작

보조 데이터베이스가 주 데이터베이스가 되도록 전환할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)  

1. [Azure Portal](https://portal.azure.com)에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. **데이터 관리** 로 스크롤한 후 **복제본** 을 선택합니다.
3. **지역 복제본** 목록에서 새 주 데이터베이스로 지정할 데이터베이스를 선택하고, 줄임표를 선택한 후 **강제 장애 조치(failover)** 를 선택합니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="드롭다운에서 강제 장애 조치(failover) 선택을 보여주는 스크린샷입니다.":::
4. **예** 를 선택하여 장애 조치(failover)를 시작합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary) 명령을 실행합니다.

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

이 명령은 보조 데이터베이스를 주 역할로 즉시 전환합니다. 이 프로세스는 일반적으로 30초 이내로 완료됩니다.

역할이 전환되는 동안 두 데이터베이스를 모두 사용할 수 없는, 0-25초의 정도의 짧은 기간이 있습니다. 주 데이터베이스에 여러 개의 보조 데이터베이스가 있는 경우 이 명령을 사용하면 새로운 주 데이터베이스에 연결할 다른 보조 데이터베이스가 자동으로 다시 구성됩니다. 전체 작업은 정상적인 상황에서 완료하는데 1분 미만이 걸려야 합니다.

> [!NOTE]
> 이 명령은 가동 중지 시에 데이터베이스의 빠른 복구를 위해 설계되었습니다. 데이터 동기화 없이 장애 조치(failover) 또는 강제 장애 조치(failover)를 트리거합니다.  주 데이터베이스가 온라인이고 명령이 실행될 때 트랜잭션을 커밋하면 일부 데이터가 손실될 수 있습니다.

## <a name="remove-secondary-database"></a>보조 데이터베이스 제거

이 작업은 보조 데이터베이스에 대한 복제를 영구적으로 중지하고 보조의 역할을 일반적인 읽기-쓰기 데이터베이스로 변경합니다. 보조 데이터베이스에 대한 연결이 끊어진 경우 명령이 성공하지만 연결이 복원된 후에야 보조는 읽기-쓰기가 수행됩니다.

# <a name="portal"></a>[포털](#tab/portal)  

1. [Azure Portal](https://portal.azure.com)에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. **복제본** 을 선택합니다.
3. **지역 복제본** 목록에서 지역 복제본 파트너 관계에서 제거하려는 데이터베이스를 선택하고, 줄임표를 선택한 후 **복제 중지** 를 선택합니다.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="드롭다운에서 복제 중지 선택을 보여주는 스크린샷입니다.":::
5. 확인 창이 열립니다. 지역에서 복제 파트너 관계에서 데이터베이스를 제거하려면 **예** 를 클릭합니다. (복제에 포함되지 않는 읽기-쓰기 데이터베이스로 설정)
 
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link) 명령을 실행합니다.

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

작업을 수행할지 여부를 확인합니다.

---

## <a name="next-steps"></a>다음 단계

* 활성 지역 복제에 대한 자세한 내용은 [활성 지역 복제](active-geo-replication-overview.md)를 참조하세요.
* 자동 장애 조치(failover) 그룹에 대해 알아보려면 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 참조하세요.
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조하세요.