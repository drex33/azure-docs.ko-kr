---
title: 지원되는 공간 분석 작업 - Azure
description: 이 참조 문서에서는 Azure Video Analyzer에서 지원하는 공간 분석 작업의 다양한 속성을 자세히 설명합니다.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 448bbf4231620118fd187ed5a9e38f357a5acf1c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103071"
---
# <a name="supported-spatial-analysis-operations"></a>지원되는 공간 분석 작업

공간 분석을 사용하면 카메라 디바이스에서 실시간 스트리밍 비디오를 분석할 수 있습니다. 구성하는 각 카메라 디바이스에 대해 작업은 Azure Video Analyzer로 전송된 JSON 메시지의 출력 스트림을 생성합니다. 

Video Analyzer는 다음과 같은 공간 분석 작업을 구현합니다. 

| 작업 식별자| 설명|
|---------|---------|
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation | 사람이 영역에 들어오거나 나갈 때 _personZoneEnterExitEvent_ 이벤트를 내보내고, 교차된 영역의 번호가 매겨진 변이 포함된 방향 정보를 제공합니다. 사용자가 영역을 나갈 때 _personZoneDwellTimeEvent_ 를 내보내고, 방향 정보와 사람이 영역 내에서 머문 시간(밀리초)을 제공합니다. |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation | 카메라의 시야에서 사람이 지정된 선을 벗어나는 경우를 추적합니다.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation | 거리 규칙을 위반하는 경우를 추적합니다.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation | 카메라 시야의 지정된 영역에 있는 사람을 계산합니다. PersonCount에서 정확한 합계를 기록하려면 단일 카메라에서 영역 전체를 완전히 포함해야 합니다.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation | 위에서 언급한 모든 시나리오를 실행하는 데 사용할 수 있는 일반 작업입니다. 이 옵션은 동일한 카메라에서 여러 시나리오를 실행하거나 시스템 리소스(예: GPU)를 더 효율적으로 사용하려는 경우에 유용합니다. |



## <a name="person-zone-crossing"></a>사람 영역 교차

**작업 식별자:**`Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation`

GitHub 샘플에서 [Person Zone 교차 작업의](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json) 예를 참조하세요.

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                     | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| `eventType`                 | 문자열  | cognitiveservices.vision.spatialanalysis-personcrossingpolygon의 경우 `zonecrossing` 또는 `zonedwelltime`이어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `trigger`                   | 문자열  | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값: "event": 누군가가 영역에 들어오거나 나갈 때 발생합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `focus`                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 16입니다. 이는 최대 정확도를 얻기 위해 권장되는 값입니다.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 TRACKER_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### <a name="orientation-parameter-settings"></a>방향 매개 변수 설정

DETECTOR_NODE_CONFIG 매개 변수 설정을 통해 방향 계산을 구성할 수 있습니다.
```json
{
    "enable_orientation": true,
}

```
| 이름                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_orientation`                     | bool    | 검색된 사람의 방향을 계산할지 여부를 나타냅니다. `enable_orientation` 는 기본적으로 True로 설정됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
### <a name="speed-parameter-settings"></a>속도 매개 변수 설정

TRACKER_NODE_CONFIG 매개 변수 설정을 통해 속도 계산을 구성할 수 있습니다.
```json
{
    "enable_speed": true,
}

```
| Name                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_speed`                     | bool    | 검색된 사람의 속도를 계산할지 여부를 나타냅니다. `enable_speed` 는 기본적으로 True로 설정됩니다. 속도와 방향이 모두 최상의 예상 값을 가질 수 있도록 하는 것이 좋습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

#### <a name="output"></a>출력:
```json
{
  "body": {
    "timestamp": 147026846756730,
    "inferences": [
      {
        "type": "entity",
        "inferenceId": "8e8269c1a9584b3a8f16a3cd7a2cd45a",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.9511422
          },
          "box": {
            "l": 0.59845686,
            "t": 0.35958588,
            "w": 0.11951797,
            "h": 0.50172085
          }
        },
        "extensions": {
          "centerGroundPointY": "0.0",
          "footprintY": "inf",
          "centerGroundPointX": "0.0",
          "mappedImageOrientation": "inf",
          "groundOrientationAngle": "inf",
          "footprintX": "inf",
          "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
          "speed": "0.0"
        }
      },
      {
        "type": "entity",
        "inferenceId": "c54c9f92dd0d442b8d1840756715a5c7",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.92762595
          },
          "box": {
            "l": 0.8098704,
            "t": 0.47707137,
            "w": 0.18019487,
            "h": 0.48659682
          }
        },
        "extensions": {
          "footprintY": "inf",
          "groundOrientationAngle": "inf",
          "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
          "mappedImageOrientation": "inf",
          "speed": "0.0",
          "centerGroundPointX": "0.0",
          "centerGroundPointY": "0.0",
          "footprintX": "inf"
        }
      },
      {
        "type": "event",
        "inferenceId": "aad2778756a94afd9055fdbb3a370d62",
        "relatedInferences": [
          "8e8269c1a9584b3a8f16a3cd7a2cd45a"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
            "zone": "retailstore",
            "status": "Enter"
          }
        }
      },
      {
        "type": "event",
        "inferenceId": "e30103d3af28485688d7c77bbe10b5b5",
        "relatedInferences": [
          "c54c9f92dd0d442b8d1840756715a5c7"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
            "status": "Enter",
            "zone": "retailstore"
          }
        }
      }
    ]
  }
```

