---
title: 영역 간 이동 대시보드
description: 영역 간 이동 대시보드를 사용 하 여 지역 간에 이동 하는 리소스를 모니터링 합니다.
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622449"
---
# <a name="move-across-region-dashboard"></a>영역 간 이동 대시보드
이 문서에서는 Azure 리소스 이동의 지역 간 이동 대시보드를 통해 지역 간에 이동 하는 리소스를 모니터링 하는 방법을 설명 합니다. 
## <a name="monitor-via-the-dashboard"></a>대시보드를 통해 모니터링
1. **Azure 리소스** 이동에서 **개요** 왼쪽 탐색 창을 선택 합니다. 두 페이지 사이를 전환 하 여 **시작** 하 고 **지역 대시보드 간에 이동할** 수 있습니다. **시작** 페이지에서는 리소스 그룹 및 지역 전체에서 구독 간에 리소스를 이동 하는 옵션을 제공 합니다.
**지역 간 이동 대시보드** 페이지에는 지역 간 이동의 모든 모니터링 정보가 단일 위치에 결합 되어 있습니다.
    [![영역 간 이동 대시보드 탭](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. 대시보드는 사용자가 만든 모든 이동 조합을 나열 합니다. 다음 두 섹션은 지역 간 이동의 상태를 캡처하는 데 사용 됩니다.
    **상태 이동의 리소스** 에서 각 상태의 리소스 비율 및 수를 모니터링 합니다.
    **오류 요약** 에서 대상 영역으로 성공적으로 이동 하기 전에 해결 해야 하는 활성 오류를 모니터링 합니다.
    [![상태 및 문제 섹션](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> 선택한 구독에 이미 생성 된 원본-대상 조합만 대시보드에 나열 됩니다.

3. 필터를 사용 하 여 원하는 **구독**, **원본 지역** 및 **대상 지역을** 선택 합니다.
    [![필터](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. 원본-대상 옆의 **모든 리소스 보기** 를 선택 하 여 세부 정보 페이지로 이동 합니다.
    [![세부 정보](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>다음 단계
이동 프로세스에 대해 [알아보기](about-move-process.md).
