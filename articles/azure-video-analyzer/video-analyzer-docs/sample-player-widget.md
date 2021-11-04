---
title: Azure Video Analyzer 플레이어 위젯 샘플
description: 이 참조 문서에서는 Video Analyzer 플레이어 위젯 샘플 응용 프로그램에 대 한 간략 한 개요를 제공 합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 842ffa07a956c2bb90bd039f25c49bf72467ecee
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557778"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Azure Video Analyzer 플레이어 위젯 샘플

이 샘플 응용 프로그램은 비디오 재생, 영역 그리기 및 비디오 클립 생성 기능과 비디오 분석기의 플레이어 위젯을 통합 하는 방법을 보여 줍니다.

* [Ava c # 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp) 복제
* [ **Src/video player** 폴더의 추가 정보](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md) 에 있는 지침을 따르세요.

## <a name="get-started"></a>시작
추가 정보 지침을 수행 하 고 앱이에서 실행 되 고 있는 경우 http://localhost:3000/
1. ' 비디오 가져오기 '를 클릭 합니다.
2. 비디오 분석기 계정의 모든 비디오 드롭다운 목록에서 비디오를 선택 합니다.

## <a name="video-player"></a>비디오 플레이어
비디오 플레이어 페이지는 포털에 표시 된 것 처럼 일반적인 플레이어를 표시 합니다.  
![비디오 플레이어의 스크린샷](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>영역 서랍
영역 서랍에서는 다각형을 그리고 비디오에서 선을 그려 영역을 만들 수 있습니다. 해당 영역 및 줄의 좌표를 받도록 이러한 영역 및 줄을 저장할 수도 있습니다.  
  
**예:**
```json
{
  "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
  "name": "Line 1",
  "line": [
    {
      "x": 0.7536813922356091,
      "y": 0.4358353510895884
    },
    {
      "x": 0.7563587684069611,
      "y": 0.7699757869249395
    }
  ]
}{
  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
  "name": "Zone 2",
  "polygon": [
    {
      "x": 0.11780455153949129,
      "y": 0.7627118644067796
    },
    {
      "x": 0.15261044176706828,
      "y": 0.774818401937046
    },
    {
      "x": 0.0749665327978581,
      "y": 0.8983050847457628
    },
    {
      "x": 0.04551539491298527,
      "y": 0.864406779661017
    }
  ]
}
```
이러한 좌표 복사는 **클립보드로 복사** 단추를 사용 하 여 수행할 수 있습니다. 각 영역 및 줄 옆에 있는 세 개의 점을 사용 하 여 영역 및 줄의 이름을 바꾸고 삭제할 수 있습니다.  
![영역 서랍의 스크린샷](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>비디오 클립
비디오 클립을 사용 하면 시작 날짜와 시간을 종료 날짜 및 시간과 함께 선택할 수 있습니다. *추가* 단추를 사용 하 여 비디오 클립을 생성할 수 있습니다.
드롭다운 목록에서 생성 된 클립 중 하나를 선택할 수 있습니다. 여기서 각 클립의 제목은 시작 날짜와 종료 날짜 및 시간입니다.
비디오 클립은 일반적인 비디오 플레이어 형식으로 볼 수 있습니다.  
![비디오 클립의 스크린샷](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>다음 단계
이러한 [지침](./player-widget.md)을 사용 하 여 사용자 지정 Video Analyzer 플레이어 위젯을 만들어 보세요.
