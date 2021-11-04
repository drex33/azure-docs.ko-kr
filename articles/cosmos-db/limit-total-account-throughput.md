---
title: Azure Cosmos DB 계정에 프로비전된 총 처리량 제한
description: Azure Cosmos DB 계정에 프로비전된 총 처리량을 제한하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1423d4444c7ec53e40f77d951acb58a63538cfbf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557797"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 프로비전된 총 처리량 제한
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[프로비전된 처리량](./set-throughput.md) 모드에서 Azure Cosmos DB 계정을 사용하는 경우 대부분의 비용은 일반적으로 계정 전체에서 프로비전한 처리량에서 비롯됩니다. 특히 이러한 비용은 다음의 직접적인 영향을 받습니다.

- 공유 처리량이 있는 데이터베이스의 수입니다.
- 전용 처리량이 있는 컨테이너의 수입니다.
- 이러한 각 리소스에 프로비전된 처리량입니다.
- 계정을 사용할 수 있는 지역 수입니다.

특히 Azure Cosmos DB를 시작할 때 계정 전체에서 프로비전한 총 처리량을 추적하는 것이 어려울 수 있습니다. 이로 인해 이 금액이 초과하지 않을 것으로 예상되는 특정 예산을 초과할 때 예기치 않은 요금이 발생할 수 있습니다. 비용을 더 잘 제어할 수 있도록 Azure Cosmos DB를 사용하면 계정에 프로비전된 총 처리량을 제한할 수 있습니다.

> [!NOTE]
> 이 기능은 [서버리스](./serverless.md) 계정에서 사용할 수 없습니다.

계정의 총 처리량에 대한 제한을 설정한 후 이 제한을 초과하는 다음 작업 중 하나는 차단되고 명시적으로 실패합니다.

- 공유 처리량을 가진 새 데이터베이스 만들기
- 전용 처리량으로 새 컨테이너 만들기
- 표준(수동) 모드로 구성된 리소스에서 프로비전된 처리량 증가
- 자동 크기 조정 모드로 구성된 리소스에서 프로비전된 최대 처리량 증가
- 계정에 새 지역 추가

> [!NOTE]
> 자동 크기 조정 모드로 구성된 리소스의 경우 계정의 총 처리량에 계산되는 리소스에 구성된 최대 처리량입니다.

> [!IMPORTANT]
> 계정에서 총 처리량 제한을 사용하도록 설정한 후에는 새 컨테이너를 만들 때 명시적 처리량 값을 전달해야 합니다. 명시적 처리량 없이 컨테이너를 만들려고 하면 현재 오류가 발생합니다.

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Azure Portal 총 처리량 제한을 설정합니다.

### <a name="new-account"></a>새 계정

포털에서 새 Azure Cosmos DB 계정을 만들 때 계정의 총 처리량을 제한하는 옵션이 있습니다.

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="새 계정을 만들 때 총 계정 처리량을 제한하는 방법을 보여주는 Azure Portal 스크린샷" border="true":::

이 옵션을 선택하면 계정의 총 처리량이 4,000 RU/s로 제한됩니다. 계정을 만든 후에 이 값을 변경할 수 있습니다.

### <a name="existing-account"></a>기존 계정

Azure Portal Azure Cosmos DB 계정으로 이동하고 왼쪽 메뉴에서 **비용 관리를** 선택합니다.

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="기존 계정의 총 계정 처리량을 업데이트하는 방법을 보여주는 Azure Portal 스크린샷" border="true":::

이 섹션에서는 계정에 프로비전된 총 처리량의 요약을 보여 줍니다. 그러면 총 처리량 제한을 구성할 수 있습니다. 다음 세 가지 옵션을 사용할 수 있습니다.

- **계정의 프로비전된 총 처리량을 무료 계층 할인 에 포함된 양으로 제한합니다.** 이 옵션은 무료 계층 계정에서만 사용할 수 있으며 계정의 총 처리량을 1,000 RU/s로 제한합니다. 이 옵션을 선택하면 프로비전된 처리량에 대한 요금이 발생하지 않습니다.
- **계정의 총 처리량을 사용자 지정 금액까지 프로비전할 수** 있습니다. 이 옵션을 사용하면 초과하지 않으려는 프로비전된 총 처리량을 입력할 수 있습니다. 입력에 해당하는 월별 예상 비용이 참조로 표시됩니다.
  > [!NOTE]
  > 이 사용자 지정 제한은 계정 전체에서 현재 프로비전된 총 처리량보다 낮을 수 없습니다.
- **제한 없음, 계정의 총 처리량을 모든 양으로 프로비전할 수 있도록 허용합니다.** 이 옵션은 제한을 사용하지 않도록 설정합니다.

## <a name="set-the-total-throughput-limit-programmatically"></a>프로그래밍 방식으로 총 처리량 제한 설정

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager Azure Cosmos DB 계정을 만들거나 업데이트할 때 속성을 설정하여 총 처리량 제한을 구성할 수 있습니다. `properties.capacity.totalThroughputLimit`

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

제한을 사용하지 않으려면 이 속성을 `-1` 로 설정합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="are-there-situations-where-the-total-provisioned-throughput-can-exceed-the-limit"></a>프로비전된 총 처리량이 제한을 초과할 수 있는 상황이 있나요?

Azure Cosmos DB는 저장된 데이터 GB당 최소 처리량 10 RU/s를 적용합니다. 이미 최소인 동안 데이터를 수집 중인 경우 리소스에 프로비전된 처리량이 자동으로 증가하여 GB당 10 RU/s를 사용합니다. 이 경우 및 이 경우에만 프로비전된 총 처리량이 설정한 제한을 초과할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB에서 [비용 계획 및 관리를 시작합니다.](./plan-manage-costs.md)
- [프로비전된 처리량](./set-throughput.md)에 대해 자세히 알아봅니다.
- [프로비전된 처리량 비용을 최적화하는](./optimize-cost-throughput.md)방법을 알아보세요.
