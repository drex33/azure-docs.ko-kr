---
title: Visual Studio Code에서 작업 다이어그램을 사용하여 로컬로 Azure Stream Analytics 쿼리 디버그
description: 이 문서에서는 Visual Studio Code용 Azure Stream Analytics 확장에서 작업 다이어그램을 사용하여 로컬로 쿼리를 디버깅하는 방법을 설명합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: bfa3e378e9627652810eb6ac1feaeb7b74522539
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529356"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code-preview"></a>Visual Studio Code(미리 보기)에서 작업 다이어그램을 사용하여 로컬로 Azure Stream Analytics 쿼리 디버그

결과를 출력하지 않거나 예기치 않은 결과를 출력하는 스트리밍 작업의 경우 문제를 해결해야 할 수 있습니다. Azure Stream Analytics용 Visual Studio Code 확장은 작업 다이어그램, 메트릭, 진단 로그 및 중간 결과를 통합하여 문제의 원인을 신속하게 격리할 수 있도록 합니다. 쿼리를 로컬로 테스트하는 동안 작업 다이어그램을 사용하여 각 단계에 대한 중간 결과 집합 및 메트릭을 검사할 수 있습니다.

## <a name="debug-a-query-using-job-diagram"></a>작업 다이어그램을 사용하여 쿼리 디버그

Azure Stream Analytics 스크립트는 입력 데이터를 출력 데이터로 변환하는 데 사용됩니다. 작업 다이어그램은 입력 원본(예: Event Hub 또는 IoT Hub)에서 여러 쿼리 단계를 거쳐 최종적으로 출력 싱크까지 데이터가 흐르는 방식을 보여 줍니다. 각 쿼리 단계는 `WITH` 문을 사용하여 스크립트에 정의된 임시 결과 집합에 매핑됩니다. 각 중간 결과 집합의 각 쿼리 단계에 대한 메트릭뿐만 아니라 데이터를 보고 이슈의 원인을 찾을 수 있습니다.

> [!NOTE]
> 이 작업 다이어그램에 표시되는 데이터 및 메트릭은 단일 노드의 로컬 테스트 용도로만 사용해야 합니다. 성능 튜닝 및 문제 해결에는 사용하지 않아야 합니다.

### <a name="start-local-testing"></a>로컬 테스트 시작

이 [빠른 시작](quick-create-visual-studio-code.md)을 사용하여 Visual Studio Code에서 Stream Analytics 작업을 만들거나 [기존 작업을 로컬 프로젝트로 내보내는](visual-studio-code-explore-jobs.md) 방법을 알아봅니다. 입력 및 출력에 대한 자격 증명은 내보낸 작업에 대해 자동으로 채워집니다.

로컬 입력 데이터로 쿼리를 테스트하려면 다음 [지침](visual-studio-code-local-run.md)을 따르세요. 라이브 입력을 사용하여 테스트하려면 다음 단계로의 [입력 이동을 구성](stream-analytics-add-inputs.md)합니다. 

*\.asaql* 스크립트 파일을 열고 **로컬에서 실행** 을 선택합니다. 그런 다음, **로컬 입력 사용** 또는 **라이브 입력 사용** 을 선택합니다. 작업 다이어그램이 창 오른쪽에 나타납니다.

### <a name="view-the-output-and-intermediate-result-set"></a>출력 및 중간 결과 집합 보기  

1. 모든 작업 출력은 Visual Studio Code 창의 오른쪽 아래에 있는 결과 창에 표시됩니다.

   > [!div class="mx-imgBorder"]
   > ![작업 출력 결과](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. 쿼리 단계를 선택하여 스크립트로 이동합니다. 왼쪽의 편집기에 있는 해당 스크립트로 자동으로 이동됩니다. 중간 결과가 Visual Studio Code 창의 오른쪽 아래에 있는 결과 창에 표시됩니다.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 미리 보기 결과](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>메트릭 보기

이 섹션에서는 다이어그램의 각 부분에 사용할 수 있는 메트릭을 살펴봅니다.

1. Visual Studio Code 창의 오른쪽 아래에 있는 **결과** 탭 옆의 **메트릭** 탭을 선택합니다.

2. 드롭다운에서 **작업** 을 선택합니다. 그래프 노드에서 빈 공간을 선택하여 작업 수준 메트릭으로 이동할 수 있습니다. 이 보기에는 작업이 실행 중일 때 10초마다 업데이트되는 모든 메트릭이 포함됩니다. 오른쪽에서 메트릭을 선택 또는 선택 취소하여 차트에서 볼 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 메트릭](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 드롭다운 목록에서 입력 데이터 원본의 이름을 선택하여 입력 메트릭을 표시합니다. 아래 스크린샷에서는 입력 원본을 *quotes* 라고 합니다. 입력 메트릭에 대한 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)를 참조하세요.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 입력 메트릭](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. 작업 다이어그램에서 쿼리 단계를 선택하거나 드롭다운 목록에서 단계 이름을 선택하여 단계 수준 메트릭을 표시합니다. 워터마크 지연이 유일하게 사용할 수 있는 단계 메트릭입니다.

   > [!div class="mx-imgBorder"]
   > ![단계 메트릭](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. 다이어그램 또는 드롭다운에서 출력을 선택하여 출력 관련 메트릭을 표시합니다. 출력 메트릭에 대한 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)를 참조하세요. 라이브 출력 싱크는 지원되지 않습니다.

   > [!div class="mx-imgBorder"]
   > ![출력 메트릭](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>진단 로그 보기

작업 수준 진단 로그에는 입력 데이터 원본 및 출력 싱크에 대한 진단 정보가 포함됩니다. 입력 노드 또는 출력 노드를 선택하면 해당 로그만 표시됩니다. 쿼리 단계를 선택하면 로그가 표시되지 않습니다. 작업 수준에서 모든 로그를 찾을 수 있으며 심각도 및 시간별로 로그를 필터링할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![진단 로그](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   로그 항목을 선택하여 전체 메시지를 확인합니다.

   > [!div class="mx-imgBorder"]
   > ![진단 로그 메시지](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>기타 작업 다이어그램 기능

필요에 따라 도구 모음에서 **중지** 또는 **일시 중지** 를 선택할 수 있습니다. 작업이 일시 중지되면 마지막 출력에서 다시 시작할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![작업 중지 또는 일시 중지](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

작업 다이어그램의 오른쪽 위에서 **작업 요약** 을 선택하여 로컬 작업에 대한 속성 및 구성을 확인합니다.

> [!div class="mx-imgBorder"]
> ![로컬 작업 요약](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>제한 사항

* 라이브 출력 싱크는 로컬 실행에서 지원되지 않습니다.

* JavaScript 함수를 사용하여 작업을 로컬로 실행하는 것은 Windows 운영 체제에서만 지원됩니다.

* C # 사용자 지정 코드 및 Azure Machine Learning 함수는 지원되지 않습니다. 

* 클라우드 입력 옵션만 [시간 정책](./stream-analytics-time-handling.md)을 지원하고, 로컬 입력 옵션은 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code를 사용하여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 살펴보기](visual-studio-code-explore-jobs.md)
* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용하여 라이브 입력에 대해 로컬로 Azure Stream Analytics 작업 테스트](visual-studio-code-local-run-live-input.md)