---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 장면, 샷 및 키 프레임
description: 이 항목에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 장면, 샷 및 키 프레임에 대한 개요를 제공합니다.
ms.topic: how-to
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b6825b5a50793589a07c16ef274d1e4231a2d4e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655946"
---
# <a name="scenes-shots-and-keyframes"></a>장면, 샷 및 키 프레임

Azure Video Analyzer for Media(이전의 Video Indexer)는 구조적 속성과 의미 체계 속성에 따라 비디오를 temporal 단위로 분할하는 기능을 지원합니다. 이 기능을 통해 고객은 다양한 세분성을 기준으로 비디오 콘텐츠를 쉽게 검색, 관리, 편집할 수 있습니다. 예를 들어, 이 항목에 설명된 장면, 샷, 키 프레임을 기준으로 할 수 있습니다.   

![장면, 샷 및 키 프레임](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>장면 검색  
 
Video Analyzer for Media는 시각 신호를 기반으로 비디오에서 장면이 변경되는 시기를 결정합니다. 장면은 단일 이벤트를 묘사하며 의미상 관련된 일련의 연속 샷으로 구성됩니다. 장면 썸네일은 기본 샷의 첫 번째 키 프레임입니다. Video Analyzer for Media는 연속 샷에서 색 일관성을 기준으로 비디오를 장면으로 분할하고 각 장면의 시작 및 종료 시간을 검색합니다. 장면 검색은 비디오의 의미 체계 측면을 수량화하는 작업을 포함하므로 어려운 작업으로 간주합니다.

> [!NOTE]
> 3개 이상의 장면을 포함하는 비디오에 적용됩니다.

## <a name="shot-detection"></a>샷 검색

Video Analyzer for Media는 인접한 프레임의 색 구성표에서 급격한 전환 및 점진적인 전환을 둘 다 추적하여 시각 신호에 따라 비디오에서 샷이 변경되는 시기를 결정합니다. 샷의 메타데이터에는 시작 및 종료 시간과 해당 샷에 포함된 키 프레임 목록이 포함됩니다. 샷은 동시에 동일한 카메라에서 가져온 연속 프레임입니다.

## <a name="keyframe-detection"></a>키 프레임 검색

Video Analyzer for Media는 각 샷을 가장 잘 표현하는 프레임을 선택합니다. 키 프레임은 미적 속성(예: 대비 및 안정성)을 기준으로 전체 비디오에서 선택된 대표 프레임입니다. Video Analyzer for Media는 고객이 키 프레임을 고해상도 이미지로 추출할 수 있는 기준인 샷 메타데이터의 일부로 키 프레임 ID 목록을 검색합니다.  

### <a name="extracting-keyframes"></a>키 프레임 추출

비디오의 고해상도 키 프레임을 추출하려면 먼저 비디오를 업로드하고 인덱싱해야 합니다.

![키 프레임](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-analyzer-for-media-website"></a>Video Analyzer for Media 웹 사이트 사용

Video Analyzer for Media 웹 사이트를 사용하여 키 프레임을 추출하려면 비디오를 업로드하고 인덱싱합니다. 인덱싱 작업이 완료되면 **다운로드** 단추를 클릭하고 **아티팩트(ZIP)** 를 선택합니다. 그러면 아티팩트 폴더가 컴퓨터에 다운로드됩니다. 

![“아티팩트”가 선택된 “다운로드” 드롭다운을 보여 주는 스크린샷](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
폴더의 압축을 풀고 폴더를 엽니다. *_KeyframeThumbnail* 폴더에서 비디오에서 추출된 모든 키 프레임을 찾습니다. 

#### <a name="with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API 사용

Video Indexer API를 사용하여 키 프레임을 가져오려면 [비디오 업로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) 호출을 사용하여 비디오를 업로드하고 인덱싱합니다. 인덱싱 작업이 완료되면 [비디오 인덱스 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)를 호출합니다. 그러면 Video Indexer가 JSON 파일의 콘텐츠에서 추출한 모든 인사이트가 제공됩니다.  

각 샷 메타데이터의 일부로 키 프레임 ID 목록이 제공됩니다. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

이제 [썸네일 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) 호출에서 이 키 프레임 ID를 각각 실행해야 합니다. 그러면 각 키 프레임 이미지가 컴퓨터에 다운로드됩니다. 

## <a name="editorial-shot-type-detection"></a>편집 샷 유형 검색

키 프레임은 출력 JSON의 샷과 연결됩니다. 

인사이트 JSON의 개별 샷과 연결된 샷 유형은 해당 편집 유형을 나타냅니다. 비디오를 클립, 트레일러로 편집하거나 예술적 용도로 특정 키 프레임 스타일을 검색하는 경우 이 샷 유형 특징이 유용할 수 있습니다. 각 샷의 첫 번째 키 프레임 분석에 따라 다양한 유형이 결정됩니다. 샷은 첫 번째 키 프레임에 나타나는 얼굴의 배율, 크기 및 위치로 식별됩니다. 

샷 크기 및 배율은 카메라와 프레임에 나타나는 얼굴 간 거리를 기준으로 결정됩니다. 이러한 속성을 사용하여 Video Analyzer for Media는 다음 샷 유형을 감지합니다.

* 와이드: 사람의 전신을 표시합니다.
* 중간: 사람의 상반신 및 얼굴을 표시합니다.
* 클로즈업: 주로 사람의 얼굴을 표시합니다.
* 익스트림 클로즈업: 화면을 채우는 사람의 얼굴을 표시합니다. 

샷 유형은 프레임 중심을 기준으로 주체 캐릭터의 위치에 따라 결정될 수도 있습니다. 이 속성은 Video Analyzer for Media에서 다음 샷 유형을 정의합니다.

* 왼쪽 얼굴: 사람이 프레임의 왼쪽에 나타납니다.
* 가운데 얼굴: 사람이 프레임의 가운데 영역에 나타납니다.
* 오른쪽 얼굴: 사람이 프레임의 오른쪽에 나타납니다.
* 실외: 사람이 실외 설정에 나타납니다.
* 실내: 사람이 실내 설정에 나타납니다.

추가 특징:

* 2인 샷: 중간 크기의 두 사람 얼굴을 표시합니다.
* 여러 얼굴: 세 명 이상의 사람입니다.


## <a name="next-steps"></a>다음 단계

[API에 의해 생성된 Video Analyzer for Media 출력 검사](video-indexer-output-json-v2.md#scenes)
