---
title: SQL Server HADR 및 범위 수준 Azure 하이브리드 혜택 공존
description: 이 문서에서는 SQL Server HADR Software Assurance 혜택과 범위 수준 Azure 하이브리드 혜택 공존하는 방법을 설명합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 97c338134430156fefa5d874d44e27c8dc34a496
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547910"
---
# <a name="sql-server-hadr-and-scope-level-azure-hybrid-benefit-coexistence"></a>SQL Server HADR 및 범위 수준 Azure 하이브리드 혜택 공존

SA(Software Assurance)의 이점 중 하나는 Azure 고객이 장애 조치(failover) 이벤트를 예상하여 재해 복구를 위해 수동 SQL Server 인스턴스를 설치하고 실행할 수 있다는 것입니다. Azure 하이브리드 혜택 범위 수준 관리는 정규화된 재해 복구 복제본이 할당된 SQL Server 라이선스를 소비하지 않도록 하여 SQL Server HADR 혜택을 지원합니다. 따라서 복제본을 별도로 관리할 필요가 없습니다. 

## <a name="hadr-benefit-selection"></a>HADR 혜택 선택

HADR 혜택을 사용하려면 다음 이미지와 같이 SQL **Virtual Machine** 리소스의 **구성** 창을 사용하여 **HADR을** 선택합니다.

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="SQL 가상 머신 구성의 HADR 옵션을 보여주는 스크린샷." lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

HADR 속성을 선택하면 범위에 중앙 집중식으로 할당된 라이선스가 해당 리소스의 SQL 소프트웨어 비용을 할인하는 데 사용되지 않습니다. 대신 종량제 미터는 자동으로 $0 HADR 미터로 전환합니다. 이 방법을 사용하면 할당된 SQL 라이선스가 라이선스가 필요한 리소스에만 사용됩니다. HADR 혜택을 고려하기 위해 SQL 라이선스 할당을 팽창할 필요가 없습니다. 아래 다이어그램은 이 개념을 보여 줍니다.

다음 이미지에 표시된 가격은 예시용으로만 사용됩니다.

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="HADR이 선택된 완전히 할인된 vCore 사용량을 보여 주는 다이어그램" border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> HADR 옵션은 AlwaysOn 가용성 그룹에서 이 SQL Server 인스턴스의 특정 역할을 반영합니다. 이를 선택하는 것은 서비스 소유자 또는 DBA의 책임이며 [적어도 SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할이 필요합니다. 이 작업은 범위 수준 라이선스 할당과 관련이 없습니다.

## <a name="next-steps"></a>다음 단계

- 범위 [수준 Azure 하이브리드 혜택 관리 FAQ를 검토합니다.](faq-azure-hybrid-benefit-scope.yml)
- Azure 하이브리드 혜택 [범위 수준 관리란?에서 할인이](sql-server-hadr-licenses.md) 적용되는 방식에 대해 알아봅니다.
- 기존 Azure 하이브리드 혜택 [환경 에서 전환하는](transition-existing.md)방법에 대해 알아봅니다.