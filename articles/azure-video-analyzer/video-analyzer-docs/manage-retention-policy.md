---
title: Azure Video Analyzer를 통해 보존 정책 관리
description: 이 항목에서는 Azure Video Analyzer를 사용하여 보존 정책을 관리하는 방법에 대해 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 10/17/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0da96aa507db1f7514ee394be52a8ca7723562db
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103015"
---
# <a name="manage-retention-policy-with-video-analyzer"></a>Video Analyzer를 통해 보존 정책 관리

Azure Video Analyzer를 사용하여 단일 원본에서 몇 초에서 몇 년 분량의 비디오 콘텐츠에 걸쳐 오랜 시간 동안 콘텐츠를 보관할 수 있습니다. 콘텐츠의 보존 정책을 명시적으로 제어하여 이전 콘텐츠가 주기적으로 잘리도록 할 수 있습니다. 다양한 보관에 다른 정책을 적용할 수 있습니다. 예를 들어, 가장 최근 3일 동안의 녹화와 계산원의 데스크 뒤의 카메라에서 가장 최근 30일간의 녹화를 유지할 수 있습니다.

## <a name="retention-period"></a>보존 기간

Video Analyzer 계정의 각 [비디오 리소스에](terminology.md#video) 대해 필요에 따라 보존 정책을 적용할 수 있습니다. 보존 기간을 지정하면(아래에 설명된 대로) 서비스는 해당 기간보다 오래된 콘텐츠를 주기적으로 트리밍합니다. 정책을 지정하지 않으면 콘텐츠가 무기한 저장됩니다.

보존 기간은 일반적으로 파이프라인 토폴로지 생성 시 비디오 싱크 노드의 속성에 설정됩니다. 섹션에서 속성을 찾을 수 `retentionPeriod` `videoCreationProperties` 있습니다. 이름에서 알 수 있듯이 이 토폴로지를 사용하여 라이브 파이프라인이 활성화되고 새 비디오 리소스가 만들어질 때 속성이 사용됩니다. 이러한 만들기 속성은 비디오 리소스가 이미 있는 경우(예: 파이프라인이 다시 활성화된 경우) 사용되지 않습니다. 다음은 모든 요금제 카메라에 3일 보존 정책을 적용할 수 있는 방법의 예입니다.

```
{
  "@type": "#Microsoft.VideoAnalyzer.VideoSink",
  "name": "{nodeName}",         
  "videoName": "{nameForVideoResource}",
  "videoCreationProperties":
  {
    "title": "{titleForVideo}",
    "description": "{descriptionForVideo}",
    "segmentLength": "PT30S",
    "retentionPeriod": "P3D"
  },
}
```

Azure Portal `retentionPeriod` 사용하거나 REST API 통해 비디오 리소스의 속성을 설정하거나 업데이트할 [](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json)수도 있습니다. 다음은 3일 보존 정책을 설정하는 예입니다.

```
"archival":
{
   "retentionPeriod": "P3D",
},
```

## <a name="rules-and-limitations"></a>규칙 및 제한 사항

* 보존 정책이 적용되는 데 최대 24시간이 걸릴 수 있습니다.
* 형식: `retentionPeriod` 속성은 ISO 8601 기간 값을 사용하며, 이 기간 값은 며칠 이상이어야 합니다. 허용되는 값 예: P1D, P20D, P1M, P365D, P1Y, P5Y
    * 최소값은 P1D이고 최대값은 P5Y입니다.
    * null로 설정하면 보존 정책이 지워지고 비디오 콘텐츠가 무기한 저장됩니다.
* 보존 정책을 사용하면 서비스에서 Blob을 찾아서 삭제할 때 추가 Azure Storage 트랜잭션 비용이 발생합니다.

## <a name="next-steps"></a>다음 단계

[녹화본 재생](playback-recordings-how-to.md)
