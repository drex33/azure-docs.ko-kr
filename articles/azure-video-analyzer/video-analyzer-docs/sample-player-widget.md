---
title: Azure Video Analyzer 플레이어 위젯 샘플
description: 이 참조 문서에서는 Video Analyzer 플레이어 위젯 샘플 애플리케이션에 대한 간략한 개요를 제공합니다.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058494"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Azure Video Analyzer 플레이어 위젯 샘플

이 샘플 애플리케이션은 비디오 재생, 영역 그리기 및 비디오 클립 생성 기능과 Video Analyzer의 플레이어 위젯의 통합을 보여 제공합니다.

* [AVA C# 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp) 복제
* [ **src/video-player** 폴더의 README에 있는](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md) 지침을 따릅니다.

## <a name="get-started"></a>시작하기
README 지침에 따라 앱이 실행되고 나면 http://localhost:3000/
1. '비디오 받기'를 클릭합니다.
2. Video Analyzer 계정의 모든 비디오 드롭다운 목록에서 비디오를 선택합니다.

## <a name="video-player"></a>비디오 플레이어
Video Player 페이지는 포털에 보이는 일반적인 플레이어를 표시합니다.  
![비디오 플레이어의 스크린샷.](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>영역 서랍
영역 서랍을 사용하면 비디오에 다각형을 그리고 선을 그려 영역을 만들 수 있습니다. 이러한 영역과 선을 저장하여 해당 영역과 선의 좌표를 받을 수도 있습니다.  
  
**예:**
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
**클립보드에** 복사 단추를 사용하여 이러한 좌표를 복사할 수 있습니다. 각 영역과 선 옆에 있는 세 개의 점을 사용하여 영역과 줄의 이름을 바꾸고 삭제할 수 있습니다.  
![영역 서랍의 스크린샷.](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>비디오 클립
비디오 클립을 사용하면 종료 날짜 및 시간과 함께 시작 날짜 및 시간을 선택할 수 있습니다. *추가* 단추를 사용하여 비디오 클립을 생성할 수 있습니다.
드롭다운 목록에서 생성된 클립을 선택할 수 있습니다. 여기서 각 클립의 제목은 시작 날짜와 종료 날짜 및 시간입니다.
(예: 형식) 그러면 비디오 클립을 일반적인 비디오 플레이어 형식으로 볼 수 있습니다.  
![비디오 클립의 스크린샷.](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>다음 단계
다음 [지침에 따라](./player-widget.md) 고유한 사용자 지정 Video Analyzer 플레이어 위젯을 만들어 보세요.

