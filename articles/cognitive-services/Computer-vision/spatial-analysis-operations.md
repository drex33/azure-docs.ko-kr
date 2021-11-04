---
title: 공간 분석 작업
titleSuffix: Azure Cognitive Services
description: 공간 분석 작업입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: 845c2e7080ccc56d49e0d930d041c55f88710aab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033497"
---
# <a name="spatial-analysis-operations"></a>공간 분석 작업

공간 분석을 사용하면 카메라 디바이스에서 실시간 스트리밍 비디오를 분석할 수 있습니다. 구성하는 각 카메라 디바이스의 경우 공간 분석 작업에서 Azure IoT Hub 인스턴스로 보내는 JSON 메시지의 출력 스트림을 생성합니다. 

공간 분석 컨테이너에서 구현하는 작업은 다음과 같습니다.

| 작업 식별자| 설명|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | 카메라 시야의 지정된 영역에 있는 사람을 계산합니다. PersonCount에서 정확한 합계를 기록하려면 단일 카메라에서 영역 전체를 완전히 포함해야 합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음, 개수가 변경되면 _personCountEvent_ 이벤트를 내보냅니다.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | 카메라의 시야에서 사람이 지정된 선을 벗어나는 경우를 추적합니다. <br>사람이 선을 벗어날 때 _personLineEvent_ 이벤트를 내보내고, 방향 정보를 제공합니다. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | 사람이 영역에 들어오거나 나갈 때 _personZoneEnterExitEvent_ 이벤트를 내보내고, 교차된 영역의 번호가 매겨진 변이 포함된 방향 정보를 제공합니다. 사용자가 영역을 나갈 때 _personZoneDwellTimeEvent_ 를 내보내고, 방향 정보와 사람이 영역 내에서 머문 시간(밀리초)을 제공합니다. |
| cognitiveservices.vision.spatialanalysis-persondistance | 거리 규칙을 위반하는 경우를 추적합니다. <br> 각 거리 위반 위치가 포함된 _personDistanceEvent_ 를 정기적으로 내보냅니다. |
| cognitiveservices.vision.spatialanalysis | 위에서 언급한 모든 시나리오를 실행하는 데 사용할 수 있는 일반 작업입니다. 이 옵션은 동일한 카메라에서 여러 시나리오를 실행하거나 시스템 리소스(예: GPU)를 더 효율적으로 사용하려는 경우에 유용합니다. |

위의 모든 작업은 처리되는 비디오 프레임을 시각화하는 기능이 있는 `.debug` 버전에서도 사용할 수 있습니다. 비디오 프레임 및 이벤트의 시각화를 사용하도록 설정하려면 호스트 컴퓨터에서 `xhost +`를 실행해야 합니다.

작업 식별자| 설명
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | 카메라 시야의 지정된 영역에 있는 사람을 계산합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음, 개수가 변경되면 _personCountEvent_ 이벤트를 내보냅니다.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | 카메라의 시야에서 사람이 지정된 선을 벗어나는 경우를 추적합니다. <br>사람이 선을 벗어날 때 _personLineEvent_ 이벤트를 내보내고, 방향 정보를 제공합니다. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | 사람이 영역에 들어오거나 나갈 때 _personZoneEnterExitEvent_ 이벤트를 내보내고, 교차된 영역의 번호가 매겨진 변이 포함된 방향 정보를 제공합니다. 사용자가 영역을 나갈 때 _personZoneDwellTimeEvent_ 를 내보내고, 방향 정보와 사람이 영역 내에서 머문 시간(밀리초)을 제공합니다. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | 거리 규칙을 위반하는 경우를 추적합니다. <br> 각 거리 위반 위치가 포함된 _personDistanceEvent_ 를 정기적으로 내보냅니다. |
| cognitiveservices.vision.spatialanalysis.debug | 위에서 언급한 모든 시나리오를 실행하는 데 사용할 수 있는 일반 작업입니다. 이 옵션은 동일한 카메라에서 여러 시나리오를 실행하거나 시스템 리소스(예: GPU)를 더 효율적으로 사용하려는 경우에 유용합니다. |

공간 분석은 [Live Video Analytics](../../azure-video-analyzer/video-analyzer-docs/overview.md)에서 비디오 AI 모듈로 실행할 수도 있습니다. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 작업 식별자| 설명|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | 카메라 시야의 지정된 영역에 있는 사람을 계산합니다. <br> 초기 _personCountEvent_ 이벤트를 내보낸 다음, 개수가 변경되면 _personCountEvent_ 이벤트를 내보냅니다.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | 카메라의 시야에서 사람이 지정된 선을 벗어나는 경우를 추적합니다. <br>사람이 선을 벗어날 때 _personLineEvent_ 이벤트를 내보내고, 방향 정보를 제공합니다. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | 사람이 영역에 들어오거나 나갈 때 _personZoneEnterExitEvent_ 이벤트를 내보내고, 교차된 영역의 번호가 매겨진 변이 포함된 방향 정보를 제공합니다. 사용자가 영역을 나갈 때 _personZoneDwellTimeEvent_ 를 내보내고, 방향 정보와 사람이 영역 내에서 머문 시간(밀리초)을 제공합니다.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | 거리 규칙을 위반하는 경우를 추적합니다. <br> 각 거리 위반 위치가 포함된 _personDistanceEvent_ 를 정기적으로 내보냅니다. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | 위에서 언급한 모든 시나리오를 실행하는 데 사용할 수 있는 일반 작업입니다. 이 옵션은 동일한 카메라에서 여러 시나리오를 실행하거나 시스템 리소스(예: GPU)를 더 효율적으로 사용하려는 경우에 유용합니다. |

