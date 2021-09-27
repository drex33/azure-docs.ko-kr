---
title: 키 업데이트 및 키 다시 생성에 대 한 Azure Cosmos DB 계정 모니터링
description: 계정 키 업데이트 된 메트릭을 사용 하 여 키 업데이트에 대 한 계정을 모니터링 합니다. 이 메트릭은 계정에 대해 기본 키 및 보조 키가 업데이트 되는 횟수와 해당 키가 변경 된 시간을 보여 줍니다.
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634104"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>키 업데이트 및 키 다시 생성에 대 한 Azure Cosmos DB 계정 모니터링

Azure Cosmos DB에 대 한 Azure Monitor는 계정을 모니터링 하기 위한 메트릭, 경고 및 로그를 제공 합니다. 대시보드를 만들고 요구 사항에 따라 대시보드를 사용자 지정할 수 있습니다. Azure Cosmos DB 메트릭은 기본적으로 수집 되므로 모든 항목을 명시적으로 사용 하도록 설정 하거나 구성할 필요가 없습니다. 키 업데이트에 대 한 계정을 모니터링 하려면 **계정 키 업데이트 된** 메트릭을 사용 합니다. 이 메트릭은 계정에 대해 기본 키 및 보조 키가 업데이트 되는 횟수와 해당 키가 변경 된 시간을 보여 줍니다. 키를 업데이트할 때 알림을 받도록 경고를 설정할 수도 있습니다.

## <a name="monitor-key-updates-with-metrics"></a>메트릭을 사용 하 여 키 업데이트 모니터링

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터** 를 선택 하 고 **메트릭** 을 선택 합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창" border="true":::

1. **메트릭** 창에서 메트릭을 보려는 리소스의 범위를 선택 합니다.

   1. 먼저 필요한 **구독** 을 선택 하 고 **리소스 종류** 필드에 **Azure Cosmos DB 계정** 을 선택 합니다. Azure Cosmos DB 계정이 있는 리소스 그룹의 목록이 표시 됩니다.

   1. **리소스 그룹** 을 선택 하 고 기존 Azure Cosmos 계정 중 하나를 선택 합니다. 적용을 선택합니다.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="메트릭을 볼 계정 범위를 선택 합니다." border="true":::

1. **메트릭** 필드에서 **계정 키 업데이트 된** 메트릭을 선택 합니다. **집계** 필드를 기본값 **개수로** 둡니다. 이 보기는 선택한 계정에 대해 기본 키 및 보조 키가 업데이트 된 총 횟수를 표시 합니다. 그래프에서 타임 라인을 선택 하 고 키가 업데이트 된 날짜 및 시간을 확인할 수도 있습니다.

1. 변경 된 키를 자세히 보려면 **분할 적용** 옵션을 선택 합니다. **KeyType** 을 선택 하 고 **제한**, **정렬** 속성을 설정 합니다. 이제 다음 이미지에 표시 된 것 처럼 그래프가 기본 키 및 보조 키 업데이트에 의해 분할 됩니다.

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="기본 키 및 보조 키가 업데이트 되는 경우 메트릭 차트" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>키 업데이트에 대 한 경고 구성

키 업데이트에 대 한 계정을 모니터링할 수 있습니다. 키를 회전 하거나 업데이트 하는 경우 종속 클라이언트 응용 프로그램을 업데이트 해야 작업을 계속할 수 있습니다. 경고를 구성 하 여 키를 업데이트할 때 알림을 받을 수 있습니다.

몇 가지 변경 내용과 함께 [경고 만들기](create-alerts.md) 문서에 있는 지침을 사용 합니다. 경고 조건을 선택할 때 **계정 키 업데이트 된** 신호를 선택 합니다. **KeyType** 차원을 선택 하 고 **기본** 또는 **보조** 키를 선택 합니다. 선택한 키 유형에 따라 달라 집니다. 키를 업데이트 하면 경고가 트리거됩니다.

다음 스크린샷은 계정 키를 업데이트할 때 중요 유형의 경고를 구성 하는 방법을 보여 줍니다.

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="계정 키가 업데이트 될 때 전자 메일 알림을 받을 수 있도록 경고 구성":::

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)