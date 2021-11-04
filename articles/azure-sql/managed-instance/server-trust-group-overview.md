---
title: 서버 신뢰 그룹
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 서버 트러스트 그룹을 사용하여 인스턴스 간의 트러스트를 관리하는 방법을 알아봅니다.
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
ms.openlocfilehash: 857af87fac233ff15bad924746c87e0856f90b1f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431050"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>서버 신뢰 그룹을 사용하여 인스턴스 간 트러스트 설정(Azure SQL Managed Instance)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서버 신뢰 그룹(SQL 트러스트 그룹이라고도 함)은 Azure SQL Managed Instance 인스턴스 간의 트러스트를 관리하는 데 사용되는 개념입니다. 그룹을 만들면 해당 구성원 간에 인증서 기반 신뢰가 설정됩니다. 이 신뢰는 여러 인스턴스 간 시나리오에 사용할 수 있습니다. 그룹에서 서버를 제거하거나 그룹을 삭제하면 서버 간의 신뢰가 제거됩니다. 서버 신뢰 그룹을 만들거나 삭제하려면 사용자에게 관리되는 인스턴스에 대한 쓰기 권한이 있어야 합니다.
[서버 신뢰 그룹은](/azure/templates/microsoft.sql/allversions) Azure Portal SQL **트러스트 그룹으로** 레이블이 지정되어 있는 Azure Resource Manager 개체입니다.


## <a name="set-up-group"></a>그룹 설정

서버 신뢰 그룹은 Azure PowerShell [또는](/cli/azure/sql/stg) [Azure CLI](/powershell/module/az.sql/new-azsqlservertrustgroup) 통해 설정할 수 있습니다. 

Azure Portal 사용하여 서버 트러스트 그룹을 만들려면 다음 단계를 수행합니다. 

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

2. 서버 신뢰 그룹에 추가하려는 Azure SQL Managed Instance 이동합니다.

3. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="트러스트 그룹 SQL":::

4. 트러스트 **그룹 구성 SQL** 페이지에서 **새 그룹** 아이콘을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="새 그룹":::

5. **SQL 신뢰 그룹** 생성 블레이드에서 **그룹 이름** 을 설정합니다. 그룹의 구독, 리소스 그룹 및 지역에서 고유해야 합니다. **신뢰 범위는** 서버 트러스트 그룹에서 사용하도록 설정된 인스턴스 간 시나리오의 유형을 정의합니다. 신뢰 범위가 고정되어 있습니다. 사용 가능한 모든 기능이 미리 선택되어 있으며 이 기능은 변경할 수 없습니다. **구독** 및 **리소스 그룹을** 선택하여 그룹의 구성원이 될 관리되는 인스턴스를 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="SQL 트러스트 그룹 만들기 블레이드":::

6. 모든 필수 필드를 채우면 **저장을** 선택합니다.

## <a name="edit-group"></a>그룹 편집 

서버 신뢰 그룹을 편집하려면 다음 단계를 수행합니다. 

1. Azure 포털로 이동합니다.
1. 트러스트 그룹에 속한 관리되는 인스턴스로 이동합니다.
1. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.
1. 편집하려는 신뢰 그룹을 선택합니다.
1. **그룹 구성을** 클릭합니다.

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="SQL 신뢰 그룹 구성":::

1. 그룹에서 관리되는 인스턴스를 추가하거나 제거합니다.
1. **저장을** 클릭하여 선택을 확인하거나 **취소를** 클릭하여 변경 내용을 중단합니다.

## <a name="delete-group"></a>그룹 삭제

서버 신뢰 그룹을 삭제하려면 다음 단계를 수행합니다. 

1. Azure Portal로 이동합니다.
1. SQL 트러스트 그룹에 속한 관리되는 인스턴스로 이동합니다.
1. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.
1. 삭제하려는 신뢰 그룹을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="SQL 신뢰 그룹 선택":::

1. **그룹 삭제를** 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="SQL 신뢰 그룹 삭제"::: 

1. SQL 트러스트 그룹 이름을 입력하여 삭제를 확인하고 **삭제를** 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="SQL 트러스트 그룹 삭제 확인":::

> [!NOTE]
> SQL 트러스트 그룹을 삭제해도 두 관리되는 인스턴스 간의 트러스트가 즉시 제거되지 않을 수 있습니다. 관리되는 인스턴스의 [장애 조치(failover)를](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) 호출하여 트러스트 제거를 적용할 수 있습니다. 이 문제에 대한 최신 업데이트는 [알려진 문제](../managed-instance/doc-changes-updates-known-issues.md)를 확인하시기 바랍니다.

## <a name="limitations"></a>제한 사항

서버 신뢰 그룹에는 다음과 같은 제한 사항을 적용합니다. 

 * 그룹에는 Azure SQL Managed Instance 인스턴스만 포함될 수 있습니다.
 * 그룹을 만들거나 수정할 때는 트러스트 범위를 변경할 수 없습니다.
 * 서버 신뢰 그룹의 이름은 구독, 리소스 그룹 및 지역에 대해 고유해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure SQL Managed Instance의 분산 트랜잭션에 대한 자세한 내용은 [분산 트랜잭션](../database/elastic-transactions-overview.md)을 참조하세요.
* 릴리스 업데이트 및 알려진 문제 상태는 새로운 것을 [참조하세요.](doc-changes-updates-release-notes-whats-new.md)
* 기능 요청이 있는 경우 [Managed Instance 포럼](https://feedback.azure.com/d365community/forum/a99f7006-3425-ec11-b6e6-000d3a4f0f84)에 추가하세요.
