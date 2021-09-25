---
title: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
description: Azure portal, PowerShell, CLI, Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 리소스를 관리하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: d6cf0b9ba4fe856a153abf004a81c250c59b2aa1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618593"
---
# <a name="manage-an-azure-cosmos-account-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Cosmos 계정 관리

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 Azure Portal를 사용 하 여 Azure Cosmos 계정에서 다양 한 작업을 관리 하는 방법을 설명 합니다. 

> [!TIP]
> [Azure PowerShell](manage-with-powershell.md), [Azure CLI](sql/manage-with-cli.md), [Azure Resource Manager 템플릿](./manage-with-templates.md)및 [Bicep](sql/manage-with-bicep.md)을 포함 한 다른 Azure 관리 클라이언트를 사용 하 여 Azure Cosmos DB를 관리할 수도 있습니다.

## <a name="create-an-account"></a>계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

## <a name="addremove-regions-from-your-database-account"></a>데이터베이스 계정에서 Azure 지역 추가/제거

> [!TIP]
> 새 지역이 추가되면 해당 지역이 사용 가능으로 표시되기 전에 모든 데이터를 완전히 복제하고 새 지역에 커밋해야 합니다. 이 작업에 걸리는 시간은 해당 계정 내에 저장된 데이터 양에 따라 달라집니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

1. 지역을 추가하려면 지도에서 원하는 지역에 해당하는 **+** 레이블이 있는 육각형을 선택합니다. 또는, 지역을 추가하려면 **+ 지역 추가** 옵션을 선택하고 드롭다운 메뉴에서 Azure 지역을 선택합니다.

1. 지역을 제거하려면 확인 표시가 있는 파란색 육각형을 선택하여 맵에서 하나 이상의 지역을 지웁니다. 또는 오른쪽의 지역 옆에 있는 "휴지통"(🗑) 아이콘을 선택합니다.

1. 변경 내용을 저장하려면 **확인** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="지역 추가 또는 제거 메뉴":::

단일 지역 쓰기 모드에서는 쓰기 지역을 제거할 수 없습니다. 현재 쓰기 지역을 삭제하려면 먼저 다른 Azure 지역으로 장애 조치해야 합니다.

다중 지역 쓰기 모드에서는 하나 이상의 Azure 지역이 있으면 Azure 지역을 추가하거나 제거할 수 있습니다.

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>다중 쓰기 지역 구성

**전역적으로 데이터 복제** 탭을 열고 **사용** 을 선택하여 다중 지역 쓰기를 사용하도록 설정합니다. 다중 지역 쓰기를 사용하도록 설정한 후에는 현재 계정에 있는 모든 읽기 지역이 읽기 및 쓰기 지역이 됩니다.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="다중 지역 쓰기를 구성하는 Azure Cosmos 계정 스크린샷":::

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Azure Cosmos 계정에 자동 장애 조치(failover) 사용

자동 장애 조치(failover) 옵션을 통해 Azure Cosmos DB는 장애 조치(failover) 우선 순위가 가장 높은 지역으로 장애 조치(failover)할 수 있으며, 지역을 사용할 수 없는 경우 사용자 작업이 필요하지 않습니다. 자동 장애 조치(failover)를 사용하도록 설정한 경우 지역 우선 순위를 수정할 수 있습니다. 계정에는 자동 장애 조치(failover)를 사용하도록 설정하기 위해 두 개 이상의 지역이 있어야 합니다.

1. Azure Cosmos 계정에서 **전역적으로 데이터 복제** 창을 엽니다.

2. 창의 위쪽에서 **자동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용** 을 **켜기** 로 설정합니다. 

4. **저장** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="자동 장애 조치(failover) 포털 메뉴":::

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos 계정의 장애 조치(failover) 우선 순위 설정

Cosmos 계정이 자동 장애 조치(failover)에 대해 구성된 후에는 지역에 대한 장애 조치(failover) 우선 순위를 변경할 수 있습니다.

> [!IMPORTANT]
> 계정이 자동 장애 조치(failover)에 대해 구성된 경우에는 쓰기 지역(장애 조치 우선 순위 0)을 수정할 수 없습니다. 쓰기 지역을 변경하려면 자동 장애 조치(failover)를 사용하지 않도록 설정하고 수동 장애 조치(failover)를 수행해야 합니다.

1. Azure Cosmos 계정에서 **전역적으로 데이터 복제** 창을 엽니다.

2. 창의 위쪽에서 **자동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용** 을 **켜기** 로 설정합니다.

4. 장애 조치 우선 순위를 수정하려면 마우스로 가리킬 때 나타나는 행 왼쪽의 점 세 개를 통해 읽기 지역을 끕니다.

5. **저장** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="자동 장애 조치(failover) 포털 메뉴":::

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Azure Cosmos 계정에서 수동 장애 조치(failover) 수행

> [!IMPORTANT]
> 이 작업이 성공하기 위해서는 수동 장애 조치(failover)에 대해 Azure Cosmos 계정을 구성해야 합니다.

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

2. 메뉴의 위쪽에서 **수동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **수동 장애 조치** 메뉴에서 새 쓰기 지역을 선택합니다. 이 옵션이 쓰기 영역을 변경한다는 것을 알고 있음을 나타내는 확인란을 선택합니다.

4. 장애 조치를 트리거하려면 **확인** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="수동 장애 조치(failover) 포털 메뉴":::

## <a name="next-steps"></a>다음 단계

데이터베이스 및 컨테이너와 함께 Azure Cosmos 계정을 관리하는 방법에 대한 자세한 내용 및 예제는 다음 문서를 읽어보세요.

* [Azure PowerShell을 사용하여 Azure Cosmos DB 관리](manage-with-powershell.md)
* [Azure CLI를 사용하여 Azure Cosmos DB 관리](sql/manage-with-cli.md)
* [Azure 리소스 관리 템플릿을 사용 하 여 Azure Cosmos DB 관리](./manage-with-templates.md)
* [Bicep를 사용 하 여 Azure Cosmos DB 관리](sql/manage-with-bicep.md)