## <a name="person-line-crossing"></a>사람 줄 교차

**작업 식별자:**`Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation`

GitHub 샘플에서 [사람 선 교차 작업의](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json) 예를 참조하세요.

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | 설명                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lines`                     | list    | 선 목록입니다.                                                                                                                                                                                                                                                                |
| `name`                      | 문자열  | 이 선에 대한 식별 이름입니다.                                                                                                                                                                                                                                                  |
| `line`                      | 문자열  | 각 값 쌍은 선의 시작 지점과 끝 지점을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다.                            |
| `start`                      | 값 쌍  | 선의 시작 지점에 대한 x, y 좌표입니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다.                            |
| `end`                      | 값 쌍  | 선의 끝 지점에 대한 x, y 좌표입니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다.                            |
| `type`                     | 문자열  | 여야 `linecrossing` 합니다. |
| `trigger`                     | 문자열  | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값: "event": 누군가가 선을 벗어날 때 발생합니다.|
| `outputFrequency`           | int     | 이벤트가 송신되는 속도입니다. outputFrequency = X이면 모든 X 이벤트가 송신됩니다. 예: outputFrequency = 2는 다른 모든 이벤트가 출력임을 의미합니다. outputFrequency는 이벤트와 간격 모두에 적용할 수 있습니다.                                                       |
| `focus`                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다. |
| `threshold`                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 16입니다. 이는 최대 정확도를 얻기 위해 권장되는 값입니다.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.          |
| `detectorNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                      |
| `trackerNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 TRACKER_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="person-distance"></a>사람 거리

**작업 식별자:**`Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation`

GitHub 샘플에서 [Person Distance Operation의](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json) 예를 참조하세요.

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| `trigger`           | 문자열     | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값은 개수가 변경되면 이벤트를 보내는 event 또는 개수가 변경되었는지 여부에 관계없이 정기적으로 이벤트를 보내는 interval입니다.                                                                                                                                                                                                                                                                                                                                                     |
| `focus`                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| `threshold`                | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `outputFrequency`           | int     | 이벤트가 송신되는 속도입니다. outputFrequency = X이면 모든 X 이벤트가 송신됩니다. 예: outputFrequency = 2는 다른 모든 이벤트가 출력임을 의미합니다. outputFrequency는 이벤트와 간격 모두에 적용할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                     |
| `minimumDistanceThreshold`  | float   | 사람이 이 거리보다 덜 떨어져 있을 때 "TooClose" 이벤트를 트리거하는 거리(피트)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `maximumDistanceThreshold`  | float   | 사람이 이 거리보다 더 떨어져 있을 때 "TooFar"이벤트를 트리거하는 거리(피트)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `aggregationMethod`         | 문자열  | 집계된 persondistance 결과에 대한 메서드입니다. aggregationMethod는 모드와 평균에 모두 적용됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `enableFaceMaskClassifier`  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 TRACKER_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

## <a name="person-count"></a>사람 수

**작업 식별자:**`Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation`

GitHub 샘플에서 [Person Count Operation의](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json) 예를 참조하세요.

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| `outputFrequency`           | int     | 이벤트가 송신되는 속도입니다. outputFrequency = X이면 모든 X 이벤트가 송신됩니다. 예: outputFrequency = 2는 다른 모든 이벤트가 출력임을 의미합니다. outputFrequency는 이벤트와 간격 모두에 적용할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                     |
| `trigger`                   | 문자열  | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값은 개수가 변경되면 이벤트를 보내는 event 또는 개수가 변경되었는지 여부에 관계없이 정기적으로 이벤트를 보내는 interval입니다.                                                                                                                                                                                                                                                                                                                                                           |
| `focus`                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enableFaceMaskClassifier`  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | 문자열  | 모든 공간 분석 작업에 대한 TRACKER_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

## <a name="custom-operation"></a>사용자 지정 작업

**작업 식별자:**`Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation`

GitHub 샘플에서 [사용자 지정 작업의](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json) 예제를 참조하세요.

#### <a name="parameters"></a>매개 변수

| 이름                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | 문자열 | 작업의 JSON 표현입니다. |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```
