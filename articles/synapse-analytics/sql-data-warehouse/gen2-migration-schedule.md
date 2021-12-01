---
title: 전용 SQL 풀(이전의 SQL DW)을 Gen2로 마이그레이션
description: 기존 전용 SQL 풀(이전의 SQL DW)을 Gen2로 마이그레이션하는 방법 및 지역별 마이그레이션 일정에 대한 지침입니다.
services: synapse-analytics
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 60a557be66760531e843050ee3f95d0e64ab2993
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133355145"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>전용 SQL 풀(이전의 SQL DW)을 Gen2로 업그레이드

Microsoft는 전용 SQL 풀(이전의 SQL DW)을 실행하는 데 드는 진입 수준 비용을 줄이는 데 도움이 됩니다.  이제 까다로운 쿼리를 처리할 수 있는 낮은 컴퓨팅 계층을 전용 SQL 풀(이전의 SQL DW)에서 사용할 수 있습니다. [Gen2에 대한 낮은 컴퓨팅 계층](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/) 전체 공지를 참조하세요. 새 제품은 아래 표에 나와 있는 지역에서 사용할 수 있습니다. 지원되는 지역의 경우 기존 Gen1 전용 SQL 풀(이전의 SQL DW)을 Gen2로 업그레이드할 수 있습니다.

