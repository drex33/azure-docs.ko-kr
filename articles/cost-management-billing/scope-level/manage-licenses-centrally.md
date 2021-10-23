---
title: Azure가 중앙에 할당 된 SQL 라이선스를 시간별 사용량에 적용 하는 방법
description: 이 문서에서는 Azure 하이브리드 혜택를 사용 하 여 Azure에서 시간별 사용량에 중앙에서 할당 된 SQL 라이선스를 적용 하는 방법에 대해 자세히 설명 합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 53f8538e6e34c91023bc55e7d47e5f28e9fb3e21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243850"
---
# <a name="how-azure-applies-centrally-assigned-sql-licenses-to-hourly-usage"></a>Azure가 중앙에 할당 된 SQL 라이선스를 시간별 사용량에 적용 하는 방법

이 문서에서는 범위 수준에서 SQL Server에 대 한 Azure 하이브리드 혜택 중앙에서 관리 하는 방법에 대 한 세부 정보를 제공 합니다. 프로세스는 구독 또는 청구 계정 범위에 라이선스를 할당 하는 관리자와 함께 시작 됩니다.

각 리소스는 적절 한 전체 가격 또는 종 량 제 측정기를 사용 하 여 한 시간에 한 번 사용 현황을 보고 합니다. 내부적으로 Azure에서 사용 응용 프로그램 엔진은 사용 가능한 NCLs를 평가 하 고 해당 시간에 적용 합니다. VCore 리소스 소비의 지정 된 시간 동안, 선택 된 범위에서 사용 되지 않는 n Cls가 충분 한 경우 종 량 제 미터는 해당 Azure 하이브리드 혜택 측정기로 전환 됩니다 ($0).

## <a name="license-discount"></a>라이선스 할인 

다음 다이어그램에서는 시간에 대 한 모든 SQL 리소스에서 전체 vcore 사용을 할인 하는 데 충분 하지 않은 ncls를 사용 하는 크기 프로세스를 보여 줍니다.

다음 그림에 표시 된 가격은 예를 들어 목적 으로만 사용 됩니다.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="완전히 할인 되는 vCore 소비량을 보여 주는 다이어그램" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::


범위에 있는 SQL 리소스가 사용 하는 vcore가 사용 되지 않는 n cls 수를 초과 하는 경우 초과 vcore 사용량은 적절 한 종 량 제 측정기를 사용 하 여 청구 됩니다. 다음 다이어그램은 vCore 소비가 과도 하 게 사용 된 NCLs 수를 초과 하는 경우 크기 프로세스를 보여 줍니다.

다음 그림에 표시 된 가격은 예를 들어 목적 으로만 사용 됩니다.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="부분적으로 할인 되는 소비량을 보여 주는 다이어그램" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::

할당 된 코어 라이선스가 적용 되는 Azure SQL 리소스는 시간에서 시간 마다 다를 수 있습니다. 분산은 실행 되는 리소스 및 자동화 된 시스템에서 해당 사용량을 처리 하는 순서에 따라 달라 집니다. 그러나 시스템은 선택한 범위에서 할당 된 SQL 라이선스의 최대 사용량을 확인 합니다. Cost Management를 사용 하 여 사용 현황을 모니터링할 수 있습니다. 자세한 내용은 [할당 된 라이선스 사용을 추적 하는 방법](create-sql-license-assignments.md#track-assigned-license-use)을 참조 하세요.

다음 다이어그램에서는 Azure 하이브리드 혜택 시간이 지남에 따라 할당 된 SQL Server 라이선스가 적용 되는 방법을 보여 줍니다.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="시간이 지남에 따라 NCL 사용을 보여 주는 다이어그램." border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::

## <a name="next-steps"></a>다음 단계

- [중앙에서 관리 되는 AZURE 하이브리드 혜택 FAQ](faq-azure-hybrid-benefit-scope.yml)를 검토 합니다.
- [기존 Azure 하이브리드 혜택 환경에서 전환](transition-existing.md)하는 방법에 대해 알아봅니다.