---
title: Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치
description: Azure Marketplace Azure Data Factory Analytics 솔루션을 설치하는 방법을 알아봅니다.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838677"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치

이 솔루션은 Data Factory의 전반적인 상태에 대한 요약과 함께 세부 정보를 자세히 살펴보고 예기치 않은 동작 패턴 문제를 해결할 수 있는 옵션을 제공합니다. 기본 제공되는 풍부한 보기를 통해 다음을 비롯한 주요 처리에 대한 인사이트를 얻을 수 있습니다.

* 데이터 팩터리 파이프라인, 활동 및 트리거 실행을 한눈에 보여 주는 요약
* 데이터 팩터리 활동 실행에 대한 정보를 유형별로 상세히 검색할 수 있는 기능
* 데이터 팩터리 주요 파이프라인, 활동 오류 요약

1. **Azure Marketplace** 로 이동하고 **Analytics** 필터를 선택하고 **Azure Data Factory Analytics(미리 보기)** 를 검색합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Azure Marketplace 이동하여 &quot; 분석 &quot; 필터 &quot; &quot; 를 입력하고 &quot; Azure Data Factory Analytics(미리 보기)를 선택합니다.&quot;":::

1. **Azure Data Factory Analytics(미리 보기)** 에 대한 세부 정보

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Azure Data Factory &quot; Analytics(미리 보기)에 대한 세부 정보&quot;":::

1. **만들기** 를 선택한 후 **Log Analytics 작업 영역** 을 만들거나 선택합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="새 솔루션 만들기":::

## <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

이 솔루션을 설치하면 선택한 Log Analytics 작업 영역의 통합 문서 섹션 내에 기본 보기 집합이 생성됩니다. 그 결과 다음과 같은 메트릭이 활성화됩니다.

* ADF 실행 - 1) Data Factory의 파이프라인 실행
* ADF 실행 - 2) Data Factory의 활동 실행
* ADF 실행 - 3) Data Factory의 트리거 실행
* ADF 오류 - 1) Data Factory의 상위 10개 파이프라인 오류
* ADF 오류 - 2) Data Factory의 상위 10개 활동 실행
* ADF 오류 - 3) Data Factory의 상위 10개 트리거 오류
* ADF 통계 - 1) 형식별 활동 실행
* ADF 통계 - 2) 형식별 트리거 실행
* ADF 통계 - 3) 최대 파이프라인 실행 기간

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="통합 &quot; 문서(미리 보기) &quot; 및 &quot; AzureDataFactoryAnalytics가 &quot; 강조 표시된 창":::

이전 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 만들고, 기타 작업을 수행할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="데이터 팩터리에서 실행되는 파이프라인의 그래픽 표현":::

> [!NOTE]
> Azure Data Factory Analytics(미리 보기)는 진단 로그를 _Resource-specific_ 대상 테이블로 보냅니다. _ADFPipelineRun_, _ADFTriggerRun_, _ADFActivityRun_ 테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