- **자동 업그레이드 프로세스:** 자동 업그레이드는 지역에서 서비스를 사용할 수 있게 되는 즉시 시작되지 않습니다.  특정 지역에서 자동 업그레이드가 시작되면 선택한 유지 관리 일정 동안 개별 데이터 웨어하우스 업그레이드가 수행됩니다.
- [**Gen2로 자체 업그레이드:**](#self-upgrade-to-gen2) Gen2에 대한 자체 업그레이드를 수행하여 업그레이드 시기를 컨트롤할 수 있습니다. 지역이 아직 지원되지 않는 경우 복원 지점에서 지원되는 지역의 Gen2 인스턴스로 직접 복원할 수 있습니다.

## <a name="automated-schedule-and-region-availability-table"></a>자동화 일정 및 지역 가용성 표

다음 표에서는 하위 Gen2 컴퓨팅 계층 지원 일정 및 자동 업그레이드 시작 일정을 지역별로 요약해서 보여 줍니다. 날짜는 변경될 수 있습니다. 해당 지역이 지원되는지 다시 확인하세요.

\*은 해당 지역의 특정 일정을 현재 사용할 수 없음을 나타냅니다.

| **지역** | **하위 Gen2 사용 가능** | **자동 업그레이드 시작** |
|:--- |:--- |:--- |
| 중국 동부 |\* |\* |
| 중국 북부 |\* |\* |

## <a name="automatic-upgrade-process"></a>자동 업그레이드 프로세스

위의 가용성 차트에 따라 Gen1 인스턴스에 대해 자동화된 업그레이드를 예약합니다. 전용 SQL 풀(이전의 SQL DW)의 가용성이 예기치 않게 중단되는 것을 방지하기 위해 자동 업그레이드가 유지 관리 일정 중에 예약됩니다. Gen2로 자동 업그레이드하는 지역에서는 새 Gen1 인스턴스를 만드는 기능을 사용할 수 없습니다. 자동 업그레이드가 완료되면 Gen1은 사용되지 않습니다. 일정에 대한 자세한 내용은 [유지 관리 일정 보기](maintenance-scheduling.md#view-a-maintenance-schedule)를 참조하세요.

업그레이드 프로세스 도중에 전용 SQL 풀(이전의 SQL DW)을 다시 시작하는 동안 연결이 잠시 끊어집니다(약 5분).  전용 SQL 풀(이전의 SQL DW)이 다시 시작되면 완전히 사용할 수 있게 됩니다. 그러나 업그레이드 프로세스가 백그라운드에서 데이터 파일을 계속 업그레이드하는 동안 성능이 저하될 수 있습니다. 총 성능 저하 시간은 데이터 파일의 크기에 따라 달라집니다.

다시 시작한 후에 더 큰 SLO 및 리소스 클래스를 사용하는 모든 기본 columnstore 테이블에 [Alter Index rebuild](sql-data-warehouse-tables-index.md)를 실행하여 데이터 파일 업그레이드 프로세스를 신속하게 진행할 수도 있습니다.

> [!NOTE]
> Alter Index rebuild는 오프라인 작업이며, 다시 빌드가 완료될 때까지 테이블을 사용할 수 없습니다.

## <a name="self-upgrade-to-gen2"></a>Gen2로 자체 업그레이드

기존 Gen1 전용 SQL 풀(이전의 SQL DW)에서 다음 단계를 수행하여 자체 업그레이드하도록 선택할 수 있습니다. 자체 업그레이드를 선택하는 경우 해당 지역에서 자동 업그레이드 프로세스가 시작되기 전에 업그레이드를 완료해야 합니다. 이렇게 하면 자동 업그레이드로 인해 충돌이 발생하는 위험을 방지할 수 있습니다.

자체 업그레이드를 수행할 때는 두 가지 옵션을 사용할 수 있습니다.  현재 전용 SQL 풀(이전의 SQL DW)을 현재 위치로 업그레이드하거나 Gen1 전용 SQL 풀(이전의 SQL DW)을 Gen2 인스턴스로 복원할 수 있습니다.

- [현재 위치 업그레이드](upgrade-to-latest-generation.md) - 이 옵션은 기존 Gen1 전용 SQL 풀(이전의 SQL DW)을 Gen2로 업그레이드합니다. 업그레이드 프로세스 도중에 전용 SQL 풀(이전의 SQL DW)을 다시 시작하는 동안 연결이 잠시 끊어집니다(약 5분).  다시 시작하면 완전히 사용할 수 있습니다. 업그레이드하는 동안 문제가 발생하는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 열고 “Gen2 업그레이드”를 가능한 원인으로 참조합니다.
- [복원 지점에서 업그레이드](sql-data-warehouse-restore-points.md) - 현재 Gen1 전용 SQL 풀(이전의 SQL DW)에서 사용자 정의 복원 지점을 만든 후 Gen2 인스턴스로 직접 복원합니다. 기존 Gen1 전용 SQL 풀(이전의 SQL DW)은 그대로 유지됩니다. 복원이 완료된 후에는 Gen2 전용 SQL 풀(이전의 SQL DW)을 완전히 사용할 수 있습니다.  복원된 Gen2 인스턴스에서 모든 테스트 및 유효성 검사 프로세스를 실행한 후에는 원래 Gen1 인스턴스를 삭제할 수 있습니다.

  - 1단계: Azure Portal에서 [사용자 정의 복원 지점을 만듭니다](sql-data-warehouse-restore-active-paused-dw.md).
  - 2단계: 사용자 정의 복원 지점에서 복원할 때 "성능 수준"을 기본 Gen2 계층으로 설정합니다.

업그레이드 프로세스가 백그라운드에서 데이터 파일을 계속 업그레이드하는 동안 일정 기간 동안 성능이 저하될 수 있습니다. 총 성능 저하 시간은 데이터 파일의 크기에 따라 달라집니다.

백그라운드 데이터 마이그레이션 프로세스를 더 신속히 처리하기 위해 더 큰 SLO 및 리소스 클래스에서 쿼리하는 모든 기본 columnstore 테이블에서 [Alter Index rebuild](sql-data-warehouse-tables-index.md)를 실행하여 데이터 이동을 즉시 실행할 수 있습니다.

> [!NOTE]
> Alter Index rebuild는 오프라인 작업이며, 다시 빌드가 완료될 때까지 테이블을 사용할 수 없습니다.

전용 SQL 풀(이전의 SDL DW)에 문제가 발생하는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 만들고 가능한 원인으로 “Gen2 업그레이드”를 참조합니다.

자세한 내용은 [Gen2로 업그레이드](upgrade-to-latest-generation.md)를 참조하세요.

## <a name="migration-frequently-asked-questions"></a>마이그레이션 질문과 대답

**Q: Gen2는 Gen1과 비용이 같은가요?**

- A: 예.

**Q:업그레이드는 내 자동화 스크립트에 어떤 영향을 미치나요?**

- A: 서비스 수준 목표를 참조하는 모든 자동화 스크립트는 Gen2에 해당하도록 변경해야 합니다.  자세한 내용은 [여기](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)를 참조하세요.

**Q: 자체 업그레이드는 일반적으로 기간에 얼마나 걸리나요?**

- A: 현재 위치에서 업그레이드하거나 복원 지점에서 업그레이드할 수 있습니다.

  - 원위치에서 업그레이드하면 전용 SQL 풀(이전의 SQL DW)이 일시적으로 일시 중지되고 다시 시작됩니다.  전용 SQL 풀(이전의 SQL DW)이 온라인 상태인 동안에는 백그라운드 프로세스가 계속됩니다.  
  - 복원 지점까지 업그레이드하는 경우 업그레이드가 전체 복원 프로세스를 거치게 되므로 시간이 더 오래 걸립니다.

**Q: 자동 업그레이드는 시간이 얼마나 걸리나요?**

- A: 업그레이드의 실제 가동 중지는 서비스를 일시 중지했다가 다시 시작하는 데 걸리는 시간에 불과하며 5~10분 정도입니다. 잠깐 가동 중지된 후에 백그라운드 프로세스에서 스토리지 마이그레이션이 수행됩니다. 백그라운드 프로세스의 기간은 전용 SQL 풀(이전의 SQL DW)의 크기에 따라 달라집니다.

**Q: 자동 업그레이드는 언제 진행되나요?**

- A: 유지 관리 일정 동안 진행됩니다. 선택한 유지 관리 일정을 활용하면 비즈니스 중단이 최소화됩니다.

**Q: 백그라운드 업그레이드 프로세스가 중단된 것 같으면 어떻게 해야 하나요?**

- A: Columnstore 테이블의 다시 인덱싱 작업을 시작합니다. 이 작업 동안 테이블의 다시 인덱싱 작업은 오프라인에서 수행됩니다.

**Q: Gen1에 있던 서비스 수준 목표가 Gen2에 없으면 어떻게 하나요?**

- A: Gen1에서 DW600 또는 DW1200을 실행하는 경우 Gen2는 Gen1보다 더 많은 메모리, 리소스 및 성능을 제공하므로 대신 DW500c 또는 DW1000c를 사용하는 것이 좋습니다.

**Q:지역 백업을 사용하지 않도록 설정할 수 있나요?**

- A: 아니요. 지역 백업은 지역을 사용할 수 없는 경우에도 전용 SQL 풀(이전의 SQL DW) 가용성을 보존하기 위한 엔터프라이즈 기능입니다. 문제가 더 있는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 엽니다.

**Q: Gen1과 Gen2 간의 T-SQL 구문 차이가 있나요?**

- A: Gen1과 Gen2의 T-SQL 언어 구문은 달라지지 않습니다.

**Q:Gen2는 유지 관리 기간을 지원하나요?**

- A: 예.

**Q: 지역을 업그레이드한 후 새 Gen1 인스턴스를 만들 수 있나요?**

- A: 아니요. 지역을 업그레이드한 후에는 Gen1 인스턴스를 새로 만들 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [업그레이드 단계](upgrade-to-latest-generation.md)
- [유지 관리 기간](maintenance-scheduling.md)
- [Resource Health 모니터링](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [마이그레이션 시작 전 검토 사항](upgrade-to-latest-generation.md#before-you-begin)
- [현재 위치 업그레이드 및 복원 지점에서 업그레이드](upgrade-to-latest-generation.md)
- [사용자 정의 복원 지점 만들기](sql-data-warehouse-restore-points.md)
- [Gen2로 복원하는 방법 알아보기](sql-data-warehouse-restore-active-paused-dw.md)
- [Azure Synapse Analytics 지원 요청 열기](./sql-data-warehouse-get-started-create-support-ticket.md)
