---
title: Azure에서 할당된 SQL 라이선스를 시간별 사용량에 적용하는 방법
description: 이 문서에서는 Azure가 할당된 SQL 라이선스를 Azure 하이브리드 혜택 시간별 사용량에 적용하는 방법에 대한 자세한 설명을 제공합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 69517a84fa28a6c1d9cde0cb51df82a296bd653a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710159"
---
# <a name="how-azure-applies-assigned-sql-licenses-to-hourly-usage"></a>Azure에서 할당된 SQL 라이선스를 시간별 사용량에 적용하는 방법

이 문서에서는 범위 수준에서 Azure 하이브리드 혜택 작동하여 SQL Server 라이선스를 관리하는 방법에 대한 자세한 설명을 제공합니다. 이 프로세스는 관리자가 구독 또는 청구 계정 범위에 라이선스를 할당하는 것으로 시작합니다.

각 리소스는 적절한 전체 가격 또는 종량제 미터를 사용하여 한 시간에 한 번씩 사용량을 보고합니다. Azure에서 내부적으로 사용량 애플리케이션 엔진은 사용 가능한 NCL을 평가하고 해당 시간에 적용합니다. 지정된 시간 동안 vCore 리소스 소비량의 종량제 미터는 선택한 범위에 사용되지 않은 NCL이 충분히 있는 경우 0($0) 가격으로 해당 Azure 하이브리드 혜택 미터로 전환됩니다.

## <a name="license-discount"></a>라이선스 할인 

다음 다이어그램에서는 사용이 되지 않은 NCL이 시간 동안 모든 SQL 리소스에서 전체 vCore 소비를 할인할 만큼 충분한 경우의 할인 프로세스를 보여 주는 다이어그램입니다.

다음 이미지에 표시된 가격은 예시용으로만 사용됩니다.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="완전히 할인된 vCore 사용량을 보여 주는 다이어그램" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::


범위에서 SQL 리소스의 vCore 사용량이 사용되지 않은 NCL 수를 초과하면 적절한 종량제 미터를 사용하여 초과 vCore 사용 요금이 청구됩니다. 다음 다이어그램에서는 vCore 사용량이 사용되지 않은 NCL 수를 초과하는 경우의 할인 프로세스를 보여 있습니다.

다음 이미지에 표시된 가격은 예시용으로만 사용됩니다.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="부분적으로 할인된 사용량을 보여 주는 다이어그램." border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::

할당된 Core 라이선스가 적용되는 Azure SQL 리소스는 시간마다 다를 수 있습니다. 분산은 실행되는 리소스와 자동화된 시스템이 사용을 처리하는 순서에 따라 달라집니다. 그러나 시스템은 선택한 범위에서 할당된 SQL 라이선스의 최대 사용량을 보장합니다. Cost Management 사용하여 사용량을 모니터링할 수 있습니다. 자세한 내용은 [할당된 라이선스 사용량을 추적하는 방법을 참조하세요.](create-sql-license-assignments.md#track-assigned-license-use)

다음 다이어그램에서는 할당된 SQL Server 라이선스가 시간에 따라 적용되어 최대 Azure 하이브리드 혜택 할인을 받는 방법을 보여줍니다.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="시간에 따라 NCL 사용을 보여 주는 다이어그램." border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::

## <a name="next-steps"></a>다음 단계

- 범위 [수준 Azure 하이브리드 혜택 관리 FAQ를 검토합니다.](faq-azure-hybrid-benefit-scope.yml)
- 기존 Azure 하이브리드 혜택 [경험에서 전환하는](transition-existing.md)방법에 대해 알아봅니다.