Live Video Analytics 작업은 처리되는 비디오 프레임을 시각화하는 기능이 있는 `.debug` 버전(예: cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug)에서도 사용할 수 있습니다. 비디오 프레임 및 이벤트의 시각화를 사용하도록 설정하려면 호스트 컴퓨터에서 `xhost +`를 실행해야 합니다.

> [!IMPORTANT]
> 컴퓨터 비전 AI 모델은 인체 주위의 경계 상자를 사용하여 비디오 장면과 출력에서 인간의 현재 상태를 검색하여 찾습니다. AI 모델은 개인의 ID 또는 인구 통계를 검색하려고 시도하지 않습니다.

이러한 각 공간 분석 작업에 필요한 매개 변수는 다음과 같습니다.

| 조작 매개 변수| 설명|
|---------|---------|
| OperationID | 위 표의 작업 식별자입니다.|
| 사용 | 부울: true 또는 false|
| VIDEO_URL| 카메라 디바이스에 대한 RTSP URL(예: `rtsp://username:password@url`)입니다. 공간 분석은 RTSP, http 또는 mp4를 통해 H.264로 인코딩된 스트림을 지원합니다. Video_URL은 AES 암호화를 이용하여 난독 처리된 base64 문자열 값으로 제공할 수 있으며, 비디오 URL이 난독 처리된 경우 `KEY_ENV` 및 `IV_ENV`를 환경 변수로 제공해야 합니다. 키 및 암호화를 생성하는 샘플 유틸리티는 [여기](/dotnet/api/system.security.cryptography.aesmanaged)서 확인할 수 있습니다. |
| VIDEO_SOURCE_ID | 카메라 디바이스 또는 비디오 스트림에 대한 식별 이름입니다. 이벤트 JSON 출력과 함께 반환됩니다.|
| VIDEO_IS_LIVE| 카메라 디바이스의 경우 true이고, 기록된 비디오의 경우 false입니다.|
| VIDEO_DECODE_GPU_INDEX| 비디오 프레임을 디코딩하는 GPU입니다. 기본값은 0입니다. 및 와 같은 다른 노드 구성의 와 동일해야 `gpu_index` `DETECTOR_NODE_CONFIG` `CAMERACALIBRATOR_NODE_CONFIG` 합니다.|
| INPUT_VIDEO_WIDTH | 입력 비디오/스트림의 프레임 너비(예: 1920)입니다. 선택적인 필드이며, 제공되는 경우 프레임은 가로 세로 비율을 유지하면서 이 크기로 조정됩니다.|
| DETECTOR_NODE_CONFIG | 탐지기 노드를 실행할 GPU를 나타내는 JSON입니다. `"{ \"gpu_index\": 0 }",` 형식이어야 합니다.|
| TRACKER_NODE_CONFIG | 추적기 노드에서 속도를 계산할지 여부를 나타내는 JSON입니다. `"{ \"enable_speed\": true }",` 형식이어야 합니다.|
| CAMERA_CONFIG | 여러 카메라에 대해 보정된 카메라 매개 변수를 나타내는 JSON입니다. 사용한 기술에 보정이 필요하고 카메라 매개 변수가 이미 있는 경우 이 구성을 사용하여 직접 제공할 수 있습니다. `"{ \"cameras\": [{\"source_id\": \"endcomputer.0.persondistancegraph.detector+end_computer1\", \"camera_height\": 13.105561256408691, \"camera_focal_length\": 297.60003662109375, \"camera_tiltup_angle\": 0.9738943576812744}] }"`, `source_id` 형식은 각 카메라를 식별하는 데 사용됩니다. 게시한 이벤트의 `source_info`에서 가져올 수 있습니다. `do_calibration=false`가 `DETECTOR_NODE_CONFIG`에 있을 때만 적용됩니다.|
| CAMERACALIBRATOR_NODE_CONFIG | 카메라 보정기 노드를 실행할 GPU 및 보정 사용 여부를 나타내는 JSON입니다. `"{ \"gpu_index\": 0, \"do_calibration\": true, \"enable_orientation\": true}",` 형식이어야 합니다.|
| CALIBRATION_CONFIG | 카메라 보정의 작동 방식을 제어하는 매개 변수를 나타내는 JSON입니다. `"{\"enable_recalibration\": true, \"quality_check_frequency_seconds\": 86400}",` 형식이어야 합니다.|
| SPACEANALYTICS_CONFIG | 아래에서 설명한 대로 영역 및 선에 대한 JSON 구성입니다.|
| ENABLE_FACE_MASK_CLASSIFIER | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 `True`이고, 사용하지 않도록 설정하려면 `False`입니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 `"INPUT_VIDEO_WIDTH": 1920`이어야 합니다. 감지된 사람이 카메라를 향하고 있지 않거나 카메라에서 너무 멀리 떨어져 있으면 얼굴 마스크 특성이 반환되지 않습니다. 자세한 내용은 [카메라 배치](spatial-analysis-camera-placement.md) 가이드를 참조하세요. |

