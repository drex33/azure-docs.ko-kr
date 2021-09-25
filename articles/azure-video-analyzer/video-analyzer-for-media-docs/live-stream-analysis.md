---
title: Azure Video Analyzer for Media(이전의 Video Indexer)를 사용한 라이브 스트림 분석
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 라이브 스트림 분석을 수행하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 18017bbe4d83d11e360efebd7d69eed8d67ae15a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627447"
---
# <a name="live-stream-analysis-with-video-analyzer-for-media"></a>Video Analyzer for Media를 사용하여 라이브 스트림 분석

Azure Video Analyzer for Media(이전의 Video Indexer)는 비디오 및 오디오 파일에서 깊이 있는 인사이트를 오프라인으로 추출하기 위해 설계된 Azure 서비스입니다. 이미 만든 지정된 미디어 파일을 미리 분석하기 위한 것입니다. 그러나 일부 사용 사례의 경우 운영 및 기타 사용 사례의 잠금을 신속하게 해제하려면 가능한 빨리 라이브 피드에서 미디어 인사이트를 가져오는 것이 중요합니다. 예를 들어 콘텐츠 생산자가 라이브 스트림의 이러한 풍부한 메타데이터를 사용하여 TV 프로덕션을 자동화할 수 있습니다.

이 문서에서 설명하는 솔루션을 통해 고객은 라이브 피드에서 근 실시간 해상도로 Video Analyzer for Media를 사용할 수 있습니다. 인덱싱 지연 시간은 인덱싱되는 데이터의 청크, 입력 해상도, 콘텐츠 형식, 이 프로세스에 사용되는 컴퓨팅에 따라 이 솔루션을 사용하는 경우 4분이나 걸릴 수 있습니다.

![라이브 스트림의 Video Analyzer for Media 메타데이터](./media/live-stream-analysis/live-stream-analysis01.png)

*그림 1 - 라이브 스트림에 Video Analyzer for Media 메타데이터를 표시하는 샘플 플레이어*

현재 [스트림 분석 솔루션](https://aka.ms/livestreamanalysis)은 Azure Functions 및 두 개의 Logic Apps를 사용하여 Video Analyzer for Media로 Azure Media Services의 라이브 채널에서 라이브 프로그램을 처리하고 근 실시간으로 생성된 스트림을 보여 주는 결과를 Azure Media Player에 표시합니다.

상위 수준에서는 두 가지 주요 단계로 구성됩니다. 첫 번째 단계는 60초마다 실행되고, 마지막 60초 동안 재생된 하위 클립을 가져와서 자산을 만들고 Video Analyzer for Media를 통해 인덱싱합니다. 그런 다음 인덱싱이 완료되면 두 번째 단계가 호출됩니다. 캡처한 인사이트가 처리되고 Azure Cosmos DB에 전송되고 인덱싱된 하위 클립이 삭제됩니다.

샘플 플레이어는 라이브 스트림을 재생하고 전용 Azure 함수를 사용하여 Azure Cosmos DB에서 인사이트를 가져옵니다. 라이브 비디오와 동기화되는 메타데이터 및 썸네일을 표시합니다.

![클라우드에서 1분마다 라이브 스트림을 처리하는 두 개의 논리 앱](./media/live-stream-analysis/live-stream-analysis02.png)

*그림 2 - 클라우드에서 1분마다 라이브 스트림을 처리하는 두 개의 논리 앱.*

## <a name="step-by-step-guide"></a>단계별 가이드 

결과를 배포하기 위한 전체 코드와 단계별 가이드는 [를 사용한 라이브 미디어 분석에 대한 GitHub 프로젝트](https://aka.ms/livestreamanalysis)에서 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Video Analyzer for Media 개요](video-indexer-overview.md)
