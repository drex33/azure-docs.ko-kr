---
title: 마이그레이션 작업 모니터링 - Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 마이그레이션 작업을 모니터링하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297473"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Azure Database Migration Service를 사용하여 마이그레이션 작업 모니터링
이 문서에서는 데이터베이스 수준 및 테이블 수준 모두에서 마이그레이션의 진행률을 모니터링하는 방법을 알아봅니다.

## <a name="monitor-at-the-database-level"></a>데이터베이스 수준에서 모니터링
데이터베이스 수준에서 작업을 모니터링하려면 데이터베이스 수준 블레이드를 표시합니다.

![데이터베이스 수준 블레이드](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> 데이터베이스 하이퍼링크를 선택하면 테이블 및 해당 마이그레이션 진행률 목록이 표시됩니다.

다음 표는 데이터베이스 수준 블레이드의 필드를 나열하고 각 항목에 관련된 다양한 상태 값을 설명합니다.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>필드 이름</strong></th>
      <th><strong>필드 하위 상태</strong></th>
      <th><strong>설명</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>활동 상태</strong></td>
      <td>실행 중</td>
      <td>마이그레이션 작업이 실행 중입니다.</td>
    </tr>
    <tr>
      <td>성공</td>
      <td>마이그레이션 작업이 문제 없이 성공했습니다.</td>
    </tr>
    <tr>
      <td>오류</td>
      <td>마이그레이션이 실패했습니다. 전체 오류 메시지에 대한 마이그레이션 세부 정보에서 ‘오류 세부 정보 참조’ 링크를 선택합니다.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>상태</strong></td>
      <td>초기화 중</td>
      <td>DMS가 마이그레이션 파이프라인을 설정하고 있습니다.</td>
    </tr>
    <tr>
      <td>실행 중</td>
      <td>DMS 파이프라인이 실행 중이며 마이그레이션을 수행하고 있습니다.</td>
    </tr>
    <tr>
      <td>완료</td>
      <td>마이그레이션이 완료되었습니다.</td>
    </tr>
    <tr>
      <td>실패</td>
      <td>마이그레이션이 실패했습니다. 마이그레이션 오류를 확인하려면 마이그레이션 세부 정보를 클릭합니다.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>마이그레이션 세부 정보</strong></td>
      <td>마이그레이션 파이프라인을 초기화하는 중</td>
      <td>DMS가 마이그레이션 파이프라인을 설정하고 있습니다.</td>
    </tr>
    <tr>
      <td>전체 데이터 로드 진행 중</td>
      <td>DMS가 초기 로드를 수행하고 있습니다.</td>
    </tr>
    <tr>
      <td>중단 준비</td>
      <td>초기 로드가 완료된 후 DMS는 중단에 대한 준비로 데이터베이스를 표시합니다. 사용자는 데이터가 지속적인 동기화에서 처리되는지 확인해야 합니다.</td>
    </tr>
    <tr>
      <td>모든 변경 내용이 적용됨</td>
      <td>초기 로드 및 지속적인 동기화가 완료되었습니다. 또한 이 상태는 데이터베이스가 성공적으로 중단된 후 발생합니다.</td>
    </tr>
    <tr>
      <td>오류 세부 정보 참조</td>
      <td>오류 세부 정보를 표시하려면 링크를 클릭합니다.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>기간</strong></td>
      <td>해당 없음</td>
      <td>초기화 중인 마이그레이션 작업부터 완료된 마이그레이션 또는 오류가 발생한 마이그레이션까지의 총 시간입니다.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>테이블 수준에서 모니터링 - 빠른 요약
테이블 수준에서 작업을 모니터링하려면 테이블 수준 블레이드를 표시합니다. 블레이드의 상단에 전체 부하 및 증분 업데이트에서 마이그레이션된 행의 자세한 수가 표시됩니다. 

블레이드 하단에는 테이블이 나열되고 마이그레이션 진행률에 대한 빠른 요약이 표시됩니다.

![테이블 수준 블레이드 - 빠른 요약](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

다음 테이블에서는 테이블 수준 세부 사항에 표시된 필드를 설명합니다.

| 필드 이름        | Description       |
| ------------- | ------------- |
| **완료된 전체 부하**      | 전체 데이터 로드를 완료한 테이블의 수입니다. |
| **전체 부하 큐 대기**      | 전체 부하에 대한 큐에 대기 중인 테이블 수입니다.      |
| **전체 부하 로드 중** | 실패한 테이블 수입니다.      |
| **증분 업데이트**      | 대상에 적용된 행의 CDC(변경 데이터 캡처) 업데이트 수입니다. |
| **증분 삽입**      | 대상에 적용된 행의 CDC 삽입 수입니다.      |
| **증분 삭제** | 대상에 적용된 행의 CDC 삭제 수입니다.      |
| **보류 중인 변경 내용**      | 대상에 적용된 가져오기를 계속 대기하는 행의 CDC 수입니다. |
| **적용된 변경 내용**      | 대상에 적용된 행의 전체 CDC 업데이트, 삽입 및 삭제 수입니다.      |
| **오류 상태의 테이블** | 마이그레이션 도중 ‘오류’ 상태인 테이블 수입니다. 테이블이 오류 상태가 될 수 있는 몇 가지 예로는 대상에서 식별된 중복 항목이 있거나 데이터가 대상 테이블에서 호환되어 로딩되지 않는 경우입니다.      |

## <a name="monitor-at-table-level--detailed-summary"></a>테이블 수준에서 모니터링 - 자세한 요약
전체 로드 및 증분 데이터 동기화의 마이그레이션 진행률을 보여주는 두 개의 탭이 있습니다.
    
![전체 로드 탭](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![증분 데이터 동기화 탭](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

다음 테이블은 테이블 수준 마이그레이션 진행률에 표시된 필드를 설명합니다.

| 필드 이름        | Description       |
| ------------- | ------------- |
| **상태 - 동기화 중**      | 지속적인 동기화가 실행 중입니다. |
| **삽입**      | 대상에 적용된 행의 CDC 삽입 수입니다.      |
| **업데이트** | 대상에 적용된 행의 CDC 업데이트 수입니다.      |
| **삭제**      | 대상에 적용된 행의 CDC 삭제 수입니다. |
| **적용된 합계**      | 대상에 적용된 행의 전체 CDC 업데이트, 삽입 및 삭제 수입니다. |
| **데이터 오류** | 이 테이블에서 발생한 데이터 오류의 수입니다. 오류의 예에는 *511: 최대 허용 행 크기 %d보다 큰 %d 크기의 행을 만들 수 없음, 8114: %ls에서 %ls로의 데이터 형식 변환 오류* 가 있습니다.  고객은 오류 세부 정보를 보려면 Azure 대상의 dms_apply_exceptions 테이블에서 쿼리해야 합니다.    |

> [!NOTE]
> 적용된 삽입, 업데이트 및 삭제의 CDC 값 및 합계는 데이터베이스가 중단되거나 마이그레이션이 다시 시작되는 경우 감소할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.