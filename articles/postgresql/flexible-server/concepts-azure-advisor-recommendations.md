---
title: PostgreSQL-유연한 서버에 대 한 Azure Advisor
description: PostgreSQL-유연한 서버에 대 한 Azure Advisor 권장 사항에 대해 알아봅니다.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 329acf915e6b22a04894b9db76da6bf56ff58ea5
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286874"
---
# <a name="azure-advisor-for-postgresql---flexible-server"></a>PostgreSQL-유연한 서버에 대 한 Azure Advisor
Azure Database for PostgreSQL에 Azure Advisor가 적용되는 방법에 대해 알아보고 일반적인 질문에 대한 답변을 받으세요.
## <a name="what-is-azure-advisor-for-postgresql"></a>Azure Advisor for PostgreSQL이란 무엇인가요?
Azure Advisor 시스템은 원격 분석을 사용하여 PostgreSQL 데이터베이스에 대한 성능 및 안정성 권장 사항을 발급합니다. Advisor 권장 사항은 PostgreSQL 데이터베이스 제품 간에 분할됩니다.
* Azure Database for PostgreSQL - 단일 서버
* Azure Database for PostgreSQL - 유연한 서버
* Azure Database for PostgreSQL – 하이퍼스케일(Citus)

일부 권장 사항은 여러 제품에 공통적으로 적용되는 반면, 다른 권장 사항은 제품별 최적화를 기반으로 제공됩니다.
## <a name="where-can-i-view-my-recommendations"></a>권장 사항은 어디에서 볼 수 있나요?
권장 사항은 Azure Portal의 **개요** 탐색 사이드바에서 확인할 수 있습니다. 미리 보기가 배너 알림으로 표시되고, 리소스 사용량 그래프 바로 아래의 **알림** 섹션에서 세부 정보를 볼 수 있습니다.

:::image type="content" source="../media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Azure Advisor 권장 사항을 보여주는 Azure Portal의 스크린샷":::
## <a name="recommendation-types"></a>권장 사항 유형
Azure Database for PostgreSQL은 다음 권장 사항 유형의 우선 순위를 지정합니다.
* **성능**: PostgreSQL 서버의 속도를 향상시킵니다. 여기에는 CPU 사용량, 메모리 압력, 연결 풀링, 디스크 사용률 및 제품별 서버 매개 변수가 포함됩니다. 자세한 내용은 [Advisor 성능 권장 사항](/azure/advisor/advisor-performance-recommendations)을 참조하세요.
* **가용성**: 업무상 중요한 데이터베이스의 연속성을 보장하고 향상시키는 데 도움이 됩니다. 여기에는 스토리지 제한, 연결 제한 및 하이퍼스케일 데이터 배포 권장 사항이 포함됩니다. 자세한 내용은 [Advisor 가용성 권장 사항](/azure/advisor/advisor-high-availability-recommendations)을 참조하세요.
* **비용**: 전체 Azure 사용을 최적화하고 사용량을 줄입니다. 여기에는 서버에 적합한 크기 조정 권장 사항이 포함됩니다. 자세한 내용은 [Advisor 비용 권장 사항](/azure/advisor/advisor-cost-recommendations)을 참조하세요.

## <a name="understanding-your-recommendations"></a>권장 사항 이해
* **일별 일정**: Azure PostgreSQL 데이터베이스의 경우 서버 원격 분석을 확인 하 고 하루에 두 번 일정에 따라 권장 사항을 발급 합니다. 서버 구성을 변경 하는 경우 PST에 따라 7PM 또는 7PM에서 원격 분석을 다시 검사할 때까지 기존 권장 사항이 계속 표시 됩니다. 
* **성능 기록**: 몇몇 권장 사항은 성능 기록을 기반으로 합니다. 이러한 권장 사항은 서버가 7일 동안 동일한 구성으로 작동한 후에만 표시됩니다. 이를 통해 사용량이 많은 경우(예: 높은 CPU 작업 또는 높은 연결 볼륨)의 패턴을 지속적으로 검색할 수 있습니다. 새 서버를 프로비전하거나 새 vCore 구성으로 변경하는 경우, 이러한 권장 사항은 일시 중지됩니다. 이렇게 하면 레거시 원격 분석에서 새로 재구성된 서버에 대한 권장 사항을 트리거하지 않습니다. 그러나 이는 성능 기록 기반 권장 사항이 즉시 식별되지 않을 수도 있음을 의미합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Azure Advisor 개요](/azure/advisor/advisor-overview)를 참조하세요.
