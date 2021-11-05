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
ms.openlocfilehash: 4900a05ead0f3abcc16177a2b7ef874f310c597f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455324"
---
# <a name="azure-maps-qps-rate-limits"></a>Azure Maps QPS 속도 제한

Azure Maps는 일일 요청 수에 대한 제한은 없지만 QPS(초당 최대 쿼리 수)에 제한이 있습니다.

다음은 각 Azure Maps 서비스의 QPS 사용량 제한입니다.

| Azure Maps 서비스                                                                                         | QPS 제한   |
| ---------------------------------------------------------------------------------------------------------- | ----------- |
| 작성자: 별칭, GetTilesetDetails                                                                         | 10 |
| 작성자: 변환, 데이터 세트, 기능 상태, WFS                                                          | 50 |
| Data Service 및 Data Service v2                                                                           | 50 |
| 권한 상승 서비스                                                                                          | 50 |
| 지리적 위치 서비스                                                                                        | 50 |
| Render Service: Copyright                                                                                 | 10 |
| Render Service: 윤곽선 타일, DEM 타일, 권한 상승 타일, 고객 타일, 트래픽 타일 및 정적 맵  | 50 |
| Render Service: 도로 타일                                                                                | 500 |
| Render Service: 이미지 타일                                                                             | 250 |
| Render Service: 날씨 타일                                                                             | 100 |
| Route Service: Batch                                                                                      | 10 |
| Route Service: 비-Batch                                                                                  | 50 |
| Search Service: Batch                                                                                     | 10 |
| Search Service: 비-Batch                                                                                 | 500 |
| Search Service: 비-Batch 역방향                                                                         | 250 |
| Spatial Service                                                                                            | 50 |
| Time Zone Service                                                                                          | 50 |
| Traffic Service                                                                                            | 50 |
| Weather Service                                                                                            | 50 |

QPS 제한에 도달하면 HTTP 429 오류가 반환됩니다. 특정 QPS 제한을 늘려야 하는 경우 [Azure Portal](https://ms.portal.azure.com/)에서 Azure Maps *기술* 지원 요청을 작성하세요.
