---
title: 서버 트러스트 그룹
titleSuffix: Azure SQL Managed Instance
description: 서버 신뢰 그룹 및 Azure SQL Managed Instance 간의 신뢰를 관리하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 10/08/2020
ms.openlocfilehash: 4a3e96c33a0bef8e533b4f7f563c701a2c0a8044
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042204"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>서버 신뢰 그룹을 사용하여 SQL Managed Instance 간 신뢰 설정 및 관리
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서버 신뢰 그룹은 Azure SQL Managed Instance 간의 신뢰를 관리하는 데 사용되는 개념입니다. 그룹을 만들면 해당 구성원 간에 인증서 기반 신뢰가 설정됩니다. 이 신뢰는 여러 인스턴스 간 시나리오에 사용할 수 있습니다. 그룹에서 서버를 제거하거나 그룹을 삭제하면 서버 간의 신뢰가 제거됩니다. 서버 신뢰 그룹을 만들거나 삭제하려면 사용자에게 Managed Instance에 대한 쓰기 권한이 있어야 합니다.
[서버 신뢰 그룹](/azure/templates/microsoft.sql/allversions)은 Azure Portal에서 **SQL 신뢰 그룹** 으로 레이블이 지정된 Azure Resource Manager 개체입니다.

> [!NOTE]
> 서버 신뢰 그룹은 Azure SQL Managed Instance 간의 분산 트랜잭션 퍼블릭 미리 보기에 도입되었으며 현재 이 문서의 뒷부분에서 설명할 몇 가지 제한 사항이 있습니다.

## <a name="server-trust-group-setup"></a>서버 신뢰 그룹 설정

서버 신뢰 그룹은 [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup)이나 [Azure CLI](/cli/azure/sql/stg)를 통해 설정할 수 있습니다. 다음 섹션에서는 Azure Portal을 사용하여 서버 신뢰 그룹을 설정하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. 새로 만든 서버 신뢰 그룹에 추가하려는 Azure SQL Managed Instance로 이동합니다.

3. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="서버 트러스트 그룹":::

4. 서버 신뢰 그룹 구성 페이지에서 **새 그룹** 아이콘을 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="새 그룹":::

5. **SQL 신뢰 그룹** 생성 블레이드에서 **그룹 이름** 을 설정합니다. 그룹 이름은 그룹 구성원이 상주하는 모든 지역에서 고유해야 합니다. **신뢰 범위** 는 서버 신뢰 그룹에서 사용된 인스턴스 간 시나리오의 형식을 정의합니다. 미리 보기에서 유일하게 적용 가능한 신뢰 범위는 **분산 트랜잭션** 이므로 미리 선택되어 있으며 변경할 수 없습니다. 모든 **그룹 구성원** 은 동일한 **구독** 에 속해 있어야 하지만, 다른 리소스 그룹에 속해 있을 수 있습니다. **리소스 그룹** 및 **SQL Server/인스턴스** 를 선택하여 그룹의 구성원이 될 Azure SQL Managed Instance를 선택합니다.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="서버 신뢰 그룹 생성 블레이드":::

6. 모든 필수 필드를 채운 후 **저장** 을 클릭합니다.

## <a name="server-trust-group-maintenance-and-deletion"></a>서버 신뢰 그룹 유지 관리 및 삭제

서버 신뢰 그룹은 편집할 수 없습니다. 그룹에서 Managed Instance를 제거하려면 그룹을 삭제하고 새 그룹을 만들어야 합니다.

다음 섹션에서는 서버 신뢰 그룹 삭제 프로세스에 대해 설명합니다. 
1. Azure Portal로 이동합니다.
2. 신뢰 그룹에 속하는 Managed Instance로 이동합니다.
3. **보안** 설정에서 **SQL 신뢰 그룹** 탭을 선택합니다.
4. 삭제하려는 신뢰 그룹을 선택합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="서버 신뢰 그룹 선택":::
5. **그룹 삭제** 를 클릭합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="서버 신뢰 그룹 삭제":::
6. 삭제를 확인하려면 서버 신뢰 그룹 이름을 입력하고 **삭제** 를 클릭합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="서버 신뢰 그룹 삭제 확인":::

> [!NOTE]
> 서버 신뢰 그룹을 삭제해도 두 Managed Instance 간의 신뢰가 즉시 제거되지 않을 수 있습니다. Managed Instance의 [장애 조치(failover)](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover)를 호출하여 신뢰 제거를 적용할 수 있습니다. 이 문제에 대한 최신 업데이트는 [알려진 문제](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues)를 확인하시기 바랍니다.

## <a name="limitations"></a>제한 사항

퍼블릭 미리 보기 중에는 서버 신뢰 그룹에 다음과 같은 제한 사항이 적용됩니다.
 * 서버 신뢰 그룹의 이름은 해당 구성원이 있는 모든 지역에서 고유해야 합니다.
 * 그룹은 Azure SQL Managed Instance만 포함할 수 있으며 동일한 Azure 구독에 있어야 합니다.
 * 미리 보기에서 그룹에는 정확히 두 개의 Managed Instance가 있을 수 있습니다. 두 개 이상의 Managed Instance에서 분산 트랜잭션을 실행하려면 Managed Instance의 각 쌍에 대해 서버 신뢰 그룹을 만들어야 합니다.
 * 분산 트랜잭션은 서버 신뢰 그룹에 적용되는 유일한 범위입니다.
 * 서버 신뢰 그룹은 Azure Portal에서만 관리할 수 있습니다. PowerShell 및 CLI 지원은 추후에 제공됩니다.
 * Azure Portal에서 서버 신뢰 그룹을 편집할 수 없습니다. 서버 신뢰 그룹은 만들거나 삭제할 수만 있습니다.
 * 분산 트랜잭션의 추가 제한은 시나리오와 관련이 있을 수 있습니다. 가장 주의할 점은 VNET 또는 VNET 피어링을 사용해 프라이빗 엔드포인트를 통해 Managed Instance 간에 연결되어야 한다는 것입니다. [Managed Instance에 대한 분산 트랜잭션 제한 사항](../database/elastic-transactions-overview.md#limitations)에 대한 최신 내용을 알고 있는지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure SQL Managed Instance의 분산 트랜잭션에 대한 자세한 내용은 [분산 트랜잭션](../database/elastic-transactions-overview.md)을 참조하세요.
* 릴리스 업데이트 및 알려진 문제 상태는 새로운 것을 [참조하세요.](doc-changes-updates-release-notes-whats-new.md)
* 기능 요청이 있는 경우 [Managed Instance 포럼](https://feedback.azure.com/forums/915676-sql-managed-instance)에 추가하세요.
