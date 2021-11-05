---
title: Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치
description: Azure Marketplace Azure Data Factory Analytics 솔루션을 설치하는 방법을 알아봅니다.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07e60aef3e6e3a968d22f76351d00cb722e99f78
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850416"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치

이 솔루션은 세부 정보를 자세히 분석하고 예기치 않은 동작 패턴을 해결하는 옵션과 함께 데이터 팩터리 전체 상태 요약을 제공합니다. 풍부한 첫 보기로 다음을 비롯한 키 처리에 대한 인사이트를 얻을 수 있습니다.

* 데이터 팩터리 파이프라인, 작업 및 트리거 실행에 대한 요약을 한눈에 볼 수 있습니다.
* 유형별로 데이터 팩터리 작업 실행을 드릴다운할 수 있습니다.
* 데이터 팩터리 상위 파이프라인 작업 오류 요약입니다.

1. **Azure Marketplace** 이동하여 **분석 필터를** 선택하고 **Azure Data Factory Analytics(미리 보기) 를 검색합니다.**

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Azure Marketplace, 분석 필터 선택 및 Azure Data Factory Analytics(미리 보기) 선택을 보여 주는 스크린샷":::

1. **Azure Data Factory Analytics(미리 보기)에** 대한 세부 정보를 검토합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Azure Data Factory Analytics(미리 보기)에 대한 세부 정보를 보여 주는 스크린샷":::

1. **만들기를** 선택한 **다음, Log Analytics 작업 영역을** 만들거나 선택합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="새 솔루션 만들기를 보여 주는 스크린샷":::

## <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

이 솔루션을 설치하면 선택한 Log Analytics 작업 영역의 통합 문서 섹션 내에 기본 보기 집합이 생성됩니다. 따라서 다음 메트릭이 사용하도록 설정됩니다.

* 데이터 실행 - 1) 데이터 팩터리에서 파이프라인 실행
* ADF 실행 - 2) 데이터 팩터리에서 작업 실행
* ADF 실행 - 3) 데이터 팩터리에서 실행되는 트리거
* ADF 오류 - 1) 데이터 팩터리별 상위 10개 파이프라인 오류
* ADF 오류 - 2) 데이터 팩터리에서 상위 10개 작업 실행
* ADF 오류 - 3) 데이터 팩터리별 상위 10개 트리거 오류
* ADF 통계 - 1) 유형별 활동 실행
* ADF 통계 - 2) 유형별 트리거 실행
* ADF 통계 - 3) 최대 파이프라인 실행 기간

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="통합 문서(미리 보기) 및 AzureDataFactoryAnalytics가 강조 표시된 창을 보여주는 스크린샷.":::

이전 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 만들고, 기타 작업을 수행할 수 있습니다.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="데이터 팩터리에서 실행되는 파이프라인의 그래픽 표현을 보여 주는 스크린샷.":::

> [!NOTE]
> Azure Data Factory Analytics(미리 보기)는 _리소스별_ 대상 테이블에 진단 로그를 보냅니다. _ADFPipelineRun_, _ADFTriggerRun_, _ADFActivityRun_ 테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
