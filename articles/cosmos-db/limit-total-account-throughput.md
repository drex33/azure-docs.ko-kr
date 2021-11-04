---
title: Azure Cosmos DB 계정에 프로 비전 된 총 처리량 제한
description: Azure Cosmos DB 계정에서 프로 비전 되는 총 처리량을 제한 하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce771dd964fe75bbe8ce6a74cf2aa6c190605af9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454450"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 프로 비전 된 총 처리량 제한
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[프로 비전 된 처리량](./set-throughput.md) 모드에서 Azure Cosmos DB 계정을 사용 하는 경우 대부분의 비용은 일반적으로 계정에서 프로 비전 한 처리량의 양에 따라 제공 됩니다. 특히 이러한 비용은 다음에 의해 직접적인 영향을 받습니다.

- 공유 처리량이 있는 데이터베이스의 수입니다.
- 전용 처리량이 있는 컨테이너의 수입니다.
- 이러한 각 리소스에 대해 프로 비전 된 처리량의 양입니다.
- 계정을 사용할 수 있는 지역 수입니다.

특히 Azure Cosmos DB를 시작 하는 경우 계정에서 프로 비전 한 총 처리량을 추적 하는 것이 어려울 수 있습니다. 이로 인해 초과 될 것으로 예상 하지 않은 특정 예산으로 인해 예기치 않은 요금이 발생할 수 있습니다. 비용을 보다 효율적으로 제어할 수 있도록 Azure Cosmos DB 계정에서 프로 비전 된 총 처리량을 제한할 수 있습니다.

> [!NOTE]
> [서버](./serverless.md) 리스 계정에서는이 기능을 사용할 수 없습니다.

계정 총 처리량에 대 한 제한을 설정 하면이 한도를 초과 하는 다음 작업은 차단 되 고 명시적으로 실패 합니다.

- 공유 처리량을 사용 하 여 새 데이터베이스 만들기
- 전용 처리량을 사용 하 여 새 컨테이너 만들기
- 표준 (수동) 모드로 구성 된 리소스에서 프로 비전 된 처리량을 늘립니다.
- 자동 크기 조정 모드에서 구성 된 리소스에서 프로 비전 된 최대 처리량을 늘립니다.
- 계정에 새 영역을 추가 합니다.

> [!NOTE]
> 자동 크기 조정 모드에서 구성 된 리소스의 경우 리소스에 구성 된 최대 처리량으로, 계정의 총 처리량을 계산 합니다.

> [!IMPORTANT]
> 계정에 총 처리량 제한을 설정한 후에는 새 컨테이너를 만들 때 명시적 처리량 값을 전달 해야 합니다. 명시적 처리량을 사용 하지 않고 컨테이너를 만들려고 하면 현재 오류가 발생 합니다.

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Azure Portal 총 처리량 한도를 설정 합니다.

### <a name="new-account"></a>새 계정

포털에서 새 Azure Cosmos DB 계정을 만들 때 계정의 총 처리량을 제한할 수 있는 옵션이 있습니다.

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="새 계정을 만들 때 총 계정 처리량을 제한 하는 방법을 보여 주는 Azure Portal의 스크린샷" border="true":::

이 옵션을 선택 하면 계정의 총 처리량이 4000 r u/초로 제한 됩니다. 계정을 만든 후이 값을 변경할 수 있습니다.

### <a name="existing-account"></a>기존 계정

Azure Portal에서 Azure Cosmos DB 계정으로 이동 하 고 왼쪽 메뉴에서 **비용 관리** 를 선택 합니다.

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="기존 계정에서 총 계정 처리량을 업데이트 하는 방법을 보여 주는 Azure Portal의 스크린샷" border="true":::

이 섹션에서는 계정에 프로 비전 된 총 처리량을 요약 하 고 총 처리량 제한을 구성할 수 있습니다. 다음 세 가지 옵션을 사용할 수 있습니다.

- **계정의 총 프로 비전 된 처리량을 무료 계층 할인에 포함 된 용량으로 제한** 합니다. 이 옵션은 무료 계층 계정에만 사용할 수 있으며 계정의 총 처리량은 1000/s로 제한 됩니다. 이 옵션을 선택 하는 경우 프로 비전 된 처리량에 대 한 요금이 발생 하지 않도록 합니다.
- **계정의 총 처리량을 사용자 지정 양만큼 프로 비전** 할 수 있습니다. 이 옵션을 사용 하 여 초과 하지 않을 프로 비전 된 총 처리량을 입력할 수 있습니다. 입력에 해당 하는 월별 예상 비용은 참조로 표시 됩니다.
  > [!NOTE]
  > 이 사용자 지정 제한은 계정에서 현재 프로 비전 된 총 처리량 보다 작을 수 없습니다.
- **제한이 없으면 계정의 총 처리량을 모든 양만큼 프로 비전** 할 수 있습니다. 이 옵션은 제한을 사용 하지 않도록 설정 합니다.

## <a name="set-the-total-throughput-limit-programmatically"></a>프로그래밍 방식으로 총 처리량 제한 설정

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager를 사용 하 여 Azure Cosmos DB 계정을 만들거나 업데이트할 때 속성을 설정 하 여 총 처리량 제한을 구성할 수 있습니다 `properties.capacity.totalThroughputLimit` .

```json
{
  "location": "West US",
  "kind": "DocumentDB",
  "properties": {
    "locations": [
      {
        "locationName": "West US",
        "failoverPriority": 0,
        "isZoneRedundant": false
      }
    ],
    "databaseAccountOfferType": "Standard",
    "capacity": {
        "totalThroughputLimit": 2000
    }
  }
}
```

제한을 사용 하지 않으려면이 속성을로 설정 `-1` 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB에 대 한 [비용 계획 및 관리](./plan-manage-costs.md) 를 시작 하세요.
- [프로 비전 된 처리량](./set-throughput.md)에 대해 자세히 알아보세요.
- [프로 비전 된 처리량 비용을 최적화](./optimize-cost-throughput.md)하는 방법을 알아보세요.
