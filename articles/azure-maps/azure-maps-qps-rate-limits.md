---
title: Azure Maps QPS 속도 제한
description: Azure Maps의 초당 쿼리 수 제한에 대한 내용입니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 8dd0c657ba645d1ed2bf0e8585149aec7b486536
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316017"
---
# <a name="azure-maps-qps-rate-limits"></a>Azure Maps QPS 속도 제한

Azure Maps는 일일 요청 수에 대한 제한은 없지만 QPS(초당 최대 쿼리 수)에 제한이 있습니다.

다음은 각 Azure Maps 서비스의 가격 책정 계층별 QPS 사용량 제한입니다.

| Azure Maps 서비스 | QPS 제한: Gen 2 가격 책정 계층 | QPS 제한: Gen 1 S1 가격 책정 계층 | QPS 제한: Gen 1 S0 가격 책정 계층 |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Copyright Service | 10 | 10 | 10 |
| Creator - 별칭, TilesetDetails | 10 | 사용할 수 없음 | 사용할 수 없음 |
| Creator - 변환, 데이터 세트, 기능 상태, WFS | 50 | 사용할 수 없음 | 사용할 수 없음 |
| Data Service | 50 | 50 | 50 |
| 권한 상승 서비스 | 50 | 50 | 50 |
| 지리적 위치 서비스 | 50 | 50 | 50 |
| Render Service - 윤곽선 타일, DEM 타일, 권한 상승 타일, 고객 타일, 트래픽 타일 및 정적 맵 | 50 | 50 | 50 |
| Render Service - 도로 타일 | 500 | 500 | 50 |
| Render Service - 위성 타일 | 250 | 250 | 50 |
| Render Service - 날씨 타일 | 100 | 100 | 50 |
| Route Service - Batch | 10 | 10 | 10 |
| Route Service - 비 Batch | 50 | 50 | 50 |
| Search Service - Batch | 10 | 10 | 10 |
| Search Service - 비 Batch | 500 | 500 | 50 |
| Search Service - 비 Batch Reverse | 250 | 250 | 50 |
| Spatial Service | 50 | 50 | 50 |
| Timezone Service | 50 | 50 | 50 |
| Traffic Service | 50 | 50 | 50 |
| Weather Service | 50 | 50 | 50 |

QPS 제한에 도달하면 HTTP 429 오류가 반환됩니다. Gen 2 또는 Gen 1 가격 책정 계층을 사용하는 경우 [Azure Portal](https://ms.portal.azure.com/)에서 Azure Maps *기술* 지원 요청을 작성하여 필요할 때 특정 QPS 제한을 늘릴 수 있습니다. Gen 1 S0 가격 책정 계층에 대한 QPS 제한은 늘릴 수 없습니다.
