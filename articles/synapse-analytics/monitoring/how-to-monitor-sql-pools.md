---
title: Synapse Studio에서 SQL 풀을 모니터링하는 방법
description: Synapse Studio를 사용하여 SQL 풀을 모니터링하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467419"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Synapse Studio를 사용하여 SQL 풀 모니터링

Synapse Studio를 사용하여 작업 영역의 SQL 풀에서 SQL 스크립트를 실행할 수 있습니다.

이 문서에서는 SQL 풀을 모니터링하여 사용자가 풀의 상태와 활동을 파악하도록 하는 방법을 설명합니다.

## <a name="access-sql-pools-list"></a>SQL 풀 목록 액세스

작업 영역에서 SQL 풀 목록을 보려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역을 선택합니다.

![작업 영역에 로그인](./media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](./media/common/left-nav.png)

SQL 풀 목록을 보려면 **SQL 풀** 을 선택합니다.

 ![SQL 풀 선택](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>SQL 풀 필터링

SQL 풀 목록을 원하는 항목으로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어 보기를 필터링하여 이름이 "salesrecords" 인 SQL 풀만 볼 수 있습니다.

![샘플 필터](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>특정 SQL 풀에 대한 세부 정보 보기

SQL 풀 중 하나에 대한 세부 정보를 보려면 SQL 풀을 선택하여 세부 정보를 확인합니다.

![SQL 풀 세부 정보](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 정보는 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요. 

SQL 요청 모니터링에 대한 자세한 정보는 [Synapse Studio에서 SQL 요청 모니터링](how-to-monitor-sql-requests.md) 문서를 참조하세요.