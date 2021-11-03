---
title: Azure Stream Analytics에서 Machine Learning 스튜디오(클래식) 함수 스케일링
description: 이 아티클에서는 분할 및 스트림 단위를 구성하여 Microsoft Azure Machine Learning 스튜디오(클래식) 함수를 사용하는 Stream Analytics 작업을 스케일링하는 방법을 설명합니다.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 8d7c498f0052a3e0da024e8b4902579ab8a98fd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083292"
---
# <a name="scale-your-stream-analytics-job-with-machine-learning-studio-classic-functions"></a>Machine Learning Studio (클래식) 함수를 사용 하 여 Stream Analytics 작업 크기 조정

> [!TIP]
> 향상 된 성능 및 안정성을 위해 클래식 (Machine Learning Studio) udf 대신 [Azure Machine Learning udf](machine-learning-udf.md) 를 사용 하는 것이 좋습니다.

이 문서에서는 Machine Learning Studio (클래식) 함수를 사용 하는 Azure Stream Analytics 작업을 효율적으로 확장 하는 방법을 설명 합니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Stream Analytics의 스튜디오(클래식) 함수란 무엇인가요?

Stream Analytics의 Machine Learning 스튜디오(클래식) 함수는 Stream Analytics 쿼리 언어에 일반 함수 호출처럼 사용할 수 있습니다. 그러나 내부를 들여다보면, 함수 호출이 실제로는 스튜디오(클래식) 웹 서비스 요청입니다.

하나의 웹 서비스 API 호출에서 여러 행을 “일괄 처리”하여 스튜디오(클래식) 웹 서비스 요청의 처리량을 개선할 수 있습니다. 이러한 그룹화를 미니 매치라고 합니다. 자세한 내용은 [Machine Learning Studio (클래식) 웹 서비스](../machine-learning/classic/consume-web-services.md)를 참조 하세요. Stream Analytics에서 스튜디오(클래식)를 지원합니다.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>스튜디오(클래식) 함수를 사용하여 Stream Analytics 작업 구성

Stream Analytics 작업에서 사용하는 스튜디오(클래식) 함수를 구성하는 데는 다음과 같은 두 가지 매개 변수가 사용됩니다.

* 스튜디오(클래식) 함수 호출의 배치 크기.
* Stream Analytics 작업에 대해 프로비저닝된 SU(스트리밍 단위)의 수.

SU의 적절한 값을 정하려면 Stream Analytics 작업의 대기 시간을 최적화할지 아니면 각 SU의 처리량을 최적화할지 판단하세요. SU는 잘 파티션된 Stream Analytics 쿼리의 처리량을 개선하기 위해 작업에 항상 추가할 수 있습니다. 추가 SU가 있으면 작업 실행에 소요되는 비용이 늘어납니다.

Stream Analytics 작업의 대기 시간 ‘허용 범위’를 정합니다. 배치 크기를 늘리면 스튜디오(클래식) 요청의 대기 시간과 Stream Analytics 작업의 대기 시간이 늘어납니다.

배치 크기를 늘리면 Stream Analytics 작업이 **동일한 수** 의 스튜디오(클래식) 웹 서비스 요청으로 **더 많은 이벤트** 를 처리할 수 있습니다. 스튜디오(클래식) 웹 서비스 대기 시간은 배치 크기에 비례하여 거의 선형적으로 증가합니다. 

