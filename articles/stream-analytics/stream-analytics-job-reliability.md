---
title: Azure Stream Analytics 작업에서 서비스 중단 방지
description: 이 문서는 Stream Analytics 작업 업그레이드의 복원력을 높이기 위한 지침을 설명합니다.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: bb7c90bd1ce37c068047296d663ed6873aa6ab99
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133434485"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>서비스 업데이트 도중 Stream Analytics 작업 안정성 보장

완전히 관리되는 서비스가 되는 방법 중 하나는 새로운 서비스 기능 및 향상 기능을 빠른 속도로 도입하는 것입니다. 따라서 Stream Analytics는 매주(또는 더 자주) 서비스 업데이트 배포가 이루어질 수 있습니다. 얼마나 많은 테스트를 수행하든 관계없이 기존에 실행 중인 작업은 버그의 도입으로 인해 중단될 수 있는 위험이 있습니다. 중요 업무용 작업을 실행하는 경우 이러한 위험을 방지해야 합니다. Azure의 다음 **[쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)** 모델로 이러한 위험을 줄일 수 있습니다. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure 지역 쌍은 이러한 문제를 어떻게 해결할까요?

Stream Analytics는 지역 쌍의 작업이 별도의 일괄 처리로 업데이트되도록 보장합니다. 따라서 잠재적인 이슈를 식별하고 재구성하기 위해 업데이트 간에 충분 한 간격을 둡니다.

Stream Analytics 업데이트 배포는 지역 쌍 집합에서 동시에 발생하지 않습니다. 단, 인도 남부가 지역 쌍이지만 Stream Analytics 상태가 없는 _인도 중부는 예외_ 입니다. **동일한 그룹에 속하는** 여러 지역에서는 배포가 **동시에** 발생할 수 있습니다.

쌍을 이루는 지역에 대한 최신 정보는 **[가용성 및 쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)** 에 대한 문서에 있습니다.

쌍을 이루는 두 지역 모두에 동일한 작업을 배포하는 것이 좋습니다. 그런 다음, [이러한 작업을 모니터링](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)하여 예기치 않은 상황이 발생하면 알림을 받아야 합니다. 이러한 작업 중 하나가 Stream Analytics 서비스 업데이트 후 [실패 상태](./job-states.md)가 되면 고객 지원 서비스에 문의하여 근본 원인을 파악할 수 있습니다. 또한 다운스트림 소비자를 정상 작업 출력으로 장애 조치해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)