### <a name="detector-node-parameter-settings"></a>탐지기 노드 매개 변수 설정
다음은 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수의 예제입니다.

```json
{
"gpu_index": 0,
"enable_breakpad": false
}
```

| Name | Type| Description|
|---------|---------|---------|
| `gpu_index` | 문자열| 이 작업이 실행되는 GPU 인덱스입니다.|
| `enable_breakpad`| bool | 디버그 사용을 위해 크래시 덤프를 생성하는 데 사용되는 중단 패드를 사용할지 여부를 나타냅니다. 기본값은 `false`입니다. `true`로 설정하면 `"CapAdd": ["SYS_PTRACE"]`도 `createOptions` 컨테이너의 `HostConfig` 부분에 추가해야 합니다. 기본적으로 크래시 덤프는 [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter 앱에 업로드됩니다. 크래시 덤프를 사용자 고유의 AppCenter 앱에 업로드하려면 `RTPT_APPCENTER_APP_SECRET` 환경 변수를 앱의 앱 비밀로 재정의할 수 있습니다.|

### <a name="camera-calibration-node-parameter-settings"></a>카메라 보정 노드 매개 변수 설정
이는 모든 공간 `CAMERACALIBRATOR_NODE_CONFIG` 분석 작업에 대한 매개 변수의 예입니다.

```json
{
  "gpu_index": 0,
  "do_calibration": true,
  "enable_breakpad": false,
  "enable_orientation": true
}
```

| 이름 | Type | Description |
|---------|---------|---------|
| `do_calibration` | 문자열 | 보정이 설정되어 있음을 나타냅니다. **cognitiveservices.vision.spatialanalysis-persondistance** 가 제대로 작동하려면 `do_calibration`이 true여야 합니다. `do_calibration` 는 기본적으로 로 `True` 설정됩니다. |
| `enable_breakpad`| bool | 디버그 사용을 위해 크래시 덤프를 생성하는 데 사용되는 중단 패드를 사용할지 여부를 나타냅니다. 기본값은 `false`입니다. `true`로 설정하면 `"CapAdd": ["SYS_PTRACE"]`도 `createOptions` 컨테이너의 `HostConfig` 부분에 추가해야 합니다. 기본적으로 크래시 덤프는 [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter 앱에 업로드됩니다. 크래시 덤프를 사용자 고유의 AppCenter 앱에 업로드하려면 `RTPT_APPCENTER_APP_SECRET` 환경 변수를 앱의 앱 비밀로 재정의할 수 있습니다.
| `enable_orientation` | bool | 검색된 사람의 방향을 계산할지 여부를 나타냅니다. `enable_orientation` 는 기본적으로 로 `True` 설정됩니다. |

### <a name="calibration-config"></a>보정 구성

이는 모든 공간 `CALIBRATION_CONFIG` 분석 작업에 대한 매개 변수의 예입니다.

```json
{
  "enable_recalibration": true,
  "calibration_quality_check_frequency_seconds": 86400,
  "calibration_quality_check_sample_collect_frequency_seconds": 300,
  "calibration_quality_check_one_round_sample_collect_num": 10,
  "calibration_quality_check_queue_max_size": 1000,
  "calibration_event_frequency_seconds": -1
}
```

| 이름 | Type| Description|
|---------|---------|---------|
| `enable_recalibration` | bool | 자동 다시 보정이 설정되어 있는지 여부를 나타냅니다. 기본값은 `true`입니다.|
| `calibration_quality_check_frequency_seconds` | int | 다시 보정이 필요한지 여부를 결정하기 위한 각 품질 검사 사이의 최소 시간(초)입니다. 기본값은 `86400`(24시간)입니다. `enable_recalibration=True`인 경우에만 사용됩니다.|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | 다시 보정할 새 데이터 샘플 수집과 품질 검사 사이의 최소 시간(초)입니다. 기본값은 `300`(5분)입니다. `enable_recalibration=True`인 경우에만 사용됩니다.|
| `calibration_quality_check_one_round_sample_collect_num` | int | 샘플 수집 라운드 단위로 수집할 새 데이터 샘플의 최소 수입니다. 기본값은 `10`입니다. `enable_recalibration=True`인 경우에만 사용됩니다.|
| `calibration_quality_check_queue_max_size` | int | 카메라 모델이 보정될 때 저장할 최대 데이터 샘플 수입니다. 기본값은 `1000`입니다. `enable_recalibration=True`인 경우에만 사용됩니다.|
| `calibration_event_frequency_seconds` | int | 카메라 보정 이벤트의 출력 빈도(초)입니다. 값 `-1`을 지정하면 카메라 보정 정보가 변경되지 않은 경우 카메라 보정을 보내지 않아야 함을 나타냅니다. 기본값은 `-1`입니다.|

### <a name="camera-calibration-output"></a>카메라 보정 출력

사용하도록 설정한 경우 카메라 보정 출력에 대한 예입니다. 줄임표는 하나의 목록에 동일한 유형의 개체를 더 많이 표시합니다.

```json
{
  "type": "cameraCalibrationEvent",
  "sourceInfo": {
    "id": "camera1",
    "timestamp": "2021-04-20T21:15:59.100Z",
    "width": 640,
    "height": 360,
    "frameId": 531,
    "cameraCalibrationInfo": {
      "status": "Calibrated",
      "cameraHeight": 13.294151306152344,
      "focalLength": 372.0000305175781,
      "tiltupAngle": 0.9581864476203918,
      "lastCalibratedTime": "2021-04-20T21:15:59.058"
    }
  },
  "zonePlacementInfo": {
    "optimalZoneRegion": {
      "type": "POLYGON",
       "points": [
        {
          "x": 0.8403755868544601,
          "y": 0.5515320334261838
        },
        {
          "x": 0.15805946791862285,
          "y": 0.5487465181058496
        },
        ...
      ],
      "name": "optimal_zone_region"
    },
    "fairZoneRegion": {
      "type": "POLYGON",
      "points": [
        {
          "x": 0.7871674491392802,
          "y": 0.7437325905292479
        },
        {
          "x": 0.22065727699530516,
          "y": 0.7325905292479109
        },
        ...
      ],
      "name": "fair_zone_region"
    },
    "uniformlySpacedPersonBoundingBoxes": [
      {
        "type": "RECTANGLE",
        "points": [
          {
            "x": 0.0297339593114241,
            "y": 0.0807799442896936
          },
          {
            "x": 0.10015649452269171,
            "y": 0.2757660167130919
          }
        ]
      },
      ...
    ],
    "personBoundingBoxGroundPoints": [
      {
        "x": -22.944068908691406,
        "y": 31.487680435180664
      },
      ...
    ]
  }
}
```

`source_info`에 대한 자세한 내용은 [공간 분석 작업 출력](#spatial-analysis-operation-output)을 참조하세요.

| ZonePlacementInfo 필드 이름 | 형식| 설명|
|---------|---------|---------|
| `optimalZonePolygon` | object| 최적의 결과를 위해 작업의 선이나 영역을 배치할 수 있는 카메라 이미지의 다각형입니다. <br/> 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타내며, 다각형 점은 정규화된 좌표(0-1)를 기반으로 합니다. 여기서 왼쪽 위 모서리는 (0.0, 0.0)이고, 오른쪽 아래 모서리는 (1.0, 1.0)입니다.|
| `fairZonePolygon` | 개체| 양호하지만 최적의 결과를 제공하지는 못하는 작업의 선이나 영역을 배치할 수 있는 카메라 이미지의 다각형입니다. <br/> 콘텐츠에 대한 자세한 설명은 위의 `optimalZonePolygon`을 참조하세요. |
| `uniformlySpacedPersonBoundingBoxes` | list | 실제 공간에 균일하게 분산된 카메라 이미지 내에 있는 사람의 경계 상자 목록입니다. 값은 정규화된 좌표(0-1)를 기준으로 합니다.|
| `personBoundingBoxGroundPoints` | list | 카메라를 기준으로 하는 바닥면의 좌표 목록입니다. 각 좌표는 동일한 인덱스를 갖는 `uniformlySpacedPersonBoundingBoxes` 경계 상자의 오른쪽 하단 지점과 일치합니다. <br/> 바닥면의 좌표를 계산하는 방법에 대한 자세한 내용은 [cognitiveservices.vision.spatialanalysis-persondistance AI Insights에 대한 JSON 형식](#json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights) 섹션 아래의 `centerGroundPoint` 필드를 참조하세요. |

비디오 프레임에 시각화된 영역 배치 정보 출력 예제: ![영역 배치 정보 시각화](./media/spatial-analysis/zone-placement-info-visualization.png)

영역 배치 정보는 사용자 구성에 맞는 제안을 제공하지만 최상의 결과를 위해서는 [카메라 구성](#camera-configuration)의 지침을 따라야 합니다.

### <a name="speed-parameter-settings"></a>속도 매개 변수 설정
추적기 노드 매개 변수 설정을 통해 속도 계산을 구성할 수 있습니다.
```
{
"enable_speed": true,
}
```
| 속성 | Type| Description|
|---------|---------|---------|
| `enable_speed` | bool | 검색된 사람의 속도를 계산할지 여부를 나타냅니다. `enable_speed` 는 기본적으로로 설정 됩니다 `True` . 속도와 방향을 모두 최고 예상 값으로 설정 하는 것이 좋습니다. |

## <a name="spatial-analysis-operations-configuration-and-output"></a>공간 분석 작업 구성 및 출력

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>cognitiveservices.vision.spatialanalysis-personcount에 대한 영역 구성

다음은 영역을 구성하는 SPACEANALYTICS_CONFIG 매개 변수에 대한 JSON 입력의 예제입니다. 이 작업에서는 여러 영역을 구성할 수 있습니다.

```json
{
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
      "events": [
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```

| Name | Type| 설명|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역에 대한 식별 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타내며, 다각형 점은 정규화된 좌표(0-1)를 기반으로 합니다. 여기서 왼쪽 위 모서리는 (0.0, 0.0)이고, 오른쪽 아래 모서리는 (1.0, 1.0)입니다.   
| `threshold` | float| 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. |
| `type` | 문자열| **cognitiveservices.vision.spatialanalysis-personcount** 의 경우 `count`여야 합니다.|
| `trigger` | 문자열| 이벤트를 보내는 트리거의 형식입니다. 지원되는 값은 개수가 변경되면 이벤트를 보내는 `event` 또는 개수가 변경되었는지 여부에 관계없이 정기적으로 이벤트를 보내는 `interval`입니다.
| `output_frequency` | int | 이벤트가 송신되는 속도입니다. `output_frequency` = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 `output_frequency` = 2는 다른 모든 이벤트가 출력됨을 의미합니다. `output_frequency`는 `event` 및 `interval` 모두에 적용됩니다. |
| `focus` | 문자열| 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 `footprint`(사람의 공간), `bottom_center`(사람의 경계 상자의 아래쪽 가운데), `center`(사람의 경계 상자의 중심)일 수 있습니다.|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>cognitiveservices.vision.spatialanalysis-personcrossingline에 대한 선 구성

다음은 선을 구성하는 SPACEANALYTICS_CONFIG 매개 변수에 대한 JSON 입력의 예제입니다. 이 작업에서는 여러 교차 선을 구성할 수 있습니다.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Name | Type| 설명|
|---------|---------|---------|
| `lines` | list| 선 목록입니다.|
| `name` | 문자열| 이 선에 대한 식별 이름입니다.|
| `line` | list| 선의 정의입니다. "진입" 및 "진출"을 이해할 수 있도록 하는 방향 선입니다.|
| `start` | 값 쌍| 선의 시작 지점에 대한 x, y 좌표입니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. |
| `end` | 값 쌍| 선의 끝 지점에 대한 x, y 좌표입니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. |
| `threshold` | float| 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 16입니다. 이는 최대 정확도를 얻기 위해 권장되는 값입니다. |
| `type` | 문자열| **cognitiveservices.vision.spatialanalysis-personcrossingline** 의 경우 `linecrossing`이어야 합니다.|
|`trigger`|문자열|이벤트를 보내는 트리거의 형식입니다.<br>지원되는 값: "event": 누군가가 선을 벗어날 때 발생합니다.|
| `focus` | 문자열| 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 `footprint`(사람의 공간), `bottom_center`(사람의 경계 상자의 아래쪽 가운데), `center`(사람의 경계 상자의 중심)일 수 있습니다. 기본값은 footprint입니다.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon에 대한 영역 구성

다음은 영역을 구성하는 SPACEANALYTICS_CONFIG 매개 변수에 대한 JSON 입력의 예제입니다. 이 작업에서는 여러 영역을 구성할 수 있습니다.

```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Name | Type| 설명|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역에 대한 식별 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. 
| `target_side` | int| `polygon`으로 정의된 영역의 측면을 지정하여 사용자가 영역에 있는 동안 해당 측면을 마주하는 시간을 측정합니다. 'dwellTimeForTargetSide'는 예상 시간을 출력합니다. 각 변은 영역을 나타내는 다각형의 두 꼭짓점 사이에 번호가 매겨진 가장자리입니다. 예를 들어 다각형의 처음 두 꼭짓점 사이의 가장자리는 첫 번째 변 'side'=1을 나타냅니다. `target_side`의 값은 `[0,N-1]` 사이에 있으며, 여기서 `N`은 `polygon`의 변의 수입니다. 옵션 필드입니다.  |
| `threshold` | float| 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다.  |
| `type` | 문자열| **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** 의 경우 `zonecrossing` 또는 `zonedwelltime`이어야 합니다.|
| `trigger`|문자열|이벤트를 보내는 트리거의 형식입니다.<br>지원되는 값: "event": 누군가가 영역에 들어오거나 나갈 때 발생합니다.|
| `focus` | 문자열| 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 `footprint`(사람의 공간), `bottom_center`(사람의 경계 상자의 아래쪽 가운데), `center`(사람의 경계 상자의 중심)일 수 있습니다. 기본값은 footprint입니다.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>cognitiveservices.vision.spatialanalysis-persondistance에 대한 영역 구성

다음은 **cognitiveservices.vision.spatialanalysis-persondistance** 에 대한 영역을 구성하는 SPACEANALYTICS_CONFIG 매개 변수에 대한 JSON 입력의 예제입니다. 이 작업에서는 여러 영역을 구성할 수 있습니다.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Name | Type| 설명|
|---------|---------|---------|
| `zones` | list| 영역 목록입니다. |
| `name` | 문자열| 이 영역에 대한 식별 이름입니다.|
| `polygon` | list| 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 계산되고 사람 사이의 거리가 측정되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. 
| `threshold` | float| 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. |
| `type` | 문자열| **cognitiveservices.vision.spatialanalysis-persondistance** 의 경우 `persondistance`여야 합니다.|
| `trigger` | 문자열| 이벤트를 보내는 트리거의 형식입니다. 지원되는 값은 개수가 변경되면 이벤트를 보내는 `event` 또는 개수가 변경되었는지 여부에 관계없이 정기적으로 이벤트를 보내는 `interval`입니다.
| `output_frequency` | int | 이벤트가 송신되는 속도입니다. `output_frequency` = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 `output_frequency` = 2는 다른 모든 이벤트가 출력됨을 의미합니다. `output_frequency`는 `event` 및 `interval` 모두에 적용됩니다.|
| `minimum_distance_threshold` | float| 사람이 이 거리보다 덜 떨어져 있을 때 "TooClose" 이벤트를 트리거하는 거리(피트)입니다.|
| `maximum_distance_threshold` | float| 사람이 이 거리보다 더 떨어져 있을 때 "TooFar"이벤트를 트리거하는 거리(피트)입니다.|
| `aggregation_method` | 문자열| 집계된 persondistance 결과에 대한 메서드입니다. aggregation_method는 `mode` 및 `average` 모두에 적용됩니다.|
| `focus` | 문자열| 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 `footprint`(사람의 공간), `bottom_center`(사람의 경계 상자의 아래쪽 가운데), `center`(사람의 경계 상자의 중심)일 수 있습니다.|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>cognitiveservices.vision.spatialanalysis에 대한 구성
다음은 **cognitiveservices.vision.spatialanalysis** 에 대한 선과 영역을 구성하는 SPACEANALYTICS_CONFIG 매개 변수에 대한 JSON 입력의 예제입니다. 이 작업에서는 여러 선/영역을 구성할 수 있으며, 각 선/영역마다 다른 이벤트가 있을 수 있습니다.

```json
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```

## <a name="camera-configuration"></a>카메라 구성

영역 및 선을 구성하는 방법에 대한 자세한 내용은 [카메라 배치](spatial-analysis-camera-placement.md) 가이드를 참조하세요.

## <a name="spatial-analysis-operation-output"></a>공간 분석 작업 출력

각 작업의 이벤트는 JSON 형식으로 Azure IoT Hub로 송신됩니다.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcount AI 인사이트에 대한 JSON 형식

이 작업의 event 출력에 대한 JSON 샘플입니다.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| event 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사람 감지의 고유 식별자 크기 1의 배열|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 감지된 사람의 고유 식별자|
| `zone` | 문자열 | 교차된 영역을 나타내는 다각형의 "name" 필드|
| `trigger` | 문자열| SPACEANALYTICS_CONFIG의 `trigger` 값에 따라 'event' 또는 'interval'인 트리거 형식|

| detections 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 감지 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 유형이 RECTANGLE인 경우 왼쪽 위 및 오른쪽 아래 지점 |
| `confidence` | float| 알고리즘 신뢰도|
| `face_mask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있음을 나타냅니다. |
| `face_nomask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있지 **않음** 을 나타냅니다. |

| sourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|
| `cameraCallibrationInfo` | collection | 값 컬렉션|
| `status` | 문자열 | `state[;progress description]` 형식의 보정 상태입니다. 상태는 `Calibrating`, `Recalibrating`(다시 보정이 사용하도록 설정된 경우) 또는 `Calibrated`일 수 있습니다. 현재 보정 프로세스의 진행률을 표시하는 데 사용되는 `Calibrating` 및 `Recalibrating` 상태인 경우에만 진행률 설명 부분이 유효합니다.|
| `cameraHeight` | float | 지면 위의 카메라 높이(피트)입니다. 이는 자동 보정에서 유추됩니다. |
| `focalLength` | float | 카메라의 초점 길이(픽셀)입니다. 이는 자동 보정에서 유추됩니다. |
| `tiltUpAngle` | float | 세로 방향의 카메라 기울기 각도입니다. 이는 자동 보정에서 유추됩니다.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingline AI 인사이트에 대한 JSON 형식

이 작업의 detections 출력에 대한 JSON 샘플입니다.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| event 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사람 감지의 고유 식별자 크기 1의 배열|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 감지된 사람의 고유 식별자|
| `status` | 문자열| 선 교차 방향('CrossLeft' 또는 'CrossRight')입니다. 방향은 선의 "끝"을 향하여 "시작"에 위치해 있는 상상을 기반으로 합니다. CrossRight는 왼쪽에서 오른쪽으로 교차합니다. CrossLeft는 오른쪽에서 왼쪽으로 교차합니다.|
| `orientationDirection` | 문자열| 선을 통과한 후 감지된 사람의 방향입니다. 값은 'Left', 'Right 또는 'Straight'일 수 있습니다. `CAMERACALIBRATOR_NODE_CONFIG`에서 `enable_orientation`이 `True`로 설정된 경우 이 값이 출력됩니다. |
| `zone` | 문자열 | 교차된 선의 "name" 필드|

| detections 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 감지 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 유형이 RECTANGLE인 경우 왼쪽 위 및 오른쪽 아래 지점 |
| `groundOrientationAngle` | float| 유추된 접지 평면에서 사람 방향의 시계 방향 방사형 각도 |
| `mappedImageOrientation` | float| 2D 이미지 공간에서 사람 방향의 프로젝션된 시계 방향 방사형 각도 |
| `speed` | float| 감지된 사람의 예상 속도입니다. 단위는 `foot per second (ft/s)`입니다.|
| `confidence` | float| 알고리즘 신뢰도|
| `face_mask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있음을 나타냅니다. |
| `face_nomask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있지 **않음** 을 나타냅니다. |

| sourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|


> [!IMPORTANT]
> AI 모델은 사람이 카메라를 향하고 있는지 또는 멀리 떨어져 있는지 여부에 관계없이 사람을 감지합니다. AI 모델은 얼굴 인식을 실행하지 않으며 생체 인식 정보를 내보내지 않습니다. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon AI 인사이트에 대한 JSON 형식

`zonecrossing` 형식 SPACEANALYTICS_CONFIG를 사용하는 이 작업의 detections 출력에 대한 JSON 샘플입니다.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

`zonedwelltime` 형식 SPACEANALYTICS_CONFIG를 사용하는 이 작업의 detections 출력에 대한 JSON 샘플입니다.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
                      "dwellTime": 7132.0,
                      "dwellFrames": 20            
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": "",
             "metadata": { 
                     "groundOrientationAngle": 1.2,
                     "mappedImageOrientation": 0.3,
                     "speed": 1.2
               },
        }
    ],
    "schemaVersion": "1.0"
}
```

| event 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형입니다. 값은 _personZoneDwellTimeEvent_ 또는 _personZoneEnterExitEvent_ 일 수 있습니다.|
| `detectionsId` | array| 이 이벤트를 트리거한 사람 감지의 고유 식별자 크기 1의 배열|
| `properties` | collection| 값 컬렉션|
| `trackinId` | 문자열| 감지된 사람의 고유 식별자|
| `status` | 문자열| 다각형 교차의 방향('Enter' 또는 'Exit')입니다.|
| `side` | int| 사람이 교차한 다각형 변의 번호입니다. 각 변은 영역을 나타내는 다각형의 두 꼭짓점 사이에 번호가 매겨진 가장자리입니다. 다각형의 처음 두 꼭짓점 사이의 가장자리는 첫 번째 변을 나타냅니다. 폐색으로 인해 이벤트가 특정 변과 연결되지 않은 경우 'side'는 비어 있습니다. 예를 들어 사람이 사라졌지만 영역의 한 변을 교차하지 않은 것으로 확인되면 진출이 발생했거나, 영역에 사람이 나타났지만 변을 교차하지 않은 것으로 확인되면 진입이 발생했습니다.|
| `dwellTime` | float | 사용자가 영역에서 머문 시간을 나타내는 밀리초 수입니다. 이벤트 형식이 personZoneDwellTimeEvent인 경우 이 필드가 제공됩니다.|
| `dwellFrames` | int | 사람이 영역에서 보낸 프레임 수입니다. 이벤트 형식이 personZoneDwellTimeEvent인 경우 이 필드가 제공됩니다.|
| `dwellTimeForTargetSide` | float | 사용자가 영역에서 보낸 시간과 `target_side`와 마주한 시간을 나타내는 밀리초 수입니다. 이 필드는 `enable_orientation`이 `CAMERACALIBRATOR_NODE_CONFIG `에서 `True`이고 `target_side`의 값이 `SPACEANALYTICS_CONFIG`로 설정된 경우에 제공됩니다.|
| `avgSpeed` | float| 영역에 있는 사람의 평균 속도입니다. 단위는 `foot per second (ft/s)`입니다.|
| `minSpeed` | float| 영역에 있는 사용자의 최소 속도입니다. 단위는 `foot per second (ft/s)`입니다.|
| `zone` | 문자열 | 교차된 영역을 나타내는 다각형의 "name" 필드|

| detections 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 감지 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 유형이 RECTANGLE인 경우 왼쪽 위 및 오른쪽 아래 지점 |
| `groundOrientationAngle` | float| 유추된 접지 평면에서 사람 방향의 시계 방향 방사형 각도 |
| `mappedImageOrientation` | float| 2D 이미지 공간에서 사람 방향의 프로젝션된 시계 방향 방사형 각도 |
| `speed` | float| 감지된 사람의 예상 속도입니다. 단위는 `foot per second (ft/s)`입니다.|
| `confidence` | float| 알고리즘 신뢰도|
| `face_mask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있음을 나타냅니다. |
| `face_nomask` | float | 범위(0-1)의 특성 신뢰도 값은 감지된 사람이 얼굴 마스크를 착용하고 있지 **않음** 을 나타냅니다. |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>cognitiveservices.vision.spatialanalysis-persondistance AI 인사이트에 대한 JSON 형식

이 작업의 detections 출력에 대한 JSON 샘플입니다.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| event 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 이벤트 ID|
| `type` | 문자열| 이벤트 유형|
| `detectionsId` | array| 이 이벤트를 트리거한 사람 감지의 고유 식별자 크기 1의 배열|
| `properties` | collection| 값 컬렉션|
| `personCount` | int| 이벤트를 내보냈을 때 감지된 사람 수|
| `averageDistance` | float| 감지된 모든 사람 사이의 평균 거리(피트)|
| `minimumDistanceThreshold` | float| 사람이 이 거리보다 덜 떨어져 있을 때 "TooClose" 이벤트를 트리거하는 거리(피트)입니다.|
| `maximumDistanceThreshold` | float| 사람이 이 거리보다 더 떨어져 있을 때 "TooFar"이벤트를 트리거하는 거리(피트)입니다.|
| `eventName` | 문자열| 이벤트 이름은 `minimumDistanceThreshold`를 위반하는 경우 `TooClose`이거나, `maximumDistanceThreshold`를 위반하는 경우 `TooFar`이거나, 자동 보정을 완료하지 않은 경우 `unknown`입니다.|
| `distanceViolationPersonCount` | int| `minimumDistanceThreshold` 또는 `maximumDistanceThreshold`를 위반하여 감지된 사람 수|
| `zone` | 문자열 | 사람 사이의 거리를 모니터링한 영역을 나타내는 다각형의 "name" 필드|
| `trigger` | 문자열| SPACEANALYTICS_CONFIG의 `trigger` 값에 따라 'event' 또는 'interval'인 트리거 형식|

| detections 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 감지 ID|
| `type` | 문자열| 검색 유형|
| `region` | collection| 값 컬렉션|
| `type` | 문자열| 영역 유형|
| `points` | collection| 영역 유형이 RECTANGLE인 경우 왼쪽 위 및 오른쪽 아래 지점 |
| `confidence` | float| 알고리즘 신뢰도|
| `centerGroundPoint` | 2개의 부동 소수점 값| `x`, `y` 값은 지면에 있는 사람에 대해 유추된 위치의 좌표입니다. `x` 및 `y`는 바닥이 수평이라고 가정한 바닥 평면의 좌표입니다. 카메라의 위치가 원점입니다. |

`centerGroundPoint`를 계산할 때 `x`는 카메라 이미지 평면의 수직선을 따라 카메라에서 사람까지의 거리입니다. `y`는 카메라 이미지 평면의 수평선을 따라 카메라에서 사람까지의 거리입니다. 

![지면 중심 지점의 예](./media/spatial-analysis/x-y-chart.png) 

이 예에서 `centerGroundPoint`는 `{x: 4, y: 5}`입니다. 즉, 카메라에서 4피트, 오른쪽으로 5피트 떨어진 사람이 방을 위에서 아래로 보고 있습니다.


| sourceInfo 필드 이름 | 형식| Description|
|---------|---------|---------|
| `id` | 문자열| 카메라 ID|
| `timestamp` | date| JSON 페이로드를 내보낸 UTC 날짜|
| `width` | int | 비디오 프레임 너비|
| `height` | int | 비디오 프레임 높이|
| `frameId` | int | 프레임 식별자|
| `cameraCallibrationInfo` | collection | 값 컬렉션|
| `status` | 문자열 | `state[;progress description]` 형식의 보정 상태입니다. 상태는 `Calibrating`, `Recalibrating`(다시 보정이 사용하도록 설정된 경우) 또는 `Calibrated`일 수 있습니다. 현재 보정 프로세스의 진행률을 표시하는 데 사용되는 `Calibrating` 및 `Recalibrating` 상태인 경우에만 진행률 설명 부분이 유효합니다.|
| `cameraHeight` | float | 지면 위의 카메라 높이(피트)입니다. 이는 자동 보정에서 유추됩니다. |
| `focalLength` | float | 카메라의 초점 길이(픽셀)입니다. 이는 자동 보정에서 유추됩니다. |
| `tiltUpAngle` | float | 세로 방향의 카메라 기울기 각도입니다. 이는 자동 보정에서 유추됩니다.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>cognitiveservices.vision.spatialanalysis AI 인사이트에 대한 JSON 형식

이 작업의 출력은 구성된 `events`에 따라 달라집니다. 예를 들어 이 작업에 대해 구성된 `zonecrossing` 이벤트가 있는 경우 출력은 `cognitiveservices.vision.spatialanalysis-personcrossingpolygon`과 동일합니다.

## <a name="use-the-output-generated-by-the-container"></a>컨테이너에서 생성된 출력 사용

공간 분석 감지 또는 이벤트를 애플리케이션에 통합할 수 있습니다. 고려해야 할 몇 가지 방법은 다음과 같습니다. 

* Azure IoT Hub 엔드포인트에 연결하고 이벤트를 받을 수 있도록 Azure Event Hub SDK를 선택한 프로그래밍 언어에 사용합니다. 자세한 내용은 [기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)를 참조하세요. 
* 이벤트를 다른 엔드포인트에 보내거나 이벤트를 데이터 스토리지에 저장하도록 Azure IoT Hub에서 **메시지 라우팅** 을 설정합니다. 자세한 내용은 [IoT Hub 메시지 라우팅](../../iot-hub/iot-hub-devguide-messages-d2c.md)을 참조하세요. 
* 이벤트가 도착하면 해당 이벤트를 실시간으로 처리하고 시각화를 만들도록 Azure Stream Analytics 작업을 설정합니다. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>대규모 공간 분석 작업 배포(여러 카메라)

GPU의 최고 성능과 사용률을 얻기 위해 그래프 인스턴스를 사용하여 모든 공간 분석 작업을 여러 카메라에 배포할 수 있습니다. 아래는 15대의 카메라에서 `cognitiveservices.vision.spatialanalysis-personcrossingline` 작업을 실행하는 샘플입니다.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_calibrator0": {
                "node": "PersonCrossingLineGraph/cameracalibrator",
                "parameters": {
                    "CAMERACALIBRATOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true, \"enable_zone_placement\": true}",
                    "CALIBRATION_CONFIG": "{\"enable_recalibration\": true, \"quality_check_frequency_seconds\": 86400}",
                }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
            "PersonCrossingLineGraph/cameracalibrator": "shared_calibrator0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Name | Type| 설명|
|---------|---------|---------|
| `batch_size` | int | 모든 카메라의 해상도가 동일한 경우 `batch_size`를 해당 작업에 사용할 카메라 수로 설정하고, 그렇지 않으면 `batch_size`를 1로 설정하거나 일괄 처리가 지원되지 않음을 나타내는 기본값(1)으로 둡니다. |

## <a name="next-steps"></a>다음 단계

* [인원 수를 세는 웹 애플리케이션 배포](spatial-analysis-web-app.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 선 배치 가이드](spatial-analysis-zone-line-placement.md)