따라서 어떤 상황에서도 스튜디오(클래식) 웹 서비스에 가장 경제적인 배치 크기를 고려해야 합니다. 웹 서비스 요청의 기본 배치 크기는 1000입니다. 이 기본값은 [Stream Analytics REST API](/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") 또는 [Stream Analytics용 PowerShell 클라이언트](stream-analytics-monitor-and-manage-jobs-use-powershell.md)를 사용하여 변경할 수 있습니다.

배치 크기를 정했으면 함수가 초당 처리해야 하는 이벤트 수를 기준으로 SU(스트리밍 단위)의 수를 설정할 수 있습니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

SU 6개마다 스튜디오(클래식) 웹 서비스에 대한 20개의 동시 연결을 갖습니다. 그러나 1개의 SU 작업과 3개의 SU 작업은 20개의 동시 연결을 갖습니다.  

애플리케이션이 초당 200,000개의 이벤트를 생성하고 배치 크기가 1000이라면 웹 서비스 대기 시간은 200ms가 됩니다. 이 속도는 모든 연결이 스튜디오(클래식) 웹 서비스에 초당 5건의 요청을 보낼 수 있음을 의미합니다. 연결이 20개이면 Stream Analytics 작업은 200ms 동안 20,000개의 이벤트를 처리할 수 있으므로 초당 100,000개의 이벤트를 처리할 수 있습니다.

초당 200,000개의 이벤트를 처리하려면 Stream Analytics 작업에 동시 연결 40개가 필요하고, 12개의 SU가 필요하다는 결론이 나옵니다. 다음 다이어그램은 Stream Analytics 작업에서 스튜디오(클래식) 웹 서비스 엔드포인트로 전송되는 요청을 보여줍니다. SU 6개마다 스튜디오(클래식) 웹 서비스에 대한 최대 동시 연결 20개를 가집니다.

![스튜디오(클래식) 함수 작업 2개를 사용하여 Stream Analytics 스케일링 예제](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "스튜디오(클래식) 함수 작업 2개를 사용하여 Stream Analytics 스케일링 예제")

일반적으로 배치 크기가 ***B** 이고, 배치 크기 B의 웹 서비스 대기 시간이 _*_L_*_ 밀리초이면 SU _ *_N_**개가 포함된 Stream Analytics 작업의 처리량은 다음과 같습니다.

![스튜디오(클래식) 함수 수식을 사용하여 Stream Analytics 스케일링](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "스튜디오(클래식) 함수 수식을 사용하여 Stream Analytics 스케일링")

스튜디오(클래식) 웹 서비스에서 ‘최대 동시 호출’을 구성할 수도 있습니다. 이 매개 변수는 최댓값(현재 200)으로 설정하는 것이 좋습니다.

해당 설정에 대한 자세한 내용은 [Machine Learning 스튜디오(클래식) 웹 서비스에 대한 스케일링 문서](../machine-learning/classic/create-endpoint.md)를 참조하세요.

## <a name="example--sentiment-analysis"></a>예 – 정서 분석
다음 예제는 [Stream Analytics Machine Learning 스튜디오(클래식) 통합 자습서](stream-analytics-machine-learning-integration-tutorial.md)에 설명된 대로 감정 분석 스튜디오(클래식) 함수를 사용하는 Stream Analytics 작업을 포함하고 있습니다.

다음 예제에 표시된 대로 완전히 분할된 간단한 쿼리 다음에 **sentiment** 함수가 옵니다.

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

초당 10,000개의 트윗을 대상으로 감정 분석을 수행하는 Stream Analytics 작업을 만드는 데 필요한 구성을 살펴보겠습니다.

1개의 SU를 사용할 경우 이 Stream Analytics 작업에서 트래픽을 처리할 수 있을까요? 이 작업은 기본 배치 크기 1000을 사용하여 입력을 처리할 수 있습니다. 감정 분석 스튜디오(클래식) 웹 서비스(기본 배치 크기 1000)의 기본 대기 시간으로 인해 발생하는 대기 시간은 1초를 넘지 않습니다.

Stream Analytics 작업의 **전체적인** 또는 엔드투엔드 대기 시간은 일반적으로 몇 초입니다. 해당 Stream Analytics 작업, ‘특히’ 스튜디오(클래식) 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 배치 크기가 1000이면 10,000개의 이벤트를 처리하려면 웹 서비스로 약 10개의 요청을 보내야 합니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 배치 크기를 늘립니다.
2. 입력 스트림을 분할하여 이벤트를 병렬로 처리합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간** 이 증가합니다.

두 번째 옵션을 사용하면 더 많은 동시 스튜디오(클래식) 웹 서비스 요청을 가질 수 있도록 더 많은 SU를 프로비저닝해야 합니다. 이처럼 SU 수를 늘리면 작업 **비용** 이 늘어납니다.

각 배치 크기의 대기 시간 측정값을 사용하여 크기 조정을 살펴보겠습니다.

| 대기 시간 | Batch 크기 |
| --- | --- |
| 200ms | 1000개 이하의 이벤트로 구성된 배치 |
| 250ms | 5,000개 이벤트로 구성된 배치 |
| 300ms | 10,000개 이벤트로 구성된 배치 |
| 500ms | 25,000개 이벤트로 구성된 배치 |

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음**) 일괄 처리 크기가 **25,000** 으로 늘어날 수 있습니다. 해당 방식으로 배치 크기를 늘리면 작업에서 스튜디오(클래식) 웹 서비스에 대한 동시 연결 20개(호출당 대기 시간 500ms)를 사용하여 1,000,000개 이벤트를 처리할 수 있습니다. 스튜디오(클래식) 웹 서비스 요청에 대한 감정 함수 요청으로 인해 Stream Analytics 작업의 대기 시간이 **200ms** 에서 **500ms** 로 증가합니다. 그러나 배치 크기를 무한정 늘릴 수는 **없습니다**. 스튜디오(클래식) 웹 서비스는 작업 후 100초가 지나면 웹 서비스 요청 시간이 초과하므로 요청의 페이로드 크기가 4MB 이하여야 하기 때문입니다.
1. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

