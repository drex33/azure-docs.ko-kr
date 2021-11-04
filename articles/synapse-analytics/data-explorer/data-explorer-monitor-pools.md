---
title: '빠른 시작: Synapse Studio 사용하여 데이터 탐색기 풀 모니터링(미리 보기)'
description: 이 문서에서는 데이터 탐색기 풀을 모니터링하기 위한 정보를 제공합니다.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: d127abb6e8b493e0db3a281083d9ca17802cf529
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101230"
---
# <a name="quickstart-use-synapse-studio-to-monitor-your-data-explorer-pools-preview"></a>빠른 시작: Synapse Studio 사용하여 데이터 탐색기 풀 모니터링(미리 보기)

Azure Synapse Analytics 사용하면 데이터 탐색기를 사용하여 작업 영역의 데이터 탐색기 풀에서 쿼리, 대시보드 등을 실행할 수 있습니다.

이 문서에서는 데이터 탐색기 풀을 모니터링하여 여러 작업 영역 사용자가 사용 중인 vCore 수를 포함하여 풀의 상태를 감시하는 방법을 설명합니다.

## <a name="access-data-explorer-pools-list"></a>데이터 탐색기 풀 목록에 액세스

작업 영역에서 데이터 탐색기 풀 목록을 보려면 먼저 [Synapse Studio 열기](https://web.azuresynapse.net/)를 수행하고 작업 영역을 선택하세요.

![작업 영역에 로그인](../monitoring/media/common/login-workspace.png)

작업 영역을 연 후 왼쪽의 **모니터** 섹션을 선택합니다.

![모니터 허브 선택](../monitoring/media/common/left-nav.png)

**데이터 탐색기 풀** 을 선택하여 데이터 탐색기 풀 목록을 봅니다.

![데이터 탐색기 풀 선택](media/monitor-data-explorer-pools/monitor-hub-nav-data-explorer-pools.png)

## <a name="filter-your-data-explorer-pools"></a>데이터 탐색기 풀 필터링

데이터 탐색기 풀 목록을 관심 있는 풀로 필터링할 수 있습니다. 화면 위쪽의 필터를 사용하여 필터링할 필드를 지정할 수 있습니다.

예를 들어, 보기를 필터링하여 이름에 "test"가 포함된 데이터 탐색기 풀만 볼 수 있습니다.

![샘플 필터](media/monitor-data-explorer-pools/filter-example.png)

## <a name="view-details-about-a-specific-data-explorer-pool"></a>특정 데이터 탐색기 풀에 대한 세부 정보 보기

데이터 탐색기 풀 중 하나에 대한 세부 정보를 보려면 데이터 탐색기 풀을 선택하여 세부 정보를 보세요.

![데이터 탐색기 풀 세부 정보](media/monitor-data-explorer-pools/data-explorer-pool-details.png)
