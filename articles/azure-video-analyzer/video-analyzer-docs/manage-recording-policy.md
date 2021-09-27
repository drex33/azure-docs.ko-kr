---
title: Azure Video Analyzer를 사용하여 녹음/녹화 정책 관리
description: 이 항목에서는 Azure Video Analyzer를 사용하여 녹음/녹화 정책을 관리하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 95e5decc8092c6a40e368c395da61fd8b9904e30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762002"
---
# <a name="manage-recording-policy-with-video-analyzer"></a>Video Analyzer를 사용하여 녹음/녹화 정책 관리

Azure Video Analyzer를 사용하여 몇 주, 몇 달 또는 몇 년 동안 라이브 비디오를 클라우드에 [녹음/녹화](video-recording.md)할 수 있습니다. 이 녹음/녹화는 [연속](continuous-video-recording.md)되거나 스파스이거나 [이벤트 기반](event-based-video-recording-concept.md)일 수 있습니다. 두 경우 모두 녹음/녹화는 몇 년 동안 지속될 수 있습니다. Azure 스토리지에 기본 제공되는 [수명 주기 관리 도구](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)를 사용하여 해당 클라우드 보관의 길이(일)를 관리할 수 있습니다.  

Video Analyzer 계정은 Azure Storage 계정에 연결되어 있으며, 비디오 리소스에 녹화할 때 미디어 데이터는 스토리지 계정의 컨테이너에 기록됩니다. 수명 주기 관리를 통해 스토리지 계정에 대한 [정책](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)을 정의할 수 있습니다. 이 경우 다음과 같은 [규칙](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#lifecycle-management-rule-definition)을 지정할 수 있습니다.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

위의 규칙은 다음과 같습니다.

* 스토리지 계정의 모든 블록 Blob에 적용됩니다.
* Blob이 30일을 넘은 경우 [핫 액세스 계층에서 쿨](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)로 이동하도록 지정합니다.
* 그리고 Blob이 90일을 넘으면 삭제됩니다.

Video Analyzer를 사용하여 비디오 리소스에 녹화하는 경우 스토리지 계정에 기록되기 전에 집계할 최소 비디오 기간(초)을 지정하는 `segmentLength` 속성을 지정합니다. 비디오 리소스에는 일련의 세그먼트가 포함되며, 각 세그먼트에는 이전보다 최신인 `segmentLength` 타임스탬프가 있습니다. 수명 주기 관리 정책이 시작되면 지정된 임계값보다 오래된 세그먼트를 삭제합니다. 그러나 Video Analyzer API를 통해 나머지 세그먼트에 계속 액세스하고 재생할 수 있습니다. 자세한 내용은 [녹음/녹화 재생](playback-recordings-how-to.md)을 참조하세요. 

## <a name="limitations"></a>제한 사항

다음은 수명 주기 관리에 대한 몇 가지 알려진 제한 사항입니다.

* 정책 내에 최대 100개의 규칙을 가질 수 있으며 각 규칙은 최대 10개의 컨테이너를 지정할 수 있습니다. 따라서 다른 녹음/녹화 정책이 필요한 경우(예: 주차장을 향한 카메라의 경우 3일, 하역장에 있는 카메라의 경우 30일, 계산대 뒤에 있는 카메라의 경우 180일 보관) 하나의 스토리지 계정으로 최대 1000대의 카메라에 대한 규칙을 사용자 지정할 수 있습니다.
* 수명 주기 관리 정책 업데이트는 즉시 실행되지 않습니다. 자세한 내용은 [이 FAQ 섹션](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#faq)을 참조하세요.
* Blob이 쿨 계층으로 이동하는 정책을 적용하도록 선택하는 경우 해당 보관 부분의 재생에 영향을 줄 수 있습니다. 추가 대기 시간 또는 간헐적 오류가 표시될 수 있습니다. Video Analyzer는 보관 계층의 콘텐츠 재생을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

[녹화본 재생](playback-recordings-how-to.md)