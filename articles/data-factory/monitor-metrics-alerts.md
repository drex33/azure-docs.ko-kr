---
title: Data Factory 메트릭 및 경고
description: Azure Data Factory 모니터링에 사용할 수 있는 메트릭에 대해 알아봅니다.
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 57378ba773fd9907b0fdf0104d4165285129e958
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020201"
---
# <a name="data-factory-metrics-and-alerts"></a>Data Factory 메트릭 및 경고

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 서비스 모니터링을 사용 하도록 설정 하는 다음과 같은 메트릭 및 경고를 제공 합니다.

## <a name="data-factory-metrics"></a>Data Factory 메트릭

Azure Monitor를 사용 하 여 Azure 워크 로드의 성능 및 상태를 파악할 수 있습니다. 가장 중요한 유형의 Monitor 데이터는 메트릭이며 성능 카운터라고도 합니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.

Azure Data Factory 버전 2에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**                           | **메트릭 표시 이름**                  | **단위** | **집계 유형** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 취소 된 작업 실행 메트릭           | 개수    | 합계                | 1 분 기간 내에 취소 된 작업 실행의 총 수입니다. |
| ActivityFailedRuns                   | 실패한 활동 실행 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 활동 실행의 총 수입니다. |
| ActivitySucceededRuns                | 성공한 활동 실행 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 활동 실행의 총 수입니다. |
| PipelineCancelledRuns                 | 취소 된 파이프라인 실행 메트릭           | 개수    | 합계                | 1 분 기간 내에 취소 된 총 파이프라인 실행 수입니다. |
| PipelineFailedRuns                   | 실패한 파이프라인 실행 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 파이프라인 실행의 총 수입니다. |
| PipelineSucceededRuns                | 성공한 파이프라인 실행 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 파이프라인 실행의 총 수입니다. |
| TriggerCancelledRuns                  | 취소 된 트리거 실행 메트릭            | 개수    | 합계                | 1 분 기간 내에 취소 된 트리거 실행의 총 수입니다. |
| TriggerFailedRuns                    | 실패한 트리거 실행 메트릭              | 개수    | 합계                | 1분 기간 내에 실패한 트리거 실행의 총 수입니다. |
| TriggerSucceededRuns                 | 성공한 트리거 실행 메트릭           | 개수    | 합계                | 1분 기간 내에 성공한 트리거 실행의 총 수입니다. |
| SSISIntegrationRuntimeStartCancelled  | 취소 된 SSIS 통합 런타임 시작 메트릭           | 개수    | 합계                | 1 분 기간 내에 취소 된 총 SSIS 통합 런타임 수입니다. |
| SSISIntegrationRuntimeStartFailed    | 실패한 SSIS 통합 런타임 시작 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 SSIS 통합 런타임 시작의 총 수입니다. |
| SSISIntegrationRuntimeStartSucceeded | 성공한 SSIS 통합 런타임 시작 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 통합 런타임 시작의 총 수입니다. |
| SSISIntegrationRuntimeStopStuck      | 중단된 SSIS 통합 런타임 중지 메트릭               | 개수    | 합계                | 1분 기간 내에 중단된 SSIS 통합 런타임 중지의 총 수입니다. |
| SSISIntegrationRuntimeStopSucceeded  | 성공한 SSIS 통합 런타임 중지 메트릭           | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 통합 런타임 중지의 총 수입니다. |
| SSISPackageExecutionCancelled         | 취소 된 SSIS 패키지 실행 메트릭  | 개수    | 합계                | 1 분 기간 내에 취소 된 SSIS 패키지 실행의 총 수입니다. |
| SSISPackageExecutionFailed           | 실패한 SSIS 패키지 실행 메트릭    | 개수    | 합계                | 1분 기간 내에 실패한 SSIS 패키지 실행의 총 수입니다. |
| SSISPackageExecutionSucceeded        | 성공한 SSIS 패키지 실행 메트릭 | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 패키지 실행의 총 수입니다. |
| PipelineElapsedTimeRuns | 경과된 시간 파이프라인이 메트릭을 실행합니다. | 개수 | 합계 | 1분 이내에 파이프라인이 사용자 정의 예상 기간보다 오래 실행되는 횟수입니다. [(자세히 참조)](tutorial-operationalize-pipelines.md) |

이 메트릭에 액세스하려면 [Azure Monitor 데이터 플랫폼](../azure-monitor/data-platform.md)의 지침을 완료하세요.

> [!NOTE]
> _PipelineElapsedTimeRuns를_ 제외하고 완료되고 트리거된 작업 및 파이프라인 실행의 이벤트만 내보내됩니다. 진행 중 및 디버그 실행은 *내보내지지 않습니다.* 반면, *모든* SSIS 패키지 실행의 이벤트는 호출 메서드에 관계 없이 완료된 이벤트 및 진행 중인 이벤트를 포함하여 내보내집니다. 예를 들어 azure 지원 SQL Server Data Tools, SQL Server Management Studio, SQL Server 에이전트 또는 기타 지정된 도구에서 T-SQL 통해, Data Factory 파이프라인에서 SSIS 패키지 실행 작업의 트리거 또는 디버그 실행으로 패키지 실행을 호출할 수 있습니다.

## <a name="data-factory-alerts"></a>Data Factory 경고

Azure Portal 로그인하고 경고   >  **모니터링을** 선택하여 경고를 만듭니다.

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="포털 메뉴의 경고를 보여 주는 스크린샷.":::

### <a name="create-alerts"></a>경고 만들기

1. **+ 새 경고 규칙을** 선택하여 새 경고를 만듭니다.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="새 경고 규칙 만들기를 보여 주는 스크린샷":::

1. 경고 조건을 정의합니다.

    > [!NOTE]
    > **리소스 종류별 필터링** 드롭다운 목록에서 **모두를** 선택해야 합니다.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="리소스를 선택하기 위한 창을 열기 위한 선택 항목을 보여 주는 스크린샷":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="신호 논리를 구성하기 위한 창을 열기 위한 선택 항목을 보여 주는 스크린샷.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="신호 논리 구성을 보여 주는 스크린샷":::

1. 경고 세부 정보를 정의합니다.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="경고 세부 정보를 보여 주는 스크린샷.":::

1. 작업 그룹을 정의합니다.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="새 작업 그룹이 강조 표시된 규칙 만들기를 보여 주는 스크린샷.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="새 작업 그룹 만들기를 보여 주는 스크린샷":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="이메일, SMS, 푸시 및 음성 구성을 보여 주는 스크린샷.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="작업 그룹 정의를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

[진단 설정 및 작업 영역 구성](monitor-configure-diagnostics.md)