아래는 SU 및 배치 크기(초당 이벤트 수)에 따른 Stream Analytics 작업의 처리량을 보여 주는 테이블입니다.

| 배치 크기(ML 대기 시간) | 500(200ms) | 1,000(200ms) | 5,000(250ms) | 10,000(300ms) | 25,000(500ms) |
| --- | --- | --- | --- | --- | --- |
| **1SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12SU** |5,000 |10000 |40,000 |60,000 |100,000 |
| **18SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24SU** |10000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

이제 Stream Analytics 작업에서 스튜디오(클래식) 함수가 어떻게 작동하는지 충분히 이해하셨을 것입니다. 또한 Stream Analytics 작업이 데이터 원본에서 데이터를 “끌어오며” 각 “끌어오기”는 Stream Analytics 작업에서 처리할 이벤트 일괄 처리를 반환한다는 내용도 이해하셨을 것입니다. 끌어오기 모델이 스튜디오(클래식) 웹 서비스 요청에 어떤 영향을 미칠까요?

일반적으로 스튜디오(클래식) 함수에 대해 설정하는 배치 크기를 각 Stream Analytics 작업의 “끌어오기”에서 반환하는 이벤트 수로 나누어서 딱 떨어지는 경우는 별로 없습니다. 해당 경우 스튜디오(클래식) 웹 서비스가 “부분” 배치를 통해 호출됩니다. 부분 배치를 사용하면 끌어오기 간에 이벤트를 병합할 때 추가 작업 대기 시간 오버헤드가 발생하지 않습니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 이 세 개 함수는 아래 그림에 나와 있는 것처럼 **FUNCTION REQUESTS**, **FUNCTION EVENTS** 및 **FAILED FUNCTION REQUESTS** 입니다.

![스튜디오(클래식) 함수 메트릭을 사용하여 Stream Analytics 스케일링](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "스튜디오(클래식) 함수 메트릭을 사용하여 Stream Analytics 스케일링")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS**: 함수 요청의 수

**FUNCTION EVENTS**: 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS**: 실패한 함수 요청의 수

## <a name="key-takeaways"></a>핵심 내용

스튜디오(클래식) 함수를 사용하여 Stream Analytics 작업을 스케일링하려면 다음과 같은 요인을 고려해야 합니다.

1. 입력 이벤트 속도
2. 실행 중인 Stream Analytics 작업에 허용되는 대기 시간(따라서 스튜디오(클래식) 웹 서비스 요청의 배치 크기).
3. 프로비저닝된 Stream Analytics SU 및 스튜디오(클래식) 웹 서비스 요청 수(추가 함수 관련 비용).

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)에서 Stream Analytics 팀의 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)
