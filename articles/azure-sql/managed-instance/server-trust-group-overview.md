---
title: 서버 신뢰 그룹
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance에서 서버 신뢰 그룹을 사용 하 여 인스턴스 간 트러스트를 관리 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ad3d7c45fb621d3f12146548bdb9e5ac5693a622
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072025"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>서버 신뢰 그룹 (Azure SQL Managed Instance)을 사용 하 여 인스턴스 간 트러스트 설정
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서버 신뢰 그룹 (SQL 신뢰 그룹이 라고도 함)은 Azure SQL Managed Instance에서 인스턴스 간의 신뢰를 관리 하는 데 사용 되는 개념입니다. 그룹을 만들면 해당 구성원 간에 인증서 기반 신뢰가 설정됩니다. 이 신뢰는 여러 인스턴스 간 시나리오에 사용할 수 있습니다. 그룹에서 서버를 제거하거나 그룹을 삭제하면 서버 간의 신뢰가 제거됩니다. 서버 신뢰 그룹을 만들거나 삭제 하려면 사용자에 게 관리 되는 인스턴스에 대 한 쓰기 권한이 있어야 합니다.
[서버 트러스트 그룹](/azure/templates/microsoft.sql/allversions) 은 Azure Portal에서 **SQL 신뢰 그룹** 으로 레이블이 지정 된 Azure Resource Manager 개체입니다.


## <a name="set-up-group"></a>그룹 설정

서버 신뢰 그룹은 [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup) 또는 [Azure CLI](/cli/azure/sql/stg)를 통해 설정할 수 있습니다. 

Azure Portal를 사용 하 여 서버 신뢰 그룹을 만들려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. 서버 신뢰 그룹에 추가 하려는 Azure SQL Managed Instance로 이동 합니다.

3. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="신뢰 그룹 SQL":::

4. **SQL 신뢰 그룹** 구성 페이지에서 **새 그룹** 아이콘을 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="새 그룹":::

5. **SQL 신뢰 그룹** 생성 블레이드에서 **그룹 이름** 을 설정합니다. 그룹의 구독, 리소스 그룹 및 지역에서 고유 해야 합니다. **신뢰 범위** 는 서버 신뢰 그룹에서 사용 하도록 설정 된 인스턴스 간 시나리오의 유형을 정의 합니다. 신뢰 범위가 고정 되어 있습니다. 사용할 수 있는 모든 기능이 미리 선택 되어 있으며 변경할 수 없습니다. **구독** 및 **리소스 그룹** 을 선택 하 여 그룹의 구성원이 될 관리 되는 인스턴스를 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="SQL 신뢰 그룹 만들기 블레이드":::

6. 모든 필수 필드를 채운 후 **저장** 을 선택 합니다.

## <a name="edit-group"></a>그룹 편집 

서버 신뢰 그룹을 편집 하려면 다음 단계를 수행 합니다. 

1. Azure 포털로 이동합니다.
1. 신뢰 그룹에 속하는 관리 되는 인스턴스로 이동 합니다.
1. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.
1. 편집 하려는 신뢰 그룹을 선택 합니다.
1. **그룹 구성** 을 클릭 합니다.

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="SQL 신뢰 그룹 구성":::

1. 그룹에서 관리 되는 인스턴스를 추가 하거나 제거 합니다.
1. **저장** 을 클릭 하 여 선택 항목을 확인 하거나 취소를 클릭 하 여 변경 내용을 **취소** 합니다.

## <a name="delete-group"></a>그룹 삭제

서버 신뢰 그룹을 삭제 하려면 다음 단계를 수행 합니다. 

1. Azure Portal로 이동합니다.
1. SQL 신뢰 그룹에 속하는 관리 되는 인스턴스로 이동 합니다.
1. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.
1. 삭제하려는 신뢰 그룹을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="SQL 신뢰 그룹 선택":::

1. **그룹 삭제** 를 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="SQL 신뢰 그룹 삭제"::: 

1. SQL 신뢰 그룹 이름을 입력 하 여 삭제를 확인 하 고 **삭제** 를 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="신뢰 그룹 삭제 SQL 확인":::

> [!NOTE]
> SQL 신뢰 그룹을 삭제 하면 두 관리 되는 인스턴스 간의 트러스트가 즉시 제거 되지 않을 수도 있습니다. 관리 되는 인스턴스의 [장애 조치 (failover)](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) 를 호출 하 여 트러스트 제거를 적용할 수 있습니다. 이 문제에 대한 최신 업데이트는 [알려진 문제](../managed-instance/doc-changes-updates-known-issues.md)를 확인하시기 바랍니다.

## <a name="limitations"></a>제한 사항

서버 신뢰 그룹에는 다음과 같은 제한 사항이 적용 됩니다. 

 * 그룹에는 Azure SQL Managed Instance 인스턴스만 포함 될 수 있습니다.
 * 그룹을 만들거나 수정할 때는 트러스트 범위를 변경할 수 없습니다.
 * 서버 신뢰 그룹의 이름은 구독, 리소스 그룹 및 지역에 대해 고유 해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure SQL Managed Instance의 분산 트랜잭션에 대한 자세한 내용은 [분산 트랜잭션](../database/elastic-transactions-overview.md)을 참조하세요.
* 릴리스 업데이트 및 알려진 문제 상태는 [새로운 기능](doc-changes-updates-release-notes-whats-new.md)을 참조 하세요.
* 기능 요청이 있는 경우 [SQL Managed Instance 포럼](https://feedback.azure.com/forums/915676-sql-managed-instance)에 추가 합니다.
