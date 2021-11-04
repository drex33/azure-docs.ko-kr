---
title: 이벤트 기반 비디오 녹화를 위한 신호 게이트 구성 - Azure
description: 이 문서에서는 파이프라인에서 신호 게이트를 구성하는 방법에 대한 지침을 제공합니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4fbb530cc4cf0c4b5ea4871c922b7fff6c75ec93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102848"
---
# <a name="configuring-a-signal-gate-for-event-based-video-recording"></a>이벤트 기반 비디오 녹화를 위한 신호 게이트 구성

[!INCLUDE [header](includes/edge-env.md)]

파이프라인 내에서 [신호 게이트 프로세서 노드](../pipeline.md#signal-gate-processor)를 사용하면 이벤트에 의해 게이트가 트리거될 때 한 노드에서 다른 노드로 미디어를 전달할 수 있습니다. 게이트가 트리거되면 게이트가 열리고 지정된 기간 동안 미디어가 흐를 수 있습니다. 게이트를 트리거하는 이벤트가 없는 경우 게이트가 닫히고 미디어 흐름이 중지됩니다. 이벤트 기반 비디오 녹화에 신호 게이트 프로세서를 사용할 수 있습니다.

> [!NOTE]
> 신호 게이트 프로세서 노드 바로 뒤에 비디오 싱크 또는 파일 싱크가 있어야 합니다.

이 문서에서는 신호 게이트 프로세서를 구성하는 방법을 알아봅니다.

## <a name="suggested-prereading"></a>추천 참고 자료

- [파이프라인 토폴로지](../pipeline.md)
- [이벤트 기반 비디오 녹화](../event-based-video-recording-concept.md)

## <a name="problem"></a>문제

사용자는 이벤트에 의해 게이트가 트리거되기 전 또는 후에 특정 시간에 녹화를 시작할 수 있습니다. 사용자는 시스템 내에서 허용되는 대기 시간을 알고 있습니다. 따라서 신호 게이트 프로세서의 대기 시간을 지정하려고 합니다. 또한 새 이벤트가 수신되는 횟수에 관계없이 최소 및 최대 녹화 시간을 지정하려고 합니다.
 
### <a name="use-case-scenario"></a>사용 사례 시나리오

건물 정문이 열릴 때마다 비디오를 녹화하려 한다고 가정하겠습니다. 다음과 같이 녹화하려 합니다. 

- 문이 열리기 전 *X* 초를 녹화에 포함합니다. 
- 문이 다시 열리지 않으면 녹화를 *Y초* 이상 지속합니다. 
- 문이 반복적으로 열리는 경우 녹화를 *Z* 초 이하로 지속합니다. 
 
사용자는 도어 센서의 대기 시간이 *K* 초라는 것을 알고 있습니다. 이벤트가 지연 도착으로 무시될 가능성을 줄이기 위해 이벤트가 도착하는 시간을 *K* 초 이상 허용하려 합니다.

## <a name="solution"></a>해결 방법

문제를 해결하려면 신호 게이트 프로세서 매개 변수를 수정합니다.

신호 게이트 프로세서를 구성하려면 다음 네 가지 매개 변수를 사용합니다.

- 활성화 평가 창
- 활성화 신호 오프셋
- 최소 활성화 창
- 최대 활성화 창

신호 게이트 프로세서가 트리거되면 최소 활성화 창 동안 열린 상태로 유지됩니다. 활성화 이벤트는 가장 빠른 이벤트의 타임 스탬프와 활성화 신호 오프셋에서 시작됩니다. 

신호 게이트 프로세서가 열려 있는 상태에서 다시 트리거되면 타이머가 초기화되고 적어도 최소 활성화 시간 동안 게이트가 계속 열려 있습니다. 신호 게이트 프로세서는 절대로 최대 활성화 시간보다 오래 열려 있지 않습니다. 

시스템 지연이 발생하여 이벤트 1이 이벤트 2보다 늦게 신호 게이트 프로세서에 도착하는 경우 미디어 타임스탬프 기준으로 다른 이벤트(이벤트 2)보다 먼저 발생하는 이벤트(이벤트 1)가 무시될 수 있습니다. 이벤트 1이 이벤트 2 도착 시간과 활성화 평가 창 사이에 도착하지 않으면 이벤트 1은 무시됩니다. 신호 게이트 프로세서를 통해 전달되지 않습니다. 

상관 관계 ID는 모든 이벤트에 대해 설정됩니다. 이러한 ID는 초기 이벤트에서 설정됩니다. 이어지는 각 이벤트에 대해 순차적으로 설정됩니다.

> [!IMPORTANT]
> 미디어 시간은 미디어에서 이벤트가 발생하는 미디어 타임스탬프를 기준으로 합니다. 신호 게이트에 도착하는 이벤트 시퀀스는 미디어 시간에 도착하는 이벤트 시퀀스를 반영하지 않을 수 있습니다.

### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>이벤트가 신호 게이트에 도착하는 실제 시간을 기반으로 하는 매개 변수

* **minimumActivationTime(가능한 가장 짧은 녹화 시간)** : maximumActivationTime에 의해 중단되지 않는 한, 신호 게이트 프로세서가 새 이벤트를 수신하도록 트리거된 후 열린 상태로 유지되는 최소 시간(초)입니다.
* **maximumActivationTime(가능한 가장 긴 녹화 시간)** : 수신되는 이벤트에 관계없이 신호 게이트 프로세서가 새 이벤트를 수신하도록 트리거된 후 열린 상태로 유지되는 초기 이벤트의 최대 시간(초)입니다.
* **activationSignalOffset**: 신호 게이트 프로세서의 활성화와 비디오 녹화 시작 사이의 시간(초)입니다. 일반적으로 트리거 이벤트보다 먼저 녹화가 시작되기 때문에 이 값은 음수입니다.
* **activationEvaluationWindow**: 초기 트리거 이벤트부터 시작하며, 미디어 타임에 초기 이벤트보다 먼저 발생한 이벤트가 신호 게이트 프로세서에 도착해야 하는 시간(초)입니다. 이 시간이 지나면 이벤트가 무시되고 지연 도착으로 간주됩니다.

> [!NOTE]
> *지연 도착* 은 활성화 평가 창이 지났지만 미디어 시간의 초기 이벤트보다는 먼저 도착하는 이벤트입니다.

### <a name="limits-of-parameters"></a>매개 변수 제한

* **activationEvaluationWindow**: 0~10초
* **activationSignalOffset**: -1~1분
* **minimumActivationTime**: 10초~1시간
* **maximumActivationTime**: 10초~1시간

사용 사례에서 다음과 같이 매개 변수를 설정합니다.

* **activationEvaluationWindow**: *K* 초
* **activationSignalOffset**: *-X* 초
* **minimumActivationWindow**: *Y* 초
* **maximumActivationWindow**: *Z* 초

다음은 **신호 게이트 프로세서** 노드 섹션이 파이프라인 토폴로지에서 다음 매개 변수 값을 어떻게 찾는지 보여주는 예입니다.

* **activationEvaluationWindow**: 1초
* **activationSignalOffset**: -5초
* **minimumActivationTime**: 20초
* **maximumActivationTime**: 40초

> [!IMPORTANT]
> [ISO 8601 기간 형식](https://en.wikipedia.org/wiki/ISO_8601#Durations)은 각 매개 변수 값에 필요합니다. 예를 들어 PT1S는 1초입니다.

```
"processors":              
[
          {
            "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```

이제 이 신호 게이트 프로세서 구성이 다양한 녹화 시나리오에서 어떻게 동작하는지 살펴보겠습니다.

### <a name="recording-scenarios"></a>녹화 시나리오

**한 원본의 한 이벤트(*일반 활성화*)**

신호 게이트 프로세서는 이벤트가 게이트에 도착하기 5초(활성화 신호=5초) 전에 시작되는 녹화의 이벤트 결과 하나를 수신합니다. 게이트를 다시 트리거하는 최소 활성화 시간이 끝나기 전에 다른 이벤트가 도착하지 않으므로 나머지 녹화는 20초(최소 활성화 시간=20초)입니다.

예제 다이어그램:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/normal-activation.svg" alt-text="한 원본의 한 이벤트가 정상적으로 활성화되는 것을 보여주는 다이어그램":::

* 녹화 시간 = -오프셋 + minimumActivationTime = [E1+오프셋, E1+minimumActivationTime]

**한 원본의 두 이벤트(*다시 트리거된 활성화*)**

신호 게이트 프로세서는 이벤트가 게이트에 도착하기 5초(활성화 신호 오프셋=5초) 전에 시작되는 녹화의 이벤트 결과 2개를 수신합니다. 또한 이벤트 2는 이벤트 1보다 5초 늦게 도착합니다. 이벤트 2는 이벤트 1의 최소 활성화 시간(20초)이 끝나기 전에 도착하므로 게이트가 다시 트리거됩니다. 게이트를 다시 트리거하는 이벤트 2의 최소 활성화 시간이 끝나기 전에 다른 이벤트가 도착하지 않으므로 나머지 녹화는 20초(최소 활성화 시간=20초)입니다.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/retriggering-activation.svg" alt-text="한 원본의 2개 이벤트 활성화가 다시 트리거된 것을 보여주는 다이어그램":::

* 녹화 시간 = -오프셋 + (이벤트 2 도착 시간 - 이벤트 1 도착 시간) + minimumActivationTime

**한 원본의 *N* 개 이벤트(*최대 활성화*)**

신호 게이트 프로세서는 첫 번째 이벤트가 게이트에 도착하기 5초(활성화 신호 오프셋=5초) 전에 시작되는 녹화의 이벤트 결과 *N* 개를 수신합니다. 각 이벤트는 이전 이벤트의 최소 활성화 시간인 20초가 끝나기 전에 도착하므로 게이트가 지속적으로 다시 트리거됩니다. 첫 번째 이벤트 이후 최대 활성화 시간인 40초가 경과할 때까지 게이트가 열린 상태로 유지됩니다. 그 후 게이트가 닫히고 더 이상 새 이벤트를 허용하지 않습니다.

예제 다이어그램:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/maximum-activation.svg" alt-text="원본의 N개 이벤트 최대 활성화를 보여주는 다이어그램":::
 
* 녹화 시간 = -오프셋 + maximumActivationTime

> [!IMPORTANT]
> 위의 다이어그램에서는 모든 이벤트가 실제 시간과 미디어 시간에서 동일한 인스턴트에 도착한다고 가정합니다. 즉, 지연 도착이 없다고 가정합니다.

### <a name="naming-video-or-files"></a>비디오 또는 파일 이름 지정

파이프라인을 사용하면 클라우드 또는 에지 디바이스에서 MP4 파일로 비디오를 녹화할 수 있습니다. [연속 비디오 녹화](use-continuous-video-recording.md) 또는 [이벤트 기반 비디오 녹화](record-event-based-live-video.md)를 통해 생성될 수 있습니다.

클라우드에 기록하는 데 권장되는 명명 구조는 비디오 리소스의 이름을 로 지정하는 `<anytext>-${System.TopologyName}-${System.PipelineName}` 것입니다. 지정된 라이브 파이프라인은 하나의 RTSP 지원 IP 카메라에만 연결할 수 있으며, 해당 카메라의 입력을 하나의 비디오 리소스에 기록해야 합니다. 예를 들어 다음과 같이 비디오 싱크에서 `VideoName`을 설정할 수 있습니다.

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}"
```
대체 패턴은 `$` 기호 뒤에 중괄호를 사용해서 정의합니다( **${variableName}** ).

이벤트 기반 기록을 사용하여 에지 디바이스의 MP4 파일에 기록할 때 다음을 사용할 수 있습니다.

```
"fileNamePattern": "sampleFilesFromEVR-${System.TopologyName}-${System.PipelineName}-${fileSinkOutputName}-${System.Runtime.DateTime}"
```

> [!Note]
> 위의 예에서 **fileSinkOutputName** 변수는 라이브 파이프라인을 만드는 시기를 정의한 샘플 변수 이름입니다. 시스템 변수가 **아닙니다**. **DateTime** 을 사용하면 각 이벤트에 대해 고유한 MP4 파일 이름이 어떻게 보장되는지 확인합니다.

#### <a name="system-variables"></a>시스템 변수

사용할 수 있는 시스템 정의 변수는 다음과 같습니다.

| 시스템 변수        | Description                                                  | 예제              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | ISO8601 파일 호환 형식의 UTC 날짜 시간(기본 표현 YYYYMMDDThhmmss)입니다. | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | ISO8601 파일 호환 형식의 밀리초를 포함하는 UTC 날짜 시간(기본 표현 YYYYMMDDThhmmss.sss)입니다. | 20200222T173200.123Z |
| System.TopologyName    | 실행 중인 파이프라인 토폴로지의 사용자 제공 이름입니다.          | IngestAndRecord      |
| System.PipelineName    | 실행 중인 라이브 파이프라인의 사용자 제공 이름입니다.          | camera001            |

> [!Tip]
> 클라우드에서 비디오 이름을 지정할 때 System.Runtime.PreciseDateTime 및 System.Runtime.DateTime을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[이벤트 기반 비디오 녹화 자습서](record-event-based-live-video.md)를 진행합니다. 먼저 [topology.json](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) 파일을 편집합니다. signalgateProcessor 노드에 대한 매개 변수를 수정한 다음, 자습서의 나머지 부분을 진행합니다. 비디오 녹화를 검토하여 매개 변수의 효과를 분석합니다.
