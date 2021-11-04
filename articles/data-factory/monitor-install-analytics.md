---
title: Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치
description: Azure Marketplace에서 Azure Data Factory Analytics 솔루션을 설치 하는 방법을 알아봅니다.
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: cbb45c793ee0f4167d6ba706c144c5c46445ec90
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011393"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치

이 솔루션은 세부 정보를 자세히 설명 하 고 예기치 않은 동작 패턴의 문제를 해결 하는 옵션과 함께 데이터 팩터리의 전반적인 상태에 대 한 요약을 제공 합니다. 풍부한 기본 보기를 사용 하 여 다음을 비롯 한 주요 처리에 대 한 통찰력을 얻을 수 있습니다.

* 데이터 팩터리 파이프라인, 활동 및 트리거 실행에 대 한 간략 한 요약입니다.
* 형식별로 데이터 팩터리 작업을 드릴 수 있습니다.
* 데이터 팩터리 상위 파이프라인 활동 오류에 대 한 요약입니다.

1. **Azure Marketplace** 로 이동 하 여 **분석** 필터를 선택 하 고 **Azure Data Factory 분석 (미리 보기)** 을 검색 합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Azure Marketplace로 이동 하 고, 분석 필터를 선택 하 고, Azure Data Factory 분석 (미리 보기)을 선택 하는 것을 보여 주는 스크린샷":::

1. **Azure Data Factory 분석 (미리 보기)** 에 대 한 세부 정보를 검토 합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Azure Data Factory 분석 (미리 보기)에 대 한 세부 정보를 보여 주는 스크린샷":::

1. **만들기** 를 선택 하 고 **Log Analytics 작업 영역** 을 만들거나 선택 합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="새 솔루션을 만드는 과정을 보여 주는 스크린샷":::

## <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

이 솔루션을 설치하면 선택한 Log Analytics 작업 영역의 통합 문서 섹션 내에 기본 보기 집합이 생성됩니다. 그 결과 다음과 같은 메트릭이 활성화 됩니다.

* 데이터 실행-1) data factory에서 파이프라인 실행
* ADF 실행-2) 데이터 팩터리에서 작업 실행
* ADF 실행-3) data factory에서 트리거 실행
* ADF 오류-1) data factory 별 상위 10 개 파이프라인 오류
* ADF 오류-2) data factory에서 상위 10 개의 작업 실행
* ADF 오류-3) data factory 별 상위 10 개 트리거 오류
* ADF 통계-1) 유형별 작업 실행
* ADF 통계-2) 유형별 트리거 실행
* ADF 통계-3) 최대 파이프라인 실행 기간

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="통합 문서 (미리 보기)와 AzureDataFactoryAnalytics 강조 표시 된 창을 보여 주는 스크린샷":::

이전 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 만들고, 기타 작업을 수행할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="데이터 팩터리에서 파이프라인 실행을 그래픽으로 표시 하는 스크린샷":::

> [!NOTE]
> Azure Data Factory 분석 (미리 보기) _은 리소스 관련_ 대상 테이블에 진단 로그를 보냅니다. _ADFPipelineRun_, _ADFTriggerRun_, _ADFActivityRun_ 테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
