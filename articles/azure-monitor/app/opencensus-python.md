---
title: Azure Monitor로 Python 애플리케이션 모니터링 | Microsoft Docs
description: OpenCensus Python과 Azure Monitor를 연결하는 지침을 제공합니다.
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
author: lzchen
ms.author: lechen
ms.openlocfilehash: 14aef56cc84b505afcc58f7f3662ed133bf829c5
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162706"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python 애플리케이션용 Azure Monitor 설정

Azure Monitor는 분산 추적, 메트릭 컬렉션 및 Python 애플리케이션의 로깅을 지원합니다.

Python 애플리케이션에 대한 데이터를 추적하고 내보내기 위해 Microsoft에서 지원하는 솔루션은 [Azure Monitor 내보내기](#instrument-with-opencensus-python-sdk-with-azure-monitor-exporters)를 통한 [Opencensus Python SDK](#introducing-opencensus-python-sdk)입니다.

Python용 다른 원격 분석 SDK는 지원되지 않으며 원격 분석 솔루션으로 사용하는 것을 Microsoft에서 권장하지 않습니다.

OpenCensus가 [OpenTelemetry](https://opentelemetry.io/)로 통합되고 있음을 인지했을 수도 있습니다. 그러나 OpenTelemetry가 점진적으로 완성되는 동안 OpenCensus가 계속 권장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Python 설치. 다른 버전의 경우 약간 변경되어 작동할 가능성이 있으므로 이 문서에서는 [Python 3.7.0](https://www.python.org/downloads/release/python-370/)을 사용합니다. Opencensus Python SDK는 Python v2.7 및 v3.4 이상만 지원합니다.
- Application Insights [리소스](./create-new-resource.md)를 만듭니다. 리소스에 대한 자체 계측 키(ikey)가 할당됩니다.

## <a name="introducing-opencensus-python-sdk"></a>Opencensus Python SDK 소개

[OpenCensus](https://opencensus.io)는 분산 추적, 메트릭 및 로깅 원격 분석을 수집할 수 있는 오픈 소스 라이브러리 집합입니다. [Azure Monitor 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)를 사용하여 수집된 원격 분석을 Application Insights에 보낼 수 있습니다. 이 문서에서는 Python에 대해 OpenCensus 및 Azure Monitor 내보내기를 설정하고 Azure Monitor로 모니터링 데이터를 전송하는 프로세스를 단계별로 안내합니다.

## <a name="instrument-with-opencensus-python-sdk-with-azure-monitor-exporters"></a>Azure Monitor 내보내기용 OpenCensus Python SDK를 사용한 계측

OpenCensus Azure Monitor 내보내기를 설치합니다.

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 명령은 Python 설치에 대해 `PATH` 환경 변수를 설정했다고 가정합니다. 이 변수를 구성하지 않은 경우 Python 실행 파일이 위치하는 전체 디렉터리 경로를 제공해야 합니다. 결과로 나온 명령은 다음과 같습니다. `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK는 세 가지 Azure Monitor 내보내기를 사용하여 Azure Monitor에 대한 다양한 유형의 원격 분석을 전송합니다. 추적, 메트릭 및 로그입니다. 이러한 원격 분석 유형에 대한 자세한 내용은 [데이터 플랫폼 개요](../data-platform.md)를 참조하세요. 다음 지침을 사용하여 세 가지 내보내기를 통해 이러한 원격 분석 유형을 전송합니다.

## <a name="telemetry-type-mappings"></a>원격 분석 유형 매핑

다음은 OpenCensus에서 제공하여 Azure Monitor에서 볼 수 있는 원격 분석 유형에 매핑되는 내보내기입니다.

| 관찰성의 중심 | Azure Monitor의 원격 분석 유형    | 설명                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| 로그                    | Traces, exceptions, customEvents   | 로그 원격 분석, 예외 원격 분석, 이벤트 원격 분석 |
| 메트릭                 | customMetrics, performanceCounters | 사용자 지정 메트릭 성능 카운터                |
| 추적                 | 요청 종속성             | 들어오는 요청, 나가는 요청                |

### <a name="logs"></a>로그

1. 먼저 일부 로컬 로그 데이터를 생성해 보겠습니다.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 코드는 지속적으로 값을 입력하도록 요청합니다. 입력한 모든 값에 대해 로그 항목이 내보내집니다.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 로그 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기 도구에 직접 전달합니다. 또는 환경 변수 `APPLICATIONINSIGHTS_CONNECTION_STRING`에서 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 내보내기에서 로그 데이터를 Azure Monitor로 전송합니다. `traces`에서 데이터를 찾을 수 있습니다. 

    > [!NOTE]
    > 이 컨텍스트에서 `traces`는 `tracing`과 동일하지 않습니다. 여기서 `traces`는 `AzureLogHandler`를 활용할 때 Azure Monitor에 표시되는 원격 분석 유형을 나타냅니다. 그러나 `tracing`은 OpenCensus의 개념을 의미하며 [분산 추적](./distributed-tracing.md)과 관련이 있습니다.

    > [!NOTE]
    > 루트 로거가 경고 수준으로 구성됩니다. 즉, 보내는 로그 중 심각도가 낮은 로그는 무시되고 다시 Azure Monitor로 전송되지 않습니다. 자세한 내용은 [설명서](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)를 참조하세요.

1. custom_dimensions 필드를 사용하는 *extra* 키워드 인수의 로그 메시지에 사용자 지정 속성을 추가할 수도 있습니다. 이 속성은 Azure Monitor에 `customDimensions` 키-값 쌍으로 표시됩니다.
    > [!NOTE]
    > 이 기능이 작동하려면 custom_dimensions 필드에 사전을 전달해야 합니다. 다른 유형의 인수를 전달하면 로거가 이를 무시합니다.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

> [!NOTE]
> Application Insights 계측을 사용하는 과정의 일환으로 진단 데이터를 수집하고 Microsoft에 보냅니다. 이 데이터는 애플리케이션 Insights 실행하고 개선하는 데 도움이 됩니다. 필수가 아닌 데이터 수집을 사용하지 않도록 설정하는 옵션이 있습니다. [자세한 정보](./statsbeat.md).

#### <a name="configure-logging-for-django-applications"></a>Django 애플리케이션에 대한 로깅 구성

Django 애플리케이션에 대해 위와 같이 애플리케이션 코드에서 명시적으로 로깅을 구성하거나 Django의 로깅 구성에서 지정할 수 있습니다. 이 코드는 Django 설정 구성에 사용하는 모든 파일에 적용될 수 있습니다. Django 설정을 구성하는 방법에 대한 자세한 내용은 [Django 설정](https://docs.djangoproject.com/en/3.0/topics/settings/)을 참조하세요. 로깅 구성에 대한 자세한 내용은 [Django 로깅](https://docs.djangoproject.com/en/3.0/topics/logging/)을 참조하세요.

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

구성에 지정된 것과 동일한 이름으로 로거를 사용해야 합니다.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>예외 보내기

OpenCensus Python은 `exception` 원격 분석을 자동으로 추적하고 전송하지 않습니다. Python 로깅 라이브러리를 통해 예외를 사용하여 `AzureLogHandler`를 통해 전송됩니다. 일반 로깅과 마찬가지로 사용자 지정 속성을 추가할 수 있습니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
예외를 명시적으로 로깅해야 하므로 처리되지 않은 예외를 기록하는 방법은 사용자마다 다릅니다. OpenCensus는 예외 원격 분석을 명시적으로 기록하는 한 사용자가 이 작업을 수행하는 방법에 제한을 두지 않습니다.

#### <a name="send-events"></a>이벤트 보내기

`trace` 원격 분석을 보내는 것과 동일한 방식으로(대신 `AzureEventHandler`을 사용하여) `customEvent` 원격 분석을 보낼 수 있습니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="log-correlation"></a>로그 상관 관계

추적 컨텍스트 데이터를 통해 로그를 보강하는 방법에 대한 자세한 내용은 OpenCensus Python [로그 통합](./correlation.md#log-correlation)을 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조하세요.


### <a name="metrics"></a>메트릭

OpenCensus.stats는 4가지 집계 메서드를 지원하지만 Azure Monitor에 대해서는 부분 지원을 제공합니다.

- **개수:** 측정 지점의 수입니다. 값은 누적되며, 증가만 할 수 있고 다시 시작할 때 0으로 다시 설정됩니다. 
- **합계:** 측정 지점의 합계입니다. 값은 누적되며, 증가만 할 수 있고 다시 시작할 때 0으로 다시 설정됩니다. 
- **LastValue:** 마지막으로 기록된 값을 유지하고 다른 모든 항목을 삭제합니다.
- **분포:** 측정 지점의 히스토그램 분포입니다. 이 메서드는 **Azure Exporter에서 지원하지 않습니다**.

### <a name="count-aggregation-example"></a>개수 집계 예

1. 먼저 일부 로컬 메트릭 데이터를 생성해 보겠습니다. 사용자가 **Enter** 키를 선택하는 횟수를 추적하는 단순한 메트릭을 만들겠습니다.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. 반복적으로 코드를 실행하면 **Enter** 키를 선택하라는 메시지가 표시됩니다. **Enter** 키를 선택하는 횟수를 추적하는 메트릭이 만들어집니다. 각 항목마다 값이 증분되고 메트릭 정보는 콘솔에 표시됩니다. 이 정보에는 메트릭이 업데이트된 현재 값 및 현재 타임스탬프가 포함됩니다.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 메트릭 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기 도구에 직접 전달합니다. 또는 환경 변수 `APPLICATIONINSIGHTS_CONNECTION_STRING`에서 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. 내보내기는 고정된 간격으로 메트릭 데이터를 Azure Monitor로 전송합니다. 기본값은 15초 간격입니다. 단일 메트릭을 추적하므로 어떤 값과 타임스탬프가 포함되는지와 무관하게 이 메트릭 데이터는 간격마다 전송됩니다. 값은 누적되며, 증가만 할 수 있고 다시 시작할 때 0으로 다시 설정됩니다. `customMetrics`에서 데이터를 찾을 수 있지만 `customMetrics` 속성 valueCount, valueSum, valueMin, valueMax, and valueStdDev는 효과적으로 사용되지 않습니다.

### <a name="setting-custom-dimensions-in-metrics"></a>메트릭에서 사용자 지정 차원 설정

Opencensus Python SDK에서는 기본적으로 키/값 쌍의 사전인 `tags`를 사용하여 메트릭 원격 분석에 사용자 지정 차원을 추가할 수 있습니다. 

1. 사용할 태그를 태그 맵에 삽입합니다. 태그 맵은 사용할 수 있는 모든 사용 가능한 태그에 대한 일종의 "풀" 역할을 합니다.

    ```python
    ...
    tmap = tag_map_module.TagMap()
    tmap.insert("url", "http://example.com")
    ...
    ```

1. 특정 `View`의 경우 태그 키를 통해 해당 뷰로 메트릭을 기록할 때 사용할 태그를 지정합니다.

    ```python
    ...
    prompt_view = view_module.View("prompt view",
                                "number of prompts",
                                ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                                prompt_measure,
                                aggregation_module.CountAggregation())
    ...
    ```

1. 측정 맵에 기록할 때 태그 맵을 사용해야 합니다. `View`에 지정된 태그 키는 기록에 사용되는 태그 맵에 있어야 합니다.

    ```python
    ...
    mmap = stats_recorder.new_measurement_map()
    mmap.measure_int_put(prompt_measure, 1)
    mmap.record(tmap) # <-- pass the tag map in here
    ...
    ```

1. `customMetrics` 테이블에서 `prompt_view`를 사용하여 내보낸 모든 메트릭 레코드에는 사용자 지정 차원 `{"url":"http://example.com"}`이 있습니다.

1. 동일한 키를 사용하여 다른 값으로 태그를 생성하려면 해당 항목에 대한 새 태그 맵을 만듭니다.

    ```python
    ...
    tmap = tag_map_module.TagMap()
    tmap2 = tag_map_module.TagMap()
    tmap.insert("url", "http://example.com")
    tmap2.insert("url", "https://www.wikipedia.org/wiki/")
    ...
    ```

#### <a name="performance-counters"></a>성능 카운터

기본적으로 메트릭 내보내기 도구는 Azure Monitor로 성능 카운터 집합을 전송합니다. 메트릭 내보내기의 생성자에서 `enable_standard_metrics` 플래그를 `False`로 설정하여 이를 사용하지 않도록 설정할 수 있습니다.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

현재 전송되는 성능 카운터는 다음과 같습니다.

- 사용 가능한 메모리(바이트)
- CPU 프로세서 시간(백분율)
- 들어오는 요청 빈도(초당)
- 들어오는 요청 평균 실행 시간(밀리초)
- 프로세스 CPU 사용량(백분율)
- 프로세스 프라이빗 바이트(바이트)

`performanceCounters`에서 이러한 메트릭을 볼 수 있어야 합니다. 자세한 내용은 [성능 카운터](./performance-counters.md)를 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조하세요.

### <a name="tracing"></a>추적

> [!NOTE]
> OpenCensus에서 `tracing`는 [분산 추적](./distributed-tracing.md)을 의미합니다. `AzureExporter`는 `requests` 및 `dependency` 원격 분석을 Azure Monitor로 전송합니다.

1. 먼저 일부 추적 데이터를 로컬로 생성해 보겠습니다. Python IDLE에서 또는 원하는 편집기에서 다음 코드를 입력합니다.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 반복적으로 코드를 실행하면 값을 입력하라는 메시지가 표시됩니다. 각 항목마다 값이 셸에 출력됩니다. OpenCensus Python 모듈은 해당하는 부분 `SpanData`를 생성합니다. OpenCensus 프로젝트는 [범위 트리로 추적](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 `SpanData`를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기 도구에 직접 전달합니다. 또는 환경 변수 `APPLICATIONINSIGHTS_CONNECTION_STRING`에서 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 이제 Python 스크립트를 실행할 때 값을 입력하라는 메시지가 여전히 표시되지만 이제는 값만 셸에 인쇄됩니다. 만들어진 `SpanData`는 Azure Monitor로 전송됩니다. `dependencies`에서 내보내진 범위 데이터를 찾을 수 있습니다. 나가는 요청에 대한 자세한 내용은 OpenCensus Python [종속성](./opencensus-python-dependency.md)을 참조하세요.
들어오는 요청에 대한 자세한 내용은 OpenCensus Python [요청](./opencensus-python-request.md)을 참조하세요.

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="trace-correlation"></a>추적 상관 관계

추적 데이터의 원격 분석 상관 관계에 대한 자세한 내용은 OpenCensus Python [원격 분석 상관 관계](./correlation.md#telemetry-correlation-in-opencensus-python)를 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조하세요.

## <a name="configure-azure-monitor-exporters"></a>Azure Monitor 내보내기 도구 구성

표시된 것처럼 OpenCensus를 지원하는 3가지 Azure Monitor 내보내기 도구가 있습니다. 각 항목은 서로 다른 유형의 원격 분석을 Azure Monitor로 보냅니다. 각 내보내기에서 보내는 원격 분석의 유형을 확인하려면 다음 목록을 참조하세요.

각 내보내기 도구는 생성자를 통해 전달된 구성에 대해 동일한 인수를 수락합니다. 각각에 대한 세부 정보를 여기서 확인할 수 있습니다.

- `connection_string`: Azure Monitor 리소스에 연결하는 데 사용되는 연결 문자열입니다. `instrumentation_key`보다 우선 순위를 가집니다.
- `enable_standard_metrics`: `AzureMetricsExporter`에 사용됩니다. Azure Monitor에 자동으로 [성능 카운터](../essentials/app-insights-metrics.md#performance-counters) 메트릭을 보내도록 내보내기 도구에 신호를 보냅니다. 기본값은 `True`입니다.
- `export_interval`: 내보내는 빈도(초)를 지정하는 데 사용됩니다.
- `instrumentation_key`: Azure Monitor 리소스에 연결하는 데 사용되는 계측 키입니다.
- `logging_sampling_rate`: `AzureLogHandler`에 사용됩니다. 로그를 내보내기 위한 샘플링 주기 [0,1.0]을 제공합니다. 기본값은 1.0입니다.
- `max_batch_size`: 한 번에 내보내지는 원격 분석의 최대 크기를 지정합니다.
- `proxies`: Azure Monitor에 데이터를 보내는 데 사용할 프록시 시퀀스를 지정합니다. 자세한 내용은 [프록시](https://requests.readthedocs.io/en/master/user/advanced/#proxies)를 참조하세요.
- `storage_path`: 로컬 스토리지 폴더가 있는 위치에 대한 경로입니다(보내지 않은 원격 분석). `opencensus-ext-azure` v1.0.3부터 기본 경로는 OS 임시 디렉터리 + `opencensus-python` + `your-ikey`입니다. v1.0.3 이전에는 기본 경로가 $USER + `.opencensus` + `.azure` + `python-file-name`입니다.

## <a name="authentication-preview"></a>인증(미리 보기)
> [!NOTE]
> 인증 기능은 `opencensus-ext-azure` v1.1b0부터 사용할 수 있습니다.

각 Azure Monitor 내보내기 도구는 AAD(Azure Active Directory)를 사용한 OAuth 인증을 통해 원격 분석 페이로드를 안전하게 보내는 구성을 지원합니다.
자세한 내용은 [인증](./azure-ad-authentication.md) 문서를 확인하세요.

## <a name="view-your-data-with-queries"></a>쿼리를 사용하여 데이터 보기

**로그(분석)** 탭을 통해 애플리케이션에서 전송된 원격 분석 데이터를 볼 수 있습니다.

![붉은색 상자로 "로그(분석)"가 선택된 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

**활성** 목록:

- Azure Monitor 추적 내보내기를 사용하여 전송되는 원격 분석의 경우 들어오는 요청은 `requests`에 표시됩니다. 나가는 또는 진행 중인 요청은 `dependencies`에 표시됩니다.
- Azure Monitor 메트릭 내보내기로 전송된 원격 분석의 경우 전송된 메트릭은 `customMetrics`에 표시됩니다.
- Azure Monitor 로그 내보내기를 사용하여 전송되는 원격 분석의 경우 로그는 `traces`에 표시됩니다. 예외는 `exceptions`에 표시됩니다.

쿼리 및 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그](../logs/data-platform-logs.md)를 참조하세요.

## <a name="learn-more-about-opencensus-for-python"></a>Python용 OpenCensus에 대해 자세히 알아보기

* [GitHub의 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub의 Azure Monitor 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 통합](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 샘플 애플리케이션](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>다음 단계

* [들어오는 요청 추적](./opencensus-python-dependency.md)
* [나가는 요청 추적](./opencensus-python-request.md)
* [애플리케이션 맵](./app-map.md)
* [엔드투엔드 성능 모니터링](../app/tutorial-performance.md)

### <a name="alerts"></a>경고

* [가용성 테스트](./monitor-web-app-availability.md): 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](./proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../alerts/alerts-log.md): 메트릭이 임계값을 초과할 경우 경고 메시지를 표시하도록 설정합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.
