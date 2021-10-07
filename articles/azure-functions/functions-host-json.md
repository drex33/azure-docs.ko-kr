---
title: Azure Functions 2.x에 대한 host.json 참조
description: v2 런타임을 사용하는 Azure Functions host.json 파일에 대한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8844e76c7f01bf33bc81ef2fec733b9e538e34cc
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660517"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 이상에 대한 host.json 참조 

> [!div class="op_single_selector" title1="사용할 Azure Functions 런타임 버전을 선택합니다. "]
> * [버전 1](functions-host-json-v1.md)
> * [버전 2 이상](functions-host-json.md)

host.json 메타데이터 파일에는 함수 앱 인스턴스의 모든 함수에 영향을 주는 구성 옵션이 포함되어 있습니다. 이 문서에서는 Azure Functions 런타임의 버전 2.x부터 사용 가능한 설정을 나열합니다.  

> [!NOTE]
> 이 문서는 Azure Functions 2.x 이상의 버전을 대상으로 합니다.  Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

다른 함수 앱 구성 옵션은 함수 앱이 실행되는 위치에 따라 관리됩니다.

+ **Azure에 배포:** [애플리케이션 설정에서](functions-app-settings.md) 
+ **로컬 컴퓨터의**: [local.settings.json](functions-develop-local.md#local-settings-file) 파일.

바인딩과 관련된 host.json의 구성은 함수 앱의 각 함수에 동일하게 적용됩니다. 

또한 애플리케이션 설정을 사용하여 [환경별로 설정을 재정의하거나 적용](#override-hostjson-values)할 수 있습니다.

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

2\.x 이상의 버전을 위한 다음 샘플 *host.json* 파일에는 가능한 모든 옵션이 지정되어 있습니다(내부용인 경우 제외).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

이 설정은 [logging](#logging)의 자식입니다.

[샘플링 옵션](./configure-monitoring.md#configure-sampling)을 비롯한 Application Insights 관련 옵션을 제어합니다.

전체 JSON 구조는 이전 [host.json 예제 파일](#sample-hostjson-file)을 참조하세요.

> [!NOTE]
> 로그 샘플링으로 인해 Application Insights 모니터 블레이드에 일부 실행이 표시되지 않을 수 있습니다. 로그 샘플링을 방지하려면 `samplingSettings` 값에 `excludedTypes: "Request"`를 추가합니다.

| 속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| samplingSettings | 해당 없음 | [applicationInsights.samplingSettings](#applicationinsightssamplingsettings)를 참조합니다. |
| enableLiveMetrics | true | 라이브 메트릭 수집을 사용하도록 설정합니다. |
| enableDependencyTracking | true | 종속성 추적을 사용하도록 설정합니다. |
| enablePerformanceCountersCollection | true | Kudu 성능 카운터 수집을 사용하도록 설정합니다. |
| liveMetricsInitializationDelay | 00:00:15 | 내부 전용입니다. |
| httpAutoCollectionOptions | 해당 없음 | [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)를 참조합니다. |
| snapshotConfiguration | 해당 없음 | [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration)을 참조합니다. |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

이러한 설정에 대한 자세한 내용은 [Application Insights의 샘플링](../azure-monitor/app/sampling.md)을 참조하세요. 

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| isEnabled | true | 샘플링을 사용 여부를 설정합니다. | 
| maxTelemetryItemsPerSecond | 20 | 각 서버 호스트에서 초당 기록된 원격 분석 항목의 수입니다. 앱이 여러 호스트에서 실행되는 경우 이 값을 줄여서 전체 목표 트래픽 속도 내에서 유지합니다. | 
| evaluationInterval | 01:00:00 | 현재 원격 분석 비율이 다시 평가되는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다. |
| initialSamplingPercentage| 100.0 | 비율이 동적으로 변경되는 샘플링 프로세스가 시작될 때 적용되는 초기 샘플링 비율입니다. 디버깅하는 동안에는 값을 줄이지 마세요. |
| samplingPercentageIncreaseTimeout | 00:00:01 | 샘플링 비율 값이 변경되면 이 속성은 이후 Application Insights에서 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 높이는 시점을 결정합니다. |
| samplingPercentageDecreaseTimeout | 00:00:01 | 샘플링 비율 값이 변경되면 이 속성은 이후 Application Insights에서 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮추는 시점을 결정합니다. |
| minSamplingPercentage | 0.1 | 샘플링 비율이 변경되는 경우 이 속성은 허용되는 최소 샘플링 비율을 결정합니다. |
| maxSamplingPercentage | 100.0 | 샘플링 비율이 변경되는 경우 이 속성은 허용되는 최대 샘플링 비율을 결정합니다. |
| movingAverageRatio | 1.0 | 이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다. |
| excludedTypes | null | 샘플링하지 않으려는 형식을 세미콜론으로 구분한 목록입니다. 인식할 수 있는 형식은 `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`입니다. 지정된 형식의 모든 인스턴스가 전송되고 지정되지 않은 형식은 샘플링됩니다. |
| includedTypes | null | 샘플링할 형식을 세미콜론으로 구분한 목록이며 빈 목록은 모든 형식을 의미합니다. `excludedTypes`에 나열된 형식은 여기 나열된 형식을 재정의합니다. 인식할 수 있는 형식은 `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`입니다. 지정된 형식의 인스턴스가 샘플링되며, 지정되지 않았거나 암시되지 않은 형식은 샘플링 없이 전송됩니다. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | HTTP 트리거에 확장된 HTTP 요청 정보(들어오는 요청 상관관계 헤더, 다중 계측 키 지원, HTTP 메서드, 경로, 응답)를 사용하거나 사용하지 않도록 설정합니다. |
| enableW3CDistributedTracing | true | W3C 분산 추적 프로토콜 지원을 사용하거나 사용하지 않도록 설정하고 레거시 상관관계 스키마를 사용하도록 설정합니다. `enableHttpTriggerExtendedInfoCollection`이 true인 경우 기본적으로 사용하도록 설정됩니다. `enableHttpTriggerExtendedInfoCollection`이 false인 경우 이 플래그는 나가는 요청에만 적용되고 들어오는 요청에는 적용되지 않습니다. |
| enableResponseHeaderInjection | true | 응답에 다중 구성 요소 상관관계 헤더의 삽입을 사용하거나 사용하지 않도록 설정합니다. 삽입을 사용하도록 설정하면 여러 개의 계측 키를 사용할 때 Application Insights에서 애플리케이션 맵을 구성할 수 있습니다. `enableHttpTriggerExtendedInfoCollection`이 true인 경우 기본적으로 사용하도록 설정됩니다. `enableHttpTriggerExtendedInfoCollection`이 false인 경우 이 설정은 적용되지 않습니다. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

스냅샷에 대한 자세한 내용은 [.NET 앱에서 예외에 대한 스냅샷 디버그하기](../azure-monitor/app/snapshot-debugger.md) 및 [Application Insights 스냅샷 디버거 또는 스냅샷 보기를 사용 설정하는 문제 해결](../azure-monitor/app/snapshot-debugger-troubleshoot.md)을 참조하세요.

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| agentEndpoint | null | Application Insights 스냅샷 디버거 서비스에 연결하는 데 사용되는 엔드포인트입니다. Null인 경우 기본 엔드포인트가 사용됩니다. |
| captureSnapshotMemoryWeight | 0.5 | 스냅샷을 만들기에 충분한 메모리가 있는지 검사할 때 현재 프로세스 메모리 크기에 지정된 가중치입니다. 예상 값은 0보다 큰 진분수(0 < CaptureSnapshotMemoryWeight < 1)입니다. |
| failedRequestLimit | 3 | 원격 분석 프로세서를 사용하지 않도록 설정하기 전에 스냅샷을 요청하는 데 실패한 요청 수에 대한 제한입니다.|
| handleUntrackedExceptions | true | Application Insights 원격 분석에서 추적하지 않는 예외 추적을 사용하거나 사용하지 않도록 설정합니다. |
| isEnabled | true | 스냅샷 수집을 사용하거나 사용하지 않도록 설정합니다. | 
| isEnabledInDeveloperMode | false | 개발자 모드에서 스냅샷 수집을 사용하거나 사용하지 않도록 설정합니다. |
| isEnabledWhenProfiling | true | Application Insights Profiler에서 자세한 프로파일링 세션을 수집하는 경우에도 스냅샷 만들기를 사용하거나 사용하지 않도록 설정합니다. |
| isExceptionSnappointsEnabled | false | 예외 필터링을 사용하거나 사용하지 않도록 설정합니다. |
| isLowPrioritySnapshotUploader | true | SnapshotUploader 프로세스를 보통 우선 순위로 실행할지 여부를 결정합니다. |
| maximumCollectionPlanSize | 50 | 언제든지 추적할 수 있는 문제의 최대 개수입니다(1~9999). |
| maximumSnapshotsRequired | 3 | 단일 문제에 대해 수집된 스냅샷의 최대 개수입니다(1~999). 문제를 애플리케이션의 개별 throw 문으로 생각할 수 있습니다. 문제에 대해 수집된 스냅샷 수가 이 값에 도달하면 문제 카운터가 다시 설정되고(`problemCounterResetInterval` 참조) `thresholdForSnapshotting` 제한에 다시 도달할 때까지 해당 문제에 대한 스냅샷이 더 이상 수집되지 않습니다. |
| problemCounterResetInterval | 24:00:00 | 문제 카운터를 다시 설정하는 빈도입니다(1분~7일). 이 간격에 도달하면 모든 문제 수가 0으로 다시 설정됩니다. 스냅샷 수행에 대한 임계값에 이미 도달했지만 `maximumSnapshotsRequired`에서 스냅샷 수를 아직 생성하지 않은 기존 문제는 활성 상태로 유지됩니다. |
| provideAnonymousTelemetry | true | 익명 사용 및 오류 원격 분석을 Microsoft로 보낼지 여부를 결정합니다. Microsoft에 문의하여 스냅샷 디버거 문제를 해결하는 데 도움이 되는 경우 이 원격 분석을 사용할 수 있습니다. 사용 패턴을 모니터링하는 데도 사용됩니다. |
| reconnectInterval | 00:15:00 | 스냅샷 디버거 엔드포인트에 다시 연결하는 빈도입니다. 허용 범위는 1분에서 1일 사이입니다. |
| shadowCopyFolder | null | 이진 파일을 섀도 복사하는 데 사용할 폴더를 지정합니다. 설정하지 않는 경우 다음 환경 변수에 의해 지정된 폴더가 순서대로 시도됩니다. Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | true인 경우 SnapshotUploader의 한 인스턴스만 InstrumentationKey를 공유하는 여러 앱에 대한 스냅샷을 수집하고 업로드합니다. false로 설정하면 SnapshotUploader는 각 (ProcessName, InstrumentationKey) 튜플에 대해 고유합니다. |
| snapshotInLowPriorityThread | true | 낮은 IO 우선 순위 스레드에서의 스냅샷 처리 여부를 결정합니다. 스냅샷 만들기는 빠른 작업이지만, 스냅샷을 스냅샷 디버거 서비스에 업로드하려면 먼저 디스크에 미니덤프로 써야 합니다. 이러한 작업은 SnapshotUploader 프로세스에서 발생합니다. 이 값을 true로 설정하면 우선 순위가 낮은 IO를 사용하여 리소스에 대한 애플리케이션과 경합하지 않는 미니덤프를 씁니다. 이 값을 false로 설정하면 애플리케이션의 속도가 느려지는 대신 미니덤프 생성 속도가 빨라집니다. |
| snapshotsPerDayLimit | 30 | 1일(24시간) 동안 허용되는 스냅샷의 최대 개수입니다. 이 제한은 Application Insights 서비스 쪽에도 적용됩니다. 업로드는 애플리케이션당(즉, 계측 키당) 50회로 제한됩니다. 이 값을 사용하면 업로드 중에 결국 거부될 추가 스냅샷이 생성되지 않습니다. 값이 0이면 제한이 완전히 없어지므로 권장하지 않습니다. |
| snapshotsPerTenMinutesLimit | 1 | 10분 동안 허용되는 스냅샷의 최대 개수입니다. 이 값에는 상한이 없지만 애플리케이션의 성능에 영향을 줄 수 있으므로 프로덕션 워크로드에 대해서는 주의를 기울여야 합니다. 스냅샷 만들기는 빠르지만 스냅샷의 미니덤프를 만들어 스냅샷 디버거 서비스에 업로드하는 작업은 리소스(CPU 및 I/O 모두)를 두고 애플리케이션과 경합하게 되는 훨씬 느린 작업입니다. |
| tempFolder | null | 미니덤프 및 업로더 로그 파일을 쓸 폴더를 지정합니다. 설정하지 않으면 *%TEMP%\Dumps* 가 사용됩니다. |
| thresholdForSnapshotting | 1 | Application Insights에서 스냅샷을 요청하기 전에 충족해야 하는 예외 발생 횟수입니다. |
| uploaderProxy | null | 스냅샷 업로더 프로세스에서 사용되는 프록시 서버를 재정의합니다. 애플리케이션이 프록시 서버를 통해 인터넷에 연결하는 경우 이 설정을 사용해야 할 수 있습니다. Snapshot Collector는 애플리케이션의 프로세스 내에서 실행되며, 동일한 프록시 설정을 사용합니다. 그러나 스냅샷 업로더는 별도의 프로세스로 실행되며 프록시 서버를 수동으로 구성해야 할 수도 있습니다. 이 값이 null인 경우 Snapshot Collector는 System.Net.WebRequest.DefaultWebProxy를 검사하고 값을 스냅샷 업로더로 전달하여 프록시 주소를 자동으로 감지합니다. 이 값이 null이 아닌 경우 자동 감지가 사용되지 않으며 여기에 지정된 프록시 서버가 스냅샷 업로더에서 사용됩니다. |

## <a name="blobs"></a>Blob

구성 설정은 [Storage Blob 트리거 및 바인딩](functions-bindings-storage-blob.md#hostjson-settings)에서 찾을 수 있습니다.  

## <a name="console"></a>콘솔

이 설정은 [logging](#logging)의 자식입니다. 디버깅 모드가 아닌 경우 콘솔 로깅을 제어합니다.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": false,
          "DisableColors": true
        },
    ...
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|DisableColors|false| Linux의 컨테이너 로그에서 로그 형식을 지정 하지 않습니다. Linux에서 실행할 때 컨테이너 로그에 원치 않는 ANSI 제어 문자가 표시되면 true로 설정합니다. |
|isEnabled|false|콘솔 로깅을 사용하거나 사용하지 않도록 설정합니다.| 

## <a name="cosmosdb"></a>cosmosDb

구성 설정은 [Cosmos DB 트리거 및 바인딩](functions-bindings-cosmosdb-v2-output.md#host-json)에서 찾을 수 있습니다.

## <a name="customhandler"></a>customHandler

사용자 지정 처리기에 대한 구성 설정입니다. 자세한 내용은 [Azure Functions 사용자 지정 처리기](functions-custom-handlers.md#configuration)를 참조하세요.

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|속성 | 기본값 | Description |
| --------- | --------- | --------- |
| defaultExecutablePath | 해당 없음 | 사용자 지정 처리기 프로세스로 시작할 실행 파일입니다. 이는 사용자 지정 처리기를 사용하는 경우 필수 설정이며, 해당 값은 함수 앱 루트를 기준으로 합니다. |
| workingDirectory | ‘함수 앱 루트’ | 사용자 지정 처리기 프로세스를 시작할 작업 디렉터리입니다. 이는 선택적 설정이며 해당 값은 함수 앱 루트를 기준으로 합니다. |
| 인수 | 해당 없음 | 사용자 지정 처리기 프로세스에 전달할 명령줄 인수의 배열입니다. |
| enableForwardingHttpRequest | false | 설정하면 HTTP 트리거와 HTTP 출력으로만 구성된 모든 함수는 사용자 지정 처리기 [요청 페이로드](functions-custom-handlers.md#request-payload) 대신 원래 HTTP 요청으로 전달됩니다. |

## <a name="durabletask"></a>durableTask

구성 설정은 [지속형 함수에 대한 바인딩](durable/durable-functions-bindings.md#host-json)에서 찾을 수 있습니다.

## <a name="eventhub"></a>eventHub

구성 설정은 [이벤트 허브 트리거 및 바인딩](functions-bindings-event-hubs.md#host-json)에서 찾을 수 있습니다. 

## <a name="extensions"></a>확장

[http](#http), [eventHub](#eventhub) 등의 모든 바인딩 관련 설정이 포함된 개체를 반환하는 속성입니다.

## <a name="extensionbundle"></a>extensionBundle 

확장 번들을 사용하면 호환되는 함수 바인딩 확장 집합을 함수 앱에 추가할 수 있습니다. 자세히 알아보려면 [로컬 개발용 확장 번들](functions-bindings-register.md#extension-bundles)을 참조하세요.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

작업 호스트가 실행하는 함수 목록입니다. 빈 배열은 모든 함수를 실행한다는 의미입니다. [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. Azure의 함수 앱에서는 이 설정을 사용하는 대신 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md)의 단계를 수행하여 특정 함수를 사용하지 않도록 설정해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. 이는 timespan 문자열 형식을 따릅니다. 

| 플랜 유형 | 기본(분) | 최대(분) |
| -- | -- | -- |
| Consumption | 5 | 10 |
| 프리미엄<sup>1</sup> | 30 | -1(제한 없음)<sup>2</sup> |
| 전용(App Service) | 30 | -1(제한 없음)<sup>2</sup> |

<sup>1</sup> 프리미엄 플랜은 60분만 보장되지만 기술적으로는 제한이 없습니다.   
<sup>2</sup> `-1` 값은 제한 없는 실행을 나타내지만 고정 상한을 유지하는 것이 좋습니다.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[호스트 상태 모니터](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)에 대한 구성 설정

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------| 
|사용|true|기능의 사용 여부를 지정합니다. | 
|healthCheckInterval|10초|정기적인 백그라운드 상태 검사 사이의 간격 | 
|healthCheckWindow|2분|`healthCheckThreshold` 설정과 함께 사용되는 슬라이딩 시간 범위| 
|healthCheckThreshold|6|호스트 재생이 시작되기 전에 상태 검사 실패가 용인되는 최대 횟수| 
|counterThreshold|0.80|성능 카운터가 비정상으로 간주되는 임계값| 

## <a name="http"></a>http

구성 설정은 [http 트리거 및 바인딩](functions-bindings-http-webhook-output.md#hostjson-settings)에서 찾을 수 있습니다.

## <a name="logging"></a>logging

Application Insights를 포함한 함수 앱의 로깅 동작을 제어합니다.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|활성화할 파일 로깅의 수준을 정의합니다.  옵션은 `never`, `always`, `debugOnly`입니다. |
|logLevel|해당 없음|앱의 함수에 대한 로그 범주 필터링을 정의하는 개체입니다. 이 설정을 통해 특정 함수의 로깅을 필터링할 수 있습니다. 자세한 내용은 [로그 수준 구성](configure-monitoring.md#configure-log-levels)을 참조하세요. |
|콘솔|해당 없음| [콘솔](#console) 로깅 설정입니다. |
|applicationInsights|해당 없음| [applicationInsights](#applicationinsights) 설정입니다. |

## <a name="manageddependency"></a>managedDependency

관리형 종속성은 현재 PowerShell 기반 함수에서만 지원되는 기능입니다. 이를 통해 서비스에서 종속성을 자동으로 관리할 수 있습니다. `enabled` 속성 `true`로 설정되면 `requirements.psd1` 파일이 처리됩니다. 부 버전이 릴리스되면 종속성이 업데이트됩니다. 자세한 내용은 PowerShell 문서의 [관리형 종속성](functions-reference-powershell.md#dependency-management)을 참조하세요.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

구성 설정은 [스토리지 큐 트리거 및 바인딩](functions-bindings-storage-queue.md#host-json)에서 찾을 수 있습니다.  

## <a name="retry"></a>retry

앱의 모든 실행에 대한 [재시도 정책](./functions-bindings-error-pages.md#retry-policies-preview) 옵션을 제어합니다.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------| 
|전략|null|필수 요소. 사용하는 재시도 전략입니다. 유효한 값은 `fixedDelay` 또는 `exponentialBackoff`입니다.|
|maxRetryCount|null|필수 요소. 함수 실행당 허용되는 최대 재시도 횟수입니다. `-1`은 무기한으로 재시도하는 것을 의미합니다.|
|delayInterval|null|`fixedDelay` 전략을 통한 재시도 사이에 사용되는 지연 시간입니다.|
|minimumInterval|null|`exponentialBackoff` 전략을 사용하는 경우 재시도 최소 지연 시간입니다.|
|maximumInterval|null|`exponentialBackoff` 전략을 사용하는 경우 재시도 최대 지연 시간입니다.| 

## <a name="sendgrid"></a>sendGrid

구성 설정은 [SendGrid 트리거 및 바인딩](functions-bindings-sendgrid.md#host-json)에서 찾을 수 있습니다.

## <a name="servicebus"></a>serviceBus

구성 설정은 [Service Bus 트리거 및 바인딩](functions-bindings-service-bus.md#host-json)에서 찾을 수 있습니다.

## <a name="singleton"></a>singleton

Singleton 잠금 동작에 대한 구성 설정입니다. 자세한 내용은 [singleton 지원에 대한 GitHub 문제](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)를 참조하세요.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 적용되는 기간입니다. 잠금은 자동 갱신됩니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 적용되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작할 때 수신기 잠금을 가져올 수 없는 경우 수신기 잠금 복구에 사용되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 확보하려고 시도하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|해당 없음|잠금 확보 시도 사이의 간격입니다.| 

## <a name="version"></a>버전

이 값은 host.json의 스키마 버전을 나타냅니다. v2 이상 버전의 런타임을 대상으로 하는 함수 앱에서는 버전 문자열 `"version": "2.0"`이 필수 항목입니다. v2와 v3 간의 host.json 스키마 변경 내용은 없습니다.

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이 디렉터리의 코드가 변경되면 변경 내용이 함수에 의해 선택되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

애플리케이션을 다시 시작해야 하는 변경 내용을 모니터링하는 하나 이상의 파일 이름으로 이루어진 배열입니다.  이러한 파일의 코드가 변경되면 함수에서 업데이트를 선택합니다.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>host.json 값 재정의

host.json 파일 자체는 변경하지 않고 특정 환경에 맞게 host.json 파일의 특정 설정을 구성하거나 수정할 인스턴스가 있을 수 있습니다.  애플리케이션 설정과 동일한 값을 생성하여 특정 host.json 값을 재정의할 수 있습니다. 런타임에서 `AzureFunctionsJobHost__path__to__setting` 형식의 애플리케이션 설정을 찾으면 JSON의 `path.to.setting`에 있는 해당 host.json 설정을 재정의합니다. 애플리케이션 설정으로 표현되는 경우 JSON 계층을 나타내는 데 사용되는 점(`.`)은 이중 밑줄(`__`)로 대체됩니다. 

예를 들어 로컬로 실행할 때 Application Insight 샘플링을 사용하지 않도록 설정하려는 경우를 가정해 보겠습니다. 파일에서 로컬 host.json 파일을 변경하여 Application Insights를 사용하지 않도록 설정하면 배포 중에 이 변경 내용이 프로덕션 앱에 푸시될 수 있습니다. 이 작업을 수행하는 보다 안전한 방법은 대신 `local.settings.json` 파일에서 `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"`로 애플리케이션 설정을 만드는 것입니다. 다음 `local.settings.json` 파일에서 이를 확인할 수 있으며, 이 파일은 게시되지 않습니다.

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [host.json 파일을 업데이트하는 방법 알아보기](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
