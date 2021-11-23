---
title: Power BI를 사용하여 AI 추론 이벤트를 실시간으로 시각화
description: 연속 비디오 녹화 또는 이벤트 기반 기록을 위해 Azure Video Analyzer를 사용할 수 있습니다. 이 자습서에서는 Microsoft Power BI의 IoT Hub에서 AI 추론 이벤트를 실시간으로 시각화하는 단계를 안내합니다.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a128ea8a8894464016777705af014430ff3efc65
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938725"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>자습서: Power BI를 사용하여 AI 추론 이벤트를 실시간으로 시각화

.Azure Video Analyzer는 비디오 분석 결과를 AI 추론 이벤트의 형태로 [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)에 게시하는 기능과 함께 라이브 비디오를 캡처, 기록, 분석하는 기능을 제공합니다. 게시된 AI 추론 이벤트는 Visual Studio Code 및 Time Series Insights나 Event Hubs 같은 Azure 서비스를 비롯한 다른 대상으로 라우팅할 수 있습니다.

대시보드를 통해 비즈니스를 모니터링하고 가장 중요한 모든 메트릭을 한눈에 볼 수 있습니다. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview)를 통해 [Microsoft Power BI](https://powerbi.microsoft.com/)를 사용하여 Video Analyzer에서 생성한 AI 추론 이벤트를 시각화하면 신속하게 인사이트를 얻고 조직 내 동료들과 대시보드를 공유할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.svg" alt-text="Azure Stream Analytics를 통해 Azure Video Analyzer를 Microsoft Power BI에 연결하는 블록 다이어그램":::

이 자습서에서는 다음을 수행합니다.

- IoT Hub에서 필요한 데이터를 검색하여 Power BI로 보내는 Stream Analytics 작업을 만들고 실행
- 추론 이벤트를 생성하는 라이브 파이프라인 실행
- AI 추론을 시각화하는 Power BI 대시보드 만들기

## <a name="suggested-pre-reading"></a>추천 참고 자료

- Video Analyzer에서 [모니터링 및 로깅](monitor-log-edge.md)
- [IoT Hub 기본 제공 엔드포인트에서 디바이스-클라우드 메시지](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) 읽기
- [Power BI 대시보드](/power-bi/create-reports/service-dashboards) 소개

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 이 자습서는 라이브 비디오 피드에서 개체가 가상의 선과 교차할 때 이를 감지하는 [선 교차 샘플](use-line-crossing.md) 사용을 기반으로 합니다. 다른 파이프라인에 대한 시각화를 만들도록 선택할 수 있습니다(**IoT Hub 메시지 싱크가 있는 파이프라인이 필요**). 라이브 파이프라인이 생성되었는지 확인하고, Stream Analytics 작업을 만든 후에만 라이브 파이프라인을 활성화합니다.

  > [!TIP]
  >
  > - [선 교차 샘플](use-line-crossing.md)은 5분 길이의 비디오 녹화본을 사용합니다. 시각화에서 최상의 결과를 얻으려면 [다른 데이터 세트](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset)에서 제공하는 60분 길이의 고속도로 위 차량 녹화본을 사용하세요.
  > - rtsp 시뮬레이터에 샘플 비디오 파일을 추가하는 방법은 [FAQ](https://github.com/MicrosoftDocs/azure-docs-pr/pull/edge/faq.yml)의 구성 및 배포 섹션을 참조하세요. 추가한 후에는 새 비디오 파일을 가리키도록 `rtspUrl` 값을 편집합니다.
  > - 선 교차 샘플을 진행했고 [AVA C# 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)를 사용하는 경우에는 속성 -> 매개 변수 -> 값에서 operations.json 파일을 `"rtsp://rtspsim:554/media/camera-3600s.mkv"`로 편집하여 비디오 원본을 60분 길이의 녹화본으로 변경합니다.

- [Power BI](https://powerbi.microsoft.com/) 계정

## <a name="create-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기 및 실행

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview)는 빠르게 이동하는 데이터 스트림을 분석하고 처리하는 데 도움을 주도록 설계된 완전 관리형 실시간 분석 서비스입니다. 이 섹션에서는 Stream Analytics 작업을 만들고, 필요한 데이터를 검색하는 데 사용되는 입력, 출력 및 쿼리를 정의합니다.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** 를 선택합니다. 검색 상자에 _Stream Analytics Job_ 을 입력하고 드롭다운 목록에서 선택합니다. **Stream Analytics 작업** 개요 창에서 **만들기** 를 선택합니다.
2. 작업에 대해 다음 정보를 입력합니다.

   - **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.
   - **구독**: 선 교차 샘플을 설정하는 데 사용되는 것과 동일한 구독을 선택합니다.
   - **리소스 그룹**: IoT Edge 허브에서 선 교차 샘플을 설정할 때 사용하는 것과 동일한 리소스 그룹을 사용합니다.
   - **위치**: 리소스 그룹과 동일한 위치를 사용합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="새 Stream Analytics 작업을 만드는 스크린샷":::

3. **만들기** 를 선택합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.
2. [작업 토폴로지]에서 **입력** 을 선택합니다.
3. 입력 창에서 **스트림 입력 추가** 를 선택하고 드롭다운 목록에서 **IoT Hub** 를 선택합니다. 새 입력 창에서 다음 정보를 입력합니다.

   - **입력 별칭**: 입력의 고유한 별칭을 입력합니다(예: "iothubinput").
   - **구독에서 IoT Hub를 선택합니다.** : 이 라디오 단추를 선택합니다.
   - **구독**: 이 자습서에 사용 중인 Azure 구독을 선택합니다.
   - **IoT Hub**: 선 교차 샘플을 설정하는 데 사용되는 IoT Hub를 선택합니다.
   - **공유 액세스 정책 이름**: Stream Analytics 작업에서 IoT 허브에 사용할 공유 액세스 정책의 이름을 선택합니다. 이 자습서에서는 iothubowner를 선택할 수 있습니다. 자세히 알아보려면 [액세스 제어 및 권한](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.
   - **공유 액세스 정책 키**: 이 필드는 선택한 공유 액세스 정책 이름에 따라 자동으로 채워집니다.

   나머지 필드는 기본값으로 둡니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="Stream Analytics 작업에 IoT Hub 입력을 추가하는 스크린샷":::

4. **저장** 을 선택합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. [작업 토폴로지]에서 **출력** 을 선택합니다.
2. 출력 창에서 **추가** 를 선택한 다음, 드롭다운 목록에서 **Power BI** 를 선택합니다.
3. Power BI - 새 출력 창에서 다음을 수행합니다.

   - **출력 별칭**: 출력의 고유한 별칭을 입력합니다(예: "powerbioutput").
   - **그룹 작업 영역**: 대상 그룹 작업 영역을 선택합니다.
   - **인증 모드**: 테스트에 사용할 것이므로 기본값인 "사용자 토큰"으로 둡니다.

   > [!NOTE]
   > 프로덕션 작업에서는 [관리 ID를 사용하여 Stream Analytics 작업을 Power BI에 인증](../../stream-analytics/powerbi-output-managed-identity.md)하는 것이 좋습니다.

   - **데이터 세트 이름**: 데이터 세트 이름을 입력합니다.
   - **테이블 이름**: 테이블 이름을 입력합니다.
   - **권한 부여**: [권한 부여]를 선택하고 프롬프트의 지시에 따라 Power BI 계정에 로그인합니다(토큰은 90일간 유효함).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="Stream Analytics 작업에 Power BI 출력을 추가하는 스크린샷":::

4. **저장** 을 선택합니다.

   > [!NOTE]
   > Stream Analytics 작업의 출력으로 Power BI를 사용하는 방법에 대한 자세한 내용을 보려면 [여기](../../stream-analytics/power-bi-output.md)를 클릭하세요. Power BI 계정의 [인증 갱신](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization)에 대해 자세히 알아보세요.

### <a name="configure-the-query-for-stream-analytics-job"></a>Stream Analytics 작업에 대한 쿼리 구성

1. [작업 토폴로지]에서 **쿼리** 를 선택합니다.
2. 쿼리를 다음과 같이 변경합니다.

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - 위의 쿼리에서 FROM 절의 _i_ 는 _추론_ 배열에서 중첩되지 않는 EventProcessedUtcTime 값을 가져오기 위해 구문상 필요합니다.
   >   위의 쿼리는 [선 교차 자습서](use-line-crossing.md)에 대한 AI 추론을 가져오도록 사용자 지정되었습니다.
   > - 다른 파이프라인을 실행하는 경우 파이프라인의 AI 추론 스키마에 따라 쿼리를 사용자 지정해야 합니다. [Stream Analytics 작업에서 JSON 구문 분석](../../stream-analytics/stream-analytics-parsing-json.md)에 대해 자세히 알아보세요.

3. [YourOutputAlias]를 Stream Analytics 작업에 출력을 추가하는 단계에서 사용된 출력 별칭(예: "powerbioutput")으로 바꿉니다. 출력 및 입력 별칭의 올바른 순서를 확인합니다.
4. [YourInputAlias]를 Stream Analytics 작업에 입력을 추가하는 단계에서 사용된 입력 별칭(예: "iothubinput")으로 바꿉니다.
5. 쿼리는 다음 스크린샷과 비슷합니다. **쿼리 테스트** 를 클릭하여 테스트 결과를 EventTotal 및 해당 EventProcessedUtcTime 값 테이블로 확인합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="Stream Analytics 작업에서 쿼리를 테스트하고 저장하는 스크린샷":::

6. **쿼리 저장** 을 선택합니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 [개요]를 선택한 다음, **시작** > **지금** > **시작** 을 선택합니다. 작업이 성공적으로 시작되면 작업 상태가 [생성됨]에서 **실행 중** 으로 변경됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="Stream Analytics 작업을 시작하고 실행하는 스크린샷":::

> [!TIP]
> IoT Hub를 사용하면 기본 제공 Event Hubs에 데이터가 기본적으로 1일간 보존되며 최대 7일까지 보존할 수 있습니다. IoT Hub를 만드는 중에 보존 시간을 설정할 수 있습니다. IoT Hub의 데이터 보존 시간은 선택하는 계층과 단위의 종류에 따라 달라집니다. 자세한 내용을 보려면 [여기](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)를 클릭하세요. 데이터를 더 오래 보존하려면 [Azure Storage를 출력으로](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md) 사용하고, Power BI을 스토리지 계정의 파일에 연결합니다.

## <a name="run-a-sample-pipeline"></a>샘플 파이프라인 실행

위의 단계에서 만든 Stream Analytics 작업이 **실행 중** 상태이면 선 교차 자습서의 [샘플 프로그램 실행](use-line-crossing.md#run-the-sample-program) 섹션으로 이동하여 라이브 파이프라인을 활성화합니다. 라이브 파이프라인은 AI 추론 결과를 IoT Hub로 전송하기 시작하고, 전송된 결과를 Stream Analytics 작업에서 선택합니다.

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>AI 이벤트를 시각화하는 Power BI 대시보드 만들기

Power BI에서 다음과 같은 2가지 방법으로 스트리밍 데이터를 시각화할 수 있습니다.

1. 스트리밍 데이터 세트를 위해 만든 테이블에서 보고서를 만들고 대시보드에 고정
2. 사용자 지정 스트리밍 데이터 세트가 있는 대시보드 타일

   > [!NOTE]
   > 이 문서에서는 첫 번째 방법을 사용하여 보고서를 만든 다음, 대시보드에 고정합니다. 이 방법은 시각적 개체에 대한 데이터를 더 오래 유지하고 수신 데이터를 기반으로 자동 집계합니다. 두 번째 방법에 대한 자세한 내용은 [Power BI에서 사용자 지정 스트리밍 데이터 세트 설정](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)을 참조하세요.

### <a name="create-and-publish-a-power-bi-report"></a>Power BI 보고서 만들기 및 게시

다음 단계에서는 Power BI 서비스를 사용하여 보고서를 만들고 게시하는 방법을 보여 줍니다.

1. 샘플 파이프라인이 디바이스에서 활성화되었는지 확인합니다(파이프라인을 실행하는 이전 단계에서 시작됨).
2. [Power BI 계정](https://powerbi.microsoft.com/)에 로그인하고 상단 메뉴에서 **Power BI 서비스** 를 선택합니다.
3. 측면 메뉴에서 사용한 작업 영역을 선택합니다.
4. **전체** 탭 또는 **데이터 세트 + 데이터 흐름** 탭에 _Stream Analytics 작업에 출력 추가_ 단계에서 지정한 데이터 세트가 표시됩니다.
5. Stream Analytics 출력을 만들 때 입력한 데이터 세트 이름을 마우스 커서로 가리키고 **추가 옵션** 메뉴(데이터 세트 이름 오른쪽에 있는 점 3개)를 선택한 다음, **보고서 만들기** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="Power BI에서 보고서를 만드는 스크린샷":::

6. 선 교차 이벤트를 실시간으로 보여주는 꺾은선형 차트를 만듭니다.

   - 보고서 만들기 페이지의 **시각화** 창에서 **꺾은선형 차트** 아이콘을 선택하여 꺾은선형 차트를 추가합니다.
   - **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다.
   - **시각화** 창에서 **EventProcessedUtcTime** 을 **축** 으로 끌어서 놓습니다.
   - **EventTotal** 을 **값** 으로 끌어서 놓습니다.
   - 꺾은선형 차트가 만들어집니다. x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 라이브 파이프라인에서 EventTotal의 **합계** 에 대한 자동 요약 값을 표시합니다. EventTotal의 가장 최근 값을 얻으려면 값을 EventTotal **최댓값** 으로 변경합니다. 평균, 개수(고유) 등을 표시하도록 집계 함수를 변경할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Power BI의 선 교차 보고서를 보여주는 스크린샷":::

7. 오른쪽 위에서 **저장** 을 클릭하여 보고서를 저장합니다.

### <a name="pin-visual-to-a-dashboard"></a>대시보드에 시각적 개체 고정

오른쪽 위에서 **대시보드에 고정** 을 클릭하고 이 시각적 개체를 고정할 위치(기존 대시보드 또는 새 대시보드)를 선택한 다음, 선택한 내용에 따라 프롬프트의 지시를 따릅니다.

필요한 경우 보고서와 나란히 비디오를 재생하도록 [플레이어 위젯을 포함](embed-player-in-power-bi.md)할 수도 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="보고서가 고정되고 위젯 플레이어가 타일에 추가된 Power BI 대시보드":::

> [!NOTE]
> 보고서를 생성하는 데 사용된 방법 때문에 비디오 재생과 보고서 만들기가 동기화되지 않습니다. 좀 더 실시간에 가까운 환경을 원하는 경우 [Power BI에서 사용자 지정 스트리밍 데이터 세트 설정](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)을 참조하세요.

## <a name="interpret-the-dashboard"></a>대시보드 해석

선 교차 프로세서 노드는 토폴로지에서 lineCoordinates 매개 변수를 사용하여 지정된 선을 개체가 교차할 때 이를 감지합니다. 개체가 이러한 좌표와 교차할 때 다음 항목을 통해 이벤트가 트리거됩니다.

- EventTotal: 비디오가 시작된 후 지금까지 모든 개체가 모든 방향(시계 방향 또는 반시계 방향)에서 선과 교차한 총 횟수입니다. [선 교차 이벤트 추론](use-line-crossing.md#line-crossing-events)에 대해 자세히 알아보세요.
- EventProcessedUTCTime: 각 이벤트가 Stream Analytics 작업에서 처리된 시간입니다. 또는 추가 처리가 가능한 IoT Hub에 이벤트가 도달한 시간을 나타내는 EventEnqueuedUtcTime을 가져오도록 Stream Analytics 쿼리를 사용자 지정할 수도 있습니다.

위의 대시보드에서, 시간이 지나면서 가상의 선과 교차하는 개체가 늘어날수록 **EventTotal** 이 점점 증가하는 것을 볼 수 있습니다. 이 시각화를 사용하면 예를 들어 오후 3:52:00~오후 3:53:30 사이에 고속도로를 통행하는 차량이 많다는 것을 신속하게 확인할 수 있습니다. 그런 다음, 이러한 인사이트를 사용하여 특정 시간에 고속도로가 정체되는 원인에 대한 분석 범위를 좁힐 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 유지합니다. 그렇지 않으면 Azure Portal로 이동하고, 리소스 그룹으로 이동하고, 이 문서의 필수 구성 요소를 실행하고 Stream Analytics 작업을 만든 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

Power BI에서 **LineCrossingDataset** 데이터 세트를 만들었습니다. 제거하려면 [Power BI](https://powerbi.microsoft.com/) 계정에 로그인합니다. 왼쪽 메뉴의 **작업 영역** 에서 사용한 작업 영역을 선택합니다. **데이터 세트 + 데이터 흐름** 탭의 데이터 세트 목록에서 데이터 세트를 마우스 커서로 가리킵니다. 데이터 세트 오른쪽에 있는 세로로 나열된 점 3개를 선택하여 **추가 옵션** 메뉴를 연 다음, **삭제** 를 선택하고 프롬프트를 따릅니다. 데이터 세트를 제거하면 보고서도 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [플레이어 위젯을 Power BI에 포함](embed-player-in-power-bi.md)하여 비디오 재생을 원격 분석과 결합
- [모니터링 및 로깅 이벤트](monitor-log-edge.md)에 대해 자세히 알아보